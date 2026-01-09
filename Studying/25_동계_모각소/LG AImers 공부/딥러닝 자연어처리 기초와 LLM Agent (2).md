
# NLP Systems(자연어처리 시스템) 구현

- Preprocessing
	- 문장을 단어 단위(token)로 쪼갬.
	- NN의 Input, Output은 일종의 행렬로 정의됨.
	- Raw text를 행렬로 변환하기 위해서 필요한 과정임

- Embedding
	- 단어를 Embedding Vector로 변환함.
	- 단어 하나하나를 Numerical Vectore Space로 보냄.

- Modeling
	- Vector들을 NN에 넣어서 Output를 냄.

# Vocab의 Size 고려

- 자주 나오지 않는 단어는 Unkown word 처리함.
- 너무 많은 단어를 Vocab에 저장해도 Vocab size가 커져서 연산량이 많아지고,
- 너무 많은 단어를 Unkown word 처리해도 모델이 학습을 제대로 하지 못함.

# Word Embedding

- Bag-of-Words(BoW)
	- ex) One-Hot Encoding

- Word2Vec
	- 주변 단어를 이용하여 word embedding을 생성함.
	- CBOW(Continuous Bag-of-Words)
		- 앞뒤 단어들을 이용해서 가운데 단어를 예측함.
	- Skip-gram
		- 가운데 단어를 이용해서 주변 단어들을 예측함.

# Language Model

- 