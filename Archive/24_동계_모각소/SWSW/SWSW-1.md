> SWSW(So What So Why)
> 방학이니까
> 제대로 몰랐던 개념 제대로 알아보기

> CS 및 개발과 관련된 여러가지 내용을 여기저기서 공부하면서 정리한 내용
> 설명이 부족하거나 키워드만 적어 놓은 곳은 추후에 보충할 예정
# GitHub의 Oranization 기능

- 팀원들이 공유하는 중앙 관리 계정

- 저장소와 권한을 중앙에서 관리할 수 있도록 도와주며, 팀이나 회사 단위의 협업에 유용함

- Organization 활용
	- Branch 보호
	    - `main` 브랜치를 보호 설정하여 승인된 PR만 머지 가능
	- GitHub Actions
	    - CI/CD 파이프라인을 자동화하여 배포 및 테스트를 효율화
	- Code Owners
	    - 특정 디렉토리나 파일에 대해 리뷰 담당자를 지정
	- Issues와 Project Boards
	    - 작업 항목을 Issue로 관리하고, 프로젝트 보드로 시각화

참고:
	https://velog.io/@city7310/%EB%B0%B1%EC%97%94%EB%93%9C%EA%B0%80-%EC%9D%B4%EC%A0%95%EB%8F%84%EB%8A%94-%ED%95%B4%EC%A4%98%EC%95%BC-%ED%95%A8-2.-%EB%B2%84%EC%A0%84-%EA%B4%80%EB%A6%AC-%EC%8B%9C%EC%8A%A4%ED%85%9C%EA%B3%BC-%EB%B2%84%EC%A0%84-%EA%B4%80%EB%A6%AC-%EC%9B%B9%ED%98%B8%EC%8A%A4%ED%8C%85-%EC%84%9C%EB%B9%84%EC%8A%A4-%EA%B2%B0%EC%A0%95
# Git GUI

- GUI를 통해 git을 사용할 수 있도록 하는 애플리케이션

- GitHub Desktop, SourceTree, GitKraken 등이 있음

참고:
	https://velog.io/@city7310/%EB%B0%B1%EC%97%94%EB%93%9C%EA%B0%80-%EC%9D%B4%EC%A0%95%EB%8F%84%EB%8A%94-%ED%95%B4%EC%A4%98%EC%95%BC-%ED%95%A8-2.-%EB%B2%84%EC%A0%84-%EA%B4%80%EB%A6%AC-%EC%8B%9C%EC%8A%A4%ED%85%9C%EA%B3%BC-%EB%B2%84%EC%A0%84-%EA%B4%80%EB%A6%AC-%EC%9B%B9%ED%98%B8%EC%8A%A4%ED%8C%85-%EC%84%9C%EB%B9%84%EC%8A%A4-%EA%B2%B0%EC%A0%95
# 개발 프로세스 정립

- 필요한 이유
	- 작업들의 상태를 쉽게 알 수 있음
	- 이슈를 처리할 때 정립된 프로세스에 따라서 작업을 진행하여 코드 작성에만 집중할 수 있음

- 이슈 관리 도구
	- 이슈 트래커라고도 불림
	- 이슈 기술과 나열, 처리할 작업자 할당 등을 위해 사용함
	- JIRA, Trello, GitHub Issues & Projects 등이 쓰임

- 개발 프로세스
	- project 생성
	- 필요한 작업이 생기면 라벨과 함께 이슈 생성
	- 이슈에 맞는 project를 선택
	- 해당 이슈를 담당할 개발자 할당
	- issue/<이슈 번호> 브랜치 체크아웃
	- 작업 진행
	- pull request, reivew, merge, issue close, 브랜치 제거 순으로 진행

참고:
	https://velog.io/@city7310/%EB%B0%B1%EC%97%94%EB%93%9C%EA%B0%80-%EC%9D%B4%EC%A0%95%EB%8F%84%EB%8A%94-%ED%95%B4%EC%A4%98%EC%95%BC-%ED%95%A8-3.-%EA%B0%9C%EB%B0%9C-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EC%A0%95%EB%A6%BD

# API 설계

- API 설계는 아키텍처 기반으로 결정하는 것이 좋음

- 아키텍처에는 HTTP API, REST API, GraphQL 등이 있음

- REST API가 명시하는 모든 원칙을 만족하는 API를 작성하는 것은 어려움
	- self-descriptive
	- HATEOAS
		- hypermedia as the engine of application state
		- 어플리케이션의 상태가 hyperlink를 이용해 전이되어야 함

- 직렬화 포맷
	- 데이터를 어떤 방식으로 표현할 지를 결정해두는 것으로, 클라이언트와 서버 간의 데이터 교환이 원활하도록 함
	- XML, JSON, YAML 등이 있음

- 참고:
	- https://velog.io/@city7310/%EB%B0%B1%EC%97%94%EB%93%9C%EA%B0%80-%EC%9D%B4%EC%A0%95%EB%8F%84%EB%8A%94-%ED%95%B4%EC%A4%98%EC%95%BC-%ED%95%A8-4.-API-%EC%84%A4%EA%B3%84-%EC%9B%90%EC%B9%99%EA%B3%BC-%EC%A7%81%EB%A0%AC%ED%99%94-%ED%8F%AC%EB%A7%B7-%EA%B2%B0%EC%A0%95
