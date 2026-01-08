
- hashing
	- 평균적으로 O(1)의 시간 복잡도를 가짐
- static hashing
	- 고정된 크기의 hash table에 identifiers가 저장됨

- terminology
	- identifier density: $^n/_T$
		- n: identifier 개수
		- T: 가능한 idenifier 개수
	- loading factor: $^n/_\left(s \times b\right)$
		- s: slots
		- b: buckets
	- synonyms
		- 같은 hash값을 갖는 identifiers
	- overflow
		- full bucket에 새로운 identifier를 hash하려고 할 때
	- collision
		- 서로 다른 identifiers를 같은 bucket에 hash하려고 할 때

- hashing functions
	- requirements
		- 계산하기 쉬워야 함
		- collision이 최소가 되어야 함
		- undiased
	- uniform hash function
		- 모든 i에 대해 h(x) = i일 확률이 $^1/_b$
	
	- mid-square
		![[Pasted image 20240618203329.png]]
		- identifier를 제곱해서 중간 bits를 적절히 가져옴
		- table size가 $2^r$일 때 $r$bits를 가져옴
	
	- division
		- modulo operator 이용
		- modulo $M=2^p-1$이고 $x$를 $2^p$으로 나타낼 때, $2^p$ 앞의 값들을 바꾸는 것은 hash 값에 영향을 주지 않음
		  -> 2의 제곱 수에 너무 가깝지 않은 소수가 좋음
			![[Pasted image 20240606002130.png]]
		- divisor
			![[Pasted image 20240618203607.png]]
			- even: even -> even, odd -> odd
			- odd: any -> any
	
	- folding
		- Shift folding: add all characters into one
			- 키를 부분으로 나누어 합함
		- Folding at the boundaries: reverse every other partition before adding
			- 홀수(짝수) 번째 부분은 뒤집어서 더함
	
	- digit analysis
		- 바뀌지 않는 static file( ex. 조선왕조실록)에 대한 처리 방식
		- r진법을 사용해 ID를 숫자로 변환
		- the most skewed distributions를 가지는 digit은 버리고 남은 digit으로 hash

- overflow handling
	- open addressing
		- linear probing
			![[Pasted image 20240618210728.png]]
			- overflow가 났을 때 가장 가까운 unfilled bucket을 찾음
			- 문제점
				- table에 cluster를 형성시키고 그 cluster를 더 키울 수 있음
				- cluster 주변의 키값에 영향을 줌
		- random probing
			![[Pasted image 20240618210833.png]]
			- overflow가 났을 때 랜덤 값을 더해 available slot을 찾음
			- 하나의 hash table에 대해 동일한 순서의 동일한 랜덤 값을 사용함 (random probing sequence)
		- quadratic probing
			- overflow가 났을 때 제곱 수를 더하거나 빼서 available slot을 찾음
			- cluster가 형성되는 것을 줄일 수 있음
		- deletion in open addressing
			![[Pasted image 20240618213625.png]]
			- recode가 삭제된 자리에 tombstone이라고 불리는 mark를 새김
			- searching: tombstone을 만나면 searching 계속 진행
			- insertion: tombstone 자리에 insert 가능
	- chaining
		![[Pasted image 20240618213807.png]]
		- linked list 이용
		- 잠재적 단점
			- linked list가 길어질 수 있음
			- pointer 때문에 memory가 더 사용됨
			- 최악의 경우 하나의 linked list에 모든 element가 몰릴 수 있음 -> hash table을 쓰는 이유 X

- dynamic hashing
	- extendable hashing
		![[Pasted image 20240618223723.png]]
		- slots를 가진 buckets, buckets를 가리키는 pointers를 가진 directory 사용
		- overflow가 나면 directory doubling 후 overflow된 bucket만 splitting
		- hash function 수정
		

- diretoriless dynamic hashing
	- extendable hashing은 doubling에 의해 늘어나는 directory 사용
	- smoother growth -> linear hashing
	- linear hashing
		![[Pasted image 20240618224132.png]]
		- overflow가 난 bucket에 대해 overflow bucket을 사용해서 값 저장(chaining approach)
		- overflow가 난 bucket과는 상관없이 첫 번째 bucket부터 차례대로 splitting
		- 새로운 hash fuction 사용해서 bucket splitting
		- split pointer를 사용해서 다음에 split할 bucket을 가리킴
		- search
			![[Pasted image 20240618224039.png]]
			- $h_0(key)<sp$ 일 경우
				- 이미 split된 key값임
				- $h_1(key)$ bucket 탐색
			- 아닐 경우 $h_0(key)$ bucket 탐색

- hashing과 binary search tree 비교
	- hashing의 시간복잡도는 평균적으로 $O(1)$
	- BST의 시간복잡도는
		- 자가 균형 이진 탐색 트리의 경우 최악의 경우에도 $O(log(n))$
		- 편향 트리의 경우 $O(n)$
