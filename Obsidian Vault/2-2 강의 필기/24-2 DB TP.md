# Spring Boot, JPA, Database

- 프론트엔드가 보내는 json파일 안의 변수명과 Dto의 변수명은 같아야 함

- 기본키와 후보키 설정
	- 기본키를 사용하는 상황
		- findById() 등 레포지토리에서 찾을 때 
		- Session에 기본키를 저장할 때
	  -> 기본키의 타입이 String이어도 괜찮을까?
	  -> 기본키는 @GeneratedValue(strategy = GenerationType.AUTO)로?
	  -> (GeneratedValue 기본키 + 후보키) or 복합키
	- 복합키는 레포지토리에서 찾을 때 사용하기 힘듦
		- 일반적인 경우
			- https://precious-value.tistory.com/44
		- 복합키를 구성하는 키가 외래키일 경우
			- https://velog.io/@qwerty1434/jpa%EC%97%90%EC%84%9C-%EC%99%B8%EB%9E%98%ED%82%A4%EC%9D%B4%EC%9E%90-%EB%B3%B5%ED%95%A9%ED%82%A4%EC%9D%B8-%EA%B0%92-%EB%8B%A4%EB%A3%A8%EA%B8%B0
			- 레포지토리에서 찾을 때
				- 일반적인 경우와 똑같이 찾음
		- 복합키가 다른 테이블의 외래키에 의해 참조되는 키일 경우

- 외래키 설정
	- 외래키의 타입과 변수명은 참조하는 테이블명을 사용

- 복합키로 설정해야 하는 경우
	- 튜플이 복합키에 의해 유일성을 가져야 하는가
		@Table(name = "example", uniqueConstraints = {
		    @UniqueConstraint(columnNames = {"attribute1", "attribute2"})
		})
	- 복합키를 구성하는 키를 모두 사용해서 조회를 하는가

- @OneToMany, @ManyToOne 양방향 매핑 시에 toString()에 의한 stackoverflowerror는 어떻게 해결하나?
  -> 일단 양방향에서 @ManyToOne 단방향 매핑으로 해결함

- streamAPI 방식에서 Optional<> 연속으로 나타날 때
  -> flatMap()

- map(), flatMap()이 정확히 무엇인가?

- repository method의 반환값이 Optional<List<T>>일 때, 반환값이 없을 경우 Optional.empty()가 아니라 Optional.of(emptyList())가 반환됨

- 테이블 속성의 각 속성값이 특정 범주 안에서 나누어져야 할 때, 예를 들어 색상 속성에 대한 값이 빨강, 주황, 노랑, 초록, 파랑, 남색, 보라로 나누어져야 할 때
	- 만약 튜플을 직접 삽입해야 한다면 어떻게 범주에 대한 통일성을 지킬 수 있을까? 실수로 범주화가 잘못 진행된다면?
	- 튜플을 사용자가 삽입한다면, 데이터베이스에 String으로 삽입할까?, Enum으로 삽입할까? 내부적으로 어떻게 처리할까?
	  -> 데이터베이스에서는 도메인은 String으로, Check Constraint를 추가하고, 스프링 부트에서는 ConstraintValidator 구현
	  -> @Check (hibernate를 사용 시), @PrePersist, @PreUpdate 어노테이션 사용


- 데이터베이스의 데이터에 대한 복잡한 검증 등은 애플리케이션 코드에서 처리하는 것이 좋음

- 람다식 내에서 사용하는 변수는 final 또는 effectively final이어야 하는 이유?
- 람다식 내에서 변수의 값을 바꾸려고 하면 안 되는 이유?
	- 람다식 내에서만 변수의 값을 바꾸면 괜찮은데 람다식 외에서도 변수의 값을 바꾸는 경우 오류가 나는 것 같음. 왜?

