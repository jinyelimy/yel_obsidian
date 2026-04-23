---
type: comparison
project: "[[HR 하네스(고도화)]]"
source_repo: "https://github.com/qoxmfaktmxj/ehr-harness-plugin"
tags: [comparison, harness, ehr4, ehr5]
created: 2026-04-23
---
# EHR4 vs EHR5 차이 비교

## 이 문서의 목적
원본 플러그인의 `profiles/ehr4`와 `profiles/ehr5`는 폴더 구조는 거의 같지만, 내용은 꽤 다르다.

신입 개발자 입장에서는 이 차이를 이렇게 이해하면 된다.

- 껍데기 구조는 같다.
- 하지만 문서 규칙, 코드 예시, 리뷰 기준, 저장 패턴, 권한 주입 방식은 다르다.
- 그래서 "둘 중 하나만 잘 이해하면 나머지도 비슷하겠지"라고 보면 위험하다.

## 먼저 결론
원본 레포 기준으로 `ehr4`와 `ehr5`는 **파일 개수와 폴더 뼈대는 거의 동일**하지만, 실제 내용은 기술 스택 차이 때문에 별도 제품처럼 봐야 한다.

특히 차이가 큰 파일군:
- `skeleton/AGENTS.md.skel`
- `agents/release-reviewer.md`
- `skills/domain-knowledge/SKILL.md`
- `skills/screen-builder/SKILL.md.skel`
- `skills/procedure-tracer/SKILL.md.skel`
- `skills/codebase-navigator/SKILL.md.skel`

차이가 작거나 거의 없는 축:
- 폴더 구조 자체
- 공통 역할 분리 방식
- `design-guide` 같은 일부 보조 스킬 골격

## 비교 기준
실제 비교한 파일:
- `profiles/ehr4/*`
- `profiles/ehr5/*`
- 대표 diff:
  - `skeleton/AGENTS.md.skel`
  - `agents/release-reviewer.md`
  - `skills/domain-knowledge/SKILL.md`
  - `skills/screen-builder/SKILL.md.skel`

## 1. 공통점
먼저 같은 점부터 보는 것이 좋다.

### 폴더 구조는 같다

```text
profiles/ehrX/
├── skeleton/
├── agents/
├── skills/
└── reference/
```

즉, 두 프로필 모두 아래 철학은 공유한다.

- `skeleton`은 최종 문서 템플릿
- `agents`는 역할 분리
- `skills`는 반복 작업 가이드
- `reference`는 고정 레퍼런스

### 역할 분리 방식도 같다
- 둘 다 `screen-builder`, `procedure-tracer`, `release-reviewer`, `db-impact-reviewer`를 둔다.
- 둘 다 `domain-knowledge`, `screen-builder`, `db-query`, `impact-analyzer` 같은 스킬 축을 가진다.
- 둘 다 최종적으로 같은 종류의 결과물(`AGENTS.md`, `CLAUDE.md`, `.claude/skills/*`, `.claude/agents/*`)을 만든다.

즉, 차이는 "하네스 구조"보다 "하네스 내용"에 있다.

## 2. 가장 큰 차이: 기술 스택

| 항목 | EHR4 | EHR5 |
|------|------|------|
| 빌드 | Ant | Maven |
| 프레임워크 인상 | Spring MVC + Anyframe | Spring Boot + MyBatis |
| 매퍼 파일명 | `*-mapping-query.xml` | `*-sql-query.xml` |
| 매퍼 문법 | Anyframe Query + Velocity | MyBatis XML |
| 바인딩 스타일 | `:param`, `$query`, `$rm.xxx` | `#{param}`, `${query}`, `#{rm.xxx}` |
| 화면 계층 | JSP + IBSheet7 | JSP + IBSheet7 |
| 프로시저 호출 | CALLABLE/ProDao 중심 | CALLABLE 유지, MyBatis 방식 |

신입 기준 해석:

- EHR4는 "Velocity와 Anyframe이 섞인 레거시 스타일"
- EHR5는 "Spring Boot + MyBatis로 조금 더 정리된 스타일"

즉, 연말정산 도메인을 넣을 때도 "어느 프로필을 베이스로 삼느냐"에 따라 스킬 예시와 리뷰 기준이 크게 달라진다.

## 3. `AGENTS.md.skel` 차이
이 파일은 프로젝트 전체 규칙의 뼈대다.

### EHR4 쪽 특징
- 제목부터 `EHR4 프로젝트 규칙`처럼 버전 정체성이 강하다.
- 경로 규칙이 `src/com/hr`, `WebContent/WEB-INF/jsp` 중심이다.
- Anyframe/Velocity 문법이 전제다.
- `:ssnEnterCd`, `$query`, `$rm.x` 같은 표현이 많이 나온다.
- `B1~B6` 경계 검증 중심이다.

