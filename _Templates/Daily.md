---
type: daily
date: <% tp.date.now("YYYY-MM-DD") %>
---
# <% tp.date.now("YYYY-MM-DD, dddd") %>

## ✅ 오늘 할 일
- [ ] 

## 🔄 작업 로그
- 

## 💡 메모/아이디어
- 

## ➡️ 내일로
- 

## 🔗 오늘의 회의
```dataview
LIST
FROM "05_Meetings"
WHERE date = this.date
```
