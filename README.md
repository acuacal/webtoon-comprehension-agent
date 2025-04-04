# Webtoon Comprehension AI Agent (PoC)

## 프로젝트 개요
이 프로젝트는 웹툰 콘텐츠(이미지+텍스트)를 이해하고 분석하여 요약 결과를 제공하는 AI 에이전트의 Proof-of-Concept(PoC)입니다.

### PoC 범위
- **입력:** 단일 웹툰 에피소드의 대표 이미지(1~3컷)와 텍스트.
- **처리:**
    - MLLM(GPT-4o 등)을 활용하여 각 이미지 내용 분석.
    - LLM(GPT-4o-mini 등)을 활용하여 텍스트와 이미지 분석 결과를 종합하여 에피소드 요약 생성.
    - LangGraph를 이용한 워크플로우 구성 (이미지 분석 -> 요약 생성).
- **출력:** 에피소드 요약 및 각 이미지 분석 결과.
- **UI:** Streamlit 기반의 간단한 웹 인터페이스 제공 (샘플 에피소드 선택 또는 파일 업로드 기능, 결과 표시).

### 기술 스택
- LangChain & LangGraph
- OpenAI GPT-4o / GPT-4o-mini (또는 Gemini)
- Streamlit
- Python


## 코드 구조 설계
- `app.py`: Streamlit 웹 애플리케이션 실행 파일.
- `src/agents/webtoon_agent.py`: LangGraph 에이전트 정의 (State, Nodes, Graph).
- `src/utils/data_loader.py`: 샘플 웹툰 데이터 로딩 유틸리티.
- `data/`: 샘플 데이터 저장 폴더 (images, text, metadata.json).
- `config/`: (필요시) 설정 파일 저장.
- `tests/`: 테스트 코드 저장 폴더.
- `uv.lock` & `pyproject.toml`: 의존성 관리.
- `.env`: API 키 관리.

# PathRAG 설치 및 사용 가이드

이 프로젝트에서는 PathRAG를 사용합니다. PathRAG와 메인 프로젝트의 설치는 별도로 진행해야 합니다.

## 설치 방법

1. 먼저 PathRAG를 설치합니다:
```bash
cd PathRAG
uv pip install -e .
```

2. 그 다음 메인 프로젝트의 의존성을 설치합니다:
```bash
cd ..  # 메인 프로젝트 루트로 이동
uv pip install -e .
```

## 사용 방법

PathRAG 사용 예제는 `v1_test.py` 파일을 참고하세요.

```python
import os
from PathRAG import PathRAG, QueryParam
from PathRAG.llm import gpt_4o_mini_complete

# 작업 디렉토리 설정
WORKING_DIR = "./your_working_dir"

# API 키 설정 (필요 시)
api_key="your_api_key"
os.environ["OPENAI_API_KEY"] = api_key
base_url="https://api.openai.com/v1"
os.environ["OPENAI_API_BASE"]=base_url

# 작업 디렉토리 생성
if not os.path.exists(WORKING_DIR):
    os.mkdir(WORKING_DIR)

# PathRAG 초기화
rag = PathRAG(
    working_dir=WORKING_DIR,
    llm_model_func=gpt_4o_mini_complete,  
)

# 데이터 삽입
data_file="./text.txt"
with open(data_file) as f:
    rag.insert(f.read())

# 쿼리 수행
question="your_question"
result = rag.query(question, param=QueryParam(mode="hybrid"))
print(result)
```