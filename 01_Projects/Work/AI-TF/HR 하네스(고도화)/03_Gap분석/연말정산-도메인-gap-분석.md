---
type: gap-analysis
project: "[[HR 하네스(고도화)]]"
related: "[[Lingg-Tax]]"
tags: [gap-analysis, harness, yearend]
created: 2026-04-23
---
# 연말정산 도메인 gap 분석

## 왜 이 문서를 다시 써야 하나
이전까지는 "연말정산이 EHR4/EHR5 중 어디에 더 가까운지 아직 모른다"는 수준의 메모였다.

하지만 실제 소스 경로를 보면 이제는 그렇게 추상적으로 둘 수 없다.

이번 분석 대상:

- `C:\Users\jinyelimy\isu-hr\EHR_HR50\src\main\webapp\yjungsan`

그리고 판단 보강을 위해 함께 본 경로:

- `C:\Users\jinyelimy\isu-hr\EHR_HR50\pom.xml`
- `C:\Users\jinyelimy\isu-hr\EHR_HR50\AGENTS.md`
- `C:\Users\jinyelimy\isu-hr\EHR_HR50\src\main\java\com\hr\cpn\yjungsan`
- `C:\Users\jinyelimy\isu-hr\EHR_HR50\src\main\resources\mapper\com\hr\cpn\yjungsan`
- `C:\Users\jinyelimy\isu-hr\EHR_HR50\src\main\webapp\WEB-INF\jsp\cpn\yjungsan`
- `C:\Users\jinyelimy\isu-hr\EHR_HR50\src\main\java\yjungsan\util`

## 한 줄 결론
**상위 프로젝트는 분명 EHR5이지만, 사용자가 지정한 `src/main/webapp/yjungsan` 연말정산 소스 자체는 구조적으로 EHR4/레거시 스타일에 훨씬 더 가깝다.**

다만 더 정확하게 말하면 이렇다.

**`EHR5 호스트 프로젝트 안에, EHR4에 가까운 연말정산 레거시 섬이 크게 남아 있고, 일부 영역만 EHR5 방식으로 병행 현대화되고 있는 하이브리드 구조`**

## 판단 결과

### 최종 판정
- **연말정산 소스 경로 자체는 EHR4에 더 가까움**
- 신뢰도: **높음**

### 단서
- 프로젝트 전체 기술 스택은 EHR5
- 그러나 `src/main/webapp/yjungsan` 내부 아키텍처는 `JSP -> Rst.jsp -> xml_query -> DBConn/프로시저` 중심
- 이것은 원본 하네스의 EHR5 프로필보다 EHR4/레거시 패턴과 더 유사하다

## 1. 상위 프로젝트가 EHR5라는 근거
먼저 호스트 프로젝트 자체는 EHR5라고 보는 것이 맞다.

### 근거 1. `pom.xml`
`C:\Users\jinyelimy\isu-hr\EHR_HR50\pom.xml`

확인된 핵심:
- `spring-boot-starter-parent 2.7.18`
- `mybatis-spring-boot-starter 2.3.1`
- packaging: `war`
- name: `EHR5.0`

즉, 빌드/프레임워크 기준으로는 전형적인 EHR5 쪽이다.

### 근거 2. 프로젝트 루트 경로 구조
실제 존재 경로:
- `src/main/java` 있음
- `src/main/resources/mapper` 있음
- `src/main/webapp/WEB-INF/jsp` 있음

실제 없음:
- `src/com/hr`
- `WebContent/WEB-INF/jsp`

즉, 프로젝트 뼈대는 EHR4보다 EHR5 쪽이다.

### 근거 3. 프로젝트 자체 `AGENTS.md`
`C:\Users\jinyelimy\isu-hr\EHR_HR50\AGENTS.md`

여기에도 명시적으로 아래 내용이 있다.
- Spring Boot 2.7.18
- MyBatis 3
- `*-sql-query.xml`
- `#{param}`, `${query}`
- `src/main/java`, `src/main/resources/mapper`, `src/main/webapp`

