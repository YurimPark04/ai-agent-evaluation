# AI Agent Evaluation

문서 기반 AI 에이전트 평가를 위한 데이터 준비 및 실험 프로젝트.
사내 정책과 업무 가이드에서 질문·기준 답변·근거 문맥으로 구성된 Golden Dataset을 구축하고, 에이전트의 응답 품질을 평가하기 위함이다.

## 현재 구현 범위

| 단계 | 내용 | 상태 |
| --- | --- | --- |
| 문서 준비 | 사내 정책 및 업무 가이드 7종, PDF·Markdown·TXT | 포함 |
| PDF → Markdown | pyzerox 기반 변환 | 코드 주석 처리 |
| Markdown → TXT → Document | 내용 복사 및 UTF-8 텍스트 로딩 | 구현 |
| 모델 설정 | RAGAS용 생성 LLM 및 임베딩 모델 초기화 | 코드 작성 |
| 데이터셋 조회 | 기존 CSV의 12개 샘플 확인 | 구현 |
| 생성 및 평가 | 테스트셋 생성 호출, 에이전트 응답 평가, 지표 계산 | 미구현 |

현재 작업은 노트북 중심으로 진행하며, 데이터 전처리와 모델 설정, 기존 데이터셋 조회까지 포함함.
Markdown → TXT 변환은 문법 제거 없이 동일한 내용을 복사하는 방식.

## 프로젝트 구조

```text
ai-agent-evaluation/
├── documents/
│   ├── documents_with_english_titles/          # PDF 원본
│   ├── documents_with_english_titles_markdown/ # Markdown 문서
│   └── documents_with_english_titles_txt/      # TXT 문서
├── notebook/                                  # 전처리 및 데이터셋 실험
├── ragas_testset.csv                           # 평가 데이터셋
├── main.py                                    # 기본 메시지 출력
├── pyproject.toml                             # 실행 환경 및 의존성
├── uv.lock                                    # 의존성 버전 고정
└── README.md
```

## 주요 기술

- 실행 환경: Python 3.13, uv, Jupyter / ipykernel
- 문서 처리: pyzerox, LangChain `DirectoryLoader`·`TextLoader`
- 평가 데이터 준비: RAGAS, LiteLLM, OpenAI, pandas
- 모델 설정: `anthropic/claude-sonnet-4-5`, `text-embedding-3-large`

## 실행 방법

프로젝트 루트에서 의존성 설치:

```shell
uv sync --locked
```

1. Jupyter 지원 편집기에서 `notebook/`의 노트북 열기.
2. 프로젝트 `.venv`의 Python을 커널로 선택.
3. `Path.cwd()`가 `notebook/`인지 확인 후 필요한 셀을 순서대로 실행.

모델 사용 시 `ANTHROPIC_API_KEY`, `OPENAI_API_KEY` 환경 변수 설정 필요.
텍스트 변환·로딩과 CSV 조회는 API 키 없이 실행 가능.

상대 경로는 `notebook/` 기준이며, 프로젝트 루트에서 실행할 경우 `../` 제거 필요.
PDF 변환 코드를 사용할 경우 주석 해제 및 입출력 경로를 `documents/` 하위로 수정 필요.

## 평가 데이터셋

`ragas_testset.csv`는 질문(`user_input`), 근거 문맥(`reference_contexts`), 기준 답변(`reference`)과 페르소나·질문 유형·합성기 메타데이터로 구성됨.
동일한 질문과 기준 답변을 사용해 에이전트 응답을 비교하기 위함이다.
