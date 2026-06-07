# DevWear

> 개발자를 위한 커뮤니티 기반 티셔츠 커머스 플랫폼

DevWear는 개발 문화와 오픈소스 생태계에서 영감을 받은 티셔츠를 등록하고, 탐색하고, 구매할 수 있는 개발자 중심 쇼핑몰 프로젝트입니다.

단순한 상품 판매를 넘어 좋아요, 싫어요, 찜하기, 댓글과 같은 커뮤니티 기능을 제공하여 사용자가 상품에 대한 의견을 공유하고 자신만의 컬렉션을 구성할 수 있도록 설계되었습니다.

---

## 주요 기능

### 상품 관리

* 개발자 티셔츠 등록
* 상품 목록 조회
* 상품 상세 정보 조회
* 상품별 이미지 및 설명 관리
* 상품 가격과 판매 상태 관리
* 판매 가능한 상품과 품절 상품 구분

### 상품 구매

* 상품 구매 요청
* 구매 수량 선택
* 주문 정보 생성
* 주문 상태 관리
* 구매 완료 내역 확인

### 장바구니

* 상품 장바구니 추가
* 장바구니 수량 변경
* 장바구니 상품 삭제
* 선택 상품 주문
* 장바구니 총금액 계산

### 상품 상세 페이지

* 상품명, 가격 및 설명 표시
* 상품 이미지 제공
* 사용자 반응 정보 제공
* 댓글 목록 제공
* 구매 및 장바구니 기능 연결

### 사용자 반응

* 상품 좋아요
* 상품 싫어요
* 관심 상품 찜하기
* 사용자별 반응 상태 관리
* 상품별 반응 수 집계

### 댓글

* 상품 댓글 작성
* 댓글 목록 조회
* 댓글 수정 및 삭제
* 작성자와 작성 시간 표시

---

## 제공 상품

현재 프로젝트에서 관리하는 기본 상품은 다음과 같습니다.

| 상품         | 설명                             |
| ---------- | ------------------------------ |
| GitHub 티셔츠 | GitHub와 오픈소스 문화를 주제로 한 개발자 티셔츠 |
| Python 티셔츠 | Python 개발자를 위한 프로그래밍 테마 티셔츠    |

---

## 프로젝트 구조

```text
devwear/
├── README.md
├── docs/
│   ├── feature-list.md
│   ├── tshirt-list.md
│   ├── detail-page.md
│   ├── cart.md
│   ├── like.md
│   └── comment.md
├── src/
│   ├── products/
│   ├── cart/
│   ├── orders/
│   ├── reactions/
│   ├── comments/
│   └── users/
└── tests/
```

각 도메인은 독립적인 모듈로 분리하는 것을 기본 원칙으로 합니다.

* `products`: 상품 등록, 조회 및 상세 정보
* `cart`: 장바구니 상품과 수량 관리
* `orders`: 주문 생성과 구매 처리
* `reactions`: 좋아요, 싫어요 및 찜하기
* `comments`: 상품 댓글 관리
* `users`: 사용자와 인증 정보 관리

---

## 핵심 도메인 모델

### Product

```text
Product
├── id
├── name
├── description
├── price
├── imageUrl
├── stock
├── status
├── createdAt
└── updatedAt
```

### CartItem

```text
CartItem
├── id
├── userId
├── productId
├── quantity
├── unitPrice
└── createdAt
```

### Order

```text
Order
├── id
├── userId
├── totalPrice
├── status
├── orderedAt
└── orderItems
```

### Reaction

```text
Reaction
├── id
├── userId
├── productId
├── type
└── createdAt
```

`type`은 다음 상태 중 하나를 가집니다.

```text
LIKE
DISLIKE
BOOKMARK
```

### Comment

```text
Comment
├── id
├── userId
├── productId
├── content
├── createdAt
└── updatedAt
```

---

## 서비스 아키텍처

DevWear는 기능 확장과 유지보수를 고려하여 계층형 아키텍처를 권장합니다.

```text
Client
  │
  ▼
Controller / API Layer
  │
  ▼
Application Service
  │
  ▼
Domain Layer
  │
  ▼
Repository
  │
  ▼
Database
```

각 계층은 다음 책임을 가집니다.

| 계층                  | 책임                        |
| ------------------- | ------------------------- |
| API Layer           | 요청 검증, 응답 변환, 상태 코드 관리    |
| Application Service | 유스케이스 실행과 트랜잭션 관리         |
| Domain Layer        | 상품, 주문, 장바구니 및 반응 비즈니스 규칙 |
| Repository          | 데이터 저장과 조회                |
| Infrastructure      | 데이터베이스, 캐시, 외부 시스템 연동     |

---

## 주요 비즈니스 규칙

### 장바구니

* 동일한 상품을 다시 추가하면 기존 수량을 증가시킵니다.
* 상품 재고보다 많은 수량을 추가할 수 없습니다.
* 판매 중지 또는 품절 상품은 장바구니에 추가할 수 없습니다.
* 상품 가격이 변경되면 주문 시점의 가격을 기준으로 최종 금액을 다시 계산합니다.