즉, 호스트 하네스도 EHR5 기반으로 이미 정리되어 있다.

## 1-1. 상세분석 문서 기준 실행 축
상세분석 문서 기준으로 현재 `yjungsan`은 아래 3개 실행 축이 동시에 공존한다.

1. Spring MVC + MyBatis 운영 화면 축
2. 연도별 레거시 JSP + `xml_query` + `DBConn` 축
3. `taxApiBaseUrl` 기반 신형 REST 기준관리 축

이 한 문장만으로도 하네스 설계 방향이 거의 정리된다.

- 순수 EHR5 하네스만으로는 2번 축을 설명하기 어렵다.
- 순수 EHR4 하네스만으로는 1번과 3번 축을 설명하기 어렵다.
- 즉, 연말정산은 하이브리드 전용 규칙을 별도로 가져가야 한다.

## 2. 그런데 `src/main/webapp/yjungsan`은 왜 EHR4에 더 가까운가
핵심은 **사용자가 지정한 연말정산 경로가 호스트 프로젝트 전체 규칙과 다른 별도 구조를 강하게 보인다는 점**이다.

## 2-1. 연말정산 경로의 규모와 형태
`src/main/webapp/yjungsan` 기준 실측:

- 파일 수: **4861**
- 디렉토리 수: **1618**
- 연도 폴더 수: **16**
- 연도 범위: `y_2011` ~ `y_2026`
- `Rst.jsp` 파일 수: **1162**
- `xml_query` 디렉토리 수: **33**
- `jsp_jungsan` 디렉토리 수: **32**

이 숫자만 봐도 느낌이 온다.

- 일반적인 EHR5 화면 몇 개가 아니라,
- 연도별로 누적된 대형 레거시 서브시스템이다.

## 2-2. UI 호출 방식이 EHR5보다 훨씬 레거시 쪽이다
대표 예시:

`C:\Users\jinyelimy\isu-hr\EHR_HR50\src\main\webapp\yjungsan\y_2022\jsp_jungsan\yeaData\yeaData.jsp`

실제 패턴:
- `commonSheet.DoSearch("<%=jspPath%>/yeaData/yeaDataRst.jsp?cmd=selectCommonSheetList", ...)`
- `ajaxCall("<%=jspPath%>/yeaData/yeaDataRst.jsp?cmd=selectYeaDataDefaultInfo", ...)`
- `ajaxCall("<%=jspPath%>/yeaData/yeaDataRst.jsp?cmd=prcYeaClose", ...)`
- `ajaxCall("<%=jspPath%>/yeaData/yeaDataRst.jsp?cmd=prcYeaCalc", ...)`

즉, 이 경로의 핵심 화면은 보통 EHR5식

`JSP -> Controller(.do) -> Service -> Mapper`

보다,

`JSP -> yeaDataRst.jsp?cmd=...`

방식으로 붙는다.

이건 EHR5보다 EHR4 또는 그 이전 레거시 서브모듈과 훨씬 닮아 있다.

## 2-3. `Rst.jsp`가 사실상 백엔드 역할을 한다
대표 예시:

`C:\Users\jinyelimy\isu-hr\EHR_HR50\src\main\webapp\yjungsan\y_2022\jsp_jungsan\yeaData\yeaDataRst.jsp`

이 파일을 보면:
- `yjungsan.util.*` import
- `yjungsan.exception.*` import
- `XmlQueryParser.getQueryMap(locPath)`
- `DBConn.executeQueryMap(...)`
- `DBConn.executeQueryList(...)`
- `DBConn.executeUpdate(...)`
- `DBConn.executeProcedure(...)`
- JSP 내부에 메서드 선언
- 수동 transaction 관리 (`conn.setAutoCommit(false)`, `commit`, `rollback`)

즉, 일반적인 EHR5의 Controller/Service/Mapper 분리가 아니라,
**JSP가 직접 서비스/DAO/프로시저 호출 역할까지 같이 수행**하고 있다.

이건 매우 강한 레거시 신호다.

