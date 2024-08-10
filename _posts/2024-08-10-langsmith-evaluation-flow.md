
<<<<<<< HEAD
# Langsmith : Evalution을 위한 데이터 흐름
=======
# Langsmith 평가를 위한 데이터 흐름
>>>>>>> a5f5f8ae9ce3c28a14e381f074a11760097c6097

출처 : [https://docs.smith.langchain.com/concepts/evaluation](https://docs.smith.langchain.com/concepts/evaluation)

## Applying evaluations (Langsmith Evaluation Flow)
![image](https://docs.smith.langchain.com/assets/images/langsmith_overview-b15c77264123be88370ccdd987a5f9f0.png)


### Dataset (데이터셋)
- Developer curated examples (개발자가 큐레이션한 예제): 개발자가 수동으로 큐레이션한 예제들.
- User provided examples and/or feedback (사용자가 제공한 예제 및/또는 피드백): 사용자 로그에서 가져온 예제들.
- LLM generated examples (LLM이 생성한 예제): 대규모 언어 모델이 생성한 합성 예제들.

### Task (태스크)
- RAG - Question-answering: 질문-응답 태스크.
- Chat - Conversation: 대화 태스크.
- Code gen - Code generation: 코드 생성 태스크.
- Agent - Task completion: 에이전트 작업 완료.
- Content gen - Writing, summarization, etc: 글쓰기, 요약 등 콘텐츠 생성 태스크.

### Evaluator (평가자)
- Judges (판단자)
  - LLM-as-judge: LLM이 판단.
  - Heuristics: 휴리스틱(경험적) 판단.
  - Human: 사람이 판단.
- Modes (모드)
  - Comparison: 두 개의 생성물을 비교.
  - Reference-free: 기준 없이 생성물을 비교.
  - Ground Truth: 생성물을 기준 정답과 비교.

### Applying evals (평가 적용)
- Production Traffic: 운영 환경에서 발생하는 데이터를 사용한 성능 평가.
  - A/B testing: 여러 버전의 앱 성능 비교.
  - Online evals: 운영 환경에서 인간, 모델, 또는 휴리스틱 평가.
  - Back-testing: 과거 운영 데이터를 사용한 새로운 앱 버전 평가.
- Curated datasets / assertions: 큐레이션된 데이터셋 또는 어서션을 사용한 평가.
  - Unit tests: 참조 데이터셋 없이 가벼운 assert를 사용한 빠른 평가.
  - Offline evals: 데이터셋을 사용한 오프라인 평가.
  - Pairwise: 앱 출력물을 순위 매기기 위한 평가.


 
