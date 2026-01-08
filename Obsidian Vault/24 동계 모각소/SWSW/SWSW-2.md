> SWSW(So What So Why)
> 방학이니까
> 제대로 몰랐던 개념 제대로 알아보기

# 사용자 인증 방식

- HTTP는 stateless, connectionless한 성격을 띔

- 클라이언트는 매 HTTP 요청마다 본인에 대한 인증 정보(credential)를 포함시켜야 함

- 서버는 클라이언트로부터 받은 인증 정보를 기반으로 인증 과정을 거쳐야 함

- HTTP의 `Autorization` 요청 헤더
	- 서버가 유저 에이전트를 인증(authenticate)할 수 있도록 인증 정보(credential)를 제공하여 보호된 리소스에 접근할 수 있도록 하는 데 사용함
	- 보호된 리소스에 처음 접근하려고 함
	  -> 서버가 적어도 하나의 `WWW-Authenticate` 헤더를 포함하는 401 Unauthorized 메시지를 보냄
	  -> `WWW-Authenticate` 헤더에는 클라이언트로부터 필요한 정보들이 무엇인지 알려주는 인증 방식들(authentication schemes)이 있음
	  -> 유저 에이전트는 가장 안전한 인증 방식을 선택해서 사용자에게 인증 정보(credential)를 요청함
	  ->사용자로부터 받은 인증 정보를 인코딩하여 리소스를 재요청함

- 인증(Authentication)과 인가(Authorization)
	- `Autentication`: 사용자의 신원을 확인함
	- `Authorization`: 사용자에게 적절한 권한을 부여함

- 인증 스키마
	- `credential`을 만들어내는 방식
	- `Authorization` 헤더의 value는 `<type> <credentials>` 와 같은 형식이 표준임

- 토큰 기반 인증
	- stateless 서버와 함께 사용됨
		- stateful 서버와 대조됨 (ex. 세션을 유지하는 웹서버)
	- 사용자는 로그인 후 인증 토큰을 발급 받음
	- 토큰은 클라이언트 측에 저장됨
	- 서버의 확장성을 높일 수 있음
	- 모바일 어플리케이션에는 쿠키 컨테이너를 사용해야 하는 쿠키 기반 인증보다 토큰 기반 인증이 더 이상적임
	- Twitter, Facebook, GitHub, Google 등 많은 곳에서 토큰 기반 인증이 사용됨

- 세션 기반 인증
	- 과거 인증 시스템
	- **RESTful하지 않은 인증 방식임**
	- 세션 기반 인증의 작동 방식
		- 클라이언트가 서버에 `credential`(아이디/비밀번호)을 보내 인증을 요청함
		  -> 서버는 인증 확인 후 해당 사용자에 대한 고유한 세션 ID를 생성함
		  -> 이 세션 ID는 서버의 메모리나 데이터베이스에 저장됨
		  -> 서버는 이 세션 ID를 클라이언트에게 응답으로 보냄. 이 때 쿠키에 세션을 ID를 담아서 전달하는 경우가 많음
		  -> 클라이언트는 이후 모든 요청에서 쿠키를 통해 서버에 세션 ID를 보냄
		  -> 서버는 받은 세션 ID를 자신의 저장소에서 확인하고, 사용자의 인증 상태를 유지함
	- 메모리 과부하, 데이터베이스 성능 저하 문제가 생길 수 있음
	- 세션이 저장되어 있는 특정 서버의 메모리나 데이터베이스에 의존하게 되어 부하를 여러 서버로 분산하기 어려움
	- 분산 시스템에서 세션을 관리하기 위해서는 세션 복제, 세션 클러스터링 등을 설계해야 하는데, 이는 개발 및 유지보수에 많은 시간과 비용이 들며 오류 가능성이 증가함

- 세션 기반 인증에서 쿠키의 역할
	- 서버는 사용자가 성공적으로 로그인하면 고유한 세션 ID를 생성함
	- 생성된 세션 ID를 클라이언트의 브라우저에 쿠키 형태로 저장함
	- 브라우저는 자동으로 쿠키에 저장된 세션 ID를 HTTP 요청 헤더에 포함하여 서버로 보냄

