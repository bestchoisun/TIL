# 1주차 강의 
- DBeaver 실행하여 my sql 연결
- ctl+enter 로 쿼리문 실행

```
show tables
```

- select 쿼리문 
  - select 필드명 from 테이블명
  - limit 5 ? 5줄만 보여줘
  - distinct()  
  - count(*)
  ```
  SELECT count(distinct(name)) from users;
  ```


```
select * from orders
where payment_method = 'kakaopay'
and created_at between "2020-07-14" and "2020-08-19"
and email like 'a%com'
```
- where절
  - 문자열은 ' '해야하고
  - 숫자는 안함
  - 같음 ? == , 같지않음? !=
  - 범위조건 ? between A and B
  ```
  select * from orders
  where created_at between "2020-07-13" and "2020-07-15";
  ```
  7월 13일~14일의 정보를 볼수있음

  - 포함조건? in (A,B,D)
  - like 문법 ? '%naver.com', 'a%' '%sun%'


# 2주차 강의
- group by 
  - 그룹별 갯수, 최소값/최대값/평균/합계

- order by 
  - 기본이 오름차순, desc 붙여서 내림차순

```
select payment_method, count(*), min(created_at) from orders
	group by payment_method 
	order by min(created_at)
```
```
select user_id, count(*), min(likes), max(likes), avg(likes), sum(likes) from checkins
	group by user_id
	order by avg(likes) desc
```

- 쿼리가 실행되는 순서: from → group by → select → order by



# 3주차 강의 
- join :left join, inner join

```
select c.course_id, c.title, count(*) as cnt_notstart from courses c
inner join enrolleds e 
on c.course_id = e.course_id
where is_registered = 0
group by c.course_id
```

- union
  (  
  )
  union all 
  (
  )

# 4주차 강의
- subquery 
```
select * from point_users pu 
where pu.point > (select avg(pu2.point) from point_users pu2);
```

- with 절
```
with table1 as (
	select course_id, count(distinct(user_id)) as cnt_checkins from checkins
	group by course_id
), table2 as (
	select course_id, count(*) as cnt_total from orders
	group by course_id
)
select c.title,
       a.cnt_checkins,
       b.cnt_total,
       (a.cnt_checkins/b.cnt_total) as ratio
from table1 a inner join table2 b on a.course_id = b.course_id
inner join courses c on a.course_id = c.course_id
```
