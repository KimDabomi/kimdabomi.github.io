---
layout: single
title: "[프로그래밍 규칙] 6주차 실행하기"
categories:
  - study
sidebar:
  nav: "sidebar-category"
---

## 1. 실패 케이스를 제거할 건...! 없었다! 대신에 미연에 생길 수 있는 오류 방지하기
**변경 전**
- `user_contentmarks`에 `contentInfo`를 추가할 때, 기존 데이터들은 아직 `contentInfo`가 없기 때문에 `not null`로 DB에 추가
- `UserContentMark` Entity에 `nullable` 설정 X

**변경 후**
- 구현 도중 Dto내에서 `nullable`설정을 빼먹음..!
- 이럴거면 다 `nullable = false` 를 설정하자! 아래처럼 변경
``` java
@Column(name = "content_info", nullable = false)
private String contentInfo;
```
``` sql
ALTER TABLE user_contentmarks
    ALTER COLUMN content_info SET NOT NULL;
```