### EHR5 쪽 특징
- 제목이 좀 더 일반화되어 있고, 기술 스택 표가 더 명확하다.
- 경로 규칙이 `src/main/java`, `src/main/resources/mapper`, `src/main/webapp` 중심이다.
- MyBatis `#{}`와 `${query}`가 전제다.
- `resultType="cMap"`, `*-sql-query.xml`, 감사 컬럼, NULL 센티넬 같은 규칙이 강조된다.
- `B1~B10`까지 검토 범위가 확장된다.

### 신입 관점 핵심
둘 다 `AGENTS.md`를 만들지만, 실제 내용은 전혀 같은 문서를 약간 수정한 수준이 아니다.

특히 아래 차이는 반드시 기억해두는 편이 좋다.

- EHR4: `Velocity/Anyframe` 규칙서
- EHR5: `MyBatis/Spring Boot` 규칙서

## 4. `release-reviewer.md` 차이
이 파일이 실무적으로 가장 중요하다.

### EHR4
- `B1~B6` 경계면 검증
- Anyframe/Velocity 기반 경계 확인
- `$query`, `:bind`, `ProDao`, `ExecPrc.do` 같은 포인트 중심

### EHR5
- `B1~B10` 경계면 검증
- EHR4 기준에 더해 아래가 추가된다.
  - `*-sql-query.xml` 파일명 규칙
  - `resultType="cMap"`
  - MERGE NULL 센티넬
  - 감사 컬럼 `CHKDATE`, `CHKID`

### 왜 이 차이가 중요하나
연말정산 하네스를 고도화할 때, 나중에 "리뷰 에이전트가 무엇을 위험으로 볼 것인가"를 정의해야 한다.

그 기준은 단순히 도메인 지식만으로는 안 되고, 이런 `release-reviewer` 관점까지 같이 맞춰야 한다.

즉:
- EHR4 기준 리뷰는 레거시 문법 일치 여부를 더 많이 본다.
- EHR5 기준 리뷰는 MyBatis XML 정합성과 저장 패턴까지 더 넓게 본다.

## 5. `domain-knowledge/SKILL.md` 차이
이 파일은 두 프로필의 "세계관" 차이를 가장 크게 보여준다.

### EHR4 특징
- 분량이 더 길고, Velocity/Anyframe 설명 비중이 크다.
- `:ssnEnterCd`, `$query`, `$velocityHasNext`, `$rm.field` 같은 문법 설명이 많다.
- EHR4 고유의 50건 분할 규칙 같은 설명이 들어간다.
- Anyframe Query Service 문법 레퍼런스 자체가 별도 지식으로 존재한다.

### EHR5 특징
- 분량이 더 짧고, MyBatis/Spring Boot 기준으로 정리돼 있다.
- `#{}`와 `${query}` 패턴이 중심이다.
- `cMap`, 1000건 chunk, MyBatis CALLABLE, `ListUtil.getChunkedList()` 같은 현대화된 패턴이 들어간다.
- WTM처럼 Java 로직이 풍부한 특수 모듈 설명도 등장한다.

### 신입 관점 핵심
이 차이는 단순 문법 차이가 아니다.

- EHR4 도메인 지식은 "프레임워크 제약을 이해하는 문서"
- EHR5 도메인 지식은 "규칙 + 패턴 + 운영 안전장치"에 더 가깝다

즉, 연말정산 도메인 지식을 어디에 얹을지 고민할 때도,
- EHR4 베이스면 문법 함정 설명이 많이 필요하고
- EHR5 베이스면 규칙과 패턴 정리 쪽이 더 중요할 수 있다

## 6. `screen-builder` 차이
이 파일은 실제 화면 생성 방식의 차이를 잘 드러낸다.

### EHR4 화면 생성 느낌
- `src/com/hr/...`
- `WebContent/WEB-INF/jsp/...`
- `*-mapping-query.xml`
- Velocity 기반 MERGE/DELETE
- `$velocityHasNext`, `$rm.col`, `:ssnEnterCd`

### EHR5 화면 생성 느낌
- `src/main/java/...`
- `src/main/resources/mapper/...`
- `*-sql-query.xml`
- MyBatis `<select>`, `<update>`, `<foreach>`
- `#{rm.col}`, `${query}`, `resultType="cMap"`

### 실무적으로 무슨 뜻인가
연말정산 화면/기능 구현 스킬을 추가하려면, 예시 코드가 어느 프로필 문법을 따르는지가 중요하다.

예를 들어:
- EHR4에 EHR5식 MyBatis 예시를 넣으면 혼란이 커진다.
- EHR5에 Velocity 예시를 넣으면 바로 잘못된 베이스를 학습하게 된다.

