
# SIC

- memory
	- 1 word = 3 byte

- registers
	- A (0): Accumulator, 기본적인 연산에 사용
	- X (1): Index regsiter, 주소 저장
	- L (2): Linkage register, return address 저장
	- PC (8): Program Counter, 다음 instruction의 주소
	- SW (9): Status Word, 다양한 정보 저장
		- CC: condition code, 2비트, comparison과 conditional jump instruction에 사용

- data formats
	- integer: 3 byte
	- character: 1 byte ASCII code
	- no floating point

- machine instruction format
	- ![[Pasted image 20240919100357.png]]
	- addressing modes
		- x = 0: direct a.m., target address = address
		- x = 1: indexed a.m., target address = address + X register의 값

- instruction set
	- LDA, LDX, STA, STX, ADD, SUB, MUL, DIV
	- COMP, JLT, JEQ, JGT
		- 조합하여 if문
	- JSUB, RSUB
		- Subroutine linkage
		- jump, return
		- L register 사용
	- TD
		- device ready: CC "<"
		- not ready: CC "="
	- RD, WD
		- device에서 1byte씩 읽거나 씀

# SIC/XE

- additional registers
	- B (3): base register
	- S (4), T(5)
	- F (6)

- additional data formats
	- 48bit floating-point data type

- instruction formats
	- format 1 (1byte)
		- ![[Pasted image 20240921202110.png]]
		- no memory reference
	- format 2 (2bytes)
		- ![[Pasted image 20240921202243.png]]
		- format 1 + register operations
	- format 3 (3 bytes)
		- ![[Pasted image 20240921202418.png]]
		- relative addressing
		- e = 0
	- format 4 (4bytes)
		- ![[Pasted image 20240921202520.png]]
		- address field extension to 20 bits
		- e = 1 <=> b = p = 0

- addressing modes
- ![[Pasted image 20241023185314.png]]
- n, i
		- immediate addressing (#)
			- n = 0, i = 1
			- target address가 그대로 쓰임
			- 임의의 변수의 주소값을 사용할 때 쓰임
		- indirect addressing (&)
			- n = 1, i = 0
			- 임의의 주소값을 가진 변수를 사용할 때 쓰임
		- immediate, indirect일 때 x = 0
		
		- simple addressing for SIC
			- n = 0, i = 0
			- b, p, e까지 총 15bits가 address로 쓰임
		- simple addressing for SIC/XE
			- n = 1, i = 1
	- x
		- indexed addressing
			- simple addressing(relative, direct)과 함께 쓰임
				- n = 1, i = 0 or n = 0, i = 1
			- x = 1이면 address에 X register 값을 더함
	- b, p
		- relative addressing modes for format 3
			- base relative addressing
				- b = 1, p = 0
				- TA = B + disp/addr
			- PC relative addressing
				- b = 0, p = 1
				- TA = PC + disp/addr
			- format 4에서는 안 쓰임
		- direct addressing for foramt 3 & 4
			- b = p = 0
			- TA = disp, addr
	- e
		- e = 1이면 b = p = 0

- instruction set
	- LDB, STB
	- ADDF, SUBF, MULF, DIVF
	- RMO
		- RMO r1, r2 ? r2 <- (r1)
	- ADDR, SUBR, MULR, DIVR
		- ADDR r1, r2 ? r2 <- (r2) + (r1)
	- SVC
	- I/O instructions
		- SIO(start), TIO(test), HIO(halt)

# Assembly Language Statments
- 3 types of functional statements
	- instructions
		- assembler에 의해 해석되는 object code
		- runtime 때 실행됨
		- (코드 한 줄)
	- directives
		- assembler에게 특정 action을 지시
		- object code에 영향을 주지 않음
		- (변수 선언과 초기화)
	- macro
		- 여러 명령문을 간단히 표현하는 약어
		- (함수)
