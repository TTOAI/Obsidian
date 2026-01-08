
- K-ary tree representation
	- node 개수 = n, n >= 1
	- child field 개수 = nk
	- non-empty child field 개수 = n-1
	- empty child field 개수 = nk-(n-1) = n(k-1)+1
- Left child-right sibling representation
	- node structure
				data
			left child | right sibling
- Left child-right sibling -> Binary tree
- Binary tree
	- node의 최소 개수: 0
	- children의 순서 중요함
		- ex) left(right) subtrees만 가진 binary tree
- Binary tree의 종류
	- Skewed binary tree
	- Full binary tree: ($2^k - 1$) nodes, k = depth
	- Complete binary tree: 왼쪽부터 차례대로 채워진 binary tree

- complete tree
	- index i인 node
	- parent: i/2의 몫
	- leftChild: 2i
	- rightChild: 2i+1

- tree traversal
	- LVR: inorder, LRV: postorder, VLR: preorder

![[Pasted image 20240424154731.png]]
- iterative inorder traversal
	- stack 이용, DFS
	
	- 반복
		- left child의 마지막까지 push하며 이동
		- empty stack인지 체크
		- pop 후 출력
		- right child로 이동
	
	- DFS의 기본 아이디어:
		- 왼쪽으로 쭉 내려가기
		- 오른쪽으로 갈 수 있으면 한 칸 이동
		- 왼쪽으로 또 쭉 내려가고, ...

![[Pasted image 20240424154753.png]]
- level order traversal
	- heap 이용, BFS
	
	- 반복
		- 출력
		- left, right child push
		- pop

- threaded binary tree
	- null links를 이전 노드나 이후 노드를 가리키는 pointers(threads)로 교체
		![[Pasted image 20240619130712.png]]
	- Inorder traversal of a threaded binary tree
		![[Pasted image 20240424154831.png]]
		- insucc(threadedPointer tree)
			- 그 다음 순서의 node(successor)를 찾는 함수
			
			- right child 혹은 rightThread로 이동
				- rightThread가 false -> right child로 이동함
					- 제일 마지막 left child까지 이동
				- rightThread가 true -> rightThread로 이동함
			- 마지막으로 참조한 노드 리턴
	
	- rightThread == stack의 pop

- inserting a node as a right child
	![[Pasted image 20240424154912.png]]
	- 삽입 노드 초기화
	- 이전 노드 초기화
	- right child 있으면 insucc()
	- 삽입 노드 그 다음 순서 leftTread 초기화

- heap
	- priority queue 구현을 위해 사용
	- max heap
		- max tree 이면서 complete binary tree
		- max tree: 부모 node가 value가 자식 node의 key value보다 작지 않은 값을 갖는 tree
	
	시간복잡도: insertion, deletion 모두 $O(log(n))$

- insertion into a max heap
	![[Pasted image 20240501105033.png]]
	- heap[1]에 root 노드 있음
	
	- i == ++(\*n)
	- i번째 노드의 부모 노드는 i/2번째 노드
	- 삽입 노드가 부모 노드보다 크면 계속 부모 노드와 바꿈
	
	- 부모 노드를 타고 올라가므로 height가 중요
	- height: x >= $log_2(n+1)$인 정수 x
	- 시간복잡도: $O(log(n))$

- Deletion from a max heap
	![[Pasted image 20240501110846.png]]
	- root 노드를 삭제하는 경우만 다룸
	
	- item: root 노드의 값(삭제 값, 반환 값)
	- temp: 마지막(\*n) 노드의 값
	- (\*n)--: 노드가 하나 줄어들므로
	
	- parent: 1부터 시작
	- child: 2부터 시작, while문 시작할 때마다 child는 left child(짝수)
	- 반복
		- left, right child 중 큰 값 선택(같으면 left)
		- temp가 child보다 크거나 같으면
			- while문 벗어남
		- childe가 더 크면
			- parent를 child로, child를 child의 left child로 바꿈
	- parent노드에 temp 저장

- binary search tree
	- 모든 키 값은 하나씩 존재
	- 임의의 노드의 키 값은 left subtree보다 크고 right subtree보다 작음
	- 모든 subtree는 binary search tree
	- BST의 inorder traversal은 sorted list을 생성함

- searchig a binary search tree
	![[Pasted image 20240501112118.png]]
	- 시간복잡도
		![[Pasted image 20240501112949.png]]

- Inserting into a Binary Search Tree
	![[Pasted image 20240501113008.png]]
	- ptr
		- 추가할 노드
		- key 값: k
	- key 값 k에 대해 tree search 수행
	- temp
		- tree가 empty거나 tree에 k가 이미 있음
			- NULL
		- tree를 searth하면서 마지막으로 탐색한 노드
	
	- temp가 NULL이고 \*node가 NULL이 아닐 때(추가할 노드가 tree에 이미 있고 비어있지 않을 때)
	  -> 아무 것도 안 함
	- 추가할 노드가 tree에 없거나 비어있을 때
		- 비어있지 않을 때
		  -> 대소 비교 후 temp의 left 혹은 right child로 추가
		- tree가 비어있을 때
		  -> root노드로 추가

- height of BST
	- with n element
	- average: $O(log(n))$
	- worst: $O(n)$
	- balanced search tree
		- worst: $O(log(n))$
