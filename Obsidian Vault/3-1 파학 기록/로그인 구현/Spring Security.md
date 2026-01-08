
- 참고: https://www.devyummi.com/page?id=668bd2d92b88a1ef5f2be2e3

## 기본 배경 지식
- Servlet Container에는 클라이언트로부터 오는 요청이 Spring Boot 애플리케이션으로 가기 전에 거쳐야 하는 필터가 존재함
- Spring Security를 사용하면 Servlet Container의 필터에 Spring Security의 필터도 포함됨
- 특정한 경로에 요청이 오면 Controller 클래스에 도달하기 전 필터에서 Spring Security가 다음과 같이 검증을 함
	1. 해당 경로의 접근은 누구에게 열려 있는가
	2. 로그인이 완료된 사용자인가
	3. 해당되는 role을 가지고 있는가

## Spring Security 인가 작업
- Security Configuration
	- 인가 설정을 진행하는 클래스
	- SecurityFilterChain 설정을 진행함
- 특정 경로에 대한 인가 설정 예시 코드
	```java
	package com.example.testsecurity.config;
	
	import org.springframework.context.annotation.Bean;
	import org.springframework.context.annotation.Configuration;
	import org.springframework.security.config.annotation.web.builders.HttpSecurity;
	import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
	import org.springframework.security.web.SecurityFilterChain;
	
	@Configuration
	@EnableWebSecurity
	public class SecurityConfig {
	
	    @Bean
	    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception{
	
	        http
	                .authorizeHttpRequests((auth) -> auth
	                        .requestMatchers("/", "/login").permitAll()
	                        .requestMatchers("/admin").hasRole("ADMIN")
	                        .requestMatchers("/my/**").hasAnyRole("ADMIN", "USER")
	                        .anyRequest().authenticated()
	                );
	
	        return http.build();
	    }
	}
	```

## 스프링 버전별 구현 방식의 변화
- 스프링 부트와 스프링의 버전이 바뀌면서 스프링 시큐리티의 구현 방식도 계속 바뀜
- GitHub의 Spring 리포지토리에서 Security의 Release 항목을 통해 변경점을 확인할 수 있음
	- https://github.com/spring-projects/spring-security/releases
- 스프링 부트 3.0.X 버전까지는 HttpSecurity 클래스의 메소드를 나열하는 형식이었지만, 스프링 부트 3.1.X 버전부터는 위의 Security Config 인가 설정 예시 코드와 같이 람다 형식으로 작성하도록 바뀜
- 항상 최신 방식이 무엇인지 확인해야 함!

## 자동으로 로그인 페이지 리디렉션
- 특정 경로에 대한 접근 권한이 없는 경우 자동으로 로그인 페이지로 리디렉션 되도록 설정해야 함
- 예시 코드
	```java
	.
	.
	.
	http  
	        .formLogin((auth) -> auth  
	                .loginPage("/login")  
	                .loginProcessingUrl("/loginProc")  
	                .permitAll()  
	        );
	.
	.
	.
	```
	- "/login" 경로를 통해 로그인 페이지에 연결됨
	- 로그인 페이지에서 "/loginProc" 경로로 아이디와 패스워드를 전송함
- 로그인 페이지를 구현한 mustache 템플릿 파일 예시 코드
	```html
	<html>
	<head>
	    <meta charset="UTF-8">
	    <meta name="viewport"
	          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
	    <meta http-equiv="X-UA-Compatible" content="ie=edge">
	    <title>Document</title>
	</head>
	<body>
	    login page
	    <hr>
	    <form action="/loginProc" method="post" name="loginForm">
	        <input id="username" type="text" name="username" placeholder="id"/>
	        <input id="password" type="password" name="password" placeholder="password"/>
	        <input type="submit" value="login"/>
	    </form>
	</body>
	</html>
	```
	- formLogin 방식을 사용하면 Spring Security가 자동으로 UserDetails와 UserDetailsService를 통해 검증 후 SecurityContext에 해당 아이디에 대한 세션을 저장함 (SecurityContextHolder의 Authentication 객체)

## BCrypt 암호화 메소드
- 사용자 인증(로그인) 시 비밀번호에 대해 단방향 해시 암호화를 진행하여 비밀번호와 대조함
- 스프링 시큐리티는 암호화를 위해 BCrypt Password Encoder를 제공 및 권장함
- 해당 객체를 Bean으로 등록하여 사용
- Security Config에 암호화 Bean 추가 예시 코드
	```java
	@Bean  
	public BCryptPasswordEncoder bCryptPasswordEncoder() {  
	  
	    return new BCryptPasswordEncoder();  
	}
	```

## DB 연결
- application.properties 파일에 db 연결 관련 코드 작성
- 예시 코드
	```yml
	spring.application.name=TestSecurity  
	  
	spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver  
	spring.datasource.url=jdbc:mysql://127.0.0.1:3306/onrank?useSSL=false&useUnicode=true&serverTimezone=Asia/Seoul&allowPublicKeyRetrieval=true  
	spring.datasource.username=onrank  
	spring.datasource.password=tocnfqkf@  
	  
	#Entity 기반으로 테이블을 자동 생성 여부 결정  
	spring.jpa.hibernate.ddl-auto=none  
	  
	#Entity에서 설정한 변수명을 MySQL 디비 테이블 생성시 그대로 매핑  
	spring.jpa.hibernate.naming.physical-strategy=org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl
	```

## 회원 가입 로직

- 필드 주입 방식 X -> 생성자 주입 방식으로 변경해야 함
- 회원가입 페이지 생성(mustache)
	- 예시 코드
		```xml
		<form action="/joinProc" method="post" name="joinForm">
		    <input type="text" name="username" placeholder="Username"/>
		    <input type="password" name="password" placeholder="Password"/>
				<input type="submit" value="Join"/>
		</form>
		```
- 회원가입 관련 DTO, Controller, Service, Entity, Repository 생성
	- Service의 회원가입 메소드에서 유저 비밀번호를 저장할 때 BcryptPasswordEncoder 사용
		- 예시 코드
			```java
			data.setUsername(joinDTO.getUsername());
			data.setPassword(bCryptPasswordEncoder.encode(joinDTO.getPassword()));
			data.setRole("ROLE_USER");
			```
- SecurityConfig 접근 권한 설정
	- 예시 코드
		```java
		http
		          .authorizeHttpRequests((auth) -> auth
		                  .requestMatchers("/", "/login", "/loginProc", "/join", "/joinProc").permitAll()
		                  .requestMatchers("/admin").hasRole("ADMIN")
		                  .requestMatchers("/my/**").hasAnyRole("ADMIN", "USER")
		                  .anyRequest().authenticated()
		          );
		```

## 회원 중복 검증
