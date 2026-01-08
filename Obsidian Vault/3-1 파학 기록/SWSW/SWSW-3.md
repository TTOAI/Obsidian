> 목차
> - AWS 환경에서 서버 구축, 배포, 자동화하기 위한 하나의 시나리오
> - OAuth
> - OAuth 2.0 + JWT를 이용한 로그인 구현 방식

# OAuth

- 애플리케이션이 사용자 정보를 직접 저장하지 않고, 신뢰할 수 있는 인증 제공자(Google, Facebook, GitHub 등)을 통해 사용자 인증 및 권한 부여를 수행하는 표준 프로토콜
- Authorization Code Grant
    - 여러 인증 방식(Grant Type) 중 가장 권장되는 인증 방식
- OAuth와 OpenID Connect(OIDC)의 차이 -> 발급하는 Token의 차이
    - OAuth 2.0: 인증(X), 권한 부여(O) -> 리소스 접근을 위한 `access token` 발급
        - `access token`: API 호출을 위해 사용 (권한 부여)
        - `ID token`이 없으므로 사용자의 신원 확인 불가
    - OIDC = OAuth 2.0 + 사용자 인증 -> `access token`, `ID token` 함께 발급
        - `ID token`: 로그인한 사용자 정보를 포함 (인증)

## OAuth 2.0 + JWT를 이용한 로그인 구현 방식

1. Google Cloud Console에서 애플리케이션에 대한 OAuth 2.0 `client_id`, `client_secret` 생성 및 `redirect_uri` 추가
    
    - `client_id`
        - Google Cloud에서 발급한 OAuth 클라이언트 ID
        - 애플리케이션을 식별하는 공개 정보
        - Google OAuth 2.0 인증 서버에 요청할 때 사용
    - `client_secret`
        - 백엔드에서만 사용해야 하는 보안 정보
        - 한 번만 표시되므로 반드시 저장해야 됨
        - `authorization code`를 `access token`으로 교환할 때 백엔드에서 사용
    - `redirect_uri`: 백엔드의 엔드포인트
2. 클라이언트는 사용자가 Google 로그인 버튼을 클릭하면 Google OAuth 2.0 인증 서버로 리디렉션
    
    - `SCOPE`: 이후 백엔드로 전달될 `access_token`의 사용 범위를 나타냄
3. 사용자가 Google 로그인을 완료하면, Google OAuth 2.0 인증 서버는 클라이언트에서 지정한 redirect_uri로 Authorization Code를 포함한 응답을 보냄
    
4. 클라이언트는 `authorization code`를 백엔드 서버의 `redirect_uri`로 전달함
    
    - 클라이언트는 POST 요청을 보내거나, URL 매개변수로 전달할 수 있음
5. 백엔드는 `authorization code`를 받아 `access token`을 요청
    
    - `OpenID Connect`를 사용하여 `id_token`도 요청 가능
        
    - 요청 예시 (http)
        
        ```
        POST <https://oauth2.googleapis.com/token>
        Content-Type: application/x-www-form-urlencoded
        
        code=authorization_code
        &client_id=your_client_id
        &client_secret=your_client_secret
        &redirect_uri=your_redirect_uri
        &grant_type=authorization_code
        &scope=openid%20profile%20email%20offline
        
        ```
        
    - Google 응답 예시 (json)
        
        ```json
        {
        	"access_token": "ya29.a0AfH...",
        	"expires_in": 3600,
        	"token_type": "Bearer",
        	"id_token": "eyJhbGciOiJ...",
        	"scope": "openid profile email",
        	"refresh_token": "1//0gS9Uk38VGZb2..."
        }
        ```
        
    - `access_token`
        
        - 유효기간이 3600초(1시간)임
        - `scope`(`openid`, `email`, `profile`)과 관련된 API 요청에 사용됨
    - `refresh_token`
        
        - `access_token`은 유효 기간이 제한적임(보통 1시간)
        - `refresh_token`을 통해 새로운 `access_token` 발급 가능
        - 사용자가 다시 로그인하지 않아도 인증 상태 유지 가능
        - 백엔드에서만 사용
    - `token_type`
        
        - 기본값은 `Bearer`
            - `Bearer`
                - 토큰의 유형을 나타내는 방식
                - "이를 소지한 자가 인증된 사용자"라는 의미를 가짐
            - `Bearer` 토큰을 가지고 있으면 추가적인 인증 없이 API에 접근 가능
            - 여기에서는 `access_token`이 `Bearer` 토큰으로 사용됨
        - OpenID Connect 인증과 함께 제공되는 경우 `id_token`이 포함되면서 `token_type`이 생략되기도 함
