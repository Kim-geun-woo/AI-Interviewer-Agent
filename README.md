# 🤖 AI 면접관 에이전트 구축 프로젝트

본 프로젝트는 **LangGraph**를 활용하여 **동적 AI 면접관 에이전트**를 개발한 사례입니다.

저는 이 프로젝트에서 면접의 첫인상을 결정하는 **①사전 준비(이력서/JD 분석 및 전략 수립) 고도화**와 사용자와 에이전트를 연결하는 **②Gradio 웹 인터페이스 구현**을 담당했습니다.

---

## 📌 프로젝트 개요

* **목표**: LangGraph와 LLM을 활용하여, 이력서 기반 맞춤형 질문을 생성하고, 답변을 평가하며, 스스로 평가를 '성찰(Reflect)'하여 면접 흐름을 자율적으로 제어하는 AI 면접관 에이전트 개발
* **진행 기간**: 2025.10.28 ~ 2025.11.03 (7일)
* **참여 인원**: 총 7명
  
---

## 👥 팀 구성 및 개인 역할

| 역할 | 담당자 | 주요 업무 |
| :--------- | :--------- | :--- |
| **사전 준비 고도화 / Gradio 연결** | **본인** | - **이력서/채용공고 교차 분석:** `analyze_resume` 함수의 LLM 프롬프트를 고도화하여, 이력서와 채용공고(JD)를 동시에 분석하고 양측의 핵심 키워드를 추출하도록 개선<br>- **맞춤형 면접 전략 수립:** `generate_question_strategy` 함수의 프롬프트를 수정하여, 추출된 키워드를 기반으로 3가지 평가 분야(경험, 소통, 문제해결력)에 따른 맞춤형 면접 전략 및 예시 질문 생성<br>- **Gradio 웹 인터페이스 구현:** 사용자가 이력서와 JD를 업로드하고 LangGraph 에이전트와 실시간 면접을 진행하며, 최종 보고서를 받을 수 있는 웹 UI 구현 |
| **PM** | 팀원 A | - 과제 총괄 진행<br>- State, Graph 담당<br>- 결과 보고서 작성 |
| **답변 평가 고도화** | 팀원 B | - Reflection 노드 추가 및 그래프 수정<br>- Conversation 관리 |
| **인터뷰 진행 고도화** | 팀원 C, 팀원 D | - 조건 설계<br>- 조건에 따른 노드 연결(그래프 수정) |
| **질문 생성 고도화** | 팀원 E | - Vector DB 구성<br>- 질문 생성 시 참조 데이터베이스 활용 |
| **피드백 보고서 고도화** | 팀원 F | - 보고서 틀 구성<br>- 보고서 내용 생성을 위한 LLM 구성 |



> 🔹 본 프로젝트는 팀원 전체의 긴밀한 협업으로 진행되었습니다. 특히 LangGraph의 복잡한 상태(State)를 일관되게 관리하기 위해, Excel 시트를 활용하여 각 노드의 입출력 상태를 실시간으로 공유하고 검증하며 개발을 진행했습니다.

---

## 🛠️ 기술 스택 (Tech Stack)

| 분야 | 도구 / 라이브러리 |
| :--- | :--- |
| 언어 | Python |
| LMOps / Agent | **LangChain**, **LangGraph**, OpenAI (GPT-4o-mini) |
| 데이터 | **Chroma DB** (Vector DB), PyMuPDF, python-docx |
| UI / 환경 | **Gradio**, Google Colab |

---

## 👣 프로젝트 주요 프로세스

