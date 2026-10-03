# Bible LLM · Eden

성경 구절을 별과 은하로 탐색하고, 질문에 맞는 구절과 인물 페르소나를 바탕으로 대화하는 웹 애플리케이션입니다. React 화면과 Django API를 연결하며 LLM 답변은 SSE로 전달합니다.

SKN31 4차 3팀의 Eden 프로젝트에서 파생한 공개용 소스 스냅샷입니다. 개인 단독 개발물로 표시하지 않으며, 기존 팀 프로젝트의 기여 관계를 유지합니다. 이 저장소에는 운영 데이터와 기존 Git 이력이 포함되지 않습니다.

## 주요 기능

- Canvas 기반 은하·별 탐색과 구절 상세 화면
- 질문 주제에 따른 구절 추천 및 인물 페르소나 대화
- JWT 기반 회원 인증, 대화 세션 및 이력 관리
- LangChain/OpenAI 연동과 SSE 스트리밍
- PostgreSQL/pgvector 검색 및 선택적 Neo4j 그래프 확장
- API 없이 화면을 살펴볼 수 있는 mock 모드

## 기술 구성

| 영역 | 구성 |
| --- | --- |
| 프론트엔드 | React 19, TypeScript, Vite 7, React Router, Canvas 2D |
| 백엔드 | Python 3.12, Django 5.2 계열, DRF, Simple JWT |
| AI·검색 | LangChain, OpenAI, pgvector, Neo4j |
| 테스트·실행 | Vitest, Testing Library, pytest, Docker Compose, Nginx |

## 빠른 시작: 화면 시연

Node.js 22.12 이상인 22 계열 환경에서 실행합니다. 아래 명령은 PowerShell 기준입니다.

```powershell
cd frontend
Copy-Item .env.example .env.local
npm ci
npm run dev
```

브라우저에서 http://localhost:5173 에 접속합니다. 기본값은 `VITE_API_BASE_URL=mock`이며 회원·상담 기능은 시연용 구현입니다. 실제 LLM 호출이나 서버 저장을 의미하지 않습니다.

## Django API 연결

Python 3.12와 Docker Compose가 필요합니다. 프로젝트 루트에서 시작합니다.

```powershell
docker compose up -d db
cd server
py -3.12 -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
Copy-Item .env.example .env
```

`server/.env`에서 `SECRET_KEY`를 새 값으로 변경하고 실제 상담을 사용할 경우 `OPENAI_API_KEY`를 입력합니다. 기본 DB URL은 로컬 Docker DB에 연결됩니다. Neo4j 설정은 선택 사항입니다.

```powershell
.\.venv\Scripts\python.exe manage.py migrate
.\.venv\Scripts\python.exe manage.py runserver
```

마이그레이션으로 큐레이션 샘플이 적재됩니다. 프론트엔드의 `.env.local`을 `VITE_API_BASE_URL=http://localhost:8000`으로 변경한 뒤 Vite를 재시작합니다.

- API 문서: http://localhost:8000/api/v1/docs/
- 상태 확인: http://localhost:8000/healthz/
- 빈 `VITE_API_BASE_URL`은 mock이 아닌 같은 도메인의 API를 뜻합니다.

루트에서 `docker compose up --build`로 전체 로컬 구성을 시작할 수도 있습니다. 이 구성에는 LLM 키가 전달되지 않으므로 실제 상담 생성에는 별도의 API 환경변수 구성이 필요합니다. 로컬용 비밀번호와 DEBUG 설정을 운영에 그대로 사용하지 마세요.

## 데이터 범위

성경 전문 파일은 포함하지 않습니다. 큐레이션의 번역문 인용 필드는 공개용 안내 문구로 대체했으며, 자체 서술된 요약·스토리·묵상과 구절 참조는 유지했습니다. 따라서 화면의 인용 영역은 원본 서비스와 다릅니다.

전문 검색·임베딩 적재를 사용하려면 재배포·사용 권한을 확인한 데이터를 직접 `data/bible_structured.json`에 준비해야 합니다. 형식은 [데이터 안내](data/README.md)를 참고하세요. 이 파일은 Git 추적에서 제외됩니다. 기본 공개본만으로 전문 벡터 검색 품질을 재현할 수는 없습니다.

## 구조

```text
frontend/              UI, 은하 렌더링, mock/API 저장소, 테스트
server/config/         Django 설정과 API 라우팅
server/users/          사용자와 JWT 인증
server/chat/           대화 세션, 문맥 구성, 스트리밍
server/scripture/      구절, 검색, 그래프, 데이터 적재
server/llm_core/       LLM 체인, 프롬프트, 인물 매칭
server/tests/          백엔드 테스트
data/                  외부 데이터 준비 안내
docker-compose.yml     로컬 DB·API·웹 구성
```

## 검증 명령

```powershell
cd frontend
npm run typecheck
npm test
npm run build
cd ../server
.\.venv\Scripts\python.exe -m pytest
```

위 명령은 재현용 안내입니다. 공개본 준비 환경에는 Python과 설치된 의존성이 없어 앱 실행·빌드·테스트는 수행하지 않았습니다. 전문 데이터 의존 테스트는 데이터가 없으면 건너뜁니다.

## 공개 범위와 기여

환경변수 실값, DB 덤프, 팀원 사진, 내부 작업·운영 문서, 기존 Git 이력은 제외했습니다. 상세 변경 범위는 [공개본 검토 기록](PUBLIC_RELEASE_NOTES.md)에 정리했습니다.

원본 저장소에서 명시적 LICENSE 파일은 확인하지 못했습니다. 공동 기여 코드에 대한 재라이선스를 임의로 추가하지 않았습니다. 공개 저장소에 올리는 것과 자유로운 재사용 권한을 부여하는 것은 별개이며, 팀 기여물의 공개·라이선스 합의는 별도로 확인해야 합니다.
