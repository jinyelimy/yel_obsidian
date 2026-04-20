---
status: active
type: project
tags: [project, work, tf/ai]
created: 2026-04-20
---
# AI-TF

> 회사 AI Task Force. HR 하네스(harness) 업그레이드를 통해 다양한 모듈에서 활용 용이성 확보.

## 🎯 목표
- HR 하네스를 업그레이드하여 다양한 업무 모듈(특히 [[연말정산모듈]])에서 활용도 향상

## 📌 현재 상태
- 

## 🔗 하위 프로젝트
- [[HR하네스-업그레이드]]

## 📊 진행 중인 작업
```dataview
TABLE status, file.mtime as "수정"
FROM "01_Projects/Work/AI-TF"
WHERE type = "project" AND file.path != this.file.path
SORT file.mtime DESC
```

## 📅 최근 회의
```dataview
LIST date
FROM "05_Meetings"
WHERE contains(file.outlinks, [[AI-TF]]) OR contains(tags, "tf/ai")
SORT date DESC
LIMIT 10
```

## 🎯 미완료 액션 아이템
```dataview
TASK
FROM "05_Meetings" OR "01_Projects/Work/AI-TF"
WHERE !completed
GROUP BY file.link
```

## ❓ Open Questions
- [ ] 

## 📝 메모
- 