| 단계 | 내용 |
| :--- | :--- |
| **v1.0 분석** | 기존 규칙 기반(if-else) 면접 Agent의 한계점 분석 |
| **v2.0 설계** | **LangGraph** 기반의 유연하고 자가-성찰(`reflect`)이 가능한 에이전트 구조 설계 |
| **사전 준비 (본인)** | `analyze_resume` (이력서/JD 분석), `generate_question_strategy` (면접 전략 수립) 기능 고도화 |
| **Agent 로직 구현** | `evaluate` → `reflect` → `re_evaluate_answer` / `decide_next_step` → `generate` 등 핵심 노드 개발 | 
| **UI 연동 (본인)** | **Gradio**(`app.py`)를 활용하여 사용자가 파일을 업로드하고 실시간 채팅으로 면접을 볼 수 있는 웹 인터페이스 구현 |

---

## ✨ 주요 특징 및 구현 내용 (My Role)

### ✅ 1. 사전 준비 고도화: 이력서/JD 기반 전략 수립

면접의 품질은 '사전 준비' 단계에서 결정된다고 판단하여, 이력서와 채용공고(JD)를 심층적으로 분석하는 로직을 고도화했습니다.

* **`analyze_resume` (분석)**: LLM을 사용하여 지원자의 이력서와 회사의 JD 텍스트를 동시에 분석합니다. 두 문서에서 각각 10개의 핵심 키워드를 추출하고, 이력서 핵심 요약을 생성하여 JSON 형식으로 반환합니다. 
* **`generate_question_strategy` (전략 수립)**: 분석된 키워드와 요약본을 바탕으로, LLM이 **3가지 평가 분야(①경력과 경험, ②커뮤니케이션 능력, ③문제해결력)**에 대한 면접 전략과 구체적인 예시 질문을 생성합니다. 

