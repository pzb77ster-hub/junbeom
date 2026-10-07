# 현지거점 업무관리 대시보드

- 페이지: https://claude.ai/artifact/9qHu5Badqg4r8QQiruvDe3
- 소스: `office/index.html` (수정 후 같은 경로로 재게시하면 URL 유지)

## 데이터 구조 (artifact db)

| 컬렉션 | 필드 |
|---|---|
| `config/office` | name, subtitle, title, description, baseDate(YYYY-MM-DD) |
| `tasks/{id}` | name, kind, status, tone(ok/warn/info), summary, order |
| `actions/{id}` | date, task(과업 id), type(연락/실행/지원/통번역/행사/문서), title, detail, url |
| `milestones/{id}` | date, label, done(선택) |
| `documents/{id}` | date, kind(MOM/MOU/RD/PD/공문/회의록/계약/기타), task, title, url, note |
| `library/{id}` | date, category(발표자료/사진·영상/참고자료/템플릿/통번역본/기타), task, title, url, note |
| `contacts/{id}` | name, org, role, side(베트남 측/한국 측/현지 협력사/기타), task, phone, email, note |
| `reports/{id}` | date, kind(주간/월간/결과/출장보고/기타), period, title, summary, url |

액션·문서·자료·관계자·보고서는 페이지의 각 화면 추가 버튼 또는 Claude에게 요청해 추가합니다.
`[예시]`로 시작하는 액션은 샘플이므로 실제 기록으로 교체하세요.