## 2-4. SQL도 MyBatis가 아니라 `xml_query` 스타일이 강하다
대표 예시:

`C:\Users\jinyelimy\isu-hr\EHR_HR50\src\main\webapp\yjungsan\y_2022\xml_query\yeaData\yeaData.xml`

확인된 패턴:
- 루트가 `<root>`
- 내부가 `<query id="...">`
- 바인딩 문법이 `#ssnEnterCd#`, `#searchWorkYy#`
- 권한 주입이 `$query$`
- 연말정산 전용 프로시저/함수 호출이 XML 안에 직접 등장

이건 EHR5의
- `<!DOCTYPE mapper ...>`
- `<mapper namespace="...">`
- `#{param}`
- `${query}`

와는 분명히 다르다.

즉, **연말정산 핵심 SQL 자산은 여전히 레거시 xml_query 엔진을 중심으로 돈다.**

## 2-5. 전용 유틸 레이어도 따로 있다
예:

`C:\Users\jinyelimy\isu-hr\EHR_HR50\src\main\java\yjungsan\util\XmlQueryParser.java`

이 파일을 보면:
- `yjungsan/.../xml_query/...xml` 패턴만 허용
- SAX parser로 query XML을 직접 읽음

즉, 연말정산은 MyBatis가 아닌 **자기만의 query parser 유틸 계층**까지 따로 갖고 있다.

이 정도면 단순 UI 레거시가 아니라, **서브시스템 전체가 별도 레거시 아키텍처**라고 보는 편이 맞다.

## 3. 그럼 완전히 EHR4라고 보면 되나?
아니다. 여기서 중요한 반전이 있다.

연말정산은 **완전한 EHR4 단일 구조**가 아니라, 일부는 분명히 EHR5식으로 현대화되고 있다.

## 3-1. 현대화된 `cpn/yjungsan` Java 계층이 실제로 존재한다
실측:

- `src/main/java/com/hr/cpn/yjungsan` 파일 수: **17**
- `src/main/resources/mapper/com/hr/cpn/yjungsan` 파일 수: **8**
- `src/main/webapp/WEB-INF/jsp/cpn/yjungsan` 파일 수: **11**

대표 예시:
- `com/hr/cpn/yjungsan/yeaData/YeaDataController.java`
- `com/hr/cpn/yjungsan/yeaData/YeaDataService.java`
- `mapper/com/hr/cpn/yjungsan/yeaData/YeaData-sql-query.xml`

즉, 연말정산 영역에도 분명히 EHR5식 자산이 있다.

## 3-2. 현대화된 예시 파일도 실제로 EHR5 스타일이다

### `YeaDataController.java`
- `@Controller`
- `@RequestMapping("/YeaData.do", ...)`
- `@Autowired`
- `ModelAndView("jsonView")`

### `YeaDataService.java`
- `Dao dao`
- `dao.getMap("getYeaDataDefaultInfo", paramMap)`

### `YeaData-sql-query.xml`
- MyBatis `<!DOCTYPE mapper>`
- `<mapper namespace="cpn.yjungsan.yeaData">`
- `resultType="cMap"`
- `#{ssnEnterCd}` 문법

즉, **같은 연말정산 도메인 안에 EHR5식 재작성 흔적이 이미 존재한다.**

## 3-3. WEB-INF 쪽 연말정산 JSP도 존재한다
예:
- `WEB-INF/jsp/cpn/yjungsan/yearEndCalcMgr/yearEndCalcMgr.jsp`
- `WEB-INF/jsp/cpn/yjungsan/yearEndItemMgr/yearEndItemMgr.jsp`

이 파일들은:
- `/YearEndItemMgr.do`
- `taxApiBaseUrl/yearend/base/calc/forms/...`
- IBSheet + 현대화된 버튼/레이아웃

같은 EHR5식 흐름을 보여준다.

즉, 연말정산이 모두 `src/main/webapp/yjungsan`에만 머무는 것은 아니다.

