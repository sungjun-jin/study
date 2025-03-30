# NoSQL (Not only SQL)

## RDB의 단점

- 새로운 기능을 추가하고자 하면 기존 스키마를 변경해야 한다
    - 기존 로우가 많은 테이블일 경우 컬럼 추가 작업이 전체 서비스에 큰 영향을 줄 수 있다

- 복잡한 Join 쿼리가 많아지면 조회 성능이 하락된다
- scale out이 쉽지 않다
- 트랜잭션의 ACID를 보장하기 위해 퍼포먼스에 영향을 준다

## NoSQL의 특징
- 유연한 스키마 구조를 가진다.

    - MongoDB의 경우 컬렉션 (RDB의 테이블 개념) 생성과 데이터 삽입, 조회 방법은 다음과 같다.
        ```mongodb
        // student 컬렉션 생성
        db.createCollection("student")

        // John Jones 학생 추가
        db.student.insertOne({
            name: "John Jones"
        })

        // 위 학생과 다른 구조의 학생 추가
        db.student.insertOne({
            name: "Hope",
            address : {
                country : "Korea",
                state : "Seoul",
                city : "Gangnam"
            }
        })

        // 학생 조회
        db.student.find({name: "Hope"})
        ```

- 데이터 중복을 허용한다 (join 회피)

- scale out에 최적화 되어 있다. 대부분 서버 여러 대로 하나의 클러스터를 구성하여 사용한다.

- ACID를 포기하고 높은 처리량과 응답 속도를 추구한다


출처 : 쉬운코드 (https://www.youtube.com/watch?v=sqVByJ5tbNA&list=PLcXyemr8ZeoREWGhhZi5FZs6cvymjIBVe&index=31)