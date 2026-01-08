
# AWS 환경에서 서버 구축, 배포, 자동화하기 위한 하나의 시나리오

- 아키텍처 개요
	- 컨테이너 오케스트레이션: AWS ECS (Elastic Container Service)
	- 서버 실행 방식: AWS Fargate (서버리스)
	- 컨테이너 이미지 저장소: Amazon ECR (Elastic Container Registry)
	- CI/CD 배포 자동화: AWS CodePipeline + AWS CodeBuild
		- CodeDeploy (선택사항): ECS에 무중단 배포 설정 가능
	- 데이터 저장: Amazon RDS (관계형 데이터베이스) + Amazon S3 (파일 저장)
	- 모니터링 및 로깅: Amazon CloudWatch

- 시나리오
	- 서버리스 & 자동화에 최적화된 AWS 환경을 구축
	- ECS + Fargate를 이용해 서버 부담 없이 컨테이너 실행
	- ECR을 사용해 컨테이너 이미지를 효율적으로 관리
	- CodePipeline + CodeBuild를 이용해 CI/CD 자동화
	- RDS + S3를 이용해 데이터 및 파일을 관리
	- CloudWatch를 활용한 실시간 모니터링 및 로그 관리
	- 배포 자동화 (CodePipeline + CodeBuild) 단계와 모니터링 (CloudWatch) 단계에 Lambda + Slack Webhook을 활용한 알림 기능 추가

# AWS 비용 예측 (ChatGPT)

100명 규모의 AWS 아키텍처를 기반으로 예상 비용을 계산하겠습니다.

💡 가정 조건 (100명 서비스 기준)

컨테이너 실행 방식: AWS Fargate (ECS)
컨테이너 리소스: 1 vCPU, 2GB RAM
태스크 개수: 1개 (트래픽 증가 시 Auto Scaling 고려)
운영 시간: 하루 16시간 (월 30일)
CI/CD 배포 빈도: 하루 1~2회
RDS 데이터베이스: db.t3.micro (MySQL, PostgreSQL 등)
S3 파일 저장: 월 100GB 예상
CloudWatch 로그 저장: 월 5GB 예상

📌 AWS 비용 계산

1️⃣ Fargate (ECS) 실행 비용

Task CPU 1 vCPU
Task Memory 2 GB
Task 개수 1개
Hours per month 480시간 (16시간 x 30일)
Region 서울 (ap-northeast-2)
Storage (Ephemeral, GiB) 10GB
CPU 비용: 1 vCPU × 480시간 × $0.04656 = $44.67
Memory 비용: 2GB × 480시간 × $0.00511 = $9.82
임시 스토리지: 10GB × 480시간 × $0.000111 = $0.80
Fargate 총 비용: $55~$60 / 월

2️⃣ ECR (Elastic Container Registry) 비용

저장 공간 10GB
데이터 전송 5GB (배포 시)
저장 비용: 10GB × $0.10 = $1.00
전송 비용: 5GB × $0.09 = $0.45
ECR 총 비용: $1~$2 / 월

3️⃣ CI/CD 비용 (CodePipeline + CodeBuild)

빌드 실행 횟수 하루 2회
빌드 시간 5분 (0.083시간)
빌드 환경 1 vCPU, 3GB RAM
CodePipeline 비용: $1.00 / 월 (고정)
CodeBuild 비용:
2회/일 × 30일 = 60회
60회 × 0.083시간 × $0.005 / 빌드 분당 비용
약 $0.25 / 월
CI/CD 총 비용: $1.25~$2 / 월

4️⃣ RDS (Amazon Relational Database Service)

DB 인스턴스 유형 db.t3.micro
스토리지 20GB
백업 스토리지 5GB
인스턴스 비용: db.t3.micro (1 vCPU, 1GB RAM) = $15.00 / 월
스토리지 비용: 20GB × $0.115 / GB = $2.30 / 월
백업 스토리지: 5GB 무료 (초과 시 비용 추가)

RDS 총 비용: $17~$18 / 월

5️⃣ S3 (파일 저장소)

저장 용량 100GB
데이터 전송 (다운로드) 50GB
저장 비용: 100GB × $0.025 / GB = $2.50 / 월
전송 비용: 50GB × $0.09 / GB = $4.50 / 월
S3 총 비용: $7~$8 / 월