## 3-4. REST 기준관리 축도 별도로 존재한다
상세분석 문서 기준으로 아래 화면은 이미 별도 API 의존도가 높다.

- `YearEndItemMgrNew`
- `YearEndCalcMgr`

특징:
- repo 내부 SQL보다 `taxApiBaseUrl/...` 호출 비중이 큼
- UI는 repo 안에 있지만, 기준 데이터 처리는 외부 API 서비스가 담당

즉, 연말정산은
- 레거시 JSP 엔진
- Spring/MyBatis 운영 화면
- 외부 REST 기준관리

를 동시에 이해해야 하는 도메인이다.

## 4. 그래서 최종 판단은 이렇게 정리해야 맞다

### 잘못된 단순화
- "연말정산도 그냥 EHR5다"
- "연말정산은 완전히 EHR4다"

둘 다 반쪽짜리 설명이다.

### 더 정확한 설명
1. **프로젝트 호스트 구조는 EHR5**
2. **사용자가 지정한 핵심 연말정산 경로 `src/main/webapp/yjungsan`은 EHR4/레거시 스타일에 훨씬 가깝다**
3. **하지만 동시에 일부 기능은 `com/hr/cpn/yjungsan` + MyBatis 방식으로 현대화되고 있다**

즉, 연말정산은 **EHR5 프로젝트 안의 하이브리드/과도기 도메인**이다.

## 4-1. 도메인 관점에서는 화면군이 아니라 플랫폼에 가깝다
상세분석 문서의 표현을 빌리면, `yjungsan`은 아래 5개 레이어를 가진 연말정산 플랫폼에 가깝다.

1. 기준코드/항목 정의 레이어
2. 대상자/상태 레이어
3. 원천자료 레이어
4. 계산결과/상세계산 레이어
5. 오류검증/PDF 반영 레이어

즉, 하네스도 단순 화면 탐색 가이드가 아니라
- 기준코드
- 대상자 상태
- 원천자료
- 계산 결과
- 검증/마감

을 나눠 설명할 수 있어야 한다.

## 5. 하네스 관점에서 발생하는 핵심 gap

## 5-0. DB 중심 도메인 설명 gap
상세분석 문서를 보면 연말정산 핵심 비즈니스는 Java보다 DB에 훨씬 더 강하게 집중돼 있다.

특히 2026 귀속 기준 중심 객체:

- `PKG_CPN_YEA_2026`
- `PKG_CPN_YEA_2026_CODE`
- `PKG_CPN_YEA_2026_DISK`
- `PKG_CPN_YEA_2026_EMP`
- `PKG_CPN_YEA_2026_ERRCHK`
- `PKG_CPN_YEA_2026_SYNC`

그리고 단독 프로시저:
- `P_CPN_YEAREND_EMP`
- `P_CPN_YEAREND_MONPAY_2026`
- `P_CPN_YEA_CLOSE`
- `P_CPN_YEA_RESULT_CONFIRM`
- `P_CPN_YEA_PDF_ERRCHK_2026`
- `P_CPN_YEA_PDF_ERRUPD_2026`

즉, 화면에서 끝나는 도메인이 아니라 "결국 DB 패키지 체인을 읽어야 이해되는 도메인"이다.

원본 하네스 gap:
- 현재 문서 구조만으로는 연말정산 패키지 책임 분해가 부족하다.
- procedure tracer가 "프로시저 이름 찾기"를 넘어 "체인 책임"까지 설명하도록 강화돼야 한다.

## 5-1. 프로필 gap
원본 하네스는 `EHR4` 또는 `EHR5` 중 하나를 기준 프로필로 잡게 되어 있다.

하지만 연말정산은:
- 호스트 규칙은 EHR5
- 핵심 실무 화면/쿼리 경로는 레거시 yjungsan

이라서 **순수 EHR5 프로필만으로는 부족하고, 순수 EHR4 프로필로도 설명이 안 된다.**

### 의미
- 기준 프로필은 EHR5로 잡되,
- 연말정산 전용 레거시 서브프로필 또는 오버레이가 필요하다.

