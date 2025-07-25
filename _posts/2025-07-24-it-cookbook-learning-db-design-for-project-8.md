---
layout: single
title: "[IT CookBook, 프로젝트로 배우는 데이터베이스 설계] 8. 논리적 설계 기초"
categories:
  - study
sidebar:
  nav: "sidebar-category"
---

## 1. 논리적 설계 개요

**논리적 설계**란 개념적 설계 단계의 결과물을 DBMS가 이해할 수 있도록 변환하는 과정<br />
데이터 중심의 논리적 스키마 모델링과 처리 중심의 트랜잭션 인터페이스 설계를 포함함

### 논리적 스키마 모델링
- 데이터 중심 디비 설계 관점으로, 개념적 스키마를 논리적 스키마로 변환한 다음, 정규화 과정을 통해 논리적 스키마를 더욱 바람직한형태로 변환하고, 무결성 제약조건을 정의하는 과정

### 트랜잭션 인터페이스 설계
- 처리 중심 디비 설계 관점으로, 개념적 설계 단계에서 작성한 업무 단위의 유형별 트랜잭션 명세서를 기초로 하여, 사용자 그룹별 접근 가능한 데이터 범위와 디비 서버에 접근하는 방법 등을 결정해서 트랜잭션의 전체적인 골격과 인터페이스를 정의하는 과정


## 2. 릴레이션 스키마 변환

**릴레이션 스키마**는 관계 데이터 모델의 기본이 되는 릴레이션을 구성하는 속성들의 집합<br />
릴레이션 이름과 속성 이름들로 표현할 수 있음<br />
개체와 관계를 릴레이션 스키마로 변환하는 방법에는 차이가 있으며, 다중치 속성도 별도의 릴레이션으로 변환해야 함


## 3. 개체 타입의 변환

ER 다이어그램을 릴레이션 스키마로 변환하려면 ER 다이어그램에 포함된 각 개체와 관계를 대응하는 릴레이션 스키마로 변환해야 함

### 단순 속성을 갖는 개체 타입의 변환
- 단순 속성만 포함하는 개체 타입의 경우 개체를 구성하는 모든 속성이 릴레이션의 속성이 되고, 후보키 가운데 업무의 특성에 적합한 하나를 선택해서 기본키로 결정함
- 개념적 스키마 모델링 단계에서 기본키를 결정한 경우라면 그 기본키를 그대로 유지할 수 있음

### 복합 속성을 갖는 개체 타입의 변환
- 복합 속성을 포함하는 개체 타입의 경우 해당 복합 속성이 일반 속성인지, 기본키 속성인지를 구분해서 변환함
- 두 경우 모두 복합 속성을 구성하는 모든 속성이 릴레이션의 속성이 됨

### 약한 개체 타입의 변환
- 약한 개체 타입은 소유 개체 타입 없이는 존재할 수 없으므로 릴레이션 스키마로 변환하는 방법이 일반 개체와는 큰 차이가 있음
- 약한 개체 타입 변환 방법 및 주의사항
  - 릴레이션 스키마 변환 - 소유 개체 타입의 기본키를 약한 개체 타입의 외래키로 포함시킴
  - 기본키 결정 - 약한 개체 타입에 해당하는 릴레이션에서 소유 개체 타입에 해당하는 릴레이션을 참조하는 외래키와, 약한 개체 타입의 부분키를 조합한 속성 집합이 기본키가 됨
  - 약한 개체 타입에 소유 개체 타입의 기본키가 외래키로 포함될 때 식별 관계 타입이 간접적으로 표현됨
  - 따라서 별도로 식별 관계 타입을 릴레이션 스키마로 변환할 필요가 없음


## 4. 관계 타입의 변환

릴레이션 스키마 변환 - 관계에 속하는 개체들의 기본키 속성과 관계를 설명하는 속성을 모두 관계 릴레이션의 속성으로 표현함<br />
이때 관계에 포함된 개체들의 기본키는 모두 관계 릴레이션의 외래키가 됨

### 일대일 관계 타입의 변환 방법
- 기본키 결정 - 관계에 포함된 양쪽 개체의 기본키 가운데 하나가 관계 타입의 기본키가 됨

### 일대다 관계 타입의 변환 방법
- 기본키 결정 - 관계에 포함된 개체 가운데 n측 개체 타입의 기본키가 관계 릴레이션의 기본키가 됨
- 1측 개체의 기본키는 관계 릴레이션에 중복해서 나타나므로, 변환된 관계 릴레이션의 기본키가 될 수 없음