6️⃣ CloudWatch (로그 및 모니터링)

로그 저장 5GB
지표 데이터 1백만 개 (기본 무료)
로그 저장 비용: 5GB × $0.03 / GB = $0.15 / 월
지표 및 알람: 기본 무료 (추가 설정 시 비용 발생)
CloudWatch 총 비용: $0~$1 / 월

📌 총 비용 정리

서비스 예상 비용
Fargate (ECS) $55~$60
ECR (컨테이너 저장소) $1~$2
CI/CD (CodePipeline + CodeBuild) $1.25~$2
RDS (데이터베이스) $17~$18
S3 (파일 저장소) $7~$8
CloudWatch (로그 모니터링) $0~$1
총 예상 비용 $80~$90 / 월

📌 비용 절감 방법

Fargate Auto Scaling 설정
피크 타임이 아닐 때 태스크 개수 1 → 0으로 자동 축소
사용량에 따라 비용 절감 가능
S3 데이터 전송 최적화
CloudFront를 이용한 캐싱 적용 (외부 다운로드 트래픽 절감)
RDS 요금 절감
Amazon RDS Reserved Instance(1년 예약 인스턴스) 사용 시 약 30% 절감 가능
Fargate Spot 사용 (비용 최대 70% 절감)
일반 태스크와 별도로 Spot 태스크 설정하여 실행

📌 결론

100명 규모에서 월 $80~$90 수준으로 운영 가능
오토스케일링 및 Spot 활용 시 비용 절감 가능
CI/CD 자동화, 모니터링, 무중단 배포 환경까지 포함된 최적화된 아키텍처
이제 AWS 계산기에서 위 값을 입력하여 직접 조정하면서 비용을 최적화할 수 있습니다. 🚀

# OAuth

- 애플리케이션이 사용자 정보를 직접 저장하지 않고, 신뢰할 수 있는 인증 제공자(Google, Facebook, GitHub 등)을 통해 사용자 인증 및 권한 부여를 수행하는 표준 프로토콜

- Authorization Code Grant
	- 여러 인증 방식(Grant Type) 중 가장 권장되는 인증 방식

- OAuth와 OpenID Connect(OIDC)의 차이 -> 발급하는 Token의 차이
	- OAuth 2.0: 인증(X), 권한 부여(O) -> 리소스 접근을 위한 `access token` 발급
		- `access token`: API 호출을 위해 사용 (권한 부여)
		- `ID token`이 없으므로 사용자의 신원 확인 불가
	- OIDC = OAuth 2.0 + 사용자 인증 -> `access token`, `ID token`  함께 발급
		- `ID token`: 로그인한 사용자 정보를 포함 (인증)

- OAuth 2.0 + JWT를 이용한 로그인 구현 방식
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
			``` http
			POST https://oauth2.googleapis.com/token
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
	1. 클라이언트는 받은 JWT를 로컬 스토리지 또는 쿠키에 저장하여 이후 요청에서 사용
	    - API 요청 시 `Authorization` 헤더에 JWT 포함
		    ```http
		    GET /protected-resource
		    Authorization: Bearer your_jwt_token
```
	8. 백엔드는 API 요청 시 전달된 JWT를 검증하여 사용자 인증
	    - 예제 (Python, JWT 검증):
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
	9. `refresh_token`을 활용한 `access_token` 갱신
	    - 클라이언트는 `refresh_token`을 이용해 새로운 `access_token` 요청 가능
	    - 요청 예시:
		    ```http
		    POST https://oauth2.googleapis.com/token
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
	1. 로그아웃 처리
	    - 클라이언트 측에서 JWT 삭제 (로컬 스토리지 또는 쿠키 삭제)
	    - `refresh_token`은 Google OAuth에서 클라이언트가 명시적으로 해지하지 않는 한 유효함
	    - 강제 로그아웃이 필요할 경우 Google의 `revoke` API 호출 가능
		    ```http
		    POST https://accounts.google.com/o/oauth2/revoke
		    Content-Type: application/x-www-form-urlencoded
		    
		    token=your_refresh_token
```
	    - 성공 응답: HTTP 200 OK
	    - 이후 해당 `refresh_token`을 사용한 `access_token` 갱신이 불가능함