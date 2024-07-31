# ragas exercise

## 개요 
- Ragas (RAG Assessment)는 RAG 평가를 위한 framework 중 하나.
- Ragas evaluation metrics 복습 및 정리.

### 요약 
- Ragas에서 metric은 크게 generation, retrieval 두 영역으로 나누어 짐. 
  - generation
    - faithfulness : 생성된 답이 얼마나 사실적 정확성이 있는지 (factually acurate)
    - answer relevancy : 생성된 답이 얼마나 질문과 관련이 있는지
  - retrieval
    - context precision : 검색된 context의 precision. (노이즈가 많은지) 
    - context recall : 검색된 context의 recall. (놓치치 않았는지)
    - (잠시 precision / recall 복습)
      - precision = TP / (TP + FP). 모델 예측이 실제인 비율. 예측 정확도 높음. 스팸 메일.
      - recall = TP / (TP + FN). 실제가 모델 예측되는 비율. 놓치지 않음. 암.
- 공식 페이지의 간략 설명
  - faithfulness : how factually acurate is the generated answer 
  - answer relevancy : how relevant is the generated answer to the question 
  - context precision : the signal to noise ratio of retrieved context 
  - context recall : can it retrieve all the relevant information required to answer the question 
- 각 metric 별로 조금만 더 개념 정리하면 (자세한 공식등은 공식 페이지 참고)
  - 주요 metrics
    - faithfulness # ['question','contexts','answer'] 
      - answer가 question에 얼마나 정확한지.
      - how : answer를 개별 문장(statement)로 나누고 각각 문장에서 answer가 추론 가능한지 확인하고 계산
    - answer_relevancy # ['question','contexts','answer'] # w/ embeddings 
      - answer가 question에 얼마나 관련되었는지. 
      - how : answer에서 question을 리버스(w/llm)로 3개 만들고 실제 question과 유사도 평균 비교
    - context_precision # ['question','answer','contexts','ground_truths'] 
      - contexts중에 ground_truths가 얼마나 잘 찾아졌는지. contexts의 노이즈 정도. FP가 적음. 
      - how : contexts의 각 chunk가 question에 대해 ground_truths에 관련이 있는지 여부를 확인하고 각 precision@k를 계산하고 평균 
    - context_recall # ['question','answer','contexts','ground_truths'] 
      - ground_truths를 contexts중에 놓치치 않았는지. FN이 적음.
      - how : ground_truths answer를 개별 문장(statement)로 나누고 각각 문장이 contexts로부터 attributed 가능한지 확인하고 recall 계산
  - 그외 metrics (상황에 따라 사용)
    - context_entity_recall ['contexts','ground_truths'] 
      - retrieved context의 recall. 
      - ground_truths와 contexts 양쪽 모두 있는 엔터티 / ground_truths에만 있는 엔터티 (GE와 CE 각각 확인하고 계산)
    - answer_similarity ['question','answer','ground_truths'] # w/ embeddings 
      - 생성된 answer와 ground_truths 사이의 의미적 유사성 평가
    - answer_correctness ['question','answer','ground_truths'] # w/ embeddings

- RAGAS의 그외 특징 몇가지. 
  - Test Set을 간단하게 만들어 볼수 있음. 
    - TestsetGenerator() : langchain의 documents에 대해서 여러가지 question_type으로 질문과 답을 만들수 있음. (simple, reasoning, multi_context) 
    - llm을 generator_llm, critic_llm 별개 지정 가능.
  - Evalution을 위한 테스트용 dataset 구성.
    - 기본적으로 필요한 features : ["question", "ground_truth", "answer", "contexts"] .
    - answer, contexts, ground_truth가 일반적인 문장이 아닌 특이한 상황의 경우에는 다르게 구성해야 함. (굳이 ragas를 사용할 필요가 있을지 도메인에 따른 고민 필요.)
  - langchain, langsmith, llamaindex, langfuse 등 다양한 프레임워크들과 integration.
  
