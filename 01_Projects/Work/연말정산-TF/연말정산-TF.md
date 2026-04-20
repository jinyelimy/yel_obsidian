---
status: active
type: project
tags: [project, work, tf/yearend]
created: 2026-04-20
---
# 연말정산-TF

> 회사 연말정산 TF 업무의 입구 노트입니다. 실제 업무 이력과 일정은 전용 관리 문서에 기록합니다.

## 🎯 목표
- 연말정산 관련 업무를 접수일, 시작일, 종료일, 처리 내용 기준으로 추적한다.
- 교육, 회의, 인수인계 일정을 날짜 기준으로 관리한다.
- 처리 결과, 산출물, 후속 액션을 남겨 다음 담당자가 맥락을 바로 이해할 수 있게 한다.

## 📌 현재 상태
- 업무 접수 및 처리 이력은 [[업무관리]]에서 관리한다.
- 교육 / 회의 / 인수인계 일정은 [[일정관리]]에서 관리한다.
- 기록 방식과 파일 분류 기준은 [[운영가이드]]를 따른다.

## 🔗 빠른 링크
- [[업무관리]] - 받은 업무, 처리 과정, 시작/종료 날짜, 산출물 관리
- [[일정관리]] - 교육, 회의, 인수인계 날짜와 후속 액션 관리
- [[운영가이드]] - 작성 규칙, 상태 기준, 폴더 사용법
- [[회의록/README|회의록]] - 회의별 상세 기록
- [[인수인계/README|인수인계]] - 인수인계 항목별 상세 기록
- [[자료/README|자료]] - 참고자료, 전달자료, 캡처 정리
- [[완료/README|완료]] - 종료된 업무와 오래된 정리 문서 보관

## 🔄 사이드와 연결
- [[Lingg-Tax]] - 개인 사이드 프로젝트로, 회사 작업 인사이트 활용 가능

## 🧭 오늘 확인할 것
- [ ] [[업무관리]]에서 `진행중`, `검토중`, `보류` 업무 확인
- [ ] [[일정관리]]에서 오늘과 이번 주 교육 / 회의 / 인수인계 확인
- [ ] 회의나 인수인계 후 생긴 후속 액션을 체크박스로 남겼는지 확인
- [ ] 완료된 업무에 종료 날짜와 처리 내용이 들어갔는지 확인

## 📊 최근 수정 문서
```dataview
TABLE type, status, file.mtime as "수정"
FROM "01_Projects/Work/연말정산-TF"
WHERE file.path != this.file.path
SORT file.mtime DESC
LIMIT 20
```

## 📅 최근 회의
```dataview
LIST date
FROM "05_Meetings" OR "01_Projects/Work/연말정산-TF/회의록"
WHERE contains(file.outlinks, [[연말정산-TF]]) OR contains(tags, "tf/yearend")
SORT date DESC
LIMIT 10
```

## 🎯 미완료 액션 아이템
```dataview
TASK
FROM "01_Projects/Work/연말정산-TF" OR "05_Meetings"
WHERE !completed
GROUP BY file.link
```

## 📝 메모
- 
