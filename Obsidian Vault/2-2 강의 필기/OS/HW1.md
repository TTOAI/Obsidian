- linux-6.8.1에 mysystemcall 디렉토리 생성
- mysystemcall 디렉토리 안에 시스템 콜을 구현한 mysystemcall.c 파일 추가
- mysystemcall 디렉토리에 Makefile 추가
	- 시스템 콜을 구현한 소스 파일을 커널 빌드에 포함시키기 위해서는 해당 소스 파일을 포함하는 디렉토리의 **`Makefile`** 또는 **`Kbuild`** 파일에 다음과 같은 내용을 추가해야 합니다
- linux-6.8.1의 Makefile 수정
- include/linux/syscalls.h 헤더파일에 시스템 콜 함수 선언 추가
- arch/x86/entry/syscalls/syscall_64.tbl 시스템 콜 테이블에서 시스템 콜 등록
- make menuconfig
- make -j4