### 주문

* 주문 생성 전 상품 재고를 다시 검증합니다.
* 주문이 확정되면 해당 상품의 재고를 차감합니다.
* 결제 또는 주문 처리에 실패하면 재고 변경을 롤백합니다.
* 주문 완료 이후에는 주문 당시의 상품 가격을 보존합니다.

### 사용자 반응

* 사용자는 하나의 상품에 좋아요와 싫어요를 동시에 등록할 수 없습니다.
* 동일한 반응을 다시 선택하면 해당 반응을 취소할 수 있습니다.
* 찜하기는 좋아요 및 싫어요 상태와 독립적으로 관리합니다.

### 댓글

* 로그인한 사용자만 댓글을 작성할 수 있습니다.
* 댓글 작성자만 자신의 댓글을 수정하거나 삭제할 수 있습니다.
* 삭제된 상품에는 새로운 댓글을 작성할 수 없습니다.

---

## API 설계 예시

### 상품

| Method   | Endpoint                    | 설명       |
| -------- | --------------------------- | -------- |
| `GET`    | `/api/products`             | 상품 목록 조회 |
| `GET`    | `/api/products/{productId}` | 상품 상세 조회 |
| `POST`   | `/api/products`             | 상품 등록    |
| `PATCH`  | `/api/products/{productId}` | 상품 수정    |
| `DELETE` | `/api/products/{productId}` | 상품 삭제    |

### 장바구니

| Method   | Endpoint                   | 설명         |
| -------- | -------------------------- | ---------- |
| `GET`    | `/api/cart`                | 장바구니 조회    |
| `POST`   | `/api/cart/items`          | 장바구니 상품 추가 |
| `PATCH`  | `/api/cart/items/{itemId}` | 상품 수량 변경   |
| `DELETE` | `/api/cart/items/{itemId}` | 장바구니 상품 삭제 |

### 주문

| Method  | Endpoint                       | 설명           |
| ------- | ------------------------------ | ------------ |
| `POST`  | `/api/orders`                  | 주문 생성        |
| `GET`   | `/api/orders`                  | 사용자 주문 목록 조회 |
| `GET`   | `/api/orders/{orderId}`        | 주문 상세 조회     |
| `PATCH` | `/api/orders/{orderId}/cancel` | 주문 취소        |

### 반응 및 찜하기

| Method | Endpoint                             | 설명           |
| ------ | ------------------------------------ | ------------ |
| `PUT`  | `/api/products/{productId}/like`     | 좋아요 등록 또는 취소 |
| `PUT`  | `/api/products/{productId}/dislike`  | 싫어요 등록 또는 취소 |
| `PUT`  | `/api/products/{productId}/bookmark` | 찜하기 등록 또는 취소 |

### 댓글

| Method   | Endpoint                             | 설명       |
| -------- | ------------------------------------ | -------- |
| `GET`    | `/api/products/{productId}/comments` | 댓글 목록 조회 |
| `POST`   | `/api/products/{productId}/comments` | 댓글 작성    |
| `PATCH`  | `/api/comments/{commentId}`          | 댓글 수정    |
| `DELETE` | `/api/comments/{commentId}`          | 댓글 삭제    |

---

## 응답 형식 예시

### 상품 상세 조회

```json
{
  "id": 1,
  "name": "GitHub T-Shirt",
  "description": "A developer T-shirt inspired by open-source culture.",
  "price": 29000,
  "stock": 50,
  "status": "ON_SALE",
  "reactionSummary": {
    "likeCount": 124,
    "dislikeCount": 3,
    "bookmarked": true
  },
  "comments": [
    {
      "id": 15,
      "author": "octocat",
      "content": "원단이 편하고 디자인도 좋습니다.",
      "createdAt": "2026-06-07T10:30:00"
    }
  ]
}
```

### 공통 오류 응답

```json
{
  "code": "PRODUCT_NOT_FOUND",
  "message": "요청한 상품을 찾을 수 없습니다.",
  "timestamp": "2026-06-07T10:30:00"
}
```

---

## 시작하기

### 1. 저장소 복제

```bash
git clone https://github.com/your-organization/devwear.git
cd devwear
```

### 2. 환경 변수 설정

프로젝트 루트에 환경 변수 파일을 생성합니다.

```env
APP_ENV=development
APP_PORT=8080

DATABASE_HOST=localhost
DATABASE_PORT=5432
DATABASE_NAME=devwear
DATABASE_USERNAME=devwear
DATABASE_PASSWORD=devwear

JWT_SECRET=change-this-secret
```

실제 운영 환경에서는 비밀번호와 인증 키를 저장소에 커밋하지 마십시오.

### 3. 의존성 설치

프로젝트에서 사용하는 패키지 관리자에 따라 의존성을 설치합니다.