- 세션 기반 인증에서 쿠키 사용의 단점
	- 쿠키는 특정 도메인 및 서브도메인에서만 작동하도록 설계되어 있음(`Same-Origin Policy`)
	  -> 크로스 도메인 요청에서 쿠키를 사용하기 위해 쿠키에 `SameSite=None`, `Secure` 등과 같은 속성을 추가해야 함
	- 쿠키를 사용하기 위해 서버와 클라이언트 모두에서 CORS 정책을 명시적으로 설정해야 함
		- `CORS(Cross-Origin Resource Sharing)`: 웹 페이지 상의 제한된 리소스를 최초 자원이 서비스된 도메인 밖의 다른 도메인으로부터 요청할 수 있게 허용하는 구조
		- 서버: 응답 헤더에 `Access-Control-Allow-Origin` 및 `Access-Control-Allow-Credentials`를 설정해야 함
		- 클라이언트: 요청에 `withCredentials` 옵션을 설정하여 쿠키를 전송할 수 있도록 해야 함
	- 보안에 취약함
		- `Session Hijacking`
			- `HTTPS`, `HttpOnly`, `Secure`, `SameSite` 등의 속성을 적절히 설정하지 않았을 경우 공격자가 세션 ID를 탈취할 가능성이 있음
		- `XSS(Cross-site Scripting)`
			- 악의적인 사용자가 공격하려는 사이트에 스크립트를 넣는 기법
			- 클라이언트 측 스크립트를 통해 쿠키에 접근하여 세션 ID를 탈취할 가능성이 있음
			- JavaScript에서 쿠키에 접근하지 못하도록 `HttpOnly` 속성 설정 필요
		- `CSRF(Cross-site Request Forgery)`
			- 신뢰할 수 있는 사용자를 사칭해 웹 사이트에 원하지 않는 명령을 보내는 공격

- 토큰 기반 시스템의 작동 원리
	- stateless하므로 유저의 인증 정보를 서버나 세션에 담아두지 않음
	- 유저가 아이디와 비밀번호로 로그인을 함
	- 서버 측에서 해당 계정 정보를 검증함
	- 계정 정보가 정확하다면 서버에서 유저에게 signed 토큰을 발급해줌
		- signed 토큰: 해당 토큰이 서버에서 정상적으로 발급된 토큰임을 증명하는 signature을 지니고 있음
	- 클라이언트 측에서 서버 측으로부터 전달 받은 토큰을 저장해둠
	- 서버에 요청을 할 때마다 해당 토큰을 함께 서버에 전달함
	- 서버는 토큰을 검증하고, 요청에 응답함

- 토큰의 장점
	- 무상태(stateless), 확장성(scalability)
		- 특정 서버에 국한되지 않으므로 서버를 확장하기 적합함
	- 보안성
		- 쿠키를 사용함으로 인해 발생하는 취약점이 사라짐
	- Extensibility
		- 로그인 정보가 사용되는 분야의 확장성
		- Google 계정을 이용해 다른 웹서비스에서 로그인하는 경우
		- 토큰에 선택적인 권한(프로필 정보 가져오기)만 부여하여 발급할 수 있음
	- 웹 표준 기반
		- JWT는 토큰 기반 인증 시스템의 구현체로 웹 표준에 등록되어 있음

- 참고:
	- https://velog.io/@city7310/%EB%B0%B1%EC%97%94%EB%93%9C%EA%B0%80-%EC%9D%B4%EC%A0%95%EB%8F%84%EB%8A%94-%ED%95%B4%EC%A4%98%EC%95%BC-%ED%95%A8-5.-%EC%82%AC%EC%9A%A9%EC%9E%90-%EC%9D%B8%EC%A6%9D-%EB%B0%A9%EC%8B%9D-%EA%B2%B0%EC%A0%95
	- https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/Authorization
	- https://velopert.com/2350

