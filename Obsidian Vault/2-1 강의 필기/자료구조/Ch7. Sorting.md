
- classification of sorting
	- internal sorting
	- external sorting
		- sort하는 동안 external memory를 쓰는 algorithm

- insertion sort
	![[Pasted image 20240528234638.png]]
	- LOO(Left out of order)
	- 앞에서부터 정렬됨
	
	- 2번째부터 n번째 element를 차례대로 정렬된 배열의 올바른 곳에 삽입
	- j번째 element 고려하는 경우
		- j-1부터 1까지 거꾸로 비교하여 교환
	
	- best case: $O(n)$
	- worst case: $O(n^2)$

- bubble sort
	![[Pasted image 20240529004750.png]]
	![[Pasted image 20240529005030.png]]
	- 앞에서부터 두개씩 비교 후 교환
	- 가장 큰 값부터 뒤에서부터 정렬됨
	
	- time complexity: $O(n^2)$

- selection sort
	![[Pasted image 20240529103851.png]]
	![[Pasted image 20240529103909.png]]
	- index 0부터 n-1까지 각 자리에 올 값을 차례대로 정렬
	- index i 자리에 대해 index i+1부터 n-1을 탐색해서 최소값 선택 후 교환
	
	- time complexity: $O(n^2)$

- quick sort
	![[Pasted image 20240603142443.png]]
	- pivot: 각 부분의 제일 왼쪽 값
	- left: 전체 배열의 제일 왼쪽 값
	- right: 전체 배열의 제일 오른쪽 값
	
	- i= left, j = right + 1
	- i는 늘리고 j는 줄이면서 pivot보다 큰 i와 pivot보다 작은 j 교환
	- i >= j가 되면 swap 종료
	- 종료 당시 index j값은 pivot값보다 작으므로 pivot과 index j 교환
	- pivot 기준으로 왼쪽 부분과 오른쪽 부분에 대해 똑같은 방식으로 정렬
	
	- time complexity:
		- equal: $O(nlog(n))$
		 -> 이상적인 경우 배열이 반으로 계속 쪼개지므로 $log(n)$번 쪼개짐
		- worst: $O(n^2)$
		  -> 원소 하나씩 쪼개져 나갈 경우 n번 쪼개짐

- decision tree
	- sorting algorithm의 time complexity는 최소 $O(nlog(n))$

- merge sort
	![[Pasted image 20240605215317.png]]
	![[Pasted image 20240605220946.png]]
	![[Pasted image 20240605221007.png]]
	- mergeSort()로 쪼개고 merge()로 정렬하면서 합침
	
	- time complexity: $O(nlog(n))$

- heap sort
	![[Pasted image 20240605224609.png]]
	![[Pasted image 20240605224556.png]]
	![[Pasted image 20240605224627.png]]
	- adjust(element list\[], int root, int n)
		- 마지막 노드부터 부모 노드 index를 root로 받음
		- index를 1씩 줄이면서 모든 부모-자식 노드 정렬
		- temp: root 노드 저장
		
		- 반복
			- 자식 노드 중 큰 노드 선택
			- root 노드와 비교
			- 자식 노드가 더 크면 부모 노드에 해당 자식 노드 복사
			- 해당 자식 노드를 부모 노드로 하는 자식 노드와 비교
		- 부모 노드에 root 노드 저장
	- heapSort(element list\[], int n)
		- 반복
			- root 노드와 마지막 노드 교환
			- 마지막 노드 제외하고 root 노드에 대해  adjust()
		
		- time complexity: $O(nlog(n))$

- summary
	![[Pasted image 20240605235320.png]]