## 5-1-1. 화면 유형 분류 gap
상세분석 문서 기준으로 운영 화면도 모두 같은 부류가 아니다.

예:
- `BefComLst`: Spring/MyBatis 운영 조회 화면
- `BefComMgr`: 운영 화면이지만 마감 상태/기타소득 상세/세액 판정이 깊게 결합
- `BefYearEtcMgr`: 조회는 Spring, 저장은 레거시 `Rst.jsp`
- `YearIncomeMgr`: 메인 조회는 Spring, 팝업 상세는 레거시 연동
- `YearEndItemMgrNew`, `YearEndCalcMgr`: REST API 이관형 화면

즉, 연말정산 전용 하네스는 최소한 아래 4개 화면군을 구분해야 한다.

1. 순수 레거시 JSP군
2. Spring/MyBatis 운영화면군
3. Spring + 레거시 저장 혼합군
4. 외부 REST 기준관리군

## 5-2. 코드 탐색 gap
일반 EHR5 탐색만 하면:
- `src/main/java/com/hr/...`
- `src/main/resources/mapper/...`
- `WEB-INF/jsp/...`

만 보게 된다.

하지만 연말정산은 같이 봐야 한다.
- `src/main/webapp/yjungsan/y_20xx/jsp_jungsan/...`
- `src/main/webapp/yjungsan/y_20xx/xml_query/...`
- `src/main/java/yjungsan/util/...`
- `src/main/java/com/hr/cpn/yjungsan/...`

즉, 하네스의 code navigation 규칙이 이중 경로를 이해해야 한다.

추가로 탐색 기준에 넣어야 할 축:
- `src/main/resources/static/yjungsan`
- `docs/records/` 아래 분석 기록
- 연도별 `y_20xx` 폴더 사이의 동일 기능 비교

즉, 단순 경로 탐색이 아니라 "같은 기능의 연도별 진화"까지 염두에 둔 navigator가 필요하다.

## 5-3. SQL 분석 gap
원본 EHR5 하네스는 MyBatis `*-sql-query.xml` 중심 설명이 강하다.

하지만 연말정산은 추가로 이해해야 한다.
- `<root><query id=...>` 구조
- `#param#` 바인딩
- `$query$` 권한 주입
- `XmlQueryParser` 기반 로딩

즉, 연말정산 전용 `legacy query parser` 해설이 필요하다.

또한 상세분석 문서를 보면 SQL만 보는 것으로도 부족하다.

같이 봐야 하는 핵심 테이블군:

### 기준/정의
- `TCPN801`
- `TCPN803`

### 대상자/상태
- `TCPN811`
- `TCPN884`

### 원천/입력
- `TCPN813`
- `TCPN815`
- `TCPN817`
- `TCPN818`
- `TCPN821`
- `TCPN823`
- `TCPN825`
- `TCPN827`
- `TCPN828`
- `TCPN829`
- `TCPN830`
- `TCPN839`
- `TCPN887`

### 계산결과/검증/PDF
- `TCPN841`
- `TCPN843`
- `TCPN849`
- `TCPN851`
- `TCPN855`

즉, reference 문서에는 최소한 "연말정산 핵심 테이블 분류표"가 별도로 필요하다.

## 5-4. 백엔드 실행 흐름 gap
EHR5 기준 설명만 따르면 보통

`JSP -> Controller -> Service -> Mapper`

를 먼저 찾게 된다.

하지만 연말정산은 자주 이렇게 간다.

`JSP -> Rst.jsp -> XmlQueryParser -> DBConn -> xml_query / executeProcedure`

즉, `Rst.jsp`를 백엔드 엔트리포인트로 보는 별도 규칙이 필요하다.

그리고 Spring/MyBatis 운영 화면의 경우는 또 다른 흐름을 가진다.

`JSP -> /Xxx.do?cmd=... -> Controller -> Service -> Dao -> MyBatis mapper -> Oracle`

REST 기준관리 화면은 다시 이렇게 간다.

