# 1주차 강의 
- DBeaver 실행하여 my sql 연결
- ctl+enter 로 쿼리문 실행

- select 쿼리문 
  - 문자열은 ' '해야하고
  - 숫자는 안함

```
show tables
```

```
select * from orders
where payment_method = 'kakaopay'
and created_at between "2020-07-14" and "2020-08-19"
and email like 'a%com'
```

# 2주차 강의
- group by 
- order by 

```
select payment_method, count(*) from orders
where email like '%naver.com' and course_title = '앱개발 종합반'
group by payment_method
```

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
