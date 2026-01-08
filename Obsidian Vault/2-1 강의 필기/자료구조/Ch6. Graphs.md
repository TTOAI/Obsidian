
- definitions
	- G = (V, E)
		- V(G): vertex들의 집합
		- E(G): edge들의 집합
	- Undirected graph: (u, v) = (v, u)
	- Directed graph: <u, v> != <v, u>
	- 제한 사항(수업 한정)
		- 자기 자신으로 되돌아오는 edge 없음
		- pair of vertices가 동일한 edge는 하나밖에 없음

- complete graph
	- 최대 개수의 edge를 가지는 graph
		- undirected: n(n-1)/2
		- directed: n(n-1)

- multigraph(수업 시간 고려X)
	- 동일한 pair of vertices가 여러 개의 edges에 의해 연결될 수 있음

- terminology
	- (u, v)
		- The vertices u and v are ==adjacent==
		- The edge (u, v) is ==incident on== u and v
	- <u, v>
		- The vertex u is ==adjacent to== v
		- The vertex v is ==adjacent from== u
		- The edge <u, v> is ==incident on== u and v
	- path from vertex u to v
		- a sequence of vertices
		- vertices를 잇는 edge가 있어야 됨
		- path의 길이: edge들의 개수
		- vertex가 중복돼도 됨
	- simple path
		- 시작과 끝을 제외한 vertex들이 모두 중복되지 않는 path
		- 수업에서 multigraph를 고려하지 않으므로 edge가 중복되면 simple path 아님
	- cycle
		- 시작과 끝이 같은 simple path
	- connected (undirected)
		- u, v 사이에 path가 있음
	- connected component or component
		- undirected graph의 maximal connected subgraph
	- tree는 connected and acyclic인 graph
	- strongly connected (directed)
		- u에서 v, v에서 u로 가는 directed path가 모두 있음
	- strongly connected component
		- strongly connected인 maximal subgraph
	- degree
		- vertex에 연결된 edge의 개수
	- in-degree
		- vertex로 들어오는 edge의 개수
	- out-degree
		- vertex에서 나가는 edge의 개수

- adjacency matrix
	- a\[i]\[j]:
		- 1, (i, j) 또는 <i, j>가 있으면
		- 0
	- undirected graph
		- symmetric
		- row sum, column sum -> degree
	- digraph
		- row sum -> out-degree
		- column sum -> in-degree

- adjacency lists
	- 각각의 vertex가 하나의 list를 가짐
	- vertex i에 대한 list의 nodes는 vertices that are adjacent from i
	- nodes는 정렬될 필요 없음

- weighted edges
	- adjacency matrix: 1 대신 weight
	- adjacency list: weight field 추가

- depth first search
	![[Pasted image 20240528113211.png]]
	- visited\[]: 방문했는지 체크
	- recursive하게 작동(function call stack)

- breadth first search
	![[Pasted image 20240528115620.png]]
	- queue 이용

- dfs, bfs로 graph의 connected conmponents 만들기
	![[Pasted image 20240528123744.png]]

- spanning trees
	- 그래프 내의 모든 vertex를 포함함
	- vertex가 n개일 때 n-1개의 edge를 가진 tree

- minimum cost spanning tree 구하기
	- Kruskal’s algorithm
	- Prim’s algorithm
	- Sollin’s algorithm
	
	- greedy method
		- local optimum을 통해 global optimum을 구하는 방법

- Kruskal’s algorithm
	- T를 cycle로 만들지 않으면서 가장 작은 cost를 갖는 Edge부터 차례대로 선택
	![[Pasted image 20240528164221.png]]

- Prim’s Algorithm
	- 처음 선택한 vertex에서부터 연결된 edge들 중 가장 작은 cost를 갖는 edge를 선택하며 T를 확장
	![[Pasted image 20240528164236.png]]

- Sollin’s Algorithm
	- vertices만 있는 forest로 시작
	- stage 반복
		- 각 component에서 최소 cost edge를 선택
		- 중복되는 선택은 하나만 남기
	![[Pasted image 20240528171952.png]]

- shortest paths
	- Dijkstra's Algorithm
		![[Pasted image 20240517113407.png]]
		![[Pasted image 20240517112704.png]]
		![[Pasted image 20240528180957.png]]
		- v: 시작 vertex
		- cost\[]\[]: 인접한 vertex끼리의 cost
		- distance\[i]: v에서 i까지의 최단 거리
		- found\[i]:
			1, i를 거치는 최단경로를 고려함
			0, 아직 고려하지 않음
		- pi\[]: 최단경로 상에서 각 vertex의 이전 vertex
		
		- distance\[]에서 최단경로를 가진 vertex부터 차례대로 선택하는 걸 n-2번 반복
			- v를 제외하고 마지막 vertex는 제일 먼 경로를 가지므로 고려하지 않아도 됨
		- 모든 vertex에 대해 바로 가는 distance와 최단경로를 가진 vertex를 거쳐가는 distance 비교해서 distance 수정
		
		- Dijkstra's Algorithm은 Negative Edge Cases를 고려하지 못함
	
	- Bellman-Ford's algorithm
		- negative weight 허용
		- negative cycle을 포함하지 않는 shortest path 탐색
			- edges의 최대 개수는 (vertex 개수 - 1)
		![[Pasted image 20240528205242.png]]
		- v: 시작 vertex
		- k = 2부터 k = n-1일 때까지 반복
			- shortest path의 최대 edges 수는 n-1개이므로
		- 시작 vertex가 아니고 incoming edge가 적어도 하나 있는 vertex u에 대해
			- 각 incoming edge에 대해
			- 바로 가는 distance와 incoming edge를 거쳐서 가는 distance 비교해서 distance 수정
		- 모든 edge (u, w)에 대해 dist(w) > dist(u) + length\[u]\[w]인지 확인해서 negative cylce 존재 유무 확인