`JSP -> taxApiBaseUrl -> 외부 API`

즉, 하네스는 **3종 실행 흐름 판별 규칙**을 가져야 한다.

## 5-5. 연도 버전 gap
`y_2011` ~ `y_2026`까지 연도별 폴더가 공존한다.

즉, 같은 기능이라도:
- 어느 연도를 기준으로 볼지
- 최신 연도 기준으로 볼지
- 특정 연도 패치 차이를 따로 볼지

를 먼저 정해야 한다.

원본 하네스에는 이런 **연도 버전 축**이 거의 없다.

상세분석 문서 기준 대형 연도 폴더:
- `y_2025`: 442
- `y_2026`: 442
- `y_2024`: 393
- `y_2023`: 348
- `y_2022`: 323
- `y_2021`: 324

즉, 최신 연도만 보면 되는 게 아니라
- 최신 귀속 연도
- 질문이 많이 나오는 특정 연도
- 과거 레거시와 비교가 필요한 연도

를 분리해서 봐야 한다.

## 5-6. 프로시저 추적 gap
연말정산 `Rst.jsp` 안에서는 직접 프로시저를 호출한다.

예:
- `P_CPN_YEA_CLOSE`
- `P_CPN_YEA_CLOSE_CANCEL`
- `PKG_CPN_YEA_${year}_EMP.P_MAIN`

즉, 연말정산 전용 procedure tracer는
- Java 서비스
- xml_query
- JSP action
- 직접 procedure call

을 모두 연결해 봐야 한다.

여기에 더해 패키지 책임까지 설명해야 한다.

상세분석 문서 기준:
- `PKG_CPN_YEA_2026_CODE`: 기준코드/항목 정의 생성
- `PKG_CPN_YEA_2026_SYNC`: 원천자료를 `TCPN843`로 동기화
- `PKG_CPN_YEA_2026`: 실제 세액 계산 본체
- `PKG_CPN_YEA_2026_EMP`: 대상자 재생성/재계산
- `PKG_CPN_YEA_2026_ERRCHK`: 오류검증 결과를 `TCPN849`에 적재
- `P_CPN_YEA_CLOSE`: 마감 오케스트레이터

즉, procedure tracer는 이름 찾기보다 **역할 요약 + 앞뒤 객체 연결**이 더 중요하다.

## 5-7. 리뷰 기준 gap
순수 EHR5 reviewer 기준만 적용하면:
- MyBatis XML 정합성
- controller/service/mapper 경계

쪽으로 쏠린다.

하지만 연말정산은 같이 봐야 한다.
- `Rst.jsp` 내부 transaction 처리
- `xml_query` 안의 구형 바인딩
- scriptlet/session 처리
- 연도별 폴더 분기
- 직접 프로시저 호출

즉, 연말정산 전용 reviewer 규칙이 별도로 필요하다.

추가로 상세분석 문서가 드러낸 운영 리스크도 reviewer 기준에 들어가야 한다.

### 꼭 넣어야 할 리뷰 항목
- `P_CPN_YEA_CLOSE` 호출 전후 상태 컬럼 영향
- `TCPN811.INPUT_CLOSE_YN`, `APPRV_YN`, `FINAL_CLOSE_YN` 연동
- `TCPN843` 상세결과 훼손 가능성
- `TCPN849` 오류검증결과 누락 가능성
- PDF 반영 체인 영향

### PDF 리스크
상세분석 문서 기준:
- `P_CPN_YEA_PDF_ERRCHK_2026`: `INVALID`
- `P_CPN_YEA_PDF_ERRUPD_2026`: `INVALID`

즉, PDF 업로드/반영 쪽은 일반 화면 수정보다 훨씬 더 보수적으로 다뤄야 한다.

## 6. 우리 하네스 설계에 주는 의미

## 6-1. 기준 프로필 선택
현재 판단으로는 **기준 프로필은 EHR5**가 맞다.

이유:
- 호스트 프로젝트가 EHR5
- 실제 하네스 루트도 EHR5 규칙을 따르고 있음
- 일부 연말정산 자산은 이미 EHR5식으로 존재

