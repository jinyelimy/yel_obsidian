---
type: setup
created: 2026-04-20
---
# ⚙️ 셋업 가이드 (이 파일 먼저 읽기)

> 이 파일은 **옵시디언 최초 설정용**입니다. 일상 사용은 [[🏠 Home]]에서 시작하세요.

## 🚀 5분 셋업 체크리스트

### 1. Vault 열기
- Obsidian 다운로드 → `Open folder as vault` → 이 `MyVault` 폴더 선택

### 2. 기본 설정
**Settings → 파일 및 링크 (Files & Links)**
- 새 첨부 파일 위치: `현재 폴더 아래의 하위 폴더` → `_Attachments`
- 새 링크 형식: `가능한 경우 가장 짧은 경로`

**Settings → 코어 플러그인 (Core plugins)** - 모두 켜기
- ✅ 데일리 노트
- ✅ 템플릿
- ✅ 백링크
- ✅ 아웃고잉 링크
- ✅ 태그 패널
- ✅ 그래프 뷰
- ✅ 즐겨찾기

**Settings → 템플릿** 
- 템플릿 폴더 위치: `_Templates`

**Settings → 데일리 노트**
- 새 파일 위치: `06_Daily/{{date:YYYY}}/{{date:MM}}`
- 날짜 형식: `YYYY-MM-DD`
- 템플릿 파일 위치: `_Templates/Daily`

### 3. 커뮤니티 플러그인 설치
**Settings → 커뮤니티 플러그인 → 탐색** 에서 다음 순서로:

| 순서 | 플러그인 | 용도 | 필수도 |
|------|----------|------|--------|
| 1 | **Templater** | 템플릿에 날짜 등 자동 입력 | 🔴 필수 |
| 2 | **Dataview** | MOC의 자동 목록/표 | 🔴 필수 |
| 3 | **Calendar** | 우측에 달력 뷰 | 🟡 추천 |
| 4 | **Tasks** | `- [ ]` 통합 관리 | 🟡 추천 |
| 5 | **QuickAdd** | 단축키로 빠른 노트 | 🟢 선택 |
| 6 | **Periodic Notes** | 주간/월간 노트 | 🟢 선택 |
| 7 | **Git** | 자동 백업 | 🟡 추천 |

**Templater 설치 후** Settings → Templater
- 템플릿 폴더 위치: `_Templates`
- 새 파일 시 템플릿 트리거: 활성화

**Dataview 설치 후** Settings → Dataview
- JavaScript 쿼리 활성화: 켜기

### 4. 핵심 노트 즐겨찾기
- [[🏠 Home]] 열기 → 별 아이콘 클릭 (즐겨찾기 추가)
- 좌측 사이드바 "즐겨찾기" 패널에서 항상 접근 가능

### 5. 첫 데일리 노트 만들기
- `Ctrl+P` → "오늘의 일일 노트" 검색 → 클릭
- `06_Daily/2026/04/2026-04-20.md` 가 자동 생성됨

---

## 📁 폴더 구조 한눈에

```
MyVault/
├── 🏠 Home.md                      ← 매일 시작점
├── README.md                        ← 셋업 가이드 (이 파일)
│
├── 00_Inbox/                        ← 임시 메모
├── 01_Projects/  → [[Projects]]    ← 끝이 있는 작업
│   ├── Side/  → [[Side]]
│   └── Work/  → [[Work]]
├── 02_Areas/  → [[Areas]]          ← 꾸준한 영역
├── 03_Resources/  → [[Resources]]  ← 참고자료
├── 04_Archive/  → [[Archive]]      ← 보관소
├── 05_Meetings/  → [[Meetings]]    ← 회의록
├── 06_Daily/  → [[Daily]]          ← 데일리 노트
├── _Templates/                      ← 템플릿 4종
└── _Attachments/                    ← 이미지/첨부 자동 저장
```

각 폴더는 **동명의 MOC 노트**가 있어서, 폴더에 들어가면 항상 입구가 됩니다.

> 💡 폴더와 같은 이름의 파일이 트리에 두 번 보이는 건 정상입니다. 위는 폴더, 아래는 그 폴더의 MOC 노트.

---

## 🎯 매일 사용 흐름

| 시점 | 행동 | 단축키 |
|------|------|--------|
| 아침 | [[🏠 Home]] 열기 → 오늘 데일리 노트 | `Ctrl+P` → "오늘" |
| 회의 전 | 회의록 새 노트 + Meeting 템플릿 | `Ctrl+N` → 템플릿 |
| 작업 중 | 본문에 `[[프로젝트명]]` 링크 걸기 | `[[` 입력 |
| 퇴근 전 | 데일리 노트 마감, 미완료 정리 | - |
| 주말 | `00_Inbox` 비우기, 회고 | - |

## ⌨️ 필수 단축키 5개

| 단축키 | 기능 |
|--------|------|
| `Ctrl/Cmd + O` | 노트 빠른 열기 (가장 많이 씀) |
| `Ctrl/Cmd + P` | 명령 팔레트 (모든 기능 검색) |
| `Ctrl/Cmd + N` | 새 노트 |
| `[[` | 노트 링크 자동완성 |
| `Ctrl/Cmd + 클릭` | 링크 새 탭 열기 |

## 🔄 백업 (꼭 설정하세요)

1순위 추천: **Git 플러그인 + GitHub Private Repo (무료)**
- Settings → 커뮤니티 플러그인 → "Obsidian Git" 설치
- GitHub 가입 → Private Repo 생성 → 연동

대안:
- iCloud / Google Drive / OneDrive에 Vault 폴더 두기
- Obsidian Sync (공식 유료, $5/월)

---

이제 [[🏠 Home]]으로 가서 시작하세요! 🚀
