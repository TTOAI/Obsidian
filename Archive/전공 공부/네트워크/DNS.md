
## DNS의 계층 구조

- . -> 루트
	- 최상위, 도메인 네임 시스템의 뿌리
- com -> TLD, Top-Level Domain
	- 종류: 일반 TLD, 국가 코드
- naver -> Second-Level Domain
	- 소유자는 권한 서버를 스스로 운영하거나, 등록 대행사를 통해 설정
- www -> Subdomain
	- 소유자가 마음대로 생성할 수 있는 영역
	- 각각 다른 서버나 서비스에 연결되도록 DNS 레코드 설정 가능

## DNS Zone

- 특정 도메인과 그 하위 도메인들을 어떤 네임 서버가 관리하는 단위
- DNS 정보를 관리상의 목적으로 계층적인 영역으로 만든 개념
- 예시:
	- 루트 zone:
		- ICANN과 루트 서버가 관리
	- .com zone:
		- Verisign 같은 TLD 레지스트리가 관리
	- example.com zone:
		- example.com의 소유자가 직접 관리
	- sub.example.com을 다른 네임 서버에 위임하여 별도의 존으로 운영 가능
- DNS Zone 파일 (영역 파일):
	- 각 Zone의 정보와 Zone 내의 Record 정보들을 기술한 텍스트 파일
	- SOA 레코드, NS 레코드, A/AAAA 레코드 등 여러 레코드들이 들어감

## DNS 서버

![[Pasted image 20250918164835.png]]

- DNS 체계 안에서 실제로 도메인 이름 -> IP 주소 변환을 수행하는 서버
- 종류:
	- DNS 리졸버
	- DNS Root 네임 서버:
		- 13개의 DNS 루트 네임서버가 모든 재귀 확인자에 알려져 있음
		- 루트 네임서버는 비영리 단체인 ICANN(Internet Corporation for Assigned Names and Numbers)이 관리함
		- TLD 네임 서버를 가리켜 응답
	- TLD 네임 서버:
		- TLD 네임서버는 ICANN의 지사인 IANA(Internet Assigned Numbers Authority)가 관리
			- 일반 최상위 도메인 (ex. .com, .org, .net, .edu)
			- 국가 코드 최상위 도메인 (ex. .uk, .us, .ru)
		- 도메인의 권한 있는 네임서버(아래 참조)를 가리켜 응답
		- 특정 TLD에 속한 도메인의 NS 레코드 정보 관리
	- 권한 서버 (권한 있는 네임 서버):
		- DNS 레코드 또는 DNS Zone 파일을 관리
		- 

## DNS 리졸버

- (참고) ISP:
	- 인터넷 접속을 제공하는 통신사 (KT, SKT, LG U+ 등)
	- Reculsive Resolver(캐싱 DNS 서버)를 운영

- recursive checker, DNS recacer, Recursive Resolver 라고 불림
- 클라이언트와 DNS 서버 사이에서 중간 역할을 하는 프로그램/시스템
- DNS 서버에 차례대로 질의해서 IP 주소를 찾아옴
- 최근에 요청한 도메인 이름의 IP 주소를 요청하면 네임서버와의 통신 프로세스를 우회하고 캐시에서 요청한 레코드를 클라이언트에 전달

- 종류:
	- Stub Resolver
		- PC, 스마트폰 등의 OS에 탑재된 가벼운 DNS 클라이언트 모듈
		- 보통 /etc/resolv.conf나 네트워크 설정에서 지정된 DNS 서버로 DNS Query를 보냄
		- 지정된 DNS 서버 종류:
			- ISP가 운영하는 DNS 서버 (대부분의 경우)
			- 퍼블릭 DNS 서버 (ex. 구글 8.8.8.8, Cloudflare 1.1.1.1)
		- DNS Query 특징:
			- 프로토콜: UDP 53 / TCP 53
	- Recursive Resolver
		- Stub Resolver로부터 받은 요청을 대신 처리
		- 캐시에 있으면 바로 응답, 없으면 직접 찾아감
		- 찾아가는 순서:
			1. Root DNS 서버
			2. TLD DNS 서버
			3. 권한 서버

## DNS 레코드

- 도메인에 연계된 IP 주소 및 해당 도메인에 대한 요청의 처리 방법에 대한 정보를 제공
- 종류:
	- ==A==: 해당 도메인 주소가 가지는 IP
		- 도메인과 IP 간 일대일 / 일대다 / 다대일 매칭 가능
	- ==CNAME==: 별칭을 부여한 특정 도메인 주소
		- 동일한 IP 주소를 가진 도메인 주소가 여러 개일 때, 서브 도메인들을 하나의 메인 도메인에 매핑시키는 CNAME 레코드를 작성하면 IP 주소가 바뀌었을 때 메인 도메인의 A 레코드 하나만 변경하면 됨
	- MX: 메일을 주고 받기 위한 서비스 레코드
		- 해당 도메인과 연동되어 있는 메일 서버 확인
		- MX 레코드가 설정되어 있어야 해당 도메인을 이메일 주소로 사용 가능
	- TXT: 일반적인 텍스트 내용을 기록, String
	- ==SOA==:
		- 모든 Zone 파일은 SOA 레코드로 시작
	- HINFO: 호스트의 CPU 정보와 운영체제 정보
	- MINFO: 메일박스와 메일리스트 정보
	- PTR: IP 주소에 대한 호스트명
	- UNINFO: 사용자정보
	- ANY: 호스트에 관련된 모든 레코드들의 정보
	- ==AAAA==: IPv6 버전 A 레코드
	- SRV: 비슷한 TCP/IP 서비스를 제공하는 다수의 서버 위치 정보
	- ==NS==:
		- 해당 도메인을 관리하는 권한 있는 네임 서버를 지정하는 레코드
		- 네임 서버의 도메인 이름이 적혀 있음
		- 각 도메인마다 최소 2개 이상의 NS 레코드를 권장
		- 조직이 자신의 도메인을 직접 관리하는 경우:
			1. 대부분의 경우 도메인이 자체 도메인 내부의 네임서버에 위임되는 것을 볼 수 있음
				- ex) "example.com."는 "ns01.example.com"에 위임됨
			2. 네임 서버의 IP 주소를 알기 위해 자기 도메인의 네임 서버에 물어봄
			3. 도메인의 네임서버가 도메인 자체 내에 있어 순환 레퍼런스가 생성됨
			4. 순환 문제를 풀기 위해 Glue Record 필요
			- 대기업, 호스팅 업체 등이 자기 도메인 네임 서버를 운영
	- Glue Record:
		- 도메인의 등록 기관에서 만든 DNS 레코드
		- 네임 서버의 도메인 이름과 IP 주소를 알려줌
		- 네임 서버가 도메인의 권한 있는 네임서버에 대한 레퍼런스를 반환할 때 완전한 답변을 제공함