![[analyze_resume 함수]](https://github.com/Kim-geun-woo/AI-Interviewer-Agent/blob/main/images/analyze_resume.png)

[이미지: analyze_resume 함수]

![generate_question_strategy 함수](https://github.com/Kim-geun-woo/AI-Interviewer-Agent/blob/main/images/generate_question_strategy.png)

[이미지: generate_question_strategy 함수]

### ✅ 2. Gradio 기반 웹 인터페이스 연동

개발된 LangGraph 에이전트를 실제 사용자가 활용할 수 있도록 작성하여 Gradio 웹 인터페이스를 구현했습니다. 

* **파일 업로드**: 사용자가 이력서(PDF/DOCX)와 채용공고(JD) 파일을 업로드하면, `upload_and_initialize` 함수가 `preProcessing_Interview` 로직을 실행합니다.
* **상태 관리**: Gradio의 `session_state`를 활용하여 사용자별 면접 상태(State)를 관리했습니다.
* **실시간 채팅**: 사용자가 답변을 입력(`user_input.submit`)하면, `chat_interview` 함수가 `graph.invoke(state)`를 호출하여 에이전트의 다음 질문이나 최종 요약본을 받아와 챗봇 UI에 표시합니다.

![[Gradio 인터페이스]](https://github.com/Kim-geun-woo/AI-Interviewer-Agent/blob/main/images/Gradio%20%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4.png)

[이미지: Gradio 인터페이스]

### ✅ 3. (Project) LangGraph 기반 자가-성찰 에이전트

![[LangGraph 에이전트 흐름도]](https://github.com/Kim-geun-woo/AI-Interviewer-Agent/blob/main/images/Graph.png)

[이미지: LangGraph 에이전트 흐름도]

팀 프로젝트의 핵심 로직인 LangGraph의 자가-성찰(Self-Reflection) 흐름을 이해하고, 저의 사전 준비 모듈이 이 흐름에 잘 연동되도록 했습니다.

* **흐름**: (1)`evaluate_answer` (답변 평가) → (2)`reflect` (평가 검증) → (3a)`re_evaluate_answer` (부적절 시 재평가) 또는 (3b)`decide_next_step` (적절 시 다음 행동 결정)
* **개선**: 이 구조를 통해 LLM의 평가가 부적절할 경우(예: 점수가 너무 짜거나 후할 때) 에이전트가 스스로 이를 인지하고 재평가를 수행하여, 더 공정하고 정확한 면접을 진행할 수 있게 되었습니다.

---

## 📈 주요 개선 사항 (v1.0 → v2.0)

![인터뷰 점수 분석 시각화](https://github.com/Kim-geun-woo/AI-Interviewer-Agent/blob/main/images/%EC%A0%90%EC%88%98%20%EC%8B%9C%EA%B0%81%ED%99%94.png)

[인터뷰 점수 분석 시각화]

| 구분 | v1.0 (기존 한계) | v2.0 (주요 개선 사항) |
| :--- | :--- | :--- |
| **면접 흐름** | `if-else` 기반의 정해진 순서로만 진행  | **LLM 기반 `decide_next_step`** 도입으로 '심화 질문', '주제 전환', '종료' 등 유연한 흐름 제어  |
| **평가 품질** | 2가지 단순 기준으로 평가 | **5가지 세분화된 기준(가중치 적용)**으로 평가 <br>**`reflect` 노드**를 추가하여 평가 자체를 검증하고 **자가-성찰(Self-Correction)** 수행 |
| **사용성** | Jupyter Notebook 환경에서만 실행 | **Gradio 웹 인터페이스**를 구현하여 실제 사용자(면접자)가 쉽게 접근하고 사용할 수 있도록 개선 |
| **상태 관리** | 변수 누락 등 팀원 간 State 불일치 문제 | Excel 시트를 활용한 **State 실시간 기록 및 공유**로 디버깅 효율 향상) |

---

## 💡 회고 (What I Learned)

* **Prompt Engineering**: LLM이 `analyze_resume`와 `generate_question_strategy`에서 복잡한 요구사항(예: 3가지 카테고리, JSON 형식)을 정확히 따르도록 프롬프트를 구조화하고 개선하는 과정을 통해 프롬프트 엔지니어링의 중요성을 배웠습니다.
* **LangGraph 상태 관리**: LangGraph는 강력하지만 비동기적 흐름과 State 관리가 복잡했습니다. 팀원들과 Excel로 State를 추적하며 디버깅한 경험은 복잡한 에이전트 개발 시 협업의 핵심임을 깨달았습니다.
* **End-to-End 구현**: 단순히 기능(함수)을 개발하는 것을 넘어, 파일 입력(`PyMuPDF`)부터 에이전트 로직(`LangGraph`), 그리고 최종 UI(`Gradio`)까지 연결하는 End-to-End 애플리케이션 구축 경험을 쌓았습니다.
* **에이전트의 성찰**: `reflect` 노드를 구현하며, LLM이 단순히 작업을 수행하는 것을 넘어 '자신의 작업을 검증'하게 만드는 '성찰(Reflection)' 개념이 에이전트의 신뢰도를 높이는 핵심 요소임을 이해했습니다.

---

## 📂 프로젝트 산출물

* `결과보고서(AI_05반_09조).pptx`
* `Step1_AI면접관 Agent v1.0.ipynb` (v1.0 버전)
* `Step2_AI면접관 Agent v2.0.ipynb` (v2.0 버전)
* `Step2_제공파일_AI면접관 Agent_with Gradio.2.0.ipynb` (Gradio 실행 파일)
* `채용공고.docx` (예시 채용공고)
* `Resume_sample.pdf` (예시 이력서)
* `Gradio 면접 결과.txt` (Gradio 결과물을 `.txt` 파일로 옮김)
* `버전 관리 수정 업데이트.xlsx` (State 수정사항 공유)

---

## 🖼️ 발표 자료 (Presentation)


[![발표자료 확인](https://github.com/Kim-geun-woo/AI-Interviewer-Agent/blob/main/images/ppt.png)](https://github.com/Kim-geun-woo/AI-Interviewer-Agent/blob/main/docs/%EA%B2%B0%EA%B3%BC%EB%B3%B4%EA%B3%A0%EC%84%9C(AI_05%EB%B0%98_09%EC%A1%B0).pdf)

> 📌 썸네일을 클릭하면 발표자료를 바로 확인할 수 있습니다.
