# Korean Date & Schedule Reasoning with CoT / ReAct

이 저장소는 한국어 날짜·일정 관련 질의를 대상으로  
**Chain-of-Thought(CoT) 프롬프팅**과 **ReAct 기반 에이전트**의 성능을 비교하는 실험 코드와 데이터를 담고 있습니다.

- Task 1: 상대 날짜 표현 → 절대 날짜(`YYYY-MM-DD`) 변환
- Task 2: 문장 속 시간 표현 추출 + 절대 날짜 변환
- Task 3: 여러 제약을 만족하는 일정(날짜 리스트) 생성

자세한 실험 설정과 결과는 동봉된 Final Report를 참고하세요.

---

## Repository Structure

```text
.
├── data/              # 각 Task에 대한 입력/정답 데이터셋 (JSON)
│   ├── T1_dataset.json
│   ├── T2_dataset.json
│   └── T3_dataset.json
│
├── prompts/           # LLM 프롬프트 모음 (CoT / ReAct)
│   ├── t1_cot.txt
│   ├── t1_react_observation.txt
│   ├── t1_react_thought.txt
│   ├── t2_cot.txt
│   ├── t2_react_observation.txt
│   ├── t2_react_thought.txt
│   ├── t3_cot.txt
│   ├── t3_react_observation.txt
│   └── t3_react_thought.txt
│
├── results/           # 모델별 실행 결과 (예: gpt, solar)
│   ├── gpt/
│   └── solar/
│
├── t1.py              # Task 1 실행 스크립트
├── t2.py              # Task 2 실행 스크립트
├── t3.py              # Task 3 baseline / rule 기반 등
└── t3_llm.py          # Task 3 LLM ReAct 에이전트 실행 스크립트
```

---

## Tasks

### Task 1 — Date Normalization (T1)

- 입력: 앵커 날짜 + 상대 시간 표현 한 줄  
  - 예) `다음 주 금요일`, `이달 마지막 주말 바로 다음 평일`
- 출력: `YYYY-MM-DD` 형식의 날짜 1개  
- 목표: 순수 시간 산술 능력 평가

### Task 2 — Sentence-level Date Normalization (T2)

- 입력: 한국어 문장 전체 + 앵커 날짜  
  - 예) `다음 주 금요일이 언제인지 알려주라`
- 모델 동작:
  1. 문장 안에서 시간 표현을 찾고
  2. Task 1과 동일한 규칙으로 절대 날짜로 변환
- 출력: `YYYY-MM-DD` 형식의 날짜 1개

### Task 3 — Constraint-based Scheduling (T3)

- 입력: 앵커 날짜 + 여러 개의 제약(요일, 간격, 개수, 공휴일 제외 등)
- 출력: 제약을 만족하는 날짜 리스트  
  - 예) `["2025-03-03", "2025-03-17", "2025-03-31"]`
- CoT vs ReAct 에이전트의 복합 추론·도구 사용 능력을 비교

---

## Models & Methods

- **CoT (Chain-of-Thought)**  
  - 각 Task에 대해 few-shot 예시와 단계별 추론 예시가 포함된 프롬프트 사용
  - 한 번의 호출로 최종 답 생성

- **ReAct Agent**  
  - Thought → Action → Observation 루프를 통해  
    날짜 계산/공휴일 조회 등 **외부 도구**를 사용
  - 사용 도구 예시:
    - `calculator`: 날짜 + n일, 주 단위 이동 등
    - `calendar_db`: 공휴일·기념일 조회
    - `search`: 특정 이벤트(콘서트 등) 날짜 검색

## How to Run

아래는 기본 실행 예시입니다.  
세부 옵션은 각 스크립트의 `main` 함수를 참고해 조정하세요.

```bash
# Task 1
python t1.py

# Task 2
python t2.py

# Task 3 (rule/baseline)
python t3.py

# Task 3 (LLM ReAct agent)
python t3_llm.py
```