# JWT(JSON Web Token)

- 웹표준(RFC 7519)

- 두 개체에서 JSON 객체를 사용하여 가볍고 자가수용적인(self-contained) 방식으로 정보를 안정성 있게 전달해줌
	- `self-contained`
		- 필요한 모든 정보를 자체적으로 지니고 있음
		- 따라서 웹서버의 경우 HTTP 헤더로도, URL 파라미터로도 쉽게 전달 가능함

- JWT의 생김새
	- `aaaaaa.bbbbbb.cccccc`
	- `.`을 구분자로 하여 Base64로 인코딩된 3가지의 문자열로 구성되어 있음
	- `header`.`payload`.`signature`

- `header`
	- 두 가지의 정보를 지님
	- `typ`
		- 토큰의 타입을 지정함 -> `JWT`
	- `alg`
		- 해싱 알고리즘을 지정함
		- 보통 `HMAC SHA256` 혹은 `RSA`가 사용됨
		- 토큰을 검증할 때 사용되는 `signature` 부분에서 사용됨
``` json
{
  "typ": "JWT",
  "alg": "HS256"
}
```

- `payload`
	- 토큰에 담을 정보가 들어감
	- 정보의 한 쌍(name: value)을 `claim`이라고 함
	- `claim`의 종류: `registered`, `public`, `private`
	- `registered claim`
		- 토큰에 대한 정보들을 담기 위해 이름이 이미 정해짐
	- `public claim`
		- 충돌이 방지된(collision-resistent) 이름을 가지고 있어야 함
		- 충돌을 방지하기 위해서 이름을 URI 형식으로 지음
	- `private claim`
		- 클라이언트오 서버 협의 하에 사용되는 `claim`
		- `public claim`과 달리 이름이 중복될 수 있으므로 유의해야 함

- `signature`
	- 토큰이 변조되지 않았음을 검증하는 용도로 사용됨
	- `header`의 인코딩 값과 `payload`의 인코딩 값을 합친 후 비밀 키로 해쉬를 하여 생성함
	- 비밀 키는 토큰을 생성하는 주체만 알고 있는 개인 키임
	- `signature`는 개인 키로 생성되며, 개인 키와 쌍을 이루는 공개 키로 검증할 수 있음
	- 인증 서버에서 `signature`를 검증해 무결성을 확인함

- 참고:
	- https://velopert.com/2389

# HTTP API

- HTTP를 사용하여 클라이언트와 서버가 데이터를 주고받는 모든 API

# REST

- Representational State Transfer

- `네트워크 아키텍처 원리`의 모음
	- `네트워크 아키텍처 원리`: 자원을 정의하고 자원에 대한 주소를 지정하는 방법 전반

- 웹 상의 자료를 HTTP 위에서 `SOAP`이나 `쿠키` 기반 세션 트래킹 같은 별도의 전송 계층 없이 효율적으로 전송할 수 있도록 설계된 간결한 인터페이스
	- `SOAP`: 웹 서비스 간의 데이터 교환을 위한 프로토콜
		- Simple Object Access Protocol
		- 데이터 포맷으로 `XML`을 사용
		- REST 등장 이전에 널리 쓰였으나 무겁고 복잡하다는 단점이 있음
		- 엄격한 표준과 스펙을 따르므로 금융, 결제, 의료 등 보안과 신뢰성이 중요한 시스템에서 사용됨

- `HTTP`를 잘 활용하기 위한 원칙

