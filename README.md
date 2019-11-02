#### Sql语句50条案例demo

一. 提示:

根据文章: 

(https://zhuanlan.zhihu.com/p/43289968)
和<br>
(https://zhuanlan.zhihu.com/p/38354000)
完成;

表结构关系:
<div style="width: 300px; height: 200px;">
	<image src="https://pic1.zhimg.com/80/v2-86fd263583a6cead51675982c1735e68_hd.jpg" />
</div>


二. 建表语句:
```
/*学生表*/
CREATE TABLE `Student`(
`s_id` VARCHAR(20),
`s_name` VARCHAR(20) NOT NULL DEFAULT '',
`s_birth` VARCHAR(20) NOT NULL DEFAULT '',
`s_sex` VARCHAR(10) NOT NULL DEFAULT '',
PRIMARY KEY(`s_id`)
);
/*课程表*/
CREATE TABLE `Course`(
`c_id` VARCHAR(20),
`c_name` VARCHAR(20) NOT NULL DEFAULT '',
`t_id` VARCHAR(20) NOT NULL,
PRIMARY KEY(`c_id`)
);
/*教师表*/
CREATE TABLE `Teacher`(
`t_id` VARCHAR(20),
`t_name` VARCHAR(20) NOT NULL DEFAULT '',
PRIMARY KEY(`t_id`)
);
/*成绩表*/
CREATE TABLE `Score`(
`s_id` VARCHAR(20),
`c_id` VARCHAR(20),
`s_score` INT(3),
PRIMARY KEY(`s_id`,`c_id`)
);
```

插入数据:
```
/*插入学生表测试数据*/
insert into Student values('01' , '赵雷' , '1990-01-01' , '男');
insert into Student values('02' , '钱电' , '1990-12-21' , '男');
insert into Student values('03' , '孙风' , '1990-05-20' , '男');
insert into Student values('04' , '李云' , '1990-08-06' , '男');
insert into Student values('05' , '周梅' , '1991-12-01' , '女');
insert into Student values('06' , '吴兰' , '1992-03-01' , '女');
insert into Student values('07' , '郑竹' , '1989-07-01' , '女');
insert into Student values('08' , '王菊' , '1990-01-20' , '女');

/*课程表测试数据*/
insert into Course values('01' , '语文' , '02');
insert into Course values('02' , '数学' , '01');
insert into Course values('03' , '英语' , '03');

/*教师表测试数据*/
insert into Teacher values('01' , '张三');
insert into Teacher values('02' , '李四');
insert into Teacher values('03' , '王五');

/*成绩表测试数据*/
insert into Score values('01' , '01' , 80);
insert into Score values('01' , '02' , 90);
insert into Score values('01' , '03' , 99);
insert into Score values('02' , '01' , 70);
insert into Score values('02' , '02' , 60);
insert into Score values('02' , '03' , 80);
insert into Score values('03' , '01' , 80);
insert into Score values('03' , '02' , 80);
insert into Score values('03' , '03' , 80);
insert into Score values('04' , '01' , 50);
insert into Score values('04' , '02' , 30);
insert into Score values('04' , '03' , 20);
insert into Score values('05' , '01' , 76);
insert into Score values('05' , '02' , 87);
insert into Score values('06' , '01' , 31);
insert into Score values('06' , '03' , 34);
insert into Score values('07' , '02' , 89);
insert into Score values('07' , '03' , 98);
```

三. 练习案例:

1、查询课程编号为“01”的课程比“02”的课程成绩高的所有学生的学号
> 三个表连接，s_score分为两列:
```
SELECT st.*, a.s_score, b.s_score
FROM student st
INNER JOIN (SELECT s_id, s_score FROM Score WHERE c_id='01') a ON st.s_id=a.s_id
INNER JOIN (SELECT s_id, s_score FROM Score WHERE c_id='02') b ON a.s_id=b.s_id
WHERE a.s_score > b.s_score;
```
<br>
2、查询平均成绩大于60分的学生的学号和平均成绩

```
SELECT s_id, AVG(s_score)
FROM Score
GROUP BY s_id
HAVING AVG(s_score) > 60;
```
<br>
3、查询所有学生的学号、姓名、选课数、总成绩

```
SELECT A.s_id, A.s_name, COUNT(B.c_id) AS 选课数, SUM(B.s_score) AS 总成绩
FROM student A
JOIN score B
ON A.s_id=B.s_id
GROUP BY A.s_id, A.s_name;
```
<br>
4、查询姓“猴”的老师的个数

```
SELECT COUNT(t_id)
From teacher
where t_name like '猴%';
```
<br>
5、查询没学过“张三”老师课的学生的学号、姓名

```
SELECT * 
FROM Student
WHERE s_id NOT IN
(SELECT sc.s_id
FROM Score sc
INNER JOIN Course co ON sc.c_id=co.c_id
INNER JOIN Teacher te ON co.t_id=te.t_id
WHERE te.t_name='张三');
```
<br>
6、查询学过“张三”老师所教的所有课的同学的学号、姓名

```
SELECT * 
FROM Student
WHERE s_id IN
(SELECT sc.s_id
FROM Score sc
INNER JOIN Course co ON sc.c_id=co.c_id
INNER JOIN Teacher te ON co.t_id=te.t_id
WHERE te.t_name='张三');
```
<br>
7、查询学过编号为“01”的课程并且也学过编号为“02”的课程的学生的学号、姓名

```
SELECT *
FROM student
where s_id IN
(SELECT a.s_id 
FROM
	(SELECT *
	FROM Score
	WHERE c_id='01') a
INNER JOIN 
	(SELECT *
	FROM Score
	WHERE c_id='02') b
ON a.s_id=b.s_id);

```
<br>
8、查询学过编号为“01”的课程, 但没学过编号为“02”的课程的学生的学号、姓名

```
SELECT *
FROM student
WHERE
 s_id IN (SELECT s_id FROM Score WHERE c_id='01')
AND 
 s_id NOT IN (SELECT s_id FROM Score WHERE c_id='02');
```
<br>
9、查询课程编号为“02”的总成绩

```
SELECT SUM(s_score)
FROM Score
WHERE c_id='02';
/*或*/
SELECT c_id, SUM(s_score)
FROM score
GROUP BY c_id
HAVING c_id = '02';
```
<br>
10、查询所有课程成绩小于60分(全部课程不及格)的学生的学号、姓名

```
/*1.得出同学课程成绩小于60分的课程数*/
/*2.统计同学总共学了几门课程*/
/*3.如果相等就是全部课程不及格*/
SELECT a.s_id, t.s_name
FROM 
	(SELECT s_id, COUNT(c_id) AS cnt
	 FROM score
	 WHERE s_score<60
	 GROUP BY s_id) a
 INNER JOIN
    (SELECT s_id, COUNT(c_id) AS cnt
	 FROM score
     GROUP BY s_id) b
 ON a.s_id=b.s_id
 INNER JOIN student t ON a.s_id=t.s_id
 WHERE a.cnt=b.cnt;

/*另一种简便方法,如果最大成绩都小于60,那么这个同学所有课程不及格*/
SELECT s_id, s_name
FROM student
WHERE s_id IN
(SELECT s_id FROM score
 GROUP BY s_id
 HAVING MAX(s_score)<60);
```
<br>
11.查询没有学全所有课的学生的学号、姓名

```
SELECT st.*, sc.*
FROM student AS st
LEFT JOIN score AS sc ON st.s_id=sc.s_id
GROUP BY st.s_id
HAVING COUNT(DISTINCT sc.c_id) < (SELECT COUNT(DISTINCT c_id) FROM course);

```
<br>
12、查询至少有一门课与学号为“01”的学生所学课程相同的学生的学号和姓名

```
SELECT DISTINCT st.* 
FROM student st INNER JOIN score sc ON st.s_id=sc.s_id
WHERE sc.c_id IN
(SELECT c_id FROM score WHERE s_id='01') AND st.s_id != '01';

```

13.查询和“01”号同学所学课程完全相同的其他同学的学号

```
/*第一步是查出和01号同学(不包括自己)所学课程数相同的学生id*/
/*第二步是查出至少有一门课程和01号同学不同的学生id*/
/*最后AND两个条件, 表示课程数相等,且学的课程相同即为完全相同*/
SELECT * FROM student
WHERE s_id IN (
	SELECT s_id FROM score
	WHERE s_id != '01'
	GROUP BY s_id
	HAVING COUNT(DISTINCT c_id)=(SELECT COUNT(DISTINCT c_id) FROM score where s_id='01')
)
AND s_id NOT IN (
	SELECT DISTINCT s_id FROM score
	WHERE c_id NOT IN (
		SELECT sc.c_id 
		FROM score sc WHERE sc.s_id='01'
	)
);
```

14、查询没学过"张三"老师讲授的任一门课程的学生姓名

```
SELECT * FROM student st
WHERE st.s_id NOT IN (
	SELECT s.s_id FROM score s
	INNER JOIN course c ON s.c_id=c.c_id
	INNER JOIN teacher t ON t.t_id=c.t_id AND t.t_name='张三'
);
```

15、查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩

```
SELECT st.s_id, st.s_name, AVG(sc.s_score) FROM student st
INNER JOIN score sc ON st.s_id=sc.s_id
WHERE st.s_id IN (
	SELECT s_id FROM score
	WHERE s_score<60
	GROUP BY s_id
	HAVING COUNT(DISTINCT c_id)>=2
)
GROUP BY st.s_id, st.s_name;
```

16、检索"01"课程分数小于60，按分数降序排列的学生信息

```
SELECT * FROM student st
INNER JOIN score sc ON sc.s_id=st.s_id
WHERE sc.s_score<60 AND sc.c_id='01'
ORDER BY sc.s_score DESC;
```

17、按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩

>备注：<br>
>1.因为要选出需要的字段 用case when 当c_id='01' then 可以得到对应课程的s_core和课程名<br>
>2.因为GROUP BY 要与select 列一致，所以用统计函数MAX加case when, 得到课程分数列<br>
>3.因为每科成绩只有一个, 所以MAX取到的是最高成绩, 就是单科成绩<br>

```
SELECT s_id "学号",
MAX(CASE WHEN c_id='01' THEN s_score ELSE null END) "语文",
MAX(CASE WHEN c_id='02' THEN s_score ELSE null END) "数学",
MAX(CASE WHEN c_id='03' THEN s_score ELSE null END) "英语",
AVG(s_score) "平均成绩"
FROM score
GROUP BY s_id
ORDER BY AVG(s_score) DESC;

/** 结果 **/
学号 语文 数学 英语  平均成绩
07	null  89   98	93.5000
01	80	  90   99	89.6667
05	76	  87   null	81.5000
03	80	  80   80	80.0000
02	70	  60   80	70.0000
04	50	  30   20	33.3333
06	31	  null 34	32.5000
```

18、 查询各科成绩最高分、最低分和平均分：以如下形式显示：课程ID，课程name，最高分，最低分，平均分，及格率，中等率，优良率，优秀率

--及格为>=60，中等为：70-80，优良为：80-90，优秀为：>=90

```
SELECT
 sc.c_id "学号",
 c.c_name "课程",
 MAX(sc.s_score) "最高成绩",
 MIN(sc.s_score) "最低成绩",
 AVG(sc.s_score) "平均成绩",
 SUM(CASE WHEN sc.s_score>=60 THEN 1 ELSE 0 END)/COUNT(s_id) "及格率",
 SUM(CASE WHEN sc.s_score>=70 AND sc.s_score<80 THEN 1 ELSE 0 END)/COUNT(s_id) "中等率",
 SUM(CASE WHEN sc.s_score>=80 AND sc.s_score<90 THEN 1 ELSE 0 END)/COUNT(s_id) "优良率",
 SUM(CASE WHEN sc.s_score>=90 THEN 1 ELSE 0 END)/COUNT(s_id) "优秀率"
FROM score sc
INNER JOIN course c ON sc.c_id=c.c_id
GROUP BY sc.c_id;

/**注意sql server中应该是THEN 1.0 ELSE O.O**/
/** 结果 **/
学号 课程 最高成绩 最低成绩 平均成绩   及格率   中等率   优良率  优秀率
01 	 语文	80     31	  64.5000	0.6667	 0.3333	 0.3333	 0.0000
02	 数学	90	   30	  72.6667	0.8333   0.0000	 0.5000	 0.1667
03	 英语	99	   20	  68.5000	0.6667	 0.0000	 0.3333	 0.3333

```

19、按各科成绩进行排序，并显示排名

```
SELECT *, row_number() over(partition by c_id ORDER BY s_score DESC) 
FROM score;

/**窗口函数使用参考:https://yq.aliyun.com/articles/593698**/
```

20、查询学生的总成绩并进行排名

```
SELECT st.s_id, st.s_name, SUM(sc.s_score),
row_number() OVER(ORDER BY SUM(sc.s_score) DESC)
FROM student st
INNER JOIN score sc ON st.s_id=sc.s_id
GROUP BY st.s_id, st.s_name;
```

21 、查询不同老师所教不同课程平均分从高到低显示

```
/**以课程为主**/
SELECT c.c_id, c.c_name, AVG(s_score) as avg_score
FROM score sc
INNER JOIN course c ON sc.c_id=c.c_id
GROUP BY sc.c_id
ORDER BY avg_score DESC;

/**以老师为主**/
SELECT t.t_id, t.t_name, AVG(sc.s_score) as avg_score 
FROM score sc
INNER JOIN course c ON sc.c_id=c.c_id
INNER JOIN teacher t ON c.t_id=t.t_id
GROUP BY t.t_id, t.t_name
ORDER BY avg_score DESC;
```

22、查询所有课程的成绩第2名到第3名的学生信息及该课程成绩

```
SELECT * FROM
	(SELECT 
	st.*,
	sc.s_score ,
	row_number() OVER(partition BY sc.c_id ORDER BY sc.s_score DESC) num
	FROM student st 
	INNER JOIN score sc ON st.s_id=sc.s_id) a
WHERE num IN (2, 3);

/**结果**/
s_id s_name   s_birth  s_sex s_score  num
03	 孙风	1990-05-20	男	 	80		2
05	 周梅	1991-12-01	女	    76		3
07	 郑竹	1989-07-01	女	 	89		2
05	 周梅	1991-12-01	女	 	87		3
07	 郑竹	1989-07-01	女	 	98		2
02 	 钱电	1990-12-21	男	 	80		3
```

23、使用分段[100-85],[85-70],[70-60],[<60]来统计各科成绩，分别统计各分数段人数：课程ID和课程名称

```
SELECT 
c.c_id, 
c.c_name,
SUM(CASE WHEN sc.s_score<=100 AND sc.s_score>85 THEN 1 ELSE 0 END) "[100-85分]",
COUNT(CASE WHEN sc.s_score<=85 AND sc.s_score>70 THEN 1 ELSE null END) "[85-70分]",
COUNT(CASE WHEN sc.s_score<=70 AND sc.s_score>60 THEN 1 ELSE null END) "[70-60分]",
COUNT(CASE WHEN sc.s_score<=60 THEN 1 ELSE null END) "[<60分]"
FROM score sc
INNER JOIN course c ON sc.c_id=c.c_id
GROUP BY c.c_id, c.c_name;
```

24、查询学生平均成绩及其名次

```
SELECT 
st.s_id, 
st.s_name, 
AVG(sc.s_score),
row_number() OVER(ORDER BY AVG(sc.s_score) DESC) "排名"
FROM student st
INNER JOIN score sc ON st.s_id=sc.s_id
GROUP BY st.s_id, st.s_name;
```

25、查询各科成绩前三名的记录（不考虑成绩并列情况）

```
SELECT * FROM
	(SELECT 
	st.s_id, 
	st.s_name,
	sc.c_id, 
	sc.s_score,
	row_number() OVER(partition BY sc.c_id ORDER BY sc.s_score DESC) num
	FROM student st
	INNER JOIN score sc ON st.s_id=sc.s_id) a 
WHERE num <=3;
```

26、查询每门课程被选修的学生数

```
SELECT c.c_id, c.c_name, COUNT(DISTINCT sc.s_id) 
FROM course c 
INNER JOIN score sc ON c.c_id=sc.c_id
GROUP BY c.c_id, c.c_name;
```

27、 查询出只有两门课程的全部学生的学号和姓名

```
SELECT s_id, s_name FROM student
WHERE s_id IN (
	SELECT s_id FROM score
	GROUP BY s_id
	HAVING COUNT(DISTINCT c_id)=2
);

/*或*/
SELECT st.s_id, st.s_name
FROM student st
INNER JOIN score sc ON st.s_id=sc.s_id
GROUP BY sc.s_id
HAVING COUNT(DISTINCT sc.c_id)=2;
```

28、查询男生、女生人数

```
SELECT s_sex, COUNT(s_id) FROM student
GROUP BY s_sex;

/*或*/
SELECT 
SUM(CASE WHEN s_sex='男' THEN 1 ELSE 0 END) "男生人数",
SUM(CASE WHEN s_sex='女' THEN 1 ELSE 0 END) "女生人数"
FROM student;
```

29、查询名字中含有"风"字的学生信息

```
SELECT * FROM student WHERE s_name LIKE '%风%';
```

31、查询1990年出生的学生名单

```
SELECT * FROM student 
WHERE year(s_birth)='1990';
```

32、查询平均成绩大于等于85的所有学生的学号、姓名和平均成绩

```
SELECT st.s_id, st.s_name, AVG(sc.s_score) 
FROM student st
INNER JOIN score sc ON st.s_id=sc.s_id
GROUP BY st.s_id, st.s_name
HAVING AVG(sc.s_score)>=85;
```

33、查询每门课程的平均成绩，结果按平均成绩升序排序，平均成绩相同时，按课程号降序排列

```
SELECT c.c_id, c.c_name, AVG(sc.s_score) avg_score
FROM score sc
INNER JOIN course c ON sc.c_id=c.c_id
GROUP BY sc.c_id
ORDER BY avg_score ASC, c.c_id DESC;
```

34、查询课程名称为"数学"，且分数低于60的学生姓名和分数

```
SELECT st.s_name, sc.s_score
FROM student st, score sc, course c
WHERE st.s_id=sc.s_id AND sc.c_id=c.c_id 
AND c.c_name='数学'
AND sc.s_score<=60;
```

35、查询所有学生的课程及分数情况
>备注:<br>
>1.因为要选出需要的字段 用case when 当co.c_name='数学' then 可以得到对应的 sc.s_core<br>
>2.因为GROUP UP 要与select 列一致，所以case when 加修饰max<br>
>3.因为最后要展现出每个同学的各科成绩为一行，所以用到case<br>

```
SELECT 
st.s_id,
st.s_name, 
MAX(CASE WHEN c.c_name="数学" THEN sc.s_score ELSE null END) "数学成绩",
MAX(CASE WHEN c.c_name="语文" THEN sc.s_score ELSE null END) "语文成绩",
MAX(CASE WHEN c.c_name="英语" THEN sc.s_score ELSE null END) "英语成绩"
FROM student st 
INNER JOIN score sc ON st.s_id=sc.s_id
INNER JOIN course c ON sc.c_id=c.c_id 
GROUP BY st.s_id, st.s_name;
```

36、查询任何一门课程成绩在70分以上的姓名、课程名称和分数

```
SELECT st.s_name, c.c_name, sc.s_score
FROM student st 
INNER JOIN score sc ON st.s_id=sc.s_id
INNER JOIN course c ON sc.c_id=c.c_id
WHERE sc.s_score > 70;
```

37、查询不及格的课程并按课程号从大到小排列

```
SELECT st.s_name, c.c_name, sc.s_score
FROM student st 
INNER JOIN score sc ON st.s_id=sc.s_id
INNER JOIN course c ON sc.c_id=c.c_id 
WHERE sc.s_score<60
ORDER BY c.c_id DESC;
```

38、查询课程编号为03且课程成绩在80分以上的学生的学号和姓名

```
SELECT st.s_id, st.s_name, c.c_name, sc.s_score
FROM student st 
INNER JOIN score sc ON st.s_id=sc.s_id
INNER JOIN course c ON sc.c_id=c.c_id 
WHERE c.c_id='03' AND sc.s_score>80;
```

39、求每门课程的学生人数

```
SELECT c_id, COUNT(DISTINCT s_id) FROM score
GROUP BY c_id;
```

40、查询选修“张三”老师所授课程的学生中成绩最高的学生姓名及其成绩

```
SELECT st.s_name, sc.s_score 
FROM student st
INNER JOIN score sc ON st.s_id=sc.s_id
INNER JOIN course c ON c.c_id=sc.c_id
INNER JOIN teacher te ON te.t_id=c.t_id
WHERE te.t_name='张三'
ORDER BY sc.s_score DESC
LIMIT 0,1;
```

41.查询某个学生不同课程但成绩相同的学生编号、课程编号、学生成绩
>这里两张score表inner join关联,取交集, 其实结果就是一张表的结果<br>
>为了能和自己的课程成绩比较, 所以用到了两张相同的表<br>
```
SELECT DISTINCT sc1.s_id, sc1.c_id, sc1.s_score
FROM score sc1
INNER JOIN score sc2 ON sc1.s_id=sc2.s_id
WHERE sc1.s_score=sc2.s_score AND sc1.c_id != sc2.c_id;
```

42、查询每门课程成绩最好的前两名

```
SELECT * FROM
	(SELECT 
	 st.s_name,
	 c.c_name,
	 sc.s_score,
	 row_number() OVER(partition BY c.c_id ORDER BY sc.s_score DESC) num
	FROM student st
	INNER JOIN score sc ON st.s_id=sc.s_id
	INNER JOIN course c ON sc.c_id=c.c_id) a
WHERE num <= 2;
```

43、统计每门课程的学生选修人数（超过5人的课程才统计）。要求输出课程号和选修人数，查询结果按人数降序排列，若人数相同，按课程号升序排列

```
SELECT 
 c_id,
 CASE WHEN COUNT(DISTINCT s_id)>5 THEN COUNT(DISTINCT s_id) ELSE null END
 FROM score
GROUP BY c_id
ORDER BY COUNT(s_id) ASC, c_id DESC;
/*或*/
SELECT c_id, COUNT(s_id)
 FROM score
GROUP BY c_id
HAVING COUNT(s_id)>5 
ORDER BY COUNT(s_id) ASC, c_id DESC;
```

44、检索至少选修两门课程的学生学号

```
SELECT s_id FROM score
GROUP BY s_id
HAVING COUNT(c_id)>=2;
```

45、 查询选修了全部课程的学生信息

```
SELECT st.s_id, st.s_name, COUNT(DISTINCT c_id)
FROM score sc, student st
WHERE sc.s_id=st.s_id
GROUP BY st.s_id
HAVING COUNT(DISTINCT c_id)=(SELECT COUNT(c_id) FROM course);
```


46、查询没学过“张三”老师讲授的任一门课程的学生姓名

```
SELECT * FROM student
WHERE s_id NOT IN
	(
	SELECT DISTINCT st.s_id 
	FROM student st, score sc, teacher te, course c
	WHERE st.s_id=sc.s_id
	AND c.t_id=te.t_id
	AND te.t_name='张三'
	);
```

47、查询两门以上不及格课程的同学的学号, 姓名及其平均成绩

```
SELECT st.s_id, st.s_name, AVG(sc.s_score)
FROM student st 
INNER JOIN score sc ON st.s_id=sc.s_id
WHERE sc.s_score<60
GROUP BY st.s_id, st.s_name
HAVING COUNT(DISTINCT c_id)>=2;
```

48、查询各学生的年龄
> mysql中 datadiff()返回的是两个日期的天数

```
SELECT 
s_id, 
s_birth,
DATEDIFF(CURDATE(), s_birth)/365
FROM student;
```

49、查询本月过生日的学生(无法使用week、date(now()）
>以下函数无法在SQL server中使用 所以截取标准答案（MySQL）
```
SELECT *
FROM student
WHERE week(s_birth, 1) = week(DATE(NOW()), 1);
```


50、查询下月过生日的学生

```
SELECT *
FROM student
WHERE week(s_birth, 1) = week(DATE(NOW()), 1)+1;
```