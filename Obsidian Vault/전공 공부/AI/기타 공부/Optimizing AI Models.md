[https://www.youtube.com/watch?v=zYGDpG-pTho](https://www.youtube.com/watch?v=zYGDpG-pTho)

---

## AI Model을 optimizing하는 3가지 방법
- RAG
    - Retrieval Augmented Generation
    - AI가 학습하지 않은 외부의 최신 데이터를 AI에 제공
    - AI는 질문과 제공된 외부 데이터를 vector 형태로 embedding
    - AI는 질문과 비슷한 vector embedding 값을 갖는 외부 데이터를 찾음
    - AI는 질문에 대한 응답을 외부 데이터를 이용해서 생성함으로써 학습하지 않은 최신 데이터에 대해서도 적절한 응답 제공 가능
    - 장점: up-to-date, domain specific
    - 단점: performance, processing
        - performance: 외부 데이터를 수집하고 질문과 비교해야 하므로 그만큼 성능이 떨어짐
        - processing: vector embedding 후 데이터베이스에 저장하는 등 infra cost를 포함한 processing cost가 듦
- Fine-Tuning
    - focus하고자 하는 database를 학습시킴으로써 파라미터를 업데이트함
    - domain-specific patterns를 학습함
    - 장점: deep domain expertise, faster
    - 단점: training complexity, computational cost, maintenance, catastrophic forgetting
        - training complexity: 수 천개의 high quality example이 필요함
        - computational cost: 상당한 양의 계산량 및 GPU가 필요함
        - maintenance: 모델을 업데이트하려면 training을 다시 해야함
        - catastrophic forgetting: 특정 database를 학습하면서 기존에 학습한 정보를 잃을 수 있음
    - ex) LoRA
- Prompt Engineering
    - 모델에 전달한 프롬프트는 일련의 layer들로 처리됨 → attention mechanisms
    - 각 layer는 전달된 프롬프트의 다른 측면들을 처리함
    - 프롬프트에 특정 요소들을 추가함으로써 관련된 패턴에 모델의 attention이 향하도록 함
    - 장점: no infrastructure changing, immediate results
    - 단점: 상당한 양의 trial and error, limited to existing knowledge