하지만 여기서 끝나면 안 된다.

## 6-2. 반드시 별도 오버레이가 필요
연말정산용으로 추가해야 할 것:

- `reference`: `yjungsan` 레거시 구조 설명
- `reference`: 핵심 테이블군 분류표
- `reference`: 2026 핵심 패키지/프로시저 책임표
- `skill`: `Rst.jsp + xml_query` 탐색 방법
- `skill`: 연도 폴더 해석 규칙
- `skill`: 화면 -> 테이블 -> 패키지 -> 상태값 추적 절차
- `agent`: 하이브리드 reviewer
- `agent`: yearend procedure tracer

즉, 설계 방향은

`EHR5 base + yjungsan legacy overlay`

가 가장 자연스럽다.

## 7. 첫 수직 slice는 무엇이 좋은가
이번 분석 결과 기준으로 첫 slice 후보는 아래가 적절하다.

### 추천 1. `y_2022/yeaData`
이유:
- 대표 화면 + 대표 `Rst.jsp` + 대표 `xml_query`를 한 번에 볼 수 있음
- 프로시저 호출과 기본 조회/저장이 같이 있음
- 연말정산 중심 허브 성격이 강함
- 상세분석 문서에서도 `TCPN811`, `TCPN843`, 마감/계산 흐름과 강하게 연결됨

### 추천 2. `y_2022/medHisMgr`
이유:
- 의료비처럼 자주 질문이 나오는 도메인
- 화면/쿼리/테이블 질문으로 이어지기 쉬움

### 추천 3. `yearEndItemMgr`의 현대화 영역
이유:
- EHR5식 자산이 실제로 존재해 비교 지점이 명확함
- "레거시와 현대화가 어떻게 갈라지는지"를 파악하기 좋음

## 8. 지금 시점의 최종 정리

### 판단
- `src/main/webapp/yjungsan`은 **EHR4/레거시 쪽에 더 가깝다**
- 하지만 프로젝트 전체는 **EHR5**
- 따라서 연말정산은 **EHR5 프로젝트 안의 레거시 하이브리드 도메인**

### 도메인 핵심
- 화면 몇 개가 아니라 연말정산 플랫폼에 가까운 구조
- 핵심 비즈니스 로직은 Java보다 DB 패키지 체인에 더 강하게 집중
- 2026 귀속 기준 패키지 세트와 상태 테이블(`TCPN811`, `TCPN841`, `TCPN843`, `TCPN849`) 이해가 중요

### 하네스 전략
- 기준 프로필: **EHR5**
- 연말정산 전용 확장: **레거시 yjungsan 오버레이**

### 왜 이렇게 해야 하나
- 순수 EHR5로만 보면 `yjungsan`의 핵심 실무 경로를 놓친다
- 순수 EHR4로만 보면 호스트 프로젝트와 이미 진행된 현대화 자산을 놓친다

## TODO
- [ ] `yjungsan` 전용 reference 문서에 `JSP -> Rst.jsp -> xml_query -> DBConn -> 프로시저` 흐름 정리
- [ ] `TCPN801/803/811/813/815/817/818/841/843/849/851/855` 핵심 테이블 reference 정리
- [ ] `PKG_CPN_YEA_2026*` 패키지 책임표를 procedure tracer reference로 정리
- [ ] `y_2022/yeaData`를 첫 slice 기준 모듈로 상세 분석 시작
- [ ] 연도별 폴더(`y_2011`~`y_2026`) 중 기준 연도 선정 규칙 만들기
- [ ] `com/hr/cpn/yjungsan` 현대화 영역과 `src/main/webapp/yjungsan` 레거시 영역의 대응표 만들기
- [ ] 연말정산 전용 reviewer 체크리스트 초안 만들기
- [ ] PDF invalid 객체(`P_CPN_YEA_PDF_ERRCHK_2026`, `P_CPN_YEA_PDF_ERRUPD_2026`) 점검 절차 문서화
