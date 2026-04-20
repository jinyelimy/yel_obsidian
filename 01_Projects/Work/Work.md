---
type: moc
tags: [moc, work]
---
# 🏢 Work - 회사 업무

> **`01_Projects/Work/` 폴더의 입구 노트입니다.** 회사에서 진행하는 모든 프로젝트/TF가 여기에.

← [[Projects]] | [[🏠 Home]]

## 🚀 진행 중인 TF/프로젝트

### AI 관련
- [[AI-TF]]
  - [[HR하네스-업그레이드]]

### 연말정산 관련
- [[연말정산-TF]]
  - [[Javis-디자인]]
  - [[패치관리페이지]]

---

## 📅 이번 주 회의 (자동)

```dataview
LIST date
FROM "05_Meetings"
WHERE date >= date(today) - dur(7 days)
SORT date DESC
```

## 🎯 미완료 액션 아이템 (자동)

```dataview
TASK
FROM "05_Meetings" OR "01_Projects/Work"
WHERE !completed
GROUP BY file.link
LIMIT 20
```

---

## 💡 회사 업무 정리 팁

- **회의록은 무조건 `05_Meetings/`에**, 본문에 관련 프로젝트 링크 (`[[AI-TF]]` 등) 걸기
- **결정사항은 별도 노트로** 분리 (예: `Javis-디자인/결정사항.md`)
- **개인 학습 = `02_Areas/자기개발/`로**, 업무와 분리
