## git 기본 명령어
- git init
	- 프로젝트 시작할 때 해당 프로젝트에 .git 폴더 생성
- git add \<파일명 or . (해당 디렉토리의 파일 전부)>
	- 저장할 파일 지정
- git commit -m "변경 사항 메세지"
	- 변경 사항을 메세지와 함께 실제로 저장
- git status
	- 변경 상태 확인
- git log
	- 저장 내역 확인
	- 커밋 메세지로 코드 변경점 추측 가능

## github에 프로젝트 백업
- git remote add origin "github 주소"
	- 로컬 git 저장소에 origin이라는 이름과 해당 주소를 가진 원격 저장소를 추가
		- git push "github 주소" 브랜치명 -> 귀찮음
		- "github 주소"를 origin으로 저장
		- git push origin 브랜치명 -> 편함
- git branch -M main
	- 기본 브랜치명 master를 main으로 이름 변경 -> 관습
- git push -u origin main
	- -u: -set--upstream, 지정한 원격 저장소와 브랜치를 로컬 브랜치의 기본 업스트림 - 브랜치로 설정
	- 다음부터 git push 뒤에 origin main은 생략 가능

## git&github로 협업
- git branch
	- 브랜치 목록 확인
- git branch \<브랜치명> (보통 개발할 기능을 브랜치명으로 함)
	- 브랜치 생성
- git switch \<브랜치명>
	- 브랜치 변경
- git checkout \<브랜치명>
	- 브랜치 전환, 새 브랜치 생성 및 전환, 특정 커밋에서 파일 복구
	- switch와 restore로 checkout 대체 가능
- git restore
	- 주로 파일 복구 및 변경 취소에 사용, 특정 커밋이나 스테이징 영역에서 파일을 복구
- git switch -c \<브랜치명> or git checkout -b \<브랜치명>
	- 브랜치 생성과 동시에 브랜치 이동
- git merge \<브랜치명>
	- 현재 브랜치에 입력한 브랜치를 합침
	- 잘 안 씀 -> 대신에 github에서 pull request 후 github에서 merge
- git pull origin \<브랜치명>
	- github의 변경 사항을 내 컴퓨터에 반영

## 협업 가이드
1. 초기 코드 작성 및 github 업로드
2. dev 브랜치 생성 (main 브랜치는 배포용이므로 main 브랜치에 바로 merge X)
3. github에서 dev 브랜치를 default로 설정
4. github의 settings에서 팀원들을 collaborator로 등록
5. 팀원: git clone 하기 (git clone 할 때 맨 뒤에 . -> 추가 폴더 없이 현재 폴더에 바로 프로젝트 복사)
6. 반복
	1. (local) git pull
	2. (local) git switch -c \<브랜치명>
	3. (local) git add, git commit
	4. (local) git push -> pull request 생성, 다른 팀원들의 review
	5. (local) git pull origin dev (merge 충돌 혹은 다른 에러가 일어날 수 있기 때문에 다른 팀원들의 변경 사항을 내 dev와 미리 합쳐봄)
	6. (github) merge
	7. (local) git pull

## 알면 유용한 추가 기능
- git reset --hard \<커밋 해시>
	- 해당 커밋으로 되돌아감
	- 잘 안 씀
	- -- hard, --soft, --mixed 차이
		- --hard: 코드, 커밋 모두 변경
		- --soft: 코드 그대로, 커밋 변경, add 후
		- --mixed: 코드 그대로, 커밋 변경, add 전
- git stash
	- 임시 저장
	- 당장 commit하기 애매한 상황일 때
	- 다른 브랜치에 commit하고 싶을 때
	- -u: git add 된 적 없는 파일들도 포함해서 임시 저장
	- -m: 메세지 추가
- git stash list
	- 임시 저장 목록
- git stash apply \<stash@ 뒤 숫자>
	- 임시 저장한 내용 불러오기
- git revert \<커밋 해시>
	- 기존의 커밋을 없애지 않고 새로운 커밋을 생성하여 해당 커밋으로 되돌아감
