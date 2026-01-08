
## 프론트엔드와 백엔드 간 상호작용 및 로그인 로직 구현 과정
1. **로그인 요청 시작**
    - 사용자가 "로그인" 버튼(또는 하이퍼링크)을 클릭
    - 프론트엔드는 `location.href`를 사용해 OAuth2 인증 서버의 인가(authorization) 엔드포인트로 브라우저를 리다이렉트
    - 이때 URL에는 `client_id`, `redirect_uri`, `response_type`, `scope` 등 필요한 쿼리 파라미터가 포함됨
2. **외부 인증 처리**
    - 사용자는 외부 인증 제공자의 로그인 페이지에서 자신의 자격 증명을 입력하고, 애플리케이션에 접근 권한을 부여할지 여부를 결정
    - 인증 제공자는 사용자 인증이 완료되면, 사전에 등록된 `redirect_uri`(보통 백엔드로의 uri)로 브라우저를 리다이렉트함. 이때 인가 코드(authorization code)가 쿼리 파라미터에 포함되어 전달됨
3. **인가 코드 수신 및 토큰 교환**
    - 백엔드는 해당 인가 코드를 받아 OAuth2 제공자의 토큰 엔드포인트로 요청을 보내고, 이를 통해 액세스 토큰을 발급받음
4. **세션 생성 및 사용자 정보 처리**
    - 발급받은 액세스 토큰을 사용해 백엔드는 사용자 정보를 조회하거나, 필요한 인증 처리를 수행한 후 사용자 세션을 생성
    - 이후 백엔드는 클라이언트에게 인증이 완료되었음을 알려주고, 프론트엔드는 사용자 인터페이스를 업데이트하여 로그인 상태를 반영

---
## 백엔드 안에서의 로직
- 우리가 개발하는 서버: 클라이언트 서버 (Java Spring, Spring Security 사용)
- OAuth2 로그인을 위한 서버 (Google, Naver 등): 인증 서버 및 리소스 서버

#### OAuth2AutorizationRequestRedirectFilter
- 'org.springframework.boot:spring-boot-starter-oauth2-client' 의존성에 의해 Spring Security FilterChain에 자동으로 추가됨
- OAuth2 로그인 과정에서 자동으로 거치는 필터이며 따로 커스텀할 필요 없음
-  소셜 로그인 시도 시에 해당 필터에 의해 인증 서버의 소셜 로그인 페이지 주소로 리다이렉트됨
	- Spring Security가 Google OAuth2 로그인을 위해 기본적으로 제공하는 엔드포인트: `/oauth2/authorization/google`
- 인증 서버에서 해당 서비스의 로그인 페이지를 사용자 화면에 띄움
- 로그인을 성공하면 인증 서버에 등록해둔 클라이언트 서버의 주소로 리다이렉트되며 Authorization Code를 발급하여 줌
- 인증 서버에 등록해두는 주소는 보통 "/login/oauth2/code/서비스명"
#### ↓
#### OAuth2LoginAuthenticationFilter
- 'org.springframework.boot:spring-boot-starter-oauth2-client' 의존성에 의해 Spring Security FilterChain에 자동으로 추가됨
- OAuth2 로그인 과정에서 자동으로 거치는 필터이며 따로 커스텀할 필요 없음
- 인증 서버에서 로그인 성공을 하면 클라이언트 서버의 설정 파일에 등록해둔 주소에 따라 우리 서버로 리다이렉트됨
- 클라이언트 서버의 설정 파일에 등록해둔 주소는 인증 서버에 등록해두는 주소와 동일함
- 인증 서버에서 발급 받은 Authorization Code를 다시 인증 서버에 보내 Access Token을 발급 받음
#### ↓
#### OAuth2LoginAuthenticationProvider
- 'org.springframework.boot:spring-boot-starter-oauth2-client' 의존성에 의해 Spring Security FilterChain에 자동으로 추가됨
- OAuth2 로그인 과정에서 자동으로 거치는 Provider이며 따로 커스텀할 필요 없음
- 인증 서버에서 발급 받은 Access Token을 리소스 서버로 보내 사용자 정보를 획득함
#### ↓
#### JwtAuthenticationFilter
- 어떤 요청이든 거쳐가는 필터
- OncePerRequestFilter를 상속 받음
- 쿠키에서 Access Token을 찾음
- Access Token 유무, 토큰 만료 여부 확인
- Access Token 기반으로 OAuth2User 생성
- customOAuth2User를 가지고 스프링 시큐리티 인증 토큰 생성
- SecurityContextHolder에 Authentication을 해당 토큰으로 설정
	- SecurityFilterChain 설정 과정에서 인증 및 권한이 필요하다고 설정한 요청에 대해서는 이 Authentication 객체를 통해 사용자 인증 여부 및 권한을 확인함
