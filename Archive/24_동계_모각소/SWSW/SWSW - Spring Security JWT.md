- SecurityConfig 클래스에서 SecurityFilterChain 설정을 진행함
- SecurityFilterChain에 여러 필터가 등록되어 있고 필요에 따라 커스텀 필터를 등록함
- sessionManagement()에서 sessionCreationPolicy를 STATELESS로 등록하기 때문에 세션이 유지되지 않음
- 대신 토큰을 발급하고 매 요청마다 헤더에 있는 토큰을 검증함

## JWTUtil
- JWT 발급 및 검증을 위한 클래스 생성
- 후술되는 필터들에서 쓰임

## UsernamePasswordAuthenticationFilter
- 로그인 요청 시 검증 후 로그인 성공 및 JWT 발급을 진행하기 위한 필터
- 사용자가 로그인 요청을 하면 Spring Security의 UsernamePasswordAuthenticationFilter가 요청을 가로챔
- JWT를 사용하면 formlogin을 비활성화하므로 UsernamePasswordAuthenticationFilter를 상속 받아서 직접 구현해야 함
	- attemptAuthentication()에서 사용자 정보를 추출
	- 추출한 사용자 정보를 바탕으로 UsernamePasswordAuthenticationToken 같은 구현체가 생성됨
	- AuthenticationManager를 호출하고 Token을 넘김
	- 인증이 성공하면 AuthenticationManager가 검증된 Authentication 객체를 반환
	- successfulAuthentication 메서드에서 이 Authentication 객체를 받아 사용

## OncePerRequestFilter
- 요청에 담긴 JWT를 검증하기 위한 필터
- UsernamePasswordAuthenticationFilter 앞에 위치시킴
- 검증에 성공하면 Authentication 객체 생성 및 SecurityContextHolder에 세션 생성
- 이 세션은 STATLESS 상태로 관리되기 때문에 해당 요청이 끝나면 소멸됨