- `REST` 아키텍처에 적용되는 6가지 제한 조건
	- 인터페이스 일관성(Uniform Interface)
		- 일관적인 인터페이스를 제공해야 함
		- 모든 자원은 일관된 방식으로 접근 가능해야 함
	- 무상태(Stateless)
		- 각 요청은 독립적이어야 함
		- 서버는 클라이언트의 이전 요청 상태를 저장하지 않아야 함
	- 캐시 처리 기능(Cacheable)
		- 클라이언트는 서버의 응답을 캐싱할 수 있어야 함
		- 잘 관리되는 캐싱은 클라이언트-서버 간 상호작용을 부분적으로 또는 완전하게 제거하여 scalability와 성능을 향상시킴
	- 계층화(Layered System)
		- 시스템을 여러 계층으로 구성하여 유연성과 보안성을 높일 수 있음
		- 클라이언트는 보내는 요청이 실제 서버로 가는지, 프록시 서버를 거치는지 알 필요 없음
		- 중간 서버는 로드 밸런싱 기능이나 공유 캐시 기능을 제공함으로써 시스템 규모 확장성을 향상시키는 데 유용함
			- 로드 밸런서
			- 게이트웨이
	- 클라이언트-서버 구조
		- 클라이언트와 서버는 완전히 독립적이어야 하며, 서로의 내부 동작을 몰라도 됨
		- 아키텍처를 단순화시키고 작은 단위로 분리함으로써 클라이언트-서버의 각 파트가 독립적으로 개선될 수 있도록 해줌
	- Code on demand(optional)
		- 서버는 클라이언트가 실행할 수 있는 코드(JavaScript, Java Applet 등)를 전달할 수도 있음

- REST 인터페이스의 원칙에 대한 가이드
	- 자원의 식별
		- 요청 내에 기술된 개별 자원을 식별할 수 있어야 함
	- 메시지를 통한 리소스의 조작
		- 클라이언트가 어떤 자원을 지칭하는 메시지와 특정 메타데이터만 가지고 서버 상의 해당 자원을 변경 및 삭제할 수 있음
	- 자기서술적 메시지
		- 각 메시지는 자신을 어떻게 처리해야 하는지에 대한 충분한 정보를 포함해야 함
	- 애플리케이션의 상태에 대한 엔진으로서 하이퍼미디어(`HATEOAS`)
		- hypermedia as the engine of application state
		- 클라이언트가 관련된 리소스에 접근하기를 원한다면, 리턴되는 지시자에서 구별될 수 있어야 함
		- 클라이언트가 서버의 리소스 상태를 쉽게 탐색할 수 있도록 하이퍼링크를 제공해야 함
		- 구현과 유지보수의 어려움 등의 이유로 실무에서는 잘 사용되지 않음

- 참고:
	- https://ko.wikipedia.org/wiki/REST

# REST API

- REST 원칙을 따르는 HTTP API

- RESTful한 API 설계의 핵심 규칙
	- URI로 자원(Resource)을 표현해야 함
	- 자원에 대한 행위는 HTTP 메서드로 표현해야 함

- 자원의 표현은 `Document`, `Collection`, `Store`, `Controller` 4가지 방식으로 나뉨
	- `Document`
		- 하나의 개별 리소스(객체)를 의미함
		- 보통 특정 개체에 대한 정보를 조회하거나 수정할 때 사용됨
		- ex) `GET /sports/soccer/players/13`에서 `soccer`, `13`
	- `Collection`
		- document들의 집합
		- 새로운 개체를 생성하거나 여러 개의 개체를 조회할 때 사용됨
		- ex) `GET /sports/soccer/players/13`에서 `sports`, `players`
	- `Store`
		- 특정 key로 데이터를 저장/조회하는 방식
		- 보통 `Key-Value Store`와 같이 동작함
		- 일반적인 `REST API`에서는 비교적 적게 사용됨
		- ex) `GET /settings/theme` -> 테마 설정 값을 조회
	- `Controller`
		- 특정 자원에 대한 행위(동작, Action)를 정의할 때 사용됨
		- 보통 명령(Command) 수행, 비즈니스 로직 처리 등에 사용됨
		- `Collection`이나 `Document`와 달리 명확한 동작을 수행하는 엔드포인트
		- `REST API`에서는 가능하면 `Controller`를 최소화하고, HTTP 메서드와 URI 설계를 잘 활용하는 것이 좋음

- 참고:
	- https://bentist.tistory.com/37
	- https://ko.wikipedia.org/wiki/REST
