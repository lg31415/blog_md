title: "SQL"
date: 2015-04-19 09:12:01
tags: SQL
categories: SQL
---

### 表（MYSQL）
__ Student(sid,Sname,Sage,Ssex) 学生表__
```sql
CREATE TABLE student (
  sid varchar(10) NOT NULL,
  sName varchar(20) DEFAULT NULL,
  sAge datetime DEFAULT '1980-10-12 23:12:36',
  sSex varchar(10) DEFAULT NULL,
  PRIMARY KEY (sid)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
__Course(cid,Cname,tid) 课程表 __
```sql
CREATE TABLE course (
  cid varchar(10) NOT NULL,
  cName varchar(10) DEFAULT NULL,
  tid int(20) DEFAULT NULL,
  PRIMARY KEY (cid)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
__ SC(sid,cid,score) 成绩表__
```sql
CREATE TABLE sc (
  sid varchar(10) DEFAULT NULL,
  cid varchar(10) DEFAULT NULL,
  score int(10) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
__ Teacher(tid,Tname) 教师表__
```sql
CREATE TABLE taacher (
  tid int(10) DEFAULT NULL,
  tName varchar(10) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
__ 数据：（MySQL）__
```sql
insert  into taacher(tid,tName) values (1,'李老师'),(2,'何以琛'),(3,'叶平');
insert  into student(sid,sName,sAge,sSex) values ('1001','张三丰','1980-10-12 23:12:36','男'),('1002','张无极','1995-10-12 23:12:36','男'),('1003','李奎','1992-10-12 23:12:36','女'),('1004','李元宝','1980-10-12 23:12:36','女'),('1005','李世明','1981-10-12 23:12:36','男'),('1006','赵六','1986-10-12 23:12:36','男'),('1007','田七','1981-10-12 23:12:36','女');
insert  into sc(sid,cid,score) values ('1','001',80),('1','002',60),('1','003',75),('2','001',85),('2','002',70),('3','004',100),('3','001',90),('3','002',55),('4','002',65),('4','003',60);
insert  into course(cid,cName,tid) values ('001','企业管理',3),('002','马克思',3),('003','UML',2),('004','数据库',1),('005','英语',1);
```
__ ORACLE(表+数据)__
```sql
CREATE TABLE student (
  sid varchar2(10) NOT NULL,
  sName varchar2(20) DEFAULT NULL,
  sAge date ,
  sSex varchar2(10) DEFAULT NULL,
  PRIMARY KEY (sid)
) 

CREATE TABLE course (
  cid varchar2(10) NOT NULL,
  cName varchar2(10) DEFAULT NULL,
  tid number(20) DEFAULT NULL,
  PRIMARY KEY (cid)
) 

CREATE TABLE sc (
  sid varchar2(10) DEFAULT NULL,
  cid varchar2(10) DEFAULT NULL,
  score number(10) DEFAULT NULL
)


CREATE TABLE teacher (
  tid number(10) DEFAULT NULL,
  tName varchar2(10) DEFAULT NULL
)

insert  into course(cid,cName,tid) values ('001','企业管理',3);
insert  into course(cid,cName,tid) values ('002','马克思',3);
insert  into course(cid,cName,tid) values ('004','数据库',1);
insert  into course(cid,cName,tid) values ('005','英语',1);

insert  into sc(sid,cid,score) values ('1001','001',80);
insert  into sc(sid,cid,score) values ('1001','002',60);
insert  into sc(sid,cid,score) values ('1001','003',70);
insert  into sc(sid,cid,score) values ('1002','001',85);
insert  into sc(sid,cid,score) values ('1002','002',70);
insert  into sc(sid,cid,score) values ('1003','004',90);
insert  into sc(sid,cid,score) values ('1003','001',90);
insert  into sc(sid,cid,score) values ('1003','002',99);
insert  into sc(sid,cid,score) values ('1004','002',65);
insert  into sc(sid,cid,score) values ('1004','003',50);
insert  into sc(sid,cid,score) values ('1005','005',80);
insert  into sc(sid,cid,score) values ('1005','004',70);
insert  into sc(sid,cid,score) values ('1003','003',10);
insert  into sc(sid,cid,score) values ('1003','005',10);


insert  into student(sid,sName,sAge,sSex) values ('1001','张三丰',to_date('1980-10-12 23:12:36','YYYY-MM-DD HH24:MI:SS'),'男');
insert  into student(sid,sName,sAge,sSex) values ('1002','张无极',to_date('1995-10-12 23:12:36','YYYY-MM-DD HH24:MI:SS'),'男');
insert  into student(sid,sName,sAge,sSex) values ('1003','李奎',to_date('1992-10-12 23:12:36','YYYY-MM-DD HH24:MI:SS'),'女');
insert  into student(sid,sName,sAge,sSex) values ('1004','李元宝',to_date('1980-10-12 23:12:36','YYYY-MM-DD HH24:MI:SS'),'女');
insert  into student(sid,sName,sAge,sSex) values ('1005','李世明',to_date('1981-10-12 23:12:36','YYYY-MM-DD HH24:MI:SS'),'男');
insert  into student(sid,sName,sAge,sSex) values ('1006','赵六',to_date('1986-10-12 23:12:36','YYYY-MM-DD HH24:MI:SS'),'男');
insert  into student(sid,sName,sAge,sSex) values ('1007','田七',to_date('1981-10-12 23:12:36','YYYY-MM-DD HH24:MI:SS'),'女');

insert  into teacher(tid,tName) values (1,'李老师');
insert  into teacher(tid,tName) values (2,'何以琛');
insert  into teacher(tid,tName) values (3,'叶平');

```








## 问题：
__ 1.查询“001”课程比“002”课程成绩高的所有学生的学号;__
```sql
select a.sid from (select sid,score from SC where cid='001') a,(select sid,score 
from SC where cid='002') b 
where a.score>b.score and a.sid=b.sid; 
```
__ 2、查询平均成绩大于60分的同学的学号和平均成绩;__
```sql
select sid,avg(score) 
from sc 
group by sid having avg(score) >60; 
```
__ 3、查询所有同学的学号、姓名、选课数、总成绩;__
```sql 
select Student.sid,Student.Sname,count(SC.cid),sum(score) 
from Student left Outer join SC on Student.sid=SC.sid 
group by Student.sid,Sname 
```
__ 4、查询姓“李”的老师的个数;__
```sql
select count(distinct(Tname)) 
from Teacher 
where Tname like '李%'; 
```
__ 5、查询没学过“叶平”老师课的同学的学号、姓名;__
```sql
select Student.sid,Student.Sname 
from Student 
where sid not in (select distinct( SC.sid) from SC,Course,Teacher where SC.cid=Course.cid and Teacher.tid=Course.tid and Teacher.Tname='叶平'); 
```
__ 6、查询学过“001”并且也学过编号“002”课程的同学的学号、姓名;__
```sql
A:select Student.sid,Student.Sname from Student,SC where Student.sid=SC.sid and SC.cid='001'and exists( Select * from SC as SC_2 where SC_2.sid=SC.sid and SC_2.cid='002'); 
B:SELECT    s.sid,s.sName 
FROM    student s,  (SELECT sid,COUNT(cid) FROM  sc WHERE cid IN ('001','002') GROUP BY sid HAVING COUNT(cid)>=2) t WHERE s.sid = t.sid
```
__ 7、查询学过“叶平”老师所教的所有课的同学的学号、姓名;__
```sql
select sid,Sname 
from Student 
where sid in (select sid from SC ,Course ,Teacher where SC.cid=Course.cid and Teacher.tid=Course.tid and Teacher.Tname='叶平' group by sid having count(SC.cid)=(select count(cid) from Course,Teacher where Teacher.tid=Course.tid and Tname='叶平')); 
```
__ 8、查询课程编号“002”的成绩比课程编号“001”课程低的所有同学的学号、姓名;__
```sql
1>Select sid,Sname from (select Student.sid,Student.Sname,score ,(select score from SC SC_2 where SC_2.sid=Student.sid and SC_2.cid='002') score2 
from Student,SC where Student.sid=SC.sid and cid='001') S_2 where score2 <score; 
2>SELECT s.sid,s.sName FROM student s, 
(SELECT sid,score FROM sc WHERE cid = '001') sc_1,
(SELECT sid,score FROM sc WHERE cid = '002') sc_2
WHERE sc_1.sid = sc_2.sid AND s.sid = sc_2.sid AND sc_2.score < sc_1.score
```
__ 9、查询所有课程成绩小于60分的同学的学号、姓名;__
```sql
select sid,Sname 
from Student 
where sid not in (select Student.sid from Student,SC where S.sid=SC.sid and score>60); 
```
__ 10、查询没有学全所有课的同学的学号、姓名;__
```sql
1>
select Student.sid,Student.Sname 
from Student,SC 
where Student.sid=SC.sid group by Student.sid,Student.Sname having count(cid) <(select count(cid) from Course);
2>
SELECT s.sid,s.sname FROM student s,
(SELECT sid,COUNT(cid) FROM sc GROUP BY sid HAVING COUNT(cid) < (SELECT COUNT(cid) FROM course) )t
WHERE s.sid  = t.sid 
```
__ 11、查询至少有一门课与学号为“1001”的同学所学相同的同学的学号和姓名;__
```sql
select sid,Sname from Student,SC where Student.sid=SC.sid and cid in (select cid from SC where sid='1001'); 
```
__ 13、把“SC”表中“叶平”老师教的课的成绩都更改为此课程的平均成绩;__
```sql
UPDATE sc,(SELECT c.cid,AVG(score) avgs FROM sc,course c,teacher t WHERE sc.cid = c.cid AND 
c.tid = t.tid AND t.tName = '叶平' GROUP BY c.cid)sc_2 SET sc.score = sc_2.avgs WHERE sc.cid = sc_2.cid
```

__ 14、查询和“1002”号的同学学习的课程完全相同的其他同学学号和姓名;__
```sql
select sid from SC where cid in (select cid from SC where sid='1002') 
group by sid having count(*)=(select count(*) from SC where sid='1002'); 
```

__ 15、删除学习“叶平”老师课的SC表记录;__
```sql
DELETE  FROM sc  WHERE sc.cid IN (SELECT sc.cid FROM course c ,teacher t WHERE sc.cid = c.cid AND c.tid = t.tid AND t.tName = '叶平')
```

__ 17、按平均成绩从高到低显示所有学生的“数据库”、“企业管理”、“英语”三门的课程成绩，按如下形式显示： __学生ID,,数据库,企业管理,英语,有效课程数,有效平均分 
```sql
SELECT sid as 学生ID 
,(SELECT score FROM SC WHERE SC.sid=t.sid AND cid='004') AS 数据库 
,(SELECT score FROM SC WHERE SC.sid=t.sid AND cid='001') AS 企业管理 
,(SELECT score FROM SC WHERE SC.sid=t.sid AND cid='005') AS 英语 
,COUNT(*) AS 有效课程数, AVG(t.score) AS 平均成绩 
FROM SC AS t 
GROUP BY sid 
ORDER BY avg(t.score) 
```
__ 18、查询各科成绩最高和最低的分：以如下形式显示：课程ID，最高分，最低分__
```
select cid "课程ID",max(score) "最高分",min(score) "最低分" from sc group by cid
```
__ 19、按各科平均成绩从低到高和及格率的百分数从高到低排序__
```
oracle>
SELECT t.cid AS 课程号,MAX(course.Cname)AS 课程名,nvl(AVG(score),0) AS 平均成绩 
,100 * SUM(CASE WHEN nvl(score,0)>=60 THEN 1 ELSE 0 END)/COUNT(*) AS 及格百分数 
FROM SC T,Course 
WHERE t.cid=course.cid 
GROUP BY t.cid 
ORDER BY 100 * SUM(CASE WHEN nvl(score,0)>=60 THEN 1 ELSE 0 END)/COUNT(*) DESC 
Mysql>
SELECT t.cid AS 课程号,MAX(course.Cname)AS 课程名,IFNULL(AVG(score),0) AS 平均成绩 
,100 * SUM(CASE WHEN IFNULL(score,0)>=60 THEN 1 ELSE 0 END)/COUNT(*) AS 及格百分数 
FROM SC T,Course 
WHERE t.cid=course.cid 
GROUP BY t.cid 
ORDER BY 100 * SUM(CASE WHEN IFNULL(score,0)>=60 THEN 1 ELSE 0 END)/COUNT(*) DESC 
```


__ 20、查询如下课程平均成绩和及格率的百分数(用"1行"显示): __企业管理（001），马克思（002），OO&UML（003），数据库（004） 
```
SELECT SUM(CASE WHEN cid ='001' THEN score ELSE 0 END)/SUM(CASE cid WHEN '001' THEN 1 ELSE 0 END) AS 企业管理平均分 
,100 * SUM(CASE WHEN cid = '001' AND score >= 60 THEN 1 ELSE 0 END)/SUM(CASE WHEN cid = '001' THEN 1 ELSE 0 END) AS 企业管理及格百分数 
,SUM(CASE WHEN cid = '002' THEN score ELSE 0 END)/SUM(CASE cid WHEN '002' THEN 1 ELSE 0 END) AS 马克思平均分 
,100 * SUM(CASE WHEN cid = '002' AND score >= 60 THEN 1 ELSE 0 END)/SUM(CASE WHEN cid = '002' THEN 1 ELSE 0 END) AS 马克思及格百分数 
,SUM(CASE WHEN cid = '003' THEN score ELSE 0 END)/SUM(CASE cid WHEN '003' THEN 1 ELSE 0 END) AS UML平均分 
,100 * SUM(CASE WHEN cid = '003' AND score >= 60 THEN 1 ELSE 0 END)/SUM(CASE WHEN cid = '003' THEN 1 ELSE 0 END) AS UML及格百分数 
,SUM(CASE WHEN cid = '004' THEN score ELSE 0 END)/SUM(CASE cid WHEN '004' THEN 1 ELSE 0 END) AS 数据库平均分 
,100 * SUM(CASE WHEN cid = '004' AND score >= 60 THEN 1 ELSE 0 END)/SUM(CASE WHEN cid = '004' THEN 1 ELSE 0 END) AS 数据库及格百分数 
FROM SC 
```
__21、查询不同老师所教不同课程平均分从高到低显示 要求显示：教师ID,教师姓名，课程ID,课程名称，平均成绩__
```
SELECT MAX(t.tid) "教师ID",MAX(t.tName) "教师姓名",c.cid "课程ID", MAX(c.cName) "课程名称" ,AVG(sc.score) "平均成绩"
FROM sc,course c,teacher t WHERE sc.cid = c.cid AND c.tid = t.tid GROUP BY c.tid,c.cid
ORDER BY AVG(sc.score) DESC
```
__23、统计列印各科成绩,各分数段人数:课程ID,课程名称,[100-85],[85-70],[70-60],[ <60] __
```
SELECT SC.cid as 课程ID, Cname as 课程名称 
,SUM(CASE WHEN score BETWEEN 85 AND 100 THEN 1 ELSE 0 END) AS [100 - 85] 
,SUM(CASE WHEN score BETWEEN 70 AND 85 THEN 1 ELSE 0 END) AS [85 - 70] 
,SUM(CASE WHEN score BETWEEN 60 AND 70 THEN 1 ELSE 0 END) AS [70 - 60] 
,SUM(CASE WHEN score < 60 THEN 1 ELSE 0 END) AS [60 -] 
FROM SC,Course 
where SC.cid=Course.cid 
GROUP BY SC.cid,Cname; 
```
__26、查询每门课程被选修的学生数 __
```
select cid,count(sid) from sc group by cid; 
```
__ 27、查询出只选修了一门课程的全部学生的学号和姓名 __
```
select SC.sid,Student.Sname,count(cid) AS 选课数 
from SC ,Student 
where SC.sid=Student.sid group by SC.sid ,Student.Sname having count(cid)=1; 
```
__ 28、查询男生、女生人数__
```
Select count(Ssex) as 男生人数 from Student group by Ssex having Ssex='男'; 
Select count(Ssex) as 女生人数 from Student group by Ssex having Ssex='女'; 
```
__ 29、查询姓“张”的学生名单 __
```
SELECT Sname FROM Student WHERE Sname like '张%'; 
```
__ 30、查询同名同性学生名单，并统计同名人数__
```
SELECT sName,sSex ,COUNT(*) FROM student GROUP BY sName,sSex HAVING COUNT(*) > 1
```
__ 31、1981年出生的学生名单(注：Student表中Sage列的类型是datetime)__
```
Mysql>
select Sname, CONVERT(char (11),DATEPART(year,Sage)) as age 
from student 
where CONVERT(char(11),DATEPART(year,Sage))='1981'; 
Oracle>
select * from student where substr(to_char(sage,'yyyy-MM-dd'),1,4)= '1981'
```
__ 32、查询每门课程的平均成绩，结果按平均成绩升序排列，平均成绩相同时，按课程号降序排列__
```
Select cid,Avg(score) from SC group by cid order by Avg(score),cid DESC ; 
```
__ 33、查询平均成绩大于85的所有学生的学号、姓名和平均成绩 __
```
select Sname,SC.sid ,avg(score) 
from Student,SC 
where Student.sid=SC.sid group by SC.sid,Sname having avg(score)>85; 
```
__ 34、查询课程名称为“数据库”，且分数低于60的学生姓名和分数__
```
Select Sname,isnull(score,0) 
from Student,SC,Course 
where SC.sid=Student.sid and SC.cid=Course.cid and Course.Cname='数据库'and score <60; 
```
__ 35、查询所有学生的选课情况; __
```
SELECT SC.sid,SC.cid,Sname,Cname 
FROM SC,Student,Course 
where SC.sid=Student.sid and SC.cid=Course.cid ; 
```
__ 36、查询任何一门课程成绩在70分以上的姓名、课程名称和分数; __
```
SELECT distinct student.sid,student.Sname,SC.cid,SC.score 
FROM student,Sc 
WHERE SC.score>=70 AND SC.sid=student.sid; 
```
__ 37、查询不及格的课程，并按课程号从大到小排列 __
```
select cid from sc where scor e <60 order by cid ; 
```
__ 38、查询课程编号为003且课程成绩在80分以上的学生的学号和姓名; __
```
select SC.sid,Student.Sname from SC,Student where SC.sid=Student.sid and Score>80 and cid='003'; 
```
__ 39、求选了课程的学生人数 __
```
select count(*) from sc; 
```
__ 40、查询选修“叶平”老师所授课程的学生中，成绩最高的学生姓名及其成绩 __
```
select Student.Sname,score 
from Student,SC,Course C,Teacher 
where Student.sid=SC.sid and SC.cid=C.cid and C.tid=Teacher.tid and Teacher.Tname='叶平' and SC.score=(select max(score)from SC where cid=C.cid ); 
```
__ 41、查询各个课程及相应的选修人数 __
```
select count(*) from sc group by cid; 
```
__ 42、查询不同课程成绩相同的学生的学号、课程号、学生成绩 __
```
select distinct A.sid,B.score from SC A ,SC B where A.Score=B.Score and A.cid <>B.cid ; 
```
__ 43、查询每门功课成绩最好的前两名 __
```
SELECT * 
FROM sc t1
WHERE (
  SELECT COUNT(*)
  FROM sc t2
  WHERE t1.cid=t2.cid
  AND t2.score>=t1.score
) <=2 ORDER BY t1.cid
```
__ 44、统计每门课程的学生选修人数（超过10人的课程才统计）。要求输出课程号和选修人数，查询结果按人数降序排__列，若人数相同，按课程号升序排列 
```
SELECT  cid "课程号",COUNT(*) "选修人数" FROM sc GROUP BY cid HAVING COUNT(*) >10  ORDER BY COUNT(*) DESC,cid 
```

__ 45、检索至少选修两门课程的学生学号 __
```
select sid 
from sc 
group by sid 
having count(*) > = 2 
```
__ 46、查询全部学生都选修的课程的课程号和课程名 __
```
SELECT s.sName,c.cName, COUNT(*) FROM student s,course c, sc WHERE s.sid = sc.sid AND sc.cid = c.cid GROUP BY sc.cid HAVING COUNT(*) = (SELECT COUNT(*) FROM student)
```
__ 47、查询没学过“叶平”老师讲授的任一门课程的学生姓名 __
```
SELECT DISTINCT Sname FROM Student WHERE sid NOT IN (SELECT sid FROM Course,Teacher,SC WHERE Course.tid=Teacher.tid AND SC.cid=course.cid AND Tname='叶平'); 
```
__ 48、查询两门以上不及格课程的同学的学号及其平均成绩 __
```
select sid,avg(ifnull(score,0)) from SC where sid in (select sid from SC where score <60 group by sid having count(*)>2)group by sid; 
```
__ 49、检索“004”课程分数小于60，按分数降序排列的同学学号 __
```
select sid from SC where cid='004'and score <60 order by score desc; 
```
__ 50、删除“1002”同学的“001”课程的成绩 __
```
delete from Sc where sid='1002' and cid='001'; 
```