>–1.学生表 
>student(s_id,s_name,s_birth,s_sex) –学生编号,学生姓名, 出生年月,学生性别 
>–2.课程表 
>course(c_id,c_name,t_id) – –课程编号, 课程名称, 教师编号 
>–3.教师表 
>Teacher(t_id,t_name) –教师编号,教师姓名 
>–4.成绩表 
>Score(s_id,c_id,s_score) –学生编号,课程编号,分数



##### 1、查询"01"课程比"02"课程成绩高的学生的信息及课程分数  

```sql
select a.*, b.c_id as b_id, b.s_score as b_score, c.c_id as c_id, c.s_score as c_score 
from student a inner join score b on a.s_id=b.s_id and b.c_id='01' 
left join score c on c.c_id='02' and c.s_id=a.s_id
where c.s_score < b.s_score;
```

##### 2、查询"01"课程比"02"课程成绩低的学生的信息及课程分数 

```sql
select a.*, b.c_id as b_id, b.s_score as b_score, c.c_id as c_id, c.s_score as c_score 
from student a inner join score b on a.s_id=b.s_id and b.c_id='01' 
left join score c on c.c_id='02' and c.s_id=a.s_id
where c.s_score > b.s_score;
```

##### 3、查询平均成绩大于等于60分的同学的学生编号和学生姓名和平均成绩

```sql
select a.s_name, b.s_id, b.avg_sco
from student a join(
select s_id, avg(s_score) as avg_sco
from score
group by s_id
having AVG(s_score) >= 60) b on a.s_id=b.s_id;
```

res

> '01','赵雷','89.67'
> '02','钱电','70.00'
> '03','孙风','80.00'
> '05','周梅','81.50'
> '07','郑竹','93.50'
>
> ------
>
> '赵雷','01','89.6667'
> '钱电','02','70.0000'
> '孙风','03','80.0000'
> '周梅','05','81.5000'
> '郑竹','07','93.5000'

##### 4、查询平均成绩小于60分的同学的学生编号和学生姓名和平均成绩 (包括有成绩的和无成绩的)

```sql
select a.s_name, b.s_id, b.avg_sco
from student a join(
select s_id, avg(s_score) as avg_sco
from score
group by s_id
having AVG(s_score) < 60) b on a.s_id=b.s_id
union
select a.s_name, a.s_id, 0 as avg_sco
from student a
where a.s_id not in (select distinct s_id from score) ;
```

##### 5、查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩

```sql
select a.s_id, a.s_name, b.c_count, b.score_sum
from student a left join
(select s_id, count(c_id) c_count, SUM(s_score) score_sum
from score 
group by s_id) b 
on a.s_id = b.s_id
```

##### 6、查询"李"姓老师的数量

```sql
select count(*) from teacher where t_name like "李%"
```

##### 7、查询学过"张三"老师授课的同学的信息 

```sql
select *
from student d
where d.s_id in (
select c.s_id
from score c 
where c.c_id in (
select a.c_id 
from course a right join(
select t_id
from teacher
where t_name="张三") b
on a.t_id = b.t_id))
```

##### 8、查询没学过"张三"老师授课的同学的信息 

```sql
select *
from student d
where d.s_id not in (
select c.s_id
from score c 
where c.c_id in (
select a.c_id 
from course a right join(
select t_id
from teacher
where t_name="张三") b
on a.t_id = b.t_id))
```

##### 9、查询学过编号为"01"并且也学过编号为"02"的课程的同学的信息

```sql
select c.*
from student c
where c.s_id in 
(select a.s_id
from score a
where a.c_id='02' and a.s_id in 
(select b.s_id 
from score b
where b.c_id='01'));
```

##### 10、查询学过编号为"01"但是没有学过编号为"02"的课程的同学的信息

```sql
select *
from student c
where c.s_id in (select a.s_id
from score a
where a.c_id ='01' and a.s_id not in 
(select b.s_id 
from score b
where b.c_id ='02'))
```

11、查询没有学全所有课程的同学的信息

```sql
select *
from student b
where b.s_id in(
select a.s_id 
from score a
group by a.s_id
having count(a.c_id) < 3)
```

12、查询至少有一门课与学号为"01"的同学所学相同的同学的信息

```sql
select *
from student c
where c.s_id in (
select b.s_id
from score b
where b.c_id in (
(select a.c_id
from score a
where a.s_id = '01')))
```

13、查询和"01"号的同学学习的课程完全相同的其他同学的信息 

```sql
select d.*
from student d
where d.s_id in (
select b.s_id
from score b
where b.s_id!='01' and b.c_id in (
select a.c_id
from score a
where a.s_id='01') 
group by b.s_id
having count(1) = (
select count(c.c_id)
from score c
where c.s_id='01') )
```

14、查询没学过"张三"老师讲授的任一门课程的学生姓名

```sql
select d.s_name
from student d
where d.s_id not in 
(select distinct c.s_id
from score c
where c.c_id in 
(select b.c_id
from course b
where b.t_id in (
select a.t_id
from teacher a
where a.t_name="张三")))
```

15、查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩

```sql
select b.s_id, avg(b.s_score), c.s_name
from score b, student c
where b.s_id=c.s_id and b.s_id in 
(select a.s_id
from score a
where a.s_score < 60
group by a.s_id
having count(1) >= 2)
group by b.s_id
```

16、检索"01"课程分数小于60，按分数降序排列的学生信息

```sql
select a.*
from student a, (select s_id, s_score
from score where c_id='01' and s_score< 60
order by s_score desc)b
where a.s_id = b.s_id
```

17、按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩

```sql
select a.s_id, (select s_score from score where c_id='01' and a.s_id=s_id) as d,
(select s_score from score where c_id='02' and a.s_id=s_id) as b,
(select s_score from score where c_id='03' and a.s_id=s_id) as c,
avg(a.s_score) as avg_score
from score a
group by a.s_id
order by avg_score
```