### 다대다 관계 타입의 변환 방법
- 기본키 결정 - 기본적으로 관계에 포함된 양쪽 개체 타입의 기본키를 구성하는 모든 속성으로 구성된 속성 집합이 기본키가 됨
- 양쪽 개체 타입의 기본키들만으로 구성된 속성 집합이 유일성을 만족하지 않으면, 유일성을 만족시킬 수 있도록 다른 속성을 포함시키거나 기본키 역할을 할 설계 속성을 추가할 수 있음

### 순환 관계 타입의 변환
- 일대다 순환 관계 타입의 변환 방법
  - 릴레이션 스키마 변환
    - 관계 릴레이션의 속성으로 관계에 속하는 개체의 기본키를 외래키로 2개 포함시킨 후, 이름이 중복되지 않게 변경하고, 관계를 설명하는 속성도 관계 릴레이션에 포함시킴
    - 관계를 설명하는 속성의 수가 많지 않은 경우, 관계 릴레이션을 별도로 생성하지 않고 개체 릴레이션에 개체의 기본키를 외래키로 포함시켜서 순환 관계를 포함함
  - 기본키 결정 - n측 개체 타입의 기본키가 관계 릴레이션의 기본키가 됨
  - 개체의 기본키를 외래키로 2개 포함시킬 때, 이름이 중복되지 않도록 1개는 이름 변경을 해야함
- 다대다 순환 관계 타입 변환 방법
  - 릴레이션 스키마 변환 - 관계 릴레이션을 별도로 생성한 후, 개체의 기본키를 2개의 외래키로 포함시키고, 관계를 설명하는 속성도 관계 릴레이션에 포함시킴
  - 기본키 설정 - 2개의 외래키로 구성된 속성 집합이 관계 릴레이션의 기본키가 됨
  - 동일한 기본키가 2개 추가되므로 반드시 서로 이름이 달라야 함

### 3진 관계 타입 변환
- 릴레이션 스키마 변환
  - 3진 관계 타입에 대한 릴레이션 생성
  - 관계 타입에 참여하는 모든 개체 타입의 기본키를 관계 릴레이션에 외래키로 포함시킴
  - 관계를 설명하는 속성도 관계 릴레이션에 포함시킴
- 기본키 결정 - 외래키들로 구성된 속성 집합이 관계 릴레이션의 기본키가 되지만, 3진 관계 타입에서는 기본키가 복잡하므로 기본키 역할을 대체할 설계 속성을 추가하는 것이 바람직
- 외래키들로 구성된 복합키가 유일성을 만족하지 않으면, 유일성을 만족시킬 수 있도록 다른 속성을 포함시키거나 기본키 역할을 할 설계 속성을 추가함


## 5. 다중치 속성의 변환

### 다중치 속성의 변환
- 릴레이션 스키마 변환
  - 다중치 속성을 표현하기 위해 별도의 릴레이션을 생성
  - 다중치 속성을 갖는 개체 타입의 기본키를 외래키로 포함시킴
- 기본키 결정 - 외래키와 다중치 속성으로 구성된 속성 집합이 기본키가 됨
- 다중치 속성을 표현하기 위한 릴레이션 이름과 다중치 속성 이름은 반드시 서로 달라야 함


## 6. 슈퍼-서브 타입의 변환

- 슈퍼타입 개체를 기준으로 정제한 경우, 슈퍼타입 개체로 통합되어 하나의 개체 타입으로 변환되므로 일반적인 개체 타입 변환과 동일한 방법으로 변환하면 됨
- 서브타입 개체를 기준으로 정제한 경우, 각각의 서브타입 개체로 통합되어 서브타입 개체 수만큼 별개의 개체 타입으로 변환됨
- 슈퍼타입과 서브타입 개체 모두를 기준으로 정제한 경우, 슈퍼타입 개체의 기본키를 서브타입 개체의 기본키로 추가해야 하므로 상호 참조할 수 있도록 반드시 외래키로 지정해야 함


## 7. 릴레이션 단순화

### 일대일 관계 단순화
- 관계에 전체 참여인 개체 릴레이션에 아래 항목 추가
  - 관계에 포함된 다른 개체의 기본키에 대응하는 속성
  - 관계의 설명 속성
- 기존 관계 릴레이션 삭제

### 일대다 관계 단순화
- 관계 전체 참여인 n측 개체 릴레이션에 아래 항목 추가
  - 관계의 1측에 있는 개체의 기본키에 대응하는 속성
  - 관계의 설명 속성
- 기존 관계 릴레이션 삭제
