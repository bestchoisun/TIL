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