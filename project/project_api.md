# API 명세서 v2

### ERD

[f-pay | DrawSQL](https://drawsql.app/teams/sj-13/diagrams/f-pay)


## 공통
API 호출의 공통적인 성공 응답과 실패 응답은 아래와 같습니다.

### 성공 응답 예시
Http Status Code와 payload 객체가 응답으로써 제공됩니다.

```json
{
    "payload" : {
        "key" : "value"
    }
}
```

### 실패 응답 예시
Http Status Code와 오류 메세지가 응답으로써 제공됩니다.

```json
{
    "message" : "오류 메세지"
}
```

## 회원

페이머니를 이용하는 일반 고객에게 회원가입과 로그인 서비스 및 고객 정보 조회 기능을 제공합니다.

- **회원 가입**
    - 이메일, 비밀번호로 회원 가입합니다.

    - Request
        ```
        [POST] /v1/members/sign-up HTTP/1.1
        ```

        ```json
        {
            "email" : "abcd@gmail.com",
            "password" : "abcd123ef!@"
        }
        ```

    - Response
        ```
        HTTP/1.1 201 Created
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : null
        }
        ```

- **로그인** 
    - 회원 이메일, 비밀번호로 로그인 합니다. 로그인 성공 시 Access Token과 Refresh Token을 발급합니다.

    - Request
        ```
        [POST] /v1/members/sign-in HTTP/1.1
        ```

        ```json
        {
            "email" : "abcd@gmail.com",
            "password" : "abcd123ef!@"
        }
        ```

    - Response
        ```
        HTTP/1.1 200 OK
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : {
                "accessToken" : "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c",
                "refreshToken" : "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c"
            }
        }
        ```

- **상세 정보 조회**
    - 로그인한 회원의 상세 정보를 조회 합니다.

    - Request
        ```
        [GET] /v1/members/{memberId} HTTP/1.1
        Authorization: Bearer {ACCESS_TOKEN}
        ```

    - Response
        ```
        HTTP/1.1 200 OK
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : {
                "email" : "test@gmail.com",
                "memberType" : "MEMBER",
                "balance" : 12500
            }
        }
        ```

- **비밀번호 수정**
    - 로그인한 회원의 비밀번호를 수정 합니다.

    - Request
        ```
        [PATCH] /v1/members/{memberId} HTTP/1.1
        Authorization: Bearer {ACCESS_TOKEN}
        ```

        ```json
        {
            "password" : "abcd123ef!@"
        }
        ```

    - Response
        ```
        HTTP/1.1 200 OK
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : null
        }
        ```

- **회원 탈퇴**
    - 로그인한 회원의 회원 정보를 비활성화 하고, 탈퇴 처리를 진행합니다.

    - Request
        ```
        [DELETE] /v1/members/{memberId} HTTP/1.1
        Authorization: Bearer {ACCESS_TOKEN}
        ```

    - Response
        ```
        HTTP/1.1 200 OK
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : null
        }
        ```

## 인증 / 인가
회원의 인증 / 인가 관련 기능을 제공합니다.

- **액세스 토큰 & 리프레쉬 토큰 재발급**
    - 액세스 토큰과 리프레쉬 토큰을 새롭게 생성하여 발급합니다.

    - Request
        ```
        [POST] /v1/auth/refresh HTTP/1.1
        ```

        ```json
        {
            "refreshToken" : "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c"
        }
        ```

    - Response
        ```
        HTTP/1.1 200 OK
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : {
                "accessToken" : "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c",
                "refreshToken" : "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c"
            }
        }
        ```

## 상품

### 일반 회원

회원에게 판매하는 상품에 대한 정보를 제공합니다. 

- **판매 상품 리스트 조회**
    - 판매 중인 상품에 대한 리스트를 제공합니다. 카테고리, 프로모션 여부, 가격등의 조건에 대한 필터링 리스트도 제공합니다.
        - 상품 리스트는 커서 기반의 페이징 리스트로 제공됩니다.   

    - Request
        ```
        [GET] /v1/products?size=&cursor=&category=&name=&price= HTTP/1.1
        ```

    - Response
        ```
        HTTP/1.1 200 OK
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : {
                [
                    {
                        "productId" : 1,
                        "vendorId" : 1,
                        "name" : "테스트 상품",
                        "serialNo" : "TEST_01",
                        "quantity" : 10,
                        "price" : 10000,
                        "category" : "TICKET",
                        "status" : "ON_SALE",
                        "imgUrl" : "https://cdn.example.com/image.jpg"
                    },
                ]
            }
        }
        ```
    
- **판매 상품 상세 조회**
    - 판매 중인 상품에 대한 상세 정보를 제공합니다.

    - Request
        ```
        [GET] /v1/products/{productId} HTTP/1.1
        ```

    - Response
        ```
        HTTP/1.1 200 OK
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : {
                "productId" : 1,
                "vendorId" : 1,
                "name" : "테스트 상품",
                "serialNo" : "TEST_01",
                "quantity" : 10,
                "price" : 10000,
                "category" : "TICKET",
                "status" : "ON_SALE",
                "imgUrl" : "https://cdn.example.com/image.jpg"
            }
        }
        ```

### 가맹점 상품

가맹점에게 상품 등록 / 조회 / 수정 / 삭제에 대한 기능을 제공합니다. 상품의 가격은 일괄 원화(KRW)로 관리합니다. 

- **가맹점별 상품 등록**
    - 가맹점에서 판매할 상품을 등록합니다.

    - Request
        ```
        [POST] /v1/products/{vendorId} HTTP/1.1
        Authorization: Bearer {ACCESS_TOKEN}
        ```

        ```json
        {
            "name" : "테스트 상품",
            "quantity" : 10,
            "price" : 100,
            "category" : "TICKET",
            "imgUrl" : "https://cdn.example.com/image.jpg"
        }
        ```

    - Response
        ```
        HTTP/1.1 201 Created
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : null
        }
        ```

- **가맹점별 상품 리스트 조회**
    - 가맹점별로 등록한 상품의 리스트를 조회합니다.
    - 가맹점별 상품 리스트는 오프셋 기반의 페이징 리스트로 제공됩니다.

    - Request
        ```
        [GET] /v1/products/{vendorId}?offset=&size=&category=&name=&price= HTTP/1.1
        Authorization: Bearer {ACCESS_TOKEN}
        ```

    - Response
        ```
        HTTP/1.1 200 OK
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : {
                [
                    {
                        "productId" : 1,
                        "vendorId" : 1,
                        "name" : "테스트 상품",
                        "serialNo" : "TEST_01",
                        "quantity" : 10,
                        "price" : 10000,
                        "category" : "TICKET",
                        "status" : "ON_SALE",
                        "imgUrl" : "https://cdn.example.com/image.jpg"
                    },
                ]
            },
            "size" : 10,
            "offset" : 2,
            "total" : 25
        }
        ```

- **가맹점별 상품 상세 조회**
    - 가맹점별로 등록한 상품의 상세 정보를 조회합니다.

    - Request
        ```
        [GET] /v1/products/{vendorId}/{productId} HTTP/1.1
        Authorization: Bearer {ACCESS_TOKEN}
        ```

    - Response
        ```
        HTTP/1.1 200 OK
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : {
                "productId" : 1,
                "vendorId" : 1,
                "name" : "테스트 상품",
                "serialNo" : "TEST_01",
                "quantity" : 10,
                "price" : 10000,
                "category" : "TICKET",
                "status" : "ON_SALE",
                "imgUrl" : "https://cdn.example.com/image.jpg"
            }
        }
        ```
    
- **가맹점별 상품 수정**
    - 가맹점에서 등록한 상품에 대한 정보를 수정합니다.

    - Request
        ```
        [PUT] /v1/products/{vendorId}/{productId} HTTP/1.1
        Authorization: Bearer {ACCESS_TOKEN}
        ```

        ```json
        {
            "vendorId" : 1,
            "productId" : 1,
            "name" : "테스트 상품",
            "quantity" : 10,
            "price" : 100,
            "category" : "TICKET",
            "imgUrl" : "https://cdn.example.com/image.jpg"
        }
        ```

    - Response
        ```
        HTTP/1.1 200 OK
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : null
        }
        ```

- **가맹점별  상품 삭제**
    - 가맹점에서 등록한 상품을 비활성화 합니다. 비활성화 된 상품은 상세조회를 할 수 없으며, 상품 리스트 조회 결과에서 제외됩니다.

    - Request
        ```
        [DELETE] /v1/products/{vendorId}/{productId} HTTP/1.1
        Authorization: Bearer {ACCESS_TOKEN}
        ```

    - Response
        ```
        HTTP/1.1 200 OK
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : null
        }
        ```

## 계좌

페이머니 충전을 위한 고객의 은행계좌 정보를 관리하고 계좌 인증, 입출금 요청에 따른 뱅킹 서비스를 호출합니다. 회원의 페이머니 충전 금액 수취와 가맹점 정산 금액 송금을 위해 중앙 법인 계좌를 운영하는 서비스를 제공합니다.

### 계좌 등록 & 1원 인증

페이머니 시스템을 사용하기 위한 은행 계좌 등록을 진행합니다. 회원은 1인당 각각 한개의 은행 계좌를 등록할 수 있습니다. 

- 페이머니 이용을 위한 은행 계좌 등록 & 인증 순서
    1. **계좌 등록 / 인증 요청**
        - 시퀀스 다이어그램
            
            ```mermaid
            sequenceDiagram
                Client ->> Server : 계좌 등록 요청
                Server ->> Banking : 1원 송금 요청
                Banking ->> Server : 1원 송금 응답
                Server ->> DB : 계좌 정보 등록
                Server ->> Client : 계좌 등록 응답 (txId 발급)
            	    
            ```

        - Request
            ```
            [POST] /v1/accounts HTTP/1.1
            Authorization: Bearer {ACCESS_TOKEN}
            ```

            ```json
            {
                "bankCode" : "052",
                "accountNo" : "3333221230123"
            }
            ```

        - Response
            ```
            HTTP/1.1 201 Created
            Content-type: application/json; charset=UTF-8
            ```

            ```json
            {
                "payload" : {
                    "txId" : "4214f44e-8853-4439-b2a6-150814a4d97a"
                }
            }
            ```
        
            
    2. **1원 입금 랜덤코드 검증**
        - 시퀀스 다이어그램
            
            ```mermaid
            sequenceDiagram
                Client ->> Server : 랜덤 코드 검증 요청 (with txId)
                Server ->> Banking : 은행 랜덤 코드 검증 요청
                Banking ->> Server : 은행 랜덤 코드 검증 응답
                Server ->> DB : 계좌 인증 상태[인증 완료, 인증 실패] 업데이트
                Server ->> Client : 랜덤 코드 검증 응답
            ```

        - Request
            ```
            [POST] /v1/accounts/verify HTTP/1.1
            Authorization: Bearer {ACCESS_TOKEN}
            ```

            ```json
            {
                "txId" : "4214f44e-8853-4439-b2a6-150814a4d97a",
                "verificationCode" : "052"
            }
            ```

        - Response
            ```
            HTTP/1.1 200 OK
            Content-type: application/json; charset=UTF-8
            ```

            ```json
            {
                "payload" : null
            }
            ```

### 페이머니 사용을 위한 은행 입출금

페이머니 사용을 위한 은행 입출금 기능은 **메소드**로 제공됩니다. 해당 메소드는 **[페이머니 시스템]** 내부에서 머니 충전 및 송금에 사용됩니다.

**페이머니 송금을 위한 은행 계좌 입금**

1. 송금 회원의 페이머니 잔액 검증
2. 송금 대상의 등록된 은행 계좌로 입금 요청 
3. 송금 회원의 페이머니 잔액 차감

**페이머니 충전을 위한 은행 계좌 출금** 

1. 회원의 계좌 등록 상태 잔액 검증
2. 등록된 은행으로 출금 요청 
3. 페이머니 잔액 충전

### 가맹점의 정산금액 지급을 위한 계좌 입금

매출에 따른 정산금액 지급을 위한 계좌 입금 기능은 **메소드**로 제공됩니다. 해당 메소드는 **[정산 시스템]** 내부에서 사용됩니다.

## 페이머니 시스템

생성한 주문서를 기반으로 페이머니의 결제 / 송금 기능을 담당합니다. 회원의 페이머니 잔액에 따라 충전을 진행합니다. 결제한 데이터를 기반으로 결제 원장을 기록합니다.

### 페이머니로 상품 결제
- **페이머니 기반 상품 결제**

    - 시퀀스 다이어그램
        
        ```mermaid
        sequenceDiagram
            Client ->> Payment : 상품 결제 요청
            Payment ->> DB : 결제 요청 기록
            Payment ->> Order : 주문 상태 검증
        
            alt 잔액 부족 시 충전
                Payment ->> Banking : 계좌 출금 메소드 호출
                Banking ->> Payment : 계좌 출금 메소드 출금 응답
                Payment ->> Payment : 페이머니 충전
            end
            
            Payment ->> DB : 결제 결과[결제 완료, 결제 실패] 기록
            Payment ->> Client : 상품 결제 응답
        ```

    - Request
        ```
        [POST] /v1/payments/pay HTTP/1.1
        Authorization: Bearer {ACCESS_TOKEN}
        ```

        ```json
        {
            "orderId" : "819b1e1e-f93f-4aca-92b5-dd740429f263"
        }
        ```

    - Response
        ```
        HTTP/1.1 200 OK
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : null
        }
        ```
    
### 페이머니 충전
- **페이머니 충전**

    - 페이머니 충전 금액을 충전 요청, 상품 결제 필요 충전 금액 기준으로 **10,000원 단위 반올림** 하여 충전됩니다. 

    - 시퀀스 다이어그램
        ```mermaid
        sequenceDiagram
            Client ->> Payment : 페이머니 충전 요청
            Payment ->> Banking : 계좌 출금 메소드 호출
            Banking ->> Payment : 계좌 출금 메소드 출금 응답
            Payment ->> Banking : 출금 금액 중앙 법인계좌 입금 요청
            Payment ->> Payment : 페이머니 충전
            Payment ->> Client : 페이머니 충전 응답
        ```

    - Request
        ```
        [POST] /v1/payments/charge HTTP/1.1
        Authorization: Bearer {ACCESS_TOKEN}
        ```

        ```json
        {
            "amount" : 30000
        }
        ```

    - Response
        ```
        HTTP/1.1 200 OK
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : null
        }
        ```
    
### 페이머니 송금
- **페이머니 송금**

    - 시퀀스 다이어그램
        
        ```mermaid
        sequenceDiagram
            Client ->> Payment : 페이머니 송금 요청
            
            alt 잔액 부족 시 충전
                Payment ->> Banking : 계좌 출금 메소드 호출
                Banking ->> Payment : 계좌 출금 메소드 출금 응답
                Payment ->> Payment : 페이머니 충전
            end
            
            Payment ->> Client : 페이머니 송금 응답
        ```

    - Request
        ```
        [POST] /v1/payments/send HTTP/1.1
        Authorization: Bearer {ACCESS_TOKEN}
        ```
        ```json
        {
            "memberId" : 1,
            "amount" : 30000
        }
        ```

    - Response
        ```
        HTTP/1.1 200 OK
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : null
        }
        ```
    
## 주문

상품 구매를 위한 주문을 담당합니다. 주문한 상품 당 한개의 주문 데이터가 생성됩니다. 

### 주문 생성

상품의 주문 생성을 진행합니다. 상품의 재고 검증, 차감이 이루어지고 고유한 주문 ID를 반환합니다.

- **주문 생성**

     - 시퀀스 다이어그램
        
        ```mermaid
        sequenceDiagram
            Client ->> Order : 주문 생성 요청
            Order ->> Product : 상품 상태 및 재고 검증
            Order ->> Vendor : 가맹점 상태 검증
            Order ->> DB : 주문 데이터 생성 
            Order ->> Client : 주문 생성 응답
        ```

    - Request
        ```
        [POST] /v1/orders HTTP/1.1
        Authorization: Bearer {ACCESS_TOKEN}
        ```
        ```json
        {
            "productId" : 1,
            "quantity" : 2
        }
        ```

    - Response
        ```
        HTTP/1.1 200 OK
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : {
                "orderId" : "819b1e1e-f93f-4aca-92b5-dd740429f263"
            }
        }
        ```

### 주문 환불

주문 완료된 상품에 대한 환불처리를 진행합니다.

- **환불 요청**

    - 시퀀스 다이어그램
    
    ```mermaid
    sequenceDiagram
        Client ->>  Order : 주문 환불 요청
    		Order ->> Payment : 페이머니 환불 메소드 호출
    		Payment ->> Order : 페이머니 환불 메소드 응답
        
        Order ->> DB : 결제 상태 [환불] 업데이트
        Order ->> Client : 상품 환불 응답
    ```

    - Request
        ```
        [POST] /v1/orders/refund HTTP/1.1
        Authorization: Bearer {ACCESS_TOKEN}
        ```

        ```json
        {
            "orderId" : "819b1e1e-f93f-4aca-92b5-dd740429f263"
        }
        ```

    - Response
        ```
        HTTP/1.1 200 OK
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : null
        }
        ```
    
## 정산

상품 판매로 발생한 페이머니 매출 전표를 바탕으로 일별 가맹점 별 정산 금액을 산정하여 가맹점의 계좌로 입금합니다.

1. 결제 최종 기록 기반으로 가맹점 별 정산 금액 산정
    1. 수수료는 가맹점별 전체 매출의 **2%를** 제함
2. 산정된 정산금액을 등록된 가맹점 계좌로 입금

### 정산 배치
정산 배치가 실행될 때 세부적인 로직은 아래와 같습니다.

1. 결제 최종 기록(payment) 테이블의 정산 대상의 결제 완료 내역을 조회
2. 조회된 데이터를 기반으로 정산 내역(settlement_detail) 테이블에 payment_id를 매핑하여 생성
3. 정산 종합(settlement) 테이블에 정산일자 및 정산된 총 금액과 수수료를 계산하여 로우 생성
    1. 집계된 결제 최종 기록 (payment)의 결제 완료 내역의 amount (결제 금액)을 기반으로 매출 계산
    2. 집계된 매출에 수수료를 계산
4. 정산 수수료를 제한 금액을 가맹점 계좌에 송금

## 어드민

일반 회원과 가맹점주 회원 심사, 정보 관리 및 중앙 법인 계좌를 관리를 위한 백오피스 API를 제공합니다.

### 회원 관리

페이머니 시스템에 등록된 회원을 관리합니다.

- **회원 목록 조회**
    - 페이머니 시스템에 등록된 일반 회원과 가맹점주 회원 명단을 조회합니다.
    - 회원 목록 리스트는 오프셋 기반의 페이징 리스트로 제공됩니다.

    - Request
        ```
        [GET] /v1/admin/members?memberType= HTTP/1.1
        Authorization: Bearer {ACCESS_TOKEN}
        ```

    - Response
        ```
        HTTP/1.1 200 OK
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : {
                [
                    {
                        "memberId" : 1,
                        "email" : "test@gmail.com",
                        "status" : "REGISTERED",
                        "memberType" : "ADMIN",
                        "balance" : 24000
                    },
                    {
                        "memberId" : 2,
                        "email" : "test3@gmail.com",
                        "memberType" : "VENDOR",
                        "status" : "VENDOR_REGISTERED",
                        "balance" : 0
                    },
                ]
            },
            "size" : 10,
            "offset" : 2,
            "total" : 25
        }
        ```

- **회원 비활성화**
    - 페이머니 시스템에 등록된 일반 회원과 가맹점주 회원을 관리자 권한으로 비활성화 처리합니다.

    - Request
        ```
        [DELETE] /v1/admin/members/{memberId} HTTP/1.1
        Authorization: Bearer {ACCESS_TOKEN}
        ```

    - Response
        ```
        HTTP/1.1 200 OK
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : null
        }
        ```

### 가맹점 & 가맹점주 회원 관리

페이머니 시스템에서 가맹점을 등록하고 상품을 판매할 수 있는 가맹점주 회원과 가맹점 정보를 관리합니다.

- **가맹점주 회원 & 가맹점 등록 요청**
    - 가맹점주는 이메일과 비밀번호로 회원 등록 신청을 받고, 이후 내부 심사 단계를 거칩니다. 심사 단계가 완료되면 1~2 영업일 내에 심사 결과가 신청 이메일로 전송됩니다.
        - 시퀀스 다이어그램
            
            ```mermaid
            
            sequenceDiagram
                Client->> Admin : 가맹점주 등록 요청
                Admin ->> DB : (등록 대기 상태의) 가맹점주 회원 계정 등록
                Admin ->> DB : (등록 대기 상태의) 가맹점 등록
                Admin ->> Admin : 내부 심사
            	    
            	  alt 심사 성공
            	    Admin ->> DB : 가맹점주 회원 상태(성공) 업데이트
                    Admin ->> DB : 가맹점 상태(성공) 업데이트
            	    Admin ->> Client : 심사 성공 이메일 발송
            		else 심사 실패
            	    Admin ->> DB : 가맹점주 회원 상태(실패) 업데이트
                    Admin ->> DB : 가맹점 상태(실패) 업데이트
            		  Admin ->> Client : 심사 실패 이메일 발송
            	  end
            ```

        - Request
            ```
            [POST] /v1/admin/members/vendors HTTP/1.1
            Authorization: Bearer {ACCESS_TOKEN}
            ```

            ```json
            {
                "email" : "vendor@gmail.com",
                "password" : "abcdwef!@#ewef",
                "bizRegNo" : "11232123123",
                "name" : "가맹점",
                "repName" : "대표자명",
                "phoneNo" : "0321231232",
            }
            ```

        - Response
            ```
            HTTP/1.1 201 Created
            Content-type: application/json; charset=UTF-8
            ```

            ```json
            {
                "payload" : null
            }
            ```
            

- **가맹점주 심사 결과 처리**
    - 가맹점주 내부 심사 결과에 대한 처리를 진행합니다.
        1. 회원정보에 심사 결과에 상태를 업데이트 합니다
        2. 심사 결과를 가맹점주 이메일 주소로 전송합니다

    - Request
        ```
        [POST] /v1/admin/members/vendors/result HTTP/1.1
        Authorization: Bearer {ACCESS_TOKEN}
        ```

        ```json
        {
            "memberId" : 1
        }
        ```

    - Response
        ```
        HTTP/1.1 200 OK
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : null
        }
        ```

- **가맹점주 회원 상세 조회**
    - 페이머니 시스템에 등록된 가맹 점주들의 상세 회원 정보를 조회합니다.

    - Request
        ```
        [GET] /v1/admin/members/vendors/{memberId} HTTP/1.1
        Authorization: Bearer {ACCESS_TOKEN}
        ```

    - Response
        ```
        HTTP/1.1 201 Created
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : { 
                "memberId" : 1,
                "email" : "test@gmail.com",
                "status" : "VENDOR_REGISTERED",
                "memberType" : "VENDOR",
                "balance" : 24000
            }
        }
        ```

- **가맹점주 회원 정보 수정**
    - 페이머니 시스템에 등록된 가맹 점주의 상세 회원 정보를 수정합니다.

    - Request
        ```
        [PUT] /v1/admin/members/vendors/{memberId} HTTP/1.1
        Authorization: Bearer {ACCESS_TOKEN}
        ```

        ```json
        {
            "memberId" : 1,
            "email" : "test@gmail.com",
            "status" : "VENDOR_REGISTERED",
            "memberType" : "ADMIN"
        }
        ```

    - Response
        ```
        HTTP/1.1 200 OK
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : null
        }
        ```

- **가맹점주 회원 리스트 삭제**
    - 페이머니 시스템에 등록된 가맹 점주의 상세 회원 정보를 비활성화합니다.

    - Request
        ```
        [DELETE] /v1/admin/members/vendors/{memberId} HTTP/1.1
        Authorization: Bearer {ACCESS_TOKEN}
        ```

    - Response
        ```
        HTTP/1.1 200 OK
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : null
        }
        ```

- **가맹점 정보 수정**
    - 페이머니 시스템에 등록된 가맹점의 정보를 수정합니다.

    - Request
        ```
        [PUT] /v1/admin/vendors/{vendorId} HTTP/1.1
        Authorization: Bearer {ACCESS_TOKEN}
        ```

        ```json
        {
            "bizRegNo" : "11232123123",
            "name" : "가맹점",
            "repName" : "대표자명",
            "phoneNo" : "0321231232",
        }
        ```

    - Response
        ```
        HTTP/1.1 200 OK
        Content-type: application/json; charset=UTF-8
        ```

        ```json
        {
            "payload" : null
        }
        ```

### 중앙 법인 계좌 관리

- 회원의 페이머니 충전 금액 입금과 가맹점주에게 정산금액을 송금하기 위한 법인 계좌를 관리합니다.