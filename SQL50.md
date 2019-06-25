# SQL操作练习50

题目来源：[简书-50道SQL练习题及答案与详细分析](https://www.jianshu.com/p/476b52ee4f1b)

```
#创建学生表
CREATE TABLE Student(
	SId varchar(10) DEFAULT NULL,
	Sname varchar(10) DEFAULT NULL,
	Sage date DEFAULT NULL,
	Ssex varchar(10) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
INSERT INTO Student VALUES('01' , '赵雷' , '1990-01-01' , '男');
INSERT INTO Student VALUES('02' , '钱电' , '1990-12-21' , '男');
INSERT INTO Student VALUES('03' , '孙风' , '1990-12-20' , '男');
INSERT INTO Student VALUES('04' , '李云' , '1990-12-06' , '男');
INSERT INTO Student VALUES('05' , '周梅' , '1991-12-01' , '女');
INSERT INTO Student VALUES('06' , '吴兰' , '1992-01-01' , '女');
INSERT INTO Student VALUES('07' , '郑竹' , '1989-01-01' , '女');
INSERT INTO Student VALUES('09' , '张三' , '2017-12-20' , '女');
INSERT INTO Student VALUES('10' , '李四' , '2017-12-25' , '女');
INSERT INTO Student VALUES('11' , '李四' , '2012-06-06' , '女');
INSERT INTO Student VALUES('12' , '赵六' , '2013-06-13' , '女');
INSERT INTO Student VALUES('13' , '孙七' , '2014-06-01' , '女');
```

```
#创建课程表
CREATE TABLE Course (
  Cid varchar(10) DEFAULT NULL,
  Cname varchar(10) DEFAULT NULL,
  Tid varchar(10) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
INSERT INTO Course VALUES('01' , '语文' , '02');
INSERT INTO Course VALUES('02' , '数学' , '01');
INSERT INTO Course VALUES('03' , '英语' , '03');
```

```
#创建教师表
CREATE TABLE Teacher (
  Tid varchar(10) DEFAULT NULL,
  Tname varchar(10) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
INSERT INTO Teacher VALUES('01' , '张三');
INSERT INTO Teacher VALUES('02' , '李四');
INSERT INTO Teacher VALUES('03' , '王五');
```

```
#创建成绩表
CREATE TABLE Scores (
  Sid varchar(10) DEFAULT NULL,
  Cid varchar(10) DEFAULT NULL,
  Score decimal(18,1)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
INSERT INTO Scores VALUES('01' , '01' , 80);
INSERT INTO Scores VALUES('01' , '02' , 90);
INSERT INTO Scores VALUES('01' , '03' , 99);
INSERT INTO Scores VALUES('02' , '01' , 70);
INSERT INTO Scores VALUES('02' , '02' , 60);
INSERT INTO Scores VALUES('02' , '03' , 80);
INSERT INTO Scores VALUES('03' , '01' , 80);
INSERT INTO Scores VALUES('03' , '02' , 80);
INSERT INTO Scores VALUES('03' , '03' , 80);
INSERT INTO Scores VALUES('04' , '01' , 50);
INSERT INTO Scores VALUES('04' , '02' , 30);
INSERT INTO Scores VALUES('04' , '03' , 20);
INSERT INTO Scores VALUES('05' , '01' , 76);
INSERT INTO Scores VALUES('05' , '02' , 87);
INSERT INTO Scores VALUES('06' , '01' , 31);
INSERT INTO Scores VALUES('06' , '03' , 34);
INSERT INTO Scores VALUES('07' , '02' , 89);
INSERT INTO Scores VALUES('07' , '03' , 98);
```

## 练习题目

1. 查询" 01 "课程比" 02 "课程成绩高的学生的信息及课程分数

思路：</br>先得到成绩表中每个学生两个课程分别的得分表，然后根据学生id合并，再找出课程1大于课程2的学生，最后与学生信息的表合并。

```
SELECT * FROM 
(SELECT t1.Sid,Class1,Class2 from
(SELECT Sid,Score AS Class1 from Scores WHERE Cid = '01') AS t1,
(SELECT Sid,Score AS Class2 from Scores WHERE Cid = '02') AS t2 WHERE t1.Sid = t2.Sid AND t1.Class1>t2.Class2) AS SScore LEFT JOIN Student ON SScore.Sid = Student.Sid;
```

2. 查询同时存在" 01 "课程和" 02 "课程的情况

思路：</br>用存在课程1的表去inner join存在课程2的表

```
SELECT * FROM (SELECT Sid,Cid AS Class1 from Scores WHERE Cid = '01') AS t1 INNER JOIN 
(SELECT Sid,Cid AS Class2 from Scores WHERE Cid = '02') AS t2 ON t1.Sid = t2.Sid;
```

或者直接用id来连接两表

```
SELECT * FROM 
(SELECT Sid,Cid AS Class1 from Scores WHERE Cid = '01') AS t1,
(SELECT Sid,Cid AS Class2 from Scores WHERE Cid = '02') AS t2 where t1.Sid = t2.Sid;
```

3. 查询存在" 01 "课程但可能不存在" 02 "课程的情况(不存在时显示为 null )

思路：</br>用存在课程1的表去left join存在课程2的表

```
SELECT * FROM 
(SELECT Sid,Cid AS Class1 from Scores WHERE Cid = '01') AS t1 LEFT JOIN
(SELECT Sid,Cid AS Class2 from Scores WHERE Cid = '02') AS t2 ON t1.Sid = t2.Sid;
```

4. 查询不存在" 01 "课程但存在" 02 "课程的情况

思路：</br>和上一题类似，用存在课程1的表去right join存在课程2的表

```
SELECT * FROM 
(SELECT Sid,Cid AS Class1 from Scores WHERE Cid = '01') AS t1 RIGHT JOIN
(SELECT Sid,Cid AS Class2 from Scores WHERE Cid = '02') AS t2 ON t1.Sid = t2.Sid;
```

5. 查询平均成绩大于等于 60 分的同学的学生编号和学生姓名和平均成绩

思路：</br>分组计算每个学生的平均成绩，再用编号去找学生姓名

```
SELECT * from 
(SELECT Sid, Sname from Student) AS t1 RIGHT JOIN 
(SELECT Sid, Avg(Score) AS AVG FROM Scores group by Sid HAVING AVG > 60) AS t2 ON t1.Sid = t2.Sid;
```

6. 查询在 Scores 表存在成绩的学生信息

思路：</br>首先找有哪几个学生，再用id去信息里面查找

```
SELECT * FROM Student, (SELECT DISTINCT Sid FROM Scores) AS t1 WHERE Student.Sid = t1.Sid;
```

7. 查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩(没成绩的显示为 null )

思路：</br>首先在Score表里找学生编号、选课总数、所有课程的总成绩，再用id去信息里面查找

```
SELECT * from
(SELECT Sid,Sname from Student) AS tb1 LEFT JOIN
(SELECT Sid, count(Cid) AS CourseNum, sum(Score) AS TotalScore FROM Scores GROUP BY Sid) AS tb2 ON tb1.Sid = tb2.Sid;
```

8. 查有成绩的学生信息

思路：</br>本质来讲和第6题是一样的。这里可以换一种方法做，使用in/exists

*tips:</br>EXISTS用于检查子查询是否至少会返回一行数据，该子查询实际上并不返回任何数据，而是返回值True或False.</br>结论：IN()适合B表比A表数据小的情况</br>结论：EXISTS()适合B表比A表数据大的情况</br>in 是把外表和内表作hash join，而exists是对外表作loop，每次loop再对内表进行查询。*

```
#in
SELECT * FROM Student WHERE Student.Sid in (SELECT Sid FROM Scores);

#exists
SELECT * FROM Student WHERE EXISTS (SELECT Scores.Sid FROM Scores where Scores.Sid = Student.Sid);
``` 

9. 查询「李」姓老师的数量

```
SELECT count(*) AS LI_lastname FROM Teacher WHERE Tname LIKE '李%'; 
```

10. 查询学过「张三」老师授课的同学的信息

思路：</br>首先查询张三老师授课的课程id，再由课程id去成绩表中查询有成绩的学生id，最后查询学生表中的学生信息

```
SELECT * FROM Student WHERE Student.Sid IN
(SELECT Sid FROM Scores WHERE Scores.Cid IN
(SELECT Cid FROM Course WHERE Course.Tid IN 
(SELECT Tid FROM Teacher WHERE Tname = '张三')));
```

思路：</br>其实可以使用多表联合查询

```
SELECT Student.* FROM Student,Scores,Course,Teacher
WHERE Teacher.Tname = '张三'
AND Course.Tid = Teacher.Tid
AND Scores.Cid = Course.Cid
AND Student.Sid = Scores.Sid;
```

11. 查询没有学全所有课程的同学的信息

```
select * from Student where Sid in
(select Sid from Scores group by Sid
having Count(Cid) != (select count(Cid) from Course));
```

*tips:</br>concat()拼接字符串</br>concat_ws()拼接字符串，可一次性定义分隔符</br>group_concat()拼接groupby以后的结果，可定义分隔符以及变量顺序*

12. 查询至少有一门课与学号为" 01 "的同学所学相同的同学的信息

```
SELECT * FROM Student WHERE Student.Sid IN
(SELECT Sid FROM Scores WHERE Sid != '01' AND Cid IN (SELECT Cid FROM Scores WHERE Sid = '01'));
```

13. 查询和" 01 "号的同学学习的课程 完全相同的其他同学的信息

思路：</br>用group_concat()获得每个学生的所有课程字段，然后和01同学比较，相等的同学再去查询学生信息。

```
SELECT * FROM Student WHERE Student.Sid IN
(SELECT Sid FROM (SELECT Sid,group_concat(Cid ORDER BY Cid) AS CourseName FROM Scores GROUP BY Sid) AS tb1 
WHERE tb1.CourseName = (SELECT tb1.CourseName FROM (SELECT Sid,group_concat(Cid ORDER BY Cid) AS CourseName FROM Scores GROUP BY Sid) AS tb1 WHERE tb1.Sid = '01') 
AND Sid != '01');
```

14. 查询没学过"张三"老师讲授的任一门课程的学生姓名

思路：</br>先获取张三老师讲授的课程id，然后获取成绩表中course in
这些课程id 的学生，再去查询这些学生以外的学生信息。

```
SELECT * FROM Student WHERE Student.Sid NOT IN
(SELECT Sid FROM Teacher,Course,Scores WHERE Teacher.Tname = '张三' AND Course.Tid = Teacher.Tid AND Scores.Cid = Course.Cid);
```

15. 查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩

思路：</br>首先找到所有不及格的记录，然后按学生id分组计数大于等于2次的学生id，然后在去查找其姓名的平均成绩

```
SELECT tb2.*,Student.Sname FROM
(SELECT Sid,avg(Score) AS AVG from Scores GROUP BY Sid
HAVING Sid IN
(SELECT Sid from (SELECT * FROM Scores WHERE Scores.Score < 60) AS tb1 GROUP BY Sid
HAVING count(Cid) >= 2)) AS tb2,Student 
WHERE tb2.Sid = Student.Sid;
```

16. 检索“01”课程分数小于60，按分数降序排列的学生信息

思路：</br>首先找到01课程分数不及格的记录，然后按学生id去查找学生信息

```
SELECT Student.*, Scores.Score from Student,Scores 
WHERE Scores.Score < 60 
AND Scores.Cid = '01' 
AND Scores.Sid = Student.Sid ORDER BY Scores.Score DESC;
```

17. 按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩

```
SELECT Scores.*, tb1.AVG FROM Scores,(SELECT Sid,round(avg(Score),1) AS AVG from Scores GROUP BY Sid) AS tb1 
WHERE Scores.Sid = tb1.Sid ORDER BY tb1.AVG DESC;
```

18. 查询各科成绩最高分、最低分和平均分

以如下形式显示：课程 ID，课程 name，最高分，最低分，平均分，及格率，中等率，优良率，优秀率
及格为>=60，中等为：70-80，优良为：80-90，优秀为：>=90</br>
要求输出课程号和选修人数，查询结果按人数降序排列，若人数相同，按课程号升序排列

```
SELECT Cid,max(Score) AS MAX,min(Score) AS MIN,round(avg(Score),1) AS AVG, count(*) AS Pnum,
sum(CASE WHEN Score >=60 THEN 1 ELSE 0 END)/count(*) AS '60',
sum(CASE WHEN Score >=70 AND Score < 80 THEN 1 ELSE 0 END)/count(*) AS '70-80',
sum(CASE WHEN Score >=80 AND Score < 90 THEN 1 ELSE 0 END)/count(*) AS '80-90',
sum(CASE WHEN Score >=90 THEN 1 ELSE 0 END)/count(*) AS '90'
FROM Scores GROUP BY Cid ORDER BY Pnum DESC, Cid ASC;
```

*tips:</br>case when ? then ? else ? end*

19. 按各科成绩进行排序，并显示排名， Score 重复时保留名次空缺

思路：</br>总结在20题

20. 按各科成绩进行排序，并显示排名， Score 重复时合并名次

思路：</br>使用变量

```
#分数相同时，排名按顺序计算
SElECT Sid,Cid,Score,
CASE 
WHEN @prevcid = Cid THEN @curRank := @curRank + 1
WHEN @prevcid := Cid THEN @curRank := @curRank - @curRank + 1
END AS rank FROM Scores,(SELECT @curRank :=0, @prevcid := NULL) r ORDER BY Cid, Score DESC;

#分数相同时，排名相同，下一排名接着排
SElECT Sid,Cid,Score, 
CASE 
WHEN @prevcid = Cid AND @prevscore = Score THEN @curRank := @curRank
WHEN @prevcid = Cid AND @prevscore := Score THEN @curRank := @curRank + 1
WHEN @prevcid := Cid THEN @curRank := @curRank - @curRank + 1
END AS rank FROM Scores,(SELECT @curRank :=0,@prevscore :=0, @prevcid := NULL) r ORDER BY Cid, Score DESC;

#分数相同时，排名相同，下一排名跳过占位排
SELECT Sid,Cid,Score,finalrank FROM
(SELECT Sid,Cid,Score,
@2curRank := rank,
@2incRank := incrank,
IF(@2prevRank = Score, @2curRank, @2incRank) AS finalrank,
@2prevRank := Score
FROM
(SElECT Sid,Cid,Score, 
CASE 
WHEN @prevcid = Cid AND @prevscore = Score THEN @curRank := @curRank
WHEN @prevcid = Cid AND @prevscore := Score THEN @curRank := @curRank +1
WHEN @prevcid := Cid THEN @curRank := @curRank - @curRank + 1
END AS rank, 
CASE 
WHEN @prevcid_c = Cid THEN @incRank := @incRank +1
WHEN @prevcid_c := Cid THEN @incRank := @incRank - @incRank + 1
END AS incrank FROM Scores,(SELECT @incRank :=0, @curRank :=0, @prevscore:=0, @prevcid := NULL, @prevcid_c := NULL) r ORDER BY Cid, Score DESC) s,(
SELECT @2curRank :=NULL, @2prevRank := NULL, @2incRank := NULL) t) p;
```

21. 查询学生的总成绩，并进行排名，总分重复时保留名次空缺

思路：</br>分数相同时，排名相同，下一排名跳过占位排，但是目前的数据无法体现

```
SELECT Sid,Total,Rank
FROM (SELECT Sid,Total,
@curRank := IF(@preScore = Total, @curRank, @incRank) AS Rank,
@incRank := @incRank+1,
@preScore := Total
FROM (SELECT Sid,sum(Score) AS Total from Scores GROUP BY Sid ORDER BY Total DESC) p,(SELECT @curRank := 0,@incRank:=1, @preScore:=NULL) q) r;
```

22. 统计各科成绩各分数段人数：课程编号，课程名称，[100-85]，[85-70]，[70-60]，[60-0] 及所占百分比

```
SELECT * FROM
(SELECT Cid,
sum(CASE WHEN Score BETWEEN 0 and 60 THEN 1 ELSE 0 END) AS '[60-0]',
sum(CASE WHEN Score BETWEEN 0 and 60 THEN 1 ELSE 0 END)/count(*) AS '[60-0]/perc',
sum(CASE WHEN Score BETWEEN 60 and 70 THEN 1 ELSE 0 END) AS '[70-60]',
sum(CASE WHEN Score BETWEEN 60 and 70 THEN 1 ELSE 0 END)/count(*) AS '[70-60]/perc',
sum(CASE WHEN Score BETWEEN 70 and 85 THEN 1 ELSE 0 END) AS '[85-70]',
sum(CASE WHEN Score BETWEEN 70 and 85 THEN 1 ELSE 0 END)/count(*) AS '[85-70]/perc',
sum(CASE WHEN Score BETWEEN 85 and 100 THEN 1 ELSE 0 END) AS '[100-85]',
sum(CASE WHEN Score BETWEEN 85 and 100 THEN 1 ELSE 0 END)/count(*) AS '[100-85]/perc'
FROM Scores GROUP BY Scores.Cid) AS p LEFT JOIN Course ON p.Cid = Course.Cid;
```

23. 查询各科成绩前三名的记录

思路：</br>按照最简单的方法得到排序以后（见第20题），再取前三

```
SELECT Cid,Sid,Score FROM 
(SElECT Sid,Cid,Score,
CASE 
WHEN @prevcid = Cid THEN @curRank := @curRank + 1
WHEN @prevcid := Cid THEN @curRank := @curRank - @curRank + 1
END AS rank FROM Scores,(SELECT @curRank :=0, @prevcid := NULL) r ORDER BY Cid, Score DESC) p WHERE rank <= 3;
```

24. 查询每门课程被选修的学生数

思路：</br>分组计数

```
SELECT Cid, count(Sid) AS StudentNum FROM Scores GROUP BY Cid;
```

25. 查询出只选修两门课程的学生学号和姓名

思路：</br>分组计数后，选择课程数等于2的学生，再去找学生信息

```
SELECT Sid,Sname FROM Student WHERE Sid IN (SELECT Sid FROM Scores GROUP BY Sid
HAVING count(*) = 2);
```

26. 查询男生、女生人数

思路：</br>分组计数

```
SELECT Ssex AS 性别,count(*) AS 人数 FROM Student GROUP BY Ssex;
```

27. 查询名字中含有「风」字的学生信息

```
SELECT * FROM Student WHERE Sname LIKE '%风%';
```

28. 查询同名同姓学生名单，并统计同名人数

思路：</br>分组计数

```
SELECT Sname,count(*) AS Num FROM Student GROUP BY Sname
HAVING Num >1;
```

29. 查询 1990 年出生的学生名单

```
SELECT * FROM Student WHERE YEAR(Sage) = 1990;
```

30. 查询每门课程的平均成绩，结果按平均成绩降序排列，平均成绩相同时，按课程编号升序排列

```
SELECT Cid,avg(Score) AS AVG FROM Scores GROUP BY Cid ORDER BY AVG DESC, Cid;
```

31. 查询平均成绩大于等于 85 的所有学生的学号、姓名和平均成绩

```
SElECT p.Sid,Student.Sname,p.AVG FROM Student,
(SELECT Sid,avg(Score) AS AVG FROM Scores GROUP BY Sid
HAVING AVG >= 85) AS p WHERE p.Sid = Student.Sid;
```

32. 查询课程名称为「数学」，且分数低于 60 的学生姓名和分数

```
SElECT Student.Sname,Scores.Score FROM Student,Scores,Course 
WHERE Course.Cname = '数学'
AND Scores.Cid = Course.Cid
AND Scores.Score < 60
AND Scores.Sid = Student.Sid;
```

33. 查询所有学生的课程及分数情况（存在学生没成绩，没选课的情况）

```
SElECT Sname,Cid,Score FROM Student LEFT JOIN Scores
ON Scores.Sid = Student.Sid;
```

34. 查询任何一门课程成绩在 70 分以上的姓名、课程名称和分数

```
SElECT Sname,Cname,Score FROM Student,Course,Scores 
WHERE Score>70
AND Scores.Cid = Course.Cid
AND Scores.Sid = Student.Sid;
```

35. 查询不及格的课程

```
SELECT Cname FROM Course WHERE Cid IN (SElECT DISTINCT Cid FROM Scores WHERE Score<60);
```

36. 查询课程编号为 01 且课程成绩在 80 分以上的学生的学号和姓名

思路：</br>分组计数

```
SELECT Sid, Sname FROM Student WHERE Sid IN (SELECT Sid FROM Scores WHERE Cid = '01' AND Score >= 80);
```

37. 求每门课程的学生人数

思路：</br>同24题

38. 成绩不重复，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩

```
SELECT Student.*, Score FROM Scores,Course,Teacher,Student WHERE Tname = '张三' 
AND Teacher.Tid = Course.Tid
AND Scores.Cid = Course.Cid
AND Scores.Sid = Student.Sid ORDER BY Score DESC LIMIT 1;
```

39. 成绩有重复的情况下，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩

先伪造一个分数：
```
UPDATE Scores SET Score=90 WHERE Sid = "07"
AND Cid ="02";
```
思路：</br>找到最高分，然后找到这个得分的学生

```
SELECT Student.*, Score FROM Scores,Student WHERE Scores.Sid = Student.Sid
AND Score = (SELECT max(Score) FROM Scores,Course,Teacher WHERE Tname = '张三' 
AND Teacher.Tid = Course.Tid
AND Scores.Cid = Course.Cid);
```

40. 若有学生的多门课程成绩相同，查询该生编号、课程编号、学生成绩

```
SELECT DISTINCT * FROM (SELECT a.Sid,a.Cid,a.Score FROM Scores AS a
INNER JOIN Scores AS b
ON a.Sid = b.Sid
WHERE a.Score = b.Score
AND a.Cid != B.Cid) p;
```

41. 查询每门功成绩最好的前两名

思路：</br>见第23题

```
SELECT Cid,Sid,Score FROM 
(SElECT Sid,Cid,Score,
CASE 
WHEN @prevcid = Cid THEN @curRank := @curRank + 1
WHEN @prevcid := Cid THEN @curRank := @curRank - @curRank + 1
END AS rank FROM Scores,(SELECT @curRank :=0, @prevcid := NULL) r ORDER BY Cid, Score DESC) p WHERE rank <= 2;
```

42. 统计每门课程的学生选修人数（超过 5 人的课程才统计）。

```
SELECT Cid,count(*) AS StudentNum FROM Scores GROUP BY Cid
HAVING StudentNum > 5;
```

43. 检索至少选修两门课程的学生学号

```
SELECT Sid FROM Scores GROUP BY Sid
HAVING count(*) >= 2;
```

44. 查询选修了全部课程的学生信息

```
SELECT * FROM Student
WHERE Sid IN (SELECT Sid FROM Scores GROUP BY Sid
HAVING count(*) = (SELECT DISTINCT count(Cid) FROM Course));
```

45. 查询各学生的年龄，只按年份来算

```
SELECT Sid,year(curdate())-year(Sage) AS CurrentAge FROM Student;
```

46. 按照出生日期来算，当前月日 < 出生年月的月日则年龄减一

```
SELECT Sid,timestampdiff(year,Sage,curdate()) AS CurrentAge FROM Student;
```

47. 查询本周过生日的学生

```
SELECT * FROM Student
WHERE weekofyear(Sage) = weekofyear(curdate());
```

48. 查询下周过生日的学生

```
SELECT * FROM Student
WHERE weekofyear(Sage) = weekofyear(curdate())+1;
```

49. 查询本月过生日的学生

```
SELECT * FROM Student
WHERE month(Sage) = month(curdate());
```

50. 查询下月过生日的学生

```
SELECT * FROM Student
WHERE month(Sage) = month(curdate())+1;
```

## 总结

1. 关于SQL内置函数

>不同的SQL工具内置函数会有不同，熟悉当前使用数据库常用的一些内置函数可以更方便快捷的支持查询，比如concat()，timestampdiff()等。要学会查找便捷的函数直接做查询，而不是自己去编写复杂的计算。

2. 尽可能考虑所有可能，避免漏检；在练习时尝试不同的方法解题。

>有些特殊情况在构造的模拟数据中可能不会出现，但是一旦出现，就会发现查询条件的缺陷，所以需要模拟各种情况的测试数据，也要了解不同查询方法的细小差异，才能在真正使用时做到“万无一失”。（不过这个更多是经验积累的过程，并且应该在保证准确性的情况下去提高查询速度）

3. 学会使用变量

>需要增加SQL中的变量练习，加深了解和使用。

4. 保持逻辑清晰

>首先理清思路在“纸上”写出查询逻辑，再转化为SQL语言，避免在直接编写的过程中出现逻辑混乱。