- 그외 RAG 평가 방법들
  - RAG 평가를 위한 들법들도 많은데 응 교수님 강의에 나왔던 Trulen도 있고, langchain, llamaindex 자체적인 evaluation 구현들도 있음. 
  - 참고로 구 langchain 0.1의 evaluation에서는 아래 3가지 방식 제공. <br>langchain 0.2x로 넘어가면서 본격적인 evaluation은 langsmith 담당.
    - [https://python.langchain.com/docs/guides/evaluation/](https://python.langchain.com/docs/guides/evaluation/) (-> 0.1 문서)
      - String Evaluators : 주어진 입력에 대한 예측 문자열 평가. (참조 문자열과 비교) 
      - Trajectory Evaluators : 에이전트 작업의 전체 궤적(Trajectory) 평가. 
      - Comparison Evaluators : 하나의 공통 입력에 대한 각각 다른 두개 실행의 결과 비교 (PairWise)
    - https://docs.smith.langchain.com/how_to_guides#evaluation --> langsmith에 대해서는 별도 정리 예정.


### references: 
 - [https://docs.ragas.io/en/stable/index.html](https://docs.ragas.io/en/stable/index.html)
 - [https://docs.ragas.io/en/stable/howtos/integrations/langchain.html](https://docs.ragas.io/en/stable/howtos/integrations/langchain.html)
 - [https://docs.ragas.io/en/stable/howtos/integrations/langsmith.html](https://docs.ragas.io/en/stable/howtos/integrations/langsmith.html)


### ragas tutorial

ragas 공식 페이지의 가이드 코드를 보면 ```"explodinggradients/amnesty_qa"``` 라는 dataset을 사용하는데 ```['eval']```안에는 이런 구조로 되어 있음. 대략 해석 내용 포함. ```contexts```는 ```List[str]```로 되어있음.   

```python
import os
from dotenv import load_dotenv, find_dotenv

_ = load_dotenv(find_dotenv())

from langchain_openai import AzureChatOpenAI
from langchain_openai import AzureOpenAIEmbeddings

from langchain.prompts import ChatPromptTemplate, PromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.messages import HumanMessage, SystemMessage

llm = AzureChatOpenAI(...)
embedder = AzureOpenAIEmbeddings(...)

from ragas.metrics import (
    answer_relevancy,
    faithfulness,
    context_recall,
    context_precision,
)

from datasets import load_dataset

# loading the V2 dataset
amnesty_qa = load_dataset("explodinggradients/amnesty_qa", "english_v2")
amnesty_qa

amnesty_qa['eval']['question'][0]
# 미국 대법원의 낙태에 대한 판결은 전 세계에 어떤 영향을 미칠까요?

amnesty_qa['eval']['answer'][0]
# "미국 대법원의 낙태 판결은 다른 국가에 선례를 제공하고 생식권에 대한 세계적 담론에 영향을 미치기 때문에 세계적 의미를 가질 수 있습니다. 잠재적 의미는 다음과 같습니다.\n\n1. 다른 국가에 미치는 영향: 대법원의 판결은 다른 국가가 자체 낙태법에 대해 고심하는 데 참고점이 될 수 있습니다. 생식권 옹호자들이 각자의 관할권에서 제한적인 낙태법에 이의를 제기하는 데 사용할 수 있는 법적 주장과 추론을 제공할 수 있습니다.\n\n2. 세계적 생식권 운동 강화: 대법원의 유리한 판결은 전 세계의 생식권 운동에 활력을 불어넣고 힘을 실어 줄 수 있습니다. 여성의 권리를 옹호하는 활동가와 조직의 결집점이 되어 전 세계적으로 동원과 옹호 활동이 증가할 수 있습니다.\n\n3. 낙태 반대 운동에 대응: 반대로 낙태권을 제한하는 판결은 전 세계적으로 낙태 반대 운동을 고무시킬 수 있습니다. 그것은 그들의 주장에 합법성을 제공하고 다른 국가에서도 유사한 제한 조치를 장려할 수 있으며, 잠재적으로 기존의 생식권의 롤백으로 이어질 수 있습니다.\n\n4. 국제 원조 및 정책에 미치는 영향: 대법원의 판결은 생식 건강과 관련된 국제 원조 및 정책에 영향을 미칠 수 있습니다. 그것은 기부국 및 조직의 우선순위와 자금 조달 결정을 형성하여 잠재적으로 생식권 이니셔티브에 대한 지원 증가 또는 반대로 낙태 관련 서비스에 대한 자금 조달 제한으로 이어질 수 있습니다.\n\n5. 국제 인권 기준 형성: 이 판결은 생식권과 관련된 국제 인권 기준의 개발에 기여할 수 있습니다. 그것은 기존 인권 조약 및 협약의 해석 및 적용에 영향을 미쳐 잠재적으로 생식권이 기본 인권이라는 인식을 전 세계적으로 강화할 수 있습니다.\n\n6. 세계적 건강 영향: 대법원의 판결은 특히 제한적인 낙태법을 가진 국가에서 세계적 건강 결과에 영향을 미칠 수 있습니다. 이는 안전하고 합법적인 낙태 서비스의 가용성과 접근성에 영향을 미쳐 잠재적으로 안전하지 않은 낙태와 관련된 건강 합병증이 증가할 수 있습니다.\n\n구체적인 영향은 대법원 판결의 성격과 미국 내외의 정부, 활동가, 조직이 취한 후속 조치에 따라 달라질 수 있다는 점을 알아두는 것이 중요합니다."


amnesty_qa['eval']['contexts'][0]
# ["- 2022년 미국 대법원은 임신 중절에 대한 헌법적 권리를 인정한 50년간의 판례를 뒤집는 판결을 내렸습니다.\n- 이 판결은 엄청난 영향을 미쳤습니다. 생식 연령의 여성과 소녀 3명 중 1명이 현재 임신 ​​중절에 대한 접근성이 전혀 없거나 거의 전혀 없는 주에 살고 있습니다.\n- 임신 중절에 대한 가장 제한적인 법률을 가진 주는 산모 건강 지원이 가장 약하고 산모 사망률이 더 높으며 아동 빈곤율이 더 높습니다.\n- 미국 대법원의 판결은 미국이 전 세계적으로 행사하는 지정학적, 문화적 영향력과 지원으로 인해 국가 경계를 넘어 영향을 미쳤습니다.\n- 전 세계의 SRR 조직과 활동가들은 이 판결이 다른 국가에서 임신 중절에 반대하는 입법 및 정책 공격을 위한 토대를 마련할까봐 우려를 표명했습니다.\n- 지지자들은 또한 이 판결이 진보적 법률 개혁에 미치는 영향과 특정 아프리카 국가에서 임신 중절 지침의 채택 및 시행을 지연시키는 것을 관찰했습니다. 국가.\n- 이 판결은 국제 정책 공간에 냉각 효과를 만들어, 임신 중절 반대 국가 및 비국가 행위자들이 인권 보호를 훼손하도록 고무시켰습니다.",
# '임신 중절에 대한 미국 대법원의 판결은 미국 내부뿐만 아니라 전 세계적으로 격렬한 논쟁과 논의를 불러일으켰습니다. 많은 국가가 미국을 법적 및 사회적 문제의 리더로 보고 있기 때문에 이 판결은 다른 국가의 임신 중절에 대한 정책과 태도에 영향을 미칠 가능성이 있습니다.',
# "이 판결은 생식 권리와 여성 건강 문제를 다루는 국제 기구와 비정부 기구에도 영향을 미칠 수 있습니다. 결과에 따라 자금 지원, 옹호 활동 및 미국 대응자와의 협력에 변화가 생겨 생식 정의를 위한 세계적 투쟁에 파장이 생길 수 있습니다."]

from ragas import evaluate

result = evaluate(
    amnesty_qa["eval"],
    metrics=[
        context_precision,
        faithfulness,
        answer_relevancy,
        context_recall,
    ],
    llm = llm,
    embeddings = embedder
)

result # {'context_precision': 0.8042, 'faithfulness': 0.3547, 'answer_relevancy': 0.9354, 'context_recall': 0.9500}

result.to_pandas() # df 출력. 각 questions 마다 metrics table.
```

### ragas exercise

['question','answer','contexts','ground_truths']를 수동으로 구성해서 시험해보자.

```python
from datasets import Dataset

questions = ["What is AI?", "What is ML?"]
ground_truths = ["Artificial Intelligence", "Machine Learning"]
answers = ["AI is Artificial Intelligence.", "ML is Machine Learning."]
contexts = [["AI is Artificial Intelligence."], ["ML is a subset of AI.", "ML is Machine Learning."]]

data_dict = {
    "question": questions,
    "ground_truth": ground_truths,
    "answer": answers,
    "contexts": contexts
}

# Dataset 객체 생성
dataset = Dataset.from_dict(data_dict)

# 생성된 Dataset 확인
print(dataset) # 생략


r = evaluate(
    dataset,
    metrics=[
        context_precision,
        faithfulness,
        answer_relevancy,
        context_recall,
    ],
    llm = llm,
    embeddings = embedder
)

r # {'context_precision': 0.7500, 'faithfulness': 1.0000, 'answer_relevancy': 0.8457, 'context_recall': 1.0000}

r.to_pandas()
```

| metrics     | context_precision | faithfulness | answer_relevancy | context_recall |
| :-----:     | :---------------: | :----------: | :--------------: | :------------: |
| What is AI? | 1.0               | 1.0          | 0.826935         | 1.0            |
| What is ML? | 0.5               | 1.0          | 0.864534         | 1.0            |



### 기타 

from ragas.metrics import AnswerSimilarity # ['answer','ground_truths']
from ragas.metrics import AnswerCorrectness # ['answer','ground_truths']
from ragas.metrics.critique import conciseness # ['question','answer'] 
from ragas.metrics import ContextRelevance # ['question','contexts'] # 초기에 있었는데 지금은 사라짐... (240726)
```