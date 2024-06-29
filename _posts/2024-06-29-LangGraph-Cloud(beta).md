
# references (24/06/27 업로드) : 
 - [https://blog.langchain.dev/langgraph-cloud/](https://blog.langchain.dev/langgraph-cloud/)
 - [https://langchain-ai.github.io/langgraph/cloud/](https://langchain-ai.github.io/langgraph/cloud/)

# 요약
- LangGraph 0.1로 stable version이 됨.
- LangGraph Cloud (bata)를 발표함. langsmith와 github와 연동하여 빠르게 deploy하고 LangGraph Studio로 tracing, monitoring이 가능한 에이전트 클라우드 서비스. 신규 사용자 제어 방식 등 몇가지 기능 추가.   

# LangGraph 0.1

- stable version 됨.
- Klarna, Replit, Ally, Elastic, NCL 등 여러곳에서 사용중.

# LangGraph Cloud

- a closed source, paid product in an invite-only stage. (24/6/27 현재 closed beta) 
- [https://youtu.be/l4sMKF1dTDM (08:43)](https://youtu.be/l4sMKF1dTDM) 
- Streaming & Human-in-the-loop : LangGraph 기존 지원.
- Double-texting : 새로운 사용자 입력 방식 - reject, queue, interrupt, rollback # TODO
- Asynchronous background jobs : long-running task를 위한 것임. polling 하거나 webhook으로 확인 가능.
- Cron jobs : 스케줄링.

## LangGraph Studio 
 - 협업, 배포, 모니터링에 대한 통합된 환경 제공

## LangGraph Cloud quick start (요약)
- [https://langchain-ai.github.io/langgraph/cloud/quick_start/](https://langchain-ai.github.io/langgraph/cloud/quick_start/)
  - API Key 준비
    - Anthropic / Travily / LangSmith
  - 작성할 파일
    - agent.py - graph를 define 하는 파이썬 코드 파일.
    - requirement.txt - 파이썬 패키지 파일. 
    - langgraph.json - graph 구성에 대한 설정 파일.
    - .env - API KEY env 파일 (로컬에서 확인용. git에 올리지 말것)
  - 클라우드 배포전에 로컬에서 시험 
    - pip install langgraph-cli
    - langgraph test
      - 서버 실행 됨 (INCREDIBLY simple하므로 시험 전용으로 쓸 것)
      - curl POST ... 사용해서 test
    - github repo push
    - langsmith > 🚀 icon > New Deployment button > Import from github > 설정들 > Submit > 배포 !
    - deployments page가 나옴
      - Trace Count monitoring chart와 Recent Traces run view가 보임. (by LangSmith)
      - API Docs가 지원되며 curl 테스트도 가능함.
      - LangGraph Studio와 연결할수 있음.
      - LangGraph Studio에서 start run을 해볼수 있음.
    - 그 다음에는 LangGraph SDK로 연동할 수 있음. (클라이언트 사이드)
        - pip install langgraph_sdk
        - langgraph_sdk에서는 LANGCHAIN_API_KEY (cloud에서 제공)와 URL(배포된 LangGraph)을 가지고 client를 만들수 있고, client에서 assistants를 찾아서 thread.create를 할수 있음.