- 해당 요청 내에서 @AuthenticationPrincipal을 통해 customOAuth2User 사용 가능
#### ↓
#### CustomOAuth2UserService
- OAuth2 로그인 과정에서 거치는 서비스
- 스프링 시큐리티 필터 체인과 인증 서버, 리소스 서버를 모두 거친 후에 받아온 사용자 정보를 처리하는 서비스
- OAuth2UserService를 구현한 DefaultOAuth2UserService를 상속 받음
- loadUser() 메소드를 override하여 구현함
- loadUser() 메소드의 인자 값인 OAuth2UserRequest의 OAuth2User에 사용자 정보가 담겨 있음
- OAuth2User를 필요에 맞게 구현 및 상속하고 객체를 생성하여 리턴함
- 리턴된 객체는 Authenticatoin 객체에 저장됨
#### ↓
#### SuccessHandler
- OAuth2 로그인 과정에서 거치는 핸들러
- 인증 서버에서 로그인에 성공하고 리소스 서버에서 사용자 정보를 받아온 후 OAuth2UserService를 거쳐서 로그인에 성공한 후 실행되는 핸들러
- AuthenticationSuccessHandler를 구현한 SimpleUrlAuthenticationSuccessHandler를 상속 받음
- onAuthenticationSuccess() 메소드를 override하여 구현함
- Access Token 발급 (인증 서버에서 받는 Access Token과 다름)
	- onAuthenticationSuccess() 메소드의 인자 중 Authenticatoin 객체에 저장된 OAuth2User를 기반으로 AccessToken을 발급함
	- 로그인 성공 이후 이어지는 요청에 대해 Access Token의 유효성을 검증하고 JwtAuthenticationFilter에서 OAuth2User를 생성하는 데 사용
- Refresh Token 발급
- Access Token, Refresh Token 쿠키에 저장
- Refresh Token DB에 저장
- 사용자 정보가 DB에 이미 있는지 확인
- 신규 회원 여부에 따라 다른 url로 리디렉션

#### 핵심 로직
- JwtAuthenticationFilter, CustomOAuth2UserService, AuthenticationSuccessHandler를 구현함
	- AuthenticationSuccessHandler는 폼 로그인 방식에서 UsernamePasswordAuthenticationFilter와 비슷한 역할
- CustomOAuth2UserService에서 받아온 사용자 정보를 필요에 맞게 OAuth2User 구현 객체에 담음
- AuthenticationSuccessHandler에서 OAuth2User를 바탕으로 Access Token을 JWT 형식으로 발급하고 쿠키에 담아 사용자에게 보냄
- 로그인 이후 요청에 대해 JwtAuthenticationFilter가 요청에 포함된 Access Token의 유효성을 확인하고 Access Token을 바탕으로 OAuth2User를 생성하여 서버 세션에 포함함
- 요청을 처리할 때 서버 세션에 있는 OAuth2User를 바탕으로 로그인된 사용자의 신원을 파악함
- JWT 방식의 로그인 서비스를 구현하므로 스프링 시큐리티 설정에서 Session 정책을 Stateless로 설정함
-  서버 세션에 포함된 OAuth2User는 스프링 시큐리티의 Session Stateless 설정에 의해 해당 요청이 끝나면 사라짐
- 스프링 시큐리티 설정, JWT 검증 및 발급에 대한 코드는 따로 구현함

## 추가 구현 사항
#### 로그인 인증 실패 시
- 기본적으로 Spring Security에서는 보호된 URI에 대해 인증이 없는 상태로 요청이 들어오면, 내부적으로 AuthenticationEntryPoint를 호출
- AuthenticationEntryPoint를 통해 401 대신 302 리다이렉션 응답
- 이 때 OAuth2AuthorizationRequestRedirectFilter가 해당 요청을 받아 실제 소셜 로그인 페이지(구글 로그인 페이지)로 사용자를 이동시킴
- 인증 실패 시의 로직 커스터마이징 가능
	- SecurityFilterChain 설정에서 failureUrl() 설정
	- failureHandler 구현 후 SecurityFilterChain 설정에서 failureHandler() 설정