6. 백엔드는 받은 `id_token`을 검증하여 사용자의 정보를 확인
    
    - `id_token`은 JWT(JSON Web Token) 형식이며, Base64URL로 인코딩됨
        
    - 이를 디코딩하여 사용자의 정보를 확인 가능
        
    - 예제 (Java, Spring Security 및 Google 라이브러리 사용):
        
        ```java
        import com.google.auth.oauth2.TokenVerifier;
        import com.google.auth.oauth2.GoogleIdToken;
        import com.google.auth.oauth2.GoogleIdTokenVerifier;
        import java.util.Collections;
        
        public class GoogleTokenVerifier {
        	private static final String CLIENT_ID = "your_client_id";
        
        	public static GoogleIdToken.Payload verifyIdToken(String token) {
        		try {
        			GoogleIdTokenVerifier verifier = new GoogleIdTokenVerifier.Builder(new NetHttpTransport(), new JacksonFactory())
        					.setAudience(Collections.singletonList(CLIENT_ID))
        					.build();
        			GoogleIdToken idToken = verifier.verify(token);
        			return idToken != null ? idToken.getPayload() : null;
        		} catch (Exception e) {
        			return null;
        		}
        	}
        }
        ```
        
    - 검증된 ID 토큰에는 사용자의 이메일, 이름 등의 정보가 포함됨
        
7. 백엔드는 검증된 사용자 정보를 기반으로 자체 JWT를 발급하여 클라이언트에 전달
    
    - 이 자체 JWT는 서버에서 사용자 인증을 위한 토큰으로 활용됨
        
    - JWT에는 유저 ID, 이메일, 권한 등의 정보를 포함 가능
        
    - 예제 (Java, Spring Boot 및 jjwt 라이브러리 사용):
        
        ```java
        import io.jsonwebtoken.Jwts;
        import io.jsonwebtoken.SignatureAlgorithm;
        import java.util.Date;
        
        public class JwtUtil {
        private static final String SECRET_KEY = "your_secret_key";
        
        public static String createJwt(String userId, String email) {
        	return Jwts.builder()
        			.setSubject(userId)
        			.claim("email", email)
        			.setExpiration(new Date(System.currentTimeMillis() + 3600000))
        			.signWith(SignatureAlgorithm.HS256, SECRET_KEY)
        			.compact();
        	}
        }
        ```
        
8. 클라이언트는 받은 JWT를 로컬 스토리지 또는 쿠키에 저장하여 이후 요청에서 사용
    
    - API 요청 시 `Authorization` 헤더에 JWT 포함
    
    ```
    GET /protected-resource
    Authorization: Bearer your_jwt_token
    ```
    
9. 백엔드는 API 요청 시 전달된 JWT를 검증하여 사용자 인증
    
    - 예제 (Java, JWT 검증):
        
        ```java
        import io.jsonwebtoken.Claims;
        import io.jsonwebtoken.Jwts;
        import io.jsonwebtoken.SignatureException;
        
        public class JwtValidator {
        	private static final String SECRET_KEY = "your_secret_key";
        
        	public static Claims verifyJwt(String token) {
        		try {
        			return Jwts.parser()
        					.setSigningKey(SECRET_KEY)
        					.parseClaimsJws(token)
        					.getBody();
        		} catch (SignatureException e) {
        			return null;
        		}
        	}
        }
        ```
        
10. `refresh_token`을 활용한 `access_token` 갱신
    
    - 클라이언트는 `refresh_token`을 이용해 새로운 `access_token` 요청 가능
        
    - 요청 예시:
        
        ```
        POST <https://oauth2.googleapis.com/token>
        Content-Type: application/x-www-form-urlencoded
        
        client_id=your_client_id
        &client_secret=your_client_secret
        &refresh_token=your_refresh_token
        &grant_type=refresh_token
        ```
        
    - 응답 예시:
        
        ```json
        {
        	"access_token": "new_access_token",
        	"expires_in": 3600,
        	"token_type": "Bearer"
        }
        ```
        
11. 로그아웃 처리
    
    - 클라이언트 측에서 JWT 삭제 (로컬 스토리지 또는 쿠키 삭제)
        
    - `refresh_token`은 Google OAuth에서 클라이언트가 명시적으로 해지하지 않는 한 유효함
        
    - 강제 로그아웃이 필요할 경우 Google의 `revoke` API 호출 가능
        
        ```
        POST <https://accounts.google.com/o/oauth2/revoke>
        Content-Type: application/x-www-form-urlencoded
        
        token=your_refresh_token
        ```
        
    - 성공 응답: HTTP 200 OK
        
    - 이후 해당 `refresh_token`을 사용한 `access_token` 갱신이 불가능함