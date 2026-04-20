---
type: moc
tags: [moc]
---
# 📁 Projects

> **`01_Projects/` 폴더의 입구 노트입니다.** 여기는 "끝이 있는 작업"만 모입니다.

← [[🏠 Home]]

## 📂 분류

### 🌱 사이드 프로젝트
입구: [[Side]]

- [[연말정산모듈]]

### 🏢 회사 업무
입구: [[Work]]

- [[AI-TF]]
  - [[HR하네스-업그레이드]]
- [[연말정산-TF]]
  - [[Javis-디자인]]
  - [[패치관리페이지]]

---

## 📊 진행 상황 (자동)

### 진행 중
```dataview
TABLE status, file.folder as "위치", file.mtime as "수정"
FROM "01_Projects"
WHERE type = "project" AND status = "active"
SORT file.mtime DESC
```

### 일시 중단
```dataview
LIST
FROM "01_Projects"
WHERE type = "project" AND status = "paused"
```

---

## 💡 새 프로젝트 만들 때

1. 적절한 위치에 폴더 생성: `01_Projects/Side/내프로젝트/`
2. 그 폴더에 동명 MOC 노트 생성: `내프로젝트.md`
3. `_Templates/Project.md` 템플릿 적용
4. **이 Projects 노트에 링크 추가** (위 목록에 `[[내프로젝트]]` 추가)
5. 끝나면 → `04_Archive/`로 이동 + status를 `done`으로 변경
