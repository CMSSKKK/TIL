# SQL Join

> join이 없으면 관계형 데이터 베이스가 아니다.
>
> 하나의 테이블은 하나의 주제(테마)만을 가져야 한다면 ? 

>참고
>
>**https://github.com/egoing/sql-join , https://sql-joins.leopard.in.ua/** (SQL Joins Visualizer)



* LEFT OUTER JOIN

  * SELECT * FROM TableA A LEFT JOIN TableB B ON A.key = B.key

* INNER JOIN
  * SELECT * FROM TableA A INNER JOIN TableB B ON A.key = B.key

    > NULL == NULL -> False // inner join에는 null이 없음

* FULL OUTER JOIN

  * SELECT * FROM TableA A FULL OUTER JOIN TableB B ON A.key = B.key

    > 지원하지 않을때는 UNION 사용

* EXCLUSIVE LEFT JOIN
  * SELECT * FROM TableA A LEFT JOIN TableB B ON A.key = B.key WHERE B.key IS NULL





*생활코딩 SQL JOIN*