```bash
# npm
npm install

# yarn
yarn install

# pnpm
pnpm install
```

### 4. 애플리케이션 실행

```bash
npm run dev
```

### 5. 테스트 실행

```bash
npm test
```

---

## 테스트 전략

DevWear는 기능의 정확성과 회귀 오류 방지를 위해 다음 테스트 계층을 권장합니다.

### 단위 테스트

* 상품 가격 계산
* 장바구니 수량 변경
* 재고 검증
* 좋아요와 싫어요 상태 전환
* 주문 금액 계산
* 댓글 수정 권한 검증

### 통합 테스트

* 상품 등록부터 상세 조회까지의 흐름
* 장바구니 추가 후 주문 생성 흐름
* 주문 실패 시 재고 롤백
* 사용자 반응 집계
* 댓글 생성, 수정 및 삭제

### E2E 테스트

* 사용자의 상품 탐색
* 상품 상세 페이지 진입
* 상품 장바구니 추가
* 주문 생성
* 좋아요 및 찜하기
* 댓글 작성

---

## 보안 고려사항

* 사용자 인증 토큰 검증
* 관리자 전용 상품 등록 API 보호
* 객체 소유권 기반 댓글 수정 및 삭제 제한
* 요청 파라미터와 본문 유효성 검사
* SQL Injection 및 XSS 방지
* 비밀번호 단방향 해시 처리
* API 요청 횟수 제한
* 민감한 환경 변수의 외부 노출 방지
* 운영 환경의 HTTPS 적용

---

## 성능 및 확장성

서비스 규모가 증가할 경우 다음 최적화를 적용할 수 있습니다.

* 상품 목록 페이지네이션
* 인기 상품 및 상품 상세 정보 캐싱
* 이미지 CDN 적용
* 비동기 주문 이벤트 처리
* 재고 변경에 대한 동시성 제어
* 읽기 전용 데이터베이스 분리
* 좋아요 및 댓글 집계 결과 캐싱
* 검색 엔진을 활용한 상품 검색 고도화

특히 주문과 재고 처리는 동시에 여러 사용자가 같은 상품을 구매할 수 있으므로 낙관적 잠금, 비관적 잠금 또는 원자적 재고 감소 연산을 사용해야 합니다.

---

## 향후 개발 계획

* 사용자 회원가입 및 로그인
* OAuth 기반 소셜 로그인
* 상품 카테고리와 태그
* 상품 검색 및 정렬
* 결제 시스템 연동
* 배송지 관리
* 주문 취소 및 환불
* 상품 리뷰와 별점
* 관리자 대시보드
* 판매 통계와 인기 상품 분석
* 상품 추천 시스템
* 다국어 지원
* 실시간 재고 알림
* CI/CD 자동화
* 컨테이너 기반 배포

---

## 기여 방법

DevWear는 오픈소스 프로젝트로서 다양한 형태의 기여를 환영합니다.

1. 저장소를 Fork합니다.
2. 새로운 브랜치를 생성합니다.

```bash
git checkout -b feature/add-product-search
```

3. 변경 사항을 커밋합니다.

```bash
git commit -m "feat: add product search"
```

4. 브랜치를 원격 저장소에 Push합니다.

```bash
git push origin feature/add-product-search
```

5. Pull Request를 생성합니다.

기능을 추가할 때는 관련 테스트와 문서를 함께 작성해 주십시오.

---

## 커밋 메시지 규칙

```text
feat: 새로운 기능 추가
fix: 버그 수정
docs: 문서 변경
refactor: 코드 구조 개선
test: 테스트 추가 또는 수정
chore: 빌드 및 설정 변경
```

예시:

```text
feat: add bookmark feature
fix: prevent ordering out-of-stock products
docs: update cart API documentation
```

---

## 개발팀

| 이름  | 역할        |
| --- | --------- |
| 고양이 | Developer |
| 문어  | Developer |
| 너구리 | Developer |

---

## 라이선스

이 프로젝트는 오픈소스 프로젝트입니다.

실제 배포 전 저장소에 적용할 라이선스를 선택하고 `LICENSE` 파일을 추가해야 합니다. 일반적인 오픈소스 프로젝트에는 MIT License 또는 Apache License 2.0을 사용할 수 있습니다.

---

## 프로젝트 목표

DevWear의 목표는 단순한 쇼핑몰 구현을 넘어 다음과 같은 실무 개발 요소를 학습하고 검증할 수 있는 커머스 플랫폼을 구축하는 것입니다.

* 도메인 중심의 모듈 설계
* RESTful API 설계
* 인증과 권한 관리
* 트랜잭션과 재고 동시성 처리
* 사용자 상호작용 데이터 관리
* 테스트 가능한 애플리케이션 구조
* 확장 가능한 오픈소스 협업 환경

개발자를 위한 상품과 개발자가 참여하는 커뮤니티를 하나의 플랫폼에서 제공합니다.
