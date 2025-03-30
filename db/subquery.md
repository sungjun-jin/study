# Subquery

내부 쿼리의 결과를 기반으로 데이터를 필터링, 검색, 조작하는 데 자주 사용되는 쿼리안에 포함된 쿼리이다. select, insert, update, delete문에 포함될 수 있다.

## 서브쿼리를 활용한 예시

### 테이블 구조
예시에 사용될 총 4개의 테이블 구조는 아래와 같다.

#### department : 부서
| id | name | leader_id |
| --- | --- | --- |

#### employee : 임직원
| id | name | birth_date | sex | position | salary | dept_id |
| --- | --- | --- | --- | --- | --- | --- |

#### project : 프로젝트
| id | name | leader_id | start_date | end_date |
| --- | --- | --- | --- | --- |

#### works_on : 일하고 있는 부서
| empl_id | proj_id |
| --- | --- |

### 예시 1

**ID가 14인 임직원보다 생일이 빠른 임직원의 ID, 이름, 생일을 알고 싶다**

```sql
select id, name, birth_date
from employee
where birth_date < (
    select birth_date
    from EMPLOYEE
    where id = 14
);
```

- 위 예시의 서브쿼리는 SELECT 포함되어 있다
- 서브 쿼리는 Outer Query와 Subquery로 구성될 수 있다.
    - Outer Query는 다음과 같다.
        ```sql
        select id, name, birth_date from employee where birth_date < 
        ```
    - Subquery는 다음과 같다.
        ```sql
        select birth_date from EMPLOYEE where id = 14
        ```

### 예시 2
**ID가 1인 임직원과 같은 부서, 같은 성별인 임직원들의 ID와 이름과 직군을 알고 싶다**

```sql
select id, name, position
from employee
where (dept_id, sex) = (
    select dept_id, sex
    from employee
    where id = 1
);
```

서브 쿼리는 하나 이상의 attribute를 반환할 수 있다. 따라서 위 예시처럼 여러개의 attribute를 사용해 비교할 수 있다.

### 예시 3
**ID가 5인 임직원과 같은 프로젝트에 참여한 임직원들의 ID를 알고 싶다**

```sql
select distinct empl_id
from works_on 
where empl_id != 5 and proj_id in (
    select proj_id 
    from works_on 
    where empl_id = 5
);
```

위 결과에 더해서 임직원들의 이름을 추가적으로 알고 싶다면 아래와 같이 쿼리 작성이 가능하다.

```sql
select id, name
from employee
where id = (
    select distinct empl_id
    from works_on 
    where empl_id != 5 and proj_id in (
        select proj_id 
        from works_on 
        where empl_id = 5
    )
);
```

아래 예시와 같이 서브 쿼리는 from절에서도 사용할 수 있다. from절의 서브 쿼리의 결과를 DSTNCT_E라는 임시 테이블로 생성하여 where 절에서 비교할 수 있다.

```sql
select id,name
from employee (
    select distinct empl_id
    from works_on 
    where empl_id != 5 and proj_id in (
        select proj_id 
        from works_on 
        where empl_id = 5
    ) AS DSTNCT_E
) where id = DSTINCT_E.empl_id
```

### 예시 4
exists와 not exists문을 아래 예시처럼 서브 쿼리에 활용할 수 있다.

**ID가 7 혹은 12인 임직원이 참여한 프로젝트의 ID와 이름을 알고 싶다**
```sql
select p.id, p.name
from project p
where exists (
    select *
    from works_on w
    where w.proj_id = p.id and w.empl_id in (7,12)
);
```

**2000년대생이 없는 부서의 ID와 이름을 알고 싶다**

```sql
select id, name
from department d
where not exists (
    select * 
    from employee e
    where d.id = e.dept_id and e.birth_date >= '2000-01-01'
);
```

출처 : 쉬운코드 데이터베이스 https://www.youtube.com/watch?v=lwmwlA2WhFc&list=PLcXyemr8ZeoREWGhhZi5FZs6cvymjIBVe&index=6