즉, `screen-builder`는 도메인 지식만큼이나 프로필 종속성이 강하다.

## 7. `procedure-tracer`와 `db-query` 차이
이 둘은 겉으로는 비슷해 보여도, 실제 추적 방식이 다르다.

### EHR4 쪽 추적
- 매퍼, 서비스, 프로시저 추적 시 Anyframe/Velocity 패턴이 전제다.
- `CALL P_XXX`와 매핑 파일, `Dao/ProDao` 연결이 핵심이다.

### EHR5 쪽 추적
- MyBatis XML, `statementType="CALLABLE"`, `resultType="cMap"` 등 XML 정합성까지 같이 본다.
- DB query와 mapper XML 추적 방식이 더 구조화되어 있다.

### 신입 관점 핵심
프로시저 추적 스킬은 "둘 다 프로시저를 본다"는 공통점만 있고, 실제 추적 문법은 꽤 다르다.

## 8. `reference/CODE_MAP.md`, `DB_MAP.md` 차이
둘 다 같은 이름을 쓰지만, 내용은 각 프로필의 기본 구조를 반영한다.

### 의미
- 같은 `CODE_MAP.md`라도 EHR4는 레거시 경로/패턴 중심
- EHR5는 Maven/MyBatis 구조 중심

즉, 연말정산용 reference를 만들 때도 이름만 맞추는 게 아니라, 프로필 구조에 맞게 내용을 다시 잡아야 한다.

## 9. 무엇이 거의 안 바뀌는가
모든 게 다 다르다고 보면 또 부담이 커진다. 실제로는 안 바뀌는 축도 있다.

### 거의 유지되는 것
- 폴더 구조
- 자산 분리 원칙
- 에이전트/스킬/레퍼런스/스켈레톤이라는 분업 구조
- 하네스 생성 전체 파이프라인
- `shared` 공통층 철학

즉, "운영 구조"는 비슷하고 "도메인 내용과 기술 문법"이 다르다고 이해하면 된다.

## 10. 연말정산 하네스에 주는 시사점

### 경우 1. 실제 소스가 EHR5에 가깝다
- EHR5를 베이스로 잡는 편이 자연스럽다.
- 특히 `release-reviewer`, `screen-builder`, `db-query`에서 재사용 가치가 크다.

### 경우 2. 실제 소스가 EHR4에 가깝다
- EHR4를 베이스로 잡는 편이 안전하다.
- Velocity, Anyframe, 경로 규칙을 무시하면 스킬 전체가 어긋난다.

### 경우 3. 섞여 있다
- `shared`는 그대로 유지
- 프로필은 EHR4/EHR5 중 더 가까운 쪽을 기본으로 잡되
- 연말정산 전용 규칙을 별도 `reference/skill/agent`로 빼는 방식이 좋다

## 11. 신입 개발자가 여기서 바로 기억하면 좋은 것

### 1. 구조가 같다고 내용도 같은 게 아니다
파일 이름이 같아도 실제 규칙은 많이 다르다.

### 2. EHR4/EHR5 차이는 "문법 차이"를 넘는다
화면 생성법, 리뷰 기준, 저장 패턴, 권한 주입 방식까지 달라진다.

### 3. 연말정산 고도화는 프로필 선택부터 중요하다
잘못된 프로필을 베이스로 잡으면 이후 `screen-builder`, `release-reviewer`, `domain-knowledge`가 다 어긋난다.

## 12. 현재 우리에게 추천하는 읽기 포인트
연말정산 하네스를 본격 설계하기 전에 아래 순서로 보면 좋다.

1. `skeleton/AGENTS.md.skel`
규칙 차이 이해

2. `agents/release-reviewer.md`
검토 기준 차이 이해

3. `skills/domain-knowledge/SKILL.md`
도메인 설명 방식 차이 이해

4. `skills/screen-builder/SKILL.md.skel`
실제 구현 예시 차이 이해

## 결론
원본 플러그인의 `ehr4`와 `ehr5`는 "같은 구조를 가진 두 프로필"이지, "한쪽의 단순 업그레이드판"으로 보기 어렵다.

연말정산 하네스를 만들 때 중요한 건 이것이다.

- 공통 구조는 공유할 수 있다.
- 하지만 규칙, 예시, 리뷰 기준, 저장 패턴은 프로필에 맞게 다시 잡아야 한다.

그래서 다음 단계에서는 [[03_Gap분석/연말정산-도메인-gap-분석|연말정산 도메인 gap 분석]]에서
"우리 연말정산은 EHR4에 더 가까운가, EHR5에 더 가까운가, 아니면 별도 프로필이 필요한가"
를 판단하는 것이 자연스럽다.
