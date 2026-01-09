
- Total cost of binary search tree
	![[Pasted image 20240618232807.png]]

- Optimal BST
	![[Pasted image 20240618233455.png]]
	- ?

- AVL Trees
	- Insert
		![[Pasted image 20240618233800.png]]
		![[Pasted image 20240618233812.png]]
		![[Pasted image 20240618233825.png]]
		- insert 단계
			- parent의 bf에 따라 leftRotation(), rightRotation()
			  -> child의 bf에 따라 LL, LR, RL, RR rotation
		- parent의 나머지 child의 height
		  = child의 나머지 child의 height
		  = grandChild의 chilld 중 높은 height
		  = grandChild의 chilld 중 낮은 height + 1
		- LL, RR은 child 노드가 새로운 parent, LR, RL은 grandChild 노드가 새로운 parent
		- rotation하면서 움직인 node들의 bf 변경
		- 새로운 parent의 bf = 0
	
	- Deletion
		- 중간 노드를 삭제했을 때
			- 삭제한 노드의 직전, 직후 노드 중 depth가 큰 쪽의 노드를 가져옴
		- leaf 노드를 삭제했을 때
			- 삭제한 노드부터 root 노드까지 path를 따라가면서 bf 확인
			- bf에 따라 rotation 수행
			- root 노드에 도착하기 전까지 반복
	
	- 시간복잡도
		![[Pasted image 20240619021647.png]]
		- AVL tree는 search, delete, insert에서 모두 $O(log(n))$

- Red-Black Trees
	- properties
		1. root, external은 black
		2. 연속적인 red는 없음
		3. 각 root-external path에서 black의 개수가 같음
	
	- height는 최대 $2 \times log_2(n+1)$
	- insertion deletion의 시간복잡도 = $O(log(n))$
	
	- 특성3에 의해서 새로 추가하는 노드는 red
	- if 새로 추가하는 red에 의해서 특성2 violation
	  -> imbalance
	
	- XYr imbalance
		- uncle이 red
		- recoloring
			- grandparent는 red으로 (root면 black으로)
			- parent, uncle은 black으로
		- 특성2 violation 없어질 때까지 반복
	
	- XYb imbalance
		- uncle이 black
		- rotation
			- AVL tree처럼 rotation
			- parent는 black으로
			- child는 red로
	![[Pasted image 20240619022615.png]]
	![[Pasted image 20240619022642.png]]
	![[Pasted image 20240619022652.png]]

