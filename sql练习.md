---
title: sql练习
date: 11/5/2022 1:05:34 AM 
categories: 作业
comments: "true"
---
# 问题 #

```
		
		create database bigdata;
		use bigdata;
		
		--部门表
		dept部门表(deptno部门编号/dname部门名称/loc地点)
		create table dept (
		    deptno numeric(2),
		    dname varchar(14),
		    loc varchar(13)
		);
		
		insert into dept values (10, 'ACCOUNTING', 'NEW YORK');
		insert into dept values (20, 'RESEARCH', 'DALLAS');
		insert into dept values (30, 'SALES', 'CHICAGO');
		insert into dept values (40, 'OPERATIONS', 'BOSTON');
		
		--工资等级表
		salgrade工资等级表(grade 等级/losal此等级的最低/hisal此等级的最高)
		create table salgrade (
		    grade numeric,
		    losal numeric,
		    hisal numeric
		);
		
		insert into salgrade values (1, 700, 1200);
		insert into salgrade values (2, 1201, 1400);
		insert into salgrade values (3, 1401, 2000);
		insert into salgrade values (4, 2001, 3000);
		insert into salgrade values (5, 3001, 9999);
		
		
		--员工表
		emp员工表(empno员工号/ename员工姓名/job工作/mgr上级编号/hiredate受雇日期/sal薪金/comm佣金/deptno部门编号)
		工资 ＝ 薪金 ＋ 佣金
		
		create table emp (
		    empno numeric(4) not null,
		    ename varchar(10),
		    job varchar(9),
		    mgr numeric(4),
		    hiredate datetime,
		    sal numeric(7, 2),
		    comm numeric(7, 2),
		    deptno numeric(2)
		);
		
		
		
		insert into emp values (7369, 'SMITH', 'CLERK', 7902, '1980-12-17', 800, null, 20);
		insert into emp values (7499, 'ALLEN', 'SALESMAN', 7698, '1981-02-20', 1600, 300, 30);
		insert into emp values (7521, 'WARD', 'SALESMAN', 7698, '1981-02-22', 1250, 500, 30);
		insert into emp values (7566, 'JONES', 'MANAGER', 7839, '1981-04-02', 2975, null, 20);
		insert into emp values (7654, 'MARTIN', 'SALESMAN', 7698, '1981-09-28', 1250, 1400, 30);
		insert into emp values (7698, 'BLAKE', 'MANAGER', 7839, '1981-05-01', 2850, null, 30);
		insert into emp values (7782, 'CLARK', 'MANAGER', 7839, '1981-06-09', 2450, null, 10);
		insert into emp values (7788, 'SCOTT', 'ANALYST', 7566, '1982-12-09', 3000, null, 20);
		insert into emp values (7839, 'KING', 'PRESIDENT', null, '1981-11-17', 5000, null, 10);
		insert into emp values (7844, 'TURNER', 'SALESMAN', 7698, '1981-09-08', 1500, 0, 30);
		insert into emp values (7876, 'ADAMS', 'CLERK', 7788, '1983-01-12', 1100, null, 20);
		insert into emp values (7900, 'JAMES', 'CLERK', 7698, '1981-12-03', 950, null, 30);
		insert into emp values (7902, 'FORD', 'ANALYST', 7566, '1981-12-03', 3000, null, 20);
		insert into emp values (7934, 'MILLER', 'CLERK', 7782, '1982-01-23', 1300, null, 10);
		
		1.查询出部门编号为30的所有员工的编号和姓名
		
			table： emp
			查什么： 
				1.维度：group by
				2.指标: 聚合函数
				3.普普通通的字段：	编号和姓名
			怎么查：
				where：
					部门编号为30
			
		
		
		2.找出部门编号为10中所有经理，和部门编号为20中所有销售员的详细资料。
		3.查询所有员工详细信息，用工资降序排序，如果工资相同使用入职日期升序排序
		4.列出薪金大于1500的各种工作及从事此工作的员工人数。
		5.列出在销售部工作的员工的姓名，假定不知道销售部的部门编号。
		6.查询姓名以S开头的\以S结尾\包含S字符\第二个字母为L  __
		7.查询每种工作的最高工资、最低工资、人数
		8.列出薪金 高于 公司平均薪金的所有员工号，员工姓名，所在部门名称，上级领导，工资，工资等级
		9.列出薪金  高于  在各自部门工作的员工的平均薪金的员工姓名和薪金、部门名称。
		
		
		80% sqlboy sqlgirl
		20% 大数据平台

```


# 结题 #
## 第一题 ##
- 查询出部门编号为30的所有员工的编号和姓名
- 确定部门编号的条件 where deptno = 30
- 确定所要的信息 ename ， epmpno
- 从员工表里筛选
- ` select empno ,ename from emp where deptno = 30;`
- ` select empno ,ename from emp where deptno in (30);`
## 第二题 ##
- 找出部门编号为10中所有经理，和部门编号为20中所有销售员的详细资料。
- 确定条件 ： 
- 职业为经理的（MANAGER） ，部门编号为 10
- 职业为销售员的（SALESMAN） ， 部门编号为 20
### 1 ###
- `select * from emp where job = "MANAGER" and deptno = 10;`
- `select * from emp where job = "SALESMAN" and  deptno = 20;`
### 2 ###
- `select * from emp where job in ("MANAGER") and deptno = 10;`
- `select * from emp where job in ("SALESMAN") and  deptno = 20;`
### 3 ###
- `select * from emp where job in ("SALESMAN") and  deptno = 20;`
- `select * from emp where job in ("MANAGER") and deptno = 10;`
### 4 ###
- `select * from emp where (job, deptno) in (("SALESMAN", 20));`
- `select * from emp where (job, deptno) in (("MANAGER", 10));`
### 5 ###
- `select * from emp where (job, deptno) in (("SALESMAN", 20)  , ("MANAGER", 10));`
## 第三题 ##
- 查询所有员工详细信息，用工资降序排序，如果工资相同使用入职日期升序排序
- 条件 ：工资和入职时间
- 工资 ＝ 薪金 ＋ 佣金
- 但是mysql里一个值和null相加会变成null
- 通过 ifnull  或者 coalesce  进行转化
- 所以要转化 
- 这个题有两种解法
### 1 ###
- 先转化


```

		select 
		(sal + yy) as hh ,
		(sal + kk) as ii,
		hiredate
		from (
		    select
		    hiredate,
		    sal,
		    ifnull(comm , 0) as yy,
		    coalesce(comm , 0) as kk
		    from emp
		) as hyy
		order by hh desc,hiredate asc;
```

### 2 ###
- 转化和展示一起做


```

		select 
		hiredate,
		ifnull((sal + comm) , sal) as total,
		coalesce((sal + comm) , sal) as ti
		from emp
		order by total desc , hiredate asc;

```


## 第四题 ##
- 列出薪金大于1500的各种工作及从事此工作的员工人数
- 条件 ： 薪金 就是`sal > 1500` 的`工作` 求`人数`
- 薪金的条件可以用`where` 或者`having`
- 工作可以用`group by job`
- 人数可以用count
### 1 ###
- `select job, count(*) from emp where sal > 1500 group by job;`
## 第五题 ##
- 列出在销售部工作的员工的姓名，假定不知道销售部的部门编号
- 条件 ：  不知道部门编号 但是我们可以从表中获取
### 1 ###
- `select ename from emp left join dept on dept.deptno = emp.deptno where dname = "SALES" ;`
### 2 ###


```

	select ename from emp where (
	    select 
	    deptno
	    from dept
	    where dname = "SALES" 
	) = emp.deptno ;

```


### 3 ###


```

		select ename from emp where deptno in (
		    select 
		    deptno
		    from dept
		    where dname = "SALES" 
		) ;

```


## 第六题 ##
- 查询姓名以S开头的\以S结尾\包含S字符\第二个字母为L
- 两种方式 like 或者正则
### 1 ###
- `select ename from emp where ename like "%s%" or  ename like "_L"; `
### 2 ###
- `select ename from emp where ename REGEXP '*s*'or ename REGEXP '^.L';`
## 第七题 ##
- 查询每种工作的最高工资、最低工资、人数
- 条件 ： `工资` ， `最高工资` ， `最低工资` ， `人数`
- 这个题目前我所想到的有两种
- 第一是慢慢做 ，用嵌套的方式 ，
- 优点 ： 不用动脑
- 缺点 ： 代码量比较多
- 第二个是第一个的优化版本 
- 优点 ： 代码量少很多
- 缺点 : 难理解
- 
### 1 ###
- 我们先获取基本的数据源  
- 获取工资的情况 ，以及各种职业所对应的工资 及其人数


```


		select 
		(ifnull((sal + comm) , sal)) as sal1,
		job,
		count(*) as total_people
		from emp group by job,sal1;

```


- 接下来是获取最高工资 和最低工资 ，以及工作的种类 ,以及总人数（最高工资，以及最低工资的）



```

		select max(sal1),min(sal1),job,count(*)
		from(select 
		(ifnull((sal + comm) , sal)) as sal1,
		job,
		count(*) as total_people
		from emp group by job,sal1) as king group by job;

```


- 接下来 我们只需要连接上述的两个表 
- 一个表里对应的是每个人所对应的工资以及职业
- 因为一个表里是我们的最高的工资，最低的工资以及他所对应的工作 和总人数
- 相当于第二个表当我们的筛选条件 ， 如果符合筛选条件 就从第一个表选出这条数据
- 然后获得符合条件的人数
- 根据以上分析 ， 我们采用左连接的方式 ，
- 内连接可能会丢值 ，不过在此题中不会丢值
- 如下 ： 


```


		select 
		sal1,
		max_sal,
		min_sal,
		tjob,
		total_people
		from(
		select max(sal1) as max_sal,min(sal1) as min_sal,job as tjob,count(*)
		from(select 
		(ifnull((sal + comm) , sal)) as sal1,
		job,
		count(*) as total_people
		from emp group by job,sal1) as king group by job) as KING  
		left join 
		(
		select 
		(ifnull((sal + comm) , sal)) as sal1,
		job,
		count(*) as total_people
		from emp group by job,sal1) as yu  
		on KING.tjob = yu.job and yu.sal1 = KING.max_sal or yu.sal1 = KING.min_sal; 

```


### 2 ###
- 第二种方法 ， 我们所采用的是union
- 而不是连接
- 我们先获取 每种工作的 最高工资 和最低工资 以及工作种类 ，以及总共人数


```

					 select 
                     max(ifnull((sal + comm) , sal)) as sal_max,
                     min(ifnull((sal + comm) , sal)) as sal_min,
                     job,
                     count(*) as total_people
                     from emp group by job;

```


- 然后我们现在要做的就是统计人数了
- 我们要先判断他的最大工资和11最小工资是不是相等
- 因为相等的话，他会默认把一个人分最大，一个人分最小
- 所以要分开
- 下面就是如果最大工资和最小工资相等的时候的做法


```

		select 
		total_people as max_people ,(sal_max - sal_max) as min_people,job,sal_max,sal_min
		from(select 
		max(ifnull((sal + comm) , sal)) as sal_max,
		min(ifnull((sal + comm) , sal)) as sal_min,
		job,
		count(*) as total_people
		from emp group by job)  as king  group  by job having sal_max = sal_min;
```


- 下面是不相等的时候


```

		select 
		count(sal_max) as max_people,count(sal_min) as min_people,job,sal_max,sal_min
		from(select 
		max(ifnull((sal + comm) , sal)) as sal_max,
		min(ifnull((sal + comm) , sal)) as sal_min,
		job,
		count(*) as total_people
		from emp group by job)  as king  group  by job having sal_max != sal_min
```


- 我这里是用的`having` ， 因为我把条件放在`group by`后面了 ，这两个也可用where的不过要放在`group by`之前
- 接下来，我们用union就可以 ， 因为 我把他们的别名起的一样 ，而且列也能对的上
- 我们就可以直接用union了



```

		select 
		count(sal_max) as max_people,count(sal_min) as min_people,job,sal_max,sal_min
		from(select 
		max(ifnull((sal + comm) , sal)) as sal_max,
		min(ifnull((sal + comm) , sal)) as sal_min,
		job,
		count(*) as total_people
		from emp group by job)  as king where sal_max != sal_min group  by job 
		union
		select 
		total_people as max_people ,(sal_max - sal_max) as min_people,job,sal_max,sal_min
		from(select 
		max(ifnull((sal + comm) , sal)) as sal_max,
		min(ifnull((sal + comm) , sal)) as sal_min,
		job,
		count(*) as total_people
		from emp group by job)  as king  group  by job having sal_max = sal_min;

```


## 第七题的附加题 ##
- 找出最高的工资 最低工资 以及人数 ，职业
- 这个是我没读题的时候 ，看错题意了 ，结果做出个这么个玩意 ，姑且也当作一个题把。
- 条件 ： 所有工作中 ，`最高工资` , `最低工资` ， `人数`
### 1 ###
- 对于这个问题 ， 我们要先找到数据源 ，就是说，我们现在需要最大工资 和最小工资 ，那么为我们要先搞出这样有一个表
- `select max(ifnull((sal + comm) , sal)) as max_sal, min(coalesce((sal + comm) , sal)) as min_sal,count(*) from emp ;`
- 这个是统计所有工作中的最大和最小工资
- 然后我们要找出人数以及职业
- 这相当于我们现在有了最大最小的筛选条件 ，去有工资和职业，以及人数的表中筛选就好
- 接下来，我们就要创建一个表有工资和职业，以及人数
- `select job, (ifnull((sal + comm) , sal)) as sal1,count(sal) as total_people from emp  group by sal1 , job;`
- 接下来我们就要进行筛选了 ， 最简单的筛选就是把两个表链接上 


```

		select 
		sal1,
		job,
		total_people
		from(select max(ifnull((sal + comm) , sal)) as max_sal, min(coalesce((sal + comm) , sal))  as min_sal from emp ) as king 
		
		left join 
		
		(select job, (ifnull((sal + comm) , sal)) as sal1,count(sal) as total_people from emp  group by sal1 , job) as kk 
		on max_sal = sal1 or min_sal =sal1 ;

```


- 但是做到这里其实还可以进行优化 ， 我们可以不用链接 ， 但是上述链接是小表驱动大表的，我们也可以反过来
- 但是用in的时候我们不能用一列去匹配两列
- 因为我们最开始的数据源，分出来的是两列
- 现在我们要通过别名，把两列变成一列
- `select max(ifnull((sal + comm) , sal)) as sal from emp union select min(coalesce((sal + comm) , sal))  as sal from emp;`
- 然后我们就可以用where进行匹配了


 
```

		select 
		sal1,
		job,
		total_people
		from(select job, (ifnull((sal + comm) , sal)) as sal1,count(sal) as total_people from emp  group by sal1 , job) as kk 
		where sal1 in (select max(ifnull((sal + comm) , sal)) as sal from emp union select min(coalesce((sal + comm) , sal))  as sal from emp);

```

- 这里的in也可以用等号替代 ，只不过如果是等号，那么你后面的条件要拆开 ，而且 中间要用or

## 第八题 ##
- 列出薪金 高于 公司平均薪金的所有员工号，员工姓名，所在部门名称，上级领导，工资，工资等级
- 条件 ： `薪金`高于 `平均薪金` ， `工资等级` ，`部门名称` 
- 通过以上条件我们可知 ，这个是三个表都要用到的


### 1 ###
- 老样子 ： 我们要先获取基本的数据源
- `select avg(sal) as sal_avg from emp`
- 上述是获取平均薪金 ，是要作为我们的判断条件的
- 然后求出薪金大于平均薪金的 进行初步筛选


```

		select 
		empno,
		ename,
		deptno,
		sal,
		mgr
		from emp where sal > (select 
		avg(sal)
		from emp);

```


- 接下来我们要把初步筛选完成的表 和 部门表联合起来 ，原因是 部门表有部门编号作为连接点



```

		select
		*
		from(select 
		comm,
		empno,
		ename,
		deptno as deptno1,
		sal,
		mgr
		from emp where sal > (select 
		avg(sal)
		from emp)) as Oavg_table left join dept on Oavg_table.deptno1=dept.deptno; 
```


- 现在除了工资等级 ，和工资，其余的我们全都有了、
- 接下来，我们先加上工资 ，然后根据工资进行判断等级


```


		select 
		comm,
		empno,
		ename,
		sal,
		mgr,
		deptno1,
		dname,
		(sal + commchange) as earn
		from(
		select
		comm,
		empno,
		ename,
		sal,
		mgr,
		deptno1,
		dname,
		ifnull(comm , 0) as commchange
		from(select 
		comm,
		empno,
		ename,
		deptno as deptno1,
		sal,
		mgr
		from emp where sal > (select 
		avg(sal)
		from emp)) as Oavg_table left join dept on Oavg_table.deptno1=dept.deptno) as setlist;

```


- 接下来，我们要加上工资等级
- 在这里，我就偷个懒 因为平均薪金是2200多，那么你工资必不可能比这个少
- 那么工资等级也就不会是3及以下
- 一般的话是条件都要判断的 ，我这里就偷个懒
- 接下来，我们设置筛选条件 ，  因为工资等级没有能和别的表联合 ，或者union的列 ，那么我们只能一个个判断了
- 我们先判断大于3000的 并给与工资等级


```
select 
comm,
empno,
ename,
sal,
mgr,
deptno1,
dname,
earn,
((empno + 5) - empno) as level
from(select 
comm,
empno,
ename,
sal,
mgr,
deptno1,
dname,
(sal + commchange) as earn
from(
select
comm,
empno,
ename,
sal,
mgr,
deptno1,
dname,
ifnull(comm , 0) as commchange
from(select 
comm,
empno,
ename,
deptno as deptno1,
sal,
mgr
from emp where sal > (select 
avg(sal)
from emp)) as Oavg_table left join dept on Oavg_table.deptno1=dept.deptno) as setlist) as sal_tables where earn >3000;

```

- 接下来是小于3000的


```

		select 
		comm,
		empno,
		ename,
		sal,
		mgr,
		deptno1,
		dname,
		earn,
		((empno + 4) - empno) as level
		from(select 
		comm,
		empno,
		ename,
		sal,
		mgr,
		deptno1,
		dname,
		(sal + commchange) as earn
		from(
		select
		comm,
		empno,
		ename,
		sal,
		mgr,
		deptno1,
		dname,
		ifnull(comm , 0) as commchange
		from(select 
		comm,
		empno,
		ename,
		deptno as deptno1,
		sal,
		mgr
		from emp where sal > (select 
		avg(sal)
		from emp)) as Oavg_table left join dept on Oavg_table.deptno1=dept.deptno) as setlist) as sal_tables where earn >2000 and earn <=3000;

```


- 注意我这里是偷懒了，没有判断2000和更低的，原因在上面 ，如果想判断和上面两个一样
- 接下来 我们用unio  把他们链接到一起

 
```

		select 
		comm,
		empno,
		ename,
		sal,
		mgr,
		deptno1,
		dname,
		earn,
		((empno + 5) - empno) as level
		from(select 
		comm,
		empno,
		ename,
		sal,
		mgr,
		deptno1,
		dname,
		(sal + commchange) as earn
		from(
		select
		comm,
		empno,
		ename,
		sal,
		mgr,
		deptno1,
		dname,
		ifnull(comm , 0) as commchange
		from(select 
		comm,
		empno,
		ename,
		deptno as deptno1,
		sal,
		mgr
		from emp where sal > (select 
		avg(sal)
		from emp)) as Oavg_table left join dept on Oavg_table.deptno1=dept.deptno) as setlist) as sal_tables where earn >3000
		union
		select 
		comm,
		empno,
		ename,
		sal,
		mgr,
		deptno1,
		dname,
		earn,
		((empno + 4) - empno) as level
		from(select 
		comm,
		empno,
		ename,
		sal,
		mgr,
		deptno1,
		dname,
		(sal + commchange) as earn
		from(
		select
		comm,
		empno,
		ename,
		sal,
		mgr,
		deptno1,
		dname,
		ifnull(comm , 0) as commchange
		from(select 
		comm,
		empno,
		ename,
		deptno as deptno1,
		sal,
		mgr
		from emp where sal > (select 
		avg(sal)
		from emp)) as Oavg_table left join dept on Oavg_table.deptno1=dept.deptno) as setlist) as sal_tables where earn >2000 and earn <=3000;

```


- 就可以了 ，上面的我做法是我没动脑子，直接一点点搞出来的
### 2 ###
```


		select empno,ename,dname,sal,mgr,ifnull((sal + comm) , sal) as earn , (sal + 5  - sal) as  level from (select * from emp where sal > (select avg(sal) from  emp))  as king left join dept on king.deptno=dept.deptno having earn >  3000
		union 
		select empno,ename,dname,sal,mgr,ifnull((sal + comm) , sal) as earn , (sal + 4  - sal) as  level from (select * from emp where sal > (select avg(sal) from  emp))  as king left join dept on king.deptno=dept.deptno having earn <= 3000;

```
### 3 ###

```
    select 
    king.ename,
    king.empno,
    e1.ename as leader,
    king.earn,
    case when earn Between 700 and 1200 then 1
         when earn Between 1200 and 1400 then 2
         when earn Between 1400 and 2000 then 3
         when earn Between 2000 and 3000 then 4
         when earn Between 3000 and 9990 then 5
         end
    as sallevel
    from (
      select ename , empno , deptno , ifnull((sal + comm),sal) as earn ,mgr 
      from emp
      where sal > (
        select avg(sal)
        from emp
      )
    ) as king 
    left join dept on king.deptno=dept.deptno 
    left join emp e1 on king.mgr = e1.empno;

```

### 4 ###

```


	 // 最好不用 ，因为笛卡尔积 会造成数据膨胀
	    select 
	    king.ename,
	    king.empno,
	    e1.ename as leader,
	    king.earn,
	    s.grade
	    as sallevel
	    from (
	      select ename , empno , deptno , ifnull((sal + comm),sal) as earn ,mgr 
	      from emp
	      where sal > (
	        select avg(sal)
	        from emp
	      )
	    ) as king 
	    left join dept on king.deptno=dept.deptno 
	    left join (
	      select empno,
	      ename
	      from emp
	    ) e1 on king.mgr = e1.empno
	    cross join salgrade as s where earn Between losal and hisal ;

```

### 5 ###

```


    select 
    king.ename,
    king.empno,
    e1.ename as leader,
    king.earn,
    s.grade
    as sallevel
    from (
      select ename , empno , deptno , ifnull((sal + comm),sal) as earn ,mgr 
      from emp
      where sal > (
        select avg(sal)
        from emp
      )
    ) as king 
    left join dept on king.deptno=dept.deptno 
    left join (
      select empno,
      ename
      from emp
    ) e1 on king.mgr = e1.empno
    left join salgrade as s on earn >= losal and earn <= hisal;
```


## 第九题 ##
- 列出薪金  高于  在各自部门工作的员工的平均薪金的员工姓名和薪金、部门名称
- 条件 ： 各个部门，`薪金`高于`各个部门`的`平均薪金`
### 1 ###
- 我们可以先获取薪金，以及部门编号 ，并且按照他们进行划分
- 因为题目中主要要求的就是部门以及薪金
- `select  sal ,deptno from emp group by sal , deptno;`
- 接下来我们求各个部门的平均薪金
- `select avg(sal) as sal_avg, deptno from (select  sal ,deptno from emp group by sal , deptno) as king group by deptno;`
- 其实上述两部可以通过一步完成
- `select avg(sal) ,deptno from emp group by deptno;`
- 就是不动脑子和动脑子的区别
- 不过一般没思路的时候把数据源写上，可能会有奇效
- 接下来找出高于平均薪金的信息
```

		select
		*
		from (select avg(sal) as sal_avg, deptno from (select  sal ,deptno from emp group by sal , deptno) as king group by deptno) as avg_basic left join emp
		on emp.deptno=avg_basic.deptno  and emp.sal > avg_basic.sal_avg;
```

- 这个是用左连接来实现的
- 其实还可以要用where来实现 , 不过比较复杂 ，就不在这里写了
- 接下来我们就合并部门输出就可以了


```

		select  * 
		from  
		(select
		*
		from (select avg(sal) as sal_avg, deptno as deptno1 from (select  sal ,deptno from emp group by sal , deptno) as king group by deptno) as avg_basic left join emp
		on emp.deptno=avg_basic.deptno1  and emp.sal > avg_basic.sal_avg) as basicinfo left  join dept on basicinfo.deptno1=dept.deptno;
```


- 这个可以用in替代
- 代码如下


```

		select  * 
		from  
		(select
		*
		from (select avg(sal) as sal_avg, deptno as deptno1 from (select  sal ,deptno from emp group by sal , deptno) as king group by deptno) as avg_basic left join emp
		on emp.deptno=avg_basic.deptno1  and emp.sal > avg_basic.sal_avg) as basicinfo where basicinfo.deptno1 in (select deptno from dept);

```


- 完结撒花！
# 总结 #
- 我们做题不可以改变源头数据 ， 就是数据库里的数据
- 没有思路的时候可以写题中要的数据 ， 题是做出来的，不是看出来的
- 写了第一行 ，自然就会有后面的代码，只是看你愿不愿意开始
- 有思路自然是按照自己的思路去做
- 在有些时候 left join 可以替换成  in  但是这样的替换是在 没有大于小于判定的时候
- 如果有大于小于判定 ，则要替换要用where
- 进行in的时候如果出现一行对多行的情况 ，
- 不妨把多行化成一行 ，用union
- 或者把多余的行删除
- 对于分组 ， 我们如果没有思路可以把题中要的东西先分组，然后后面再进行分组
- 比如 ： `select avg(sal) as sal_avg, deptno from (select  sal ,deptno from emp group by sal , deptno) as king group by deptno;`
- 和 `select avg(sal) ,deptno from emp group by deptno;`
- 是一样的，但是对于没有思路的时候来说，还是上面一个更好一点，下面是块，可是有时候会忘，
- 对于几乎所有的sql题目
- 几乎没有题通过 以下做不出来的 
- 就是简单方法和笨方法的区别  ， 笨方法虽然笨 ，可是实用性强 ，简单虽然简单 ，可是难想
- 而且笨方法的速度其实主要取决于你的打字速度
- 先筛选数据 `group by` , `where` , `avg ,sum ,max ,min..` ...
- 再筛选数据 `group by` , `where` , `avg ,sum ,max ,min..` ...
- 通过链接的方式链接 `union` , `union all` ,` join`..
- 判断条件 `in` , `where` ....
- 注意 ： 组函数不可以进行嵌套使用 `where max(sal) =1` 这样就是不可以的 ，相应的 在in 里也不可以
 ， 在组函数里也不可以  ，如果非要这样操作 ，那么你要 嵌套表 , 加上别名才可以 


```

		select
		*
		from(select
		max(sal) as max_sal
		from emp
		) where max_sal =1;

```

- 上述那样才可以
- where 和 having是有区别的 作用域的区别，以及位置的区别 ，但是语法是基本上完全一样的 in ， like 之类的 
- where被放在group之前 ，havin 是在group之后
- 例子 ：  
- `select sal as sal1 ,comm  as comm1 from emp where sal1 > 1500; `
- `select sal as sal1 ,comm  as comm1 from emp where comm1 in (300);`
- 这样where是读取不到这个别名的 ，但是having是可以读取到的
- 原因在于，他们对数据操作的时机不同
- having是上面都执行完了 ，才操作
- where是和他们一起操作的
- `select sal as sal1 from emp having sal1 >1500;`
- `select sal as sal1 from emp having sal in (800);`
- 这样就可以了
# 牛客 #
##  现在运营想要了解复旦大学的每个用户在8月份练习的总题目数和回答正确的题目数情况，请取出相应明细数据，对于在8月份没有练习过的用户，答题数结果返回0 ##

- 数据源


```

		drop table if exists `user_profile`;
		drop table if  exists `question_practice_detail`;
		drop table if  exists `question_detail`;
		CREATE TABLE `user_profile` (
		  `id` int(11) NOT NULL,
		  `device_id` int(11) NOT NULL,
		  `gender` varchar(14) NOT NULL,
		  `age` int(11) DEFAULT NULL,
		  `university` varchar(32) NOT NULL,
		  `gpa` float DEFAULT NULL,
		  `active_days_within_30` int(11) DEFAULT NULL,
		  `question_cnt` int(11) DEFAULT NULL,
		  `answer_cnt` int(11) DEFAULT NULL
		) ENGINE=InnoDB DEFAULT CHARSET=utf8;
		CREATE TABLE `question_practice_detail` (
		`id` int NOT NULL,
		`device_id` int NOT NULL,
		`question_id`int NOT NULL,
		`result` varchar(32) NOT NULL,
		`date` date NOT NULL
		);
		CREATE TABLE `question_detail` (
		`id` int NOT NULL,
		`question_id`int NOT NULL,
		`difficult_level` varchar(32) NOT NULL
		);
		
		INSERT INTO user_profile VALUES(1,2138,'male',21,'北京大学',3.4,7,2,12);
		INSERT INTO user_profile VALUES(2,3214,'male',null,'复旦大学',4.0,15,5,25);
		INSERT INTO user_profile VALUES(3,6543,'female',20,'北京大学',3.2,12,3,30);
		INSERT INTO user_profile VALUES(4,2315,'female',23,'浙江大学',3.6,5,1,2);
		INSERT INTO user_profile VALUES(5,5432,'male',25,'山东大学',3.8,20,15,70);
		INSERT INTO user_profile VALUES(6,2131,'male',28,'山东大学',3.3,15,7,13);
		INSERT INTO user_profile VALUES(7,4321,'male',28,'复旦大学',3.6,9,6,52);
		INSERT INTO question_practice_detail VALUES(1,2138,111,'wrong','2021-05-03');
		INSERT INTO question_practice_detail VALUES(2,3214,112,'wrong','2021-05-09');
		INSERT INTO question_practice_detail VALUES(3,3214,113,'wrong','2021-06-15');
		INSERT INTO question_practice_detail VALUES(4,6543,111,'right','2021-08-13');
		INSERT INTO question_practice_detail VALUES(5,2315,115,'right','2021-08-13');
		INSERT INTO question_practice_detail VALUES(6,2315,116,'right','2021-08-14');
		INSERT INTO question_practice_detail VALUES(7,2315,117,'wrong','2021-08-15');
		INSERT INTO question_practice_detail VALUES(8,3214,112,'wrong','2021-05-09');
		INSERT INTO question_practice_detail VALUES(9,3214,113,'wrong','2021-08-15');
		INSERT INTO question_practice_detail VALUES(10,6543,111,'right','2021-08-13');
		INSERT INTO question_practice_detail VALUES(11,2315,115,'right','2021-08-13');
		INSERT INTO question_practice_detail VALUES(12,2315,116,'right','2021-08-14');
		INSERT INTO question_practice_detail VALUES(13,2315,117,'wrong','2021-08-15');
		INSERT INTO question_practice_detail VALUES(14,3214,112,'wrong','2021-08-16');
		INSERT INTO question_practice_detail VALUES(15,3214,113,'wrong','2021-08-18');
		INSERT INTO question_practice_detail VALUES(16,6543,111,'right','2021-08-13');
		INSERT INTO question_detail VALUES(1,111,'hard');
		INSERT INTO question_detail VALUES(2,112,'medium');
		INSERT INTO question_detail VALUES(3,113,'easy');
		INSERT INTO question_detail VALUES(4,115,'easy');
		INSERT INTO question_detail VALUES(5,116,'medium');
		INSERT INTO question_detail VALUES(6,117,'easy');
```

## 答题 ##
- 因为要用大于8月的 ， 所以 直接 用month就好
- 用if判断是不是正确

```


		select 
		profile.device_id,
		university,
		count(question_id) as total,
		sum(if(result = 'right' , 1 ,0)) as right_question_cnt
		from user_profile as profile left join question_practice_detail as detail on profile.device_id=detail.device_id  and month(detail.date)=8 where university = '复旦大学' group by profile.device_id;

```

- 第二种方法


```

		select 
		device_id,
		university,
		sum(right_question_cnt + wrong_question_cnt) as question_cnt,
		sum(right_question_cnt) as right_question_cnt
		from(select 
		device_id,
		university,
		ifnull((right_question_cnt) , 0) as right_question_cnt,
		ifnull((wrong_question_cnt) , 0) as wrong_question_cnt,
		ifnull((date) , 0) as date
		from user_profile left join (
		select device_id as ID,count(*) as right_question_cnt ,date , (device_id - device_id) as wrong_question_cnt from question_practice_detail where result  = "right" group by device_id ,date
		union
		select device_id,(device_id - device_id) as right_question_cnt,date ,count(*) as wrong_question_cnt  from question_practice_detail where result  = "wrong" group by device_id ,date) as king on king.ID = user_profile.device_id where university = "复旦大学") as king    
		where date > '2021-08-00' or date = 0  group by device_id , university ;


```

## 总结 ##
- 链接后面的on后面 ，能加上and 进行条件筛选
- month（date） 可以直接获取这个日期的月份
- if（xxx , sss , www） : if表达式 ： 如果前一个为真 ，则执行sss ，如果不是真 则 www
- 要看清题意
## 现在运营想要了解浙江大学的用户在不同难度题目下答题的正确率情况，请取出相应数据，并按照准确率升序输出。 ##
- 数据源
```


		drop table if exists `user_profile`;
		drop table if  exists `question_practice_detail`;
		drop table if  exists `question_detail`;
		CREATE TABLE `user_profile` (
		  `id` int(11) NOT NULL,
		  `device_id` int(11) NOT NULL,
		  `gender` varchar(14) NOT NULL,
		  `age` int(11) DEFAULT NULL,
		  `university` varchar(32) NOT NULL,
		  `gpa` float DEFAULT NULL,
		  `active_days_within_30` int(11) DEFAULT NULL,
		  `question_cnt` int(11) DEFAULT NULL,
		  `answer_cnt` int(11) DEFAULT NULL
		) ENGINE=InnoDB DEFAULT CHARSET=utf8;
		CREATE TABLE `question_practice_detail` (
		`id` int NOT NULL,
		`device_id` int NOT NULL,
		`question_id`int NOT NULL,
		`result` varchar(32) NOT NULL,
		`date` date NOT NULL
		);
		CREATE TABLE `question_detail` (
		`id` int NOT NULL,
		`question_id`int NOT NULL,
		`difficult_level` varchar(32) NOT NULL
		);
		
		INSERT INTO user_profile VALUES(1,2138,'male',21,'北京大学',3.4,7,2,12);
		INSERT INTO user_profile VALUES(2,3214,'male',null,'复旦大学',4.0,15,5,25);
		INSERT INTO user_profile VALUES(3,6543,'female',20,'北京大学',3.2,12,3,30);
		INSERT INTO user_profile VALUES(4,2315,'female',23,'浙江大学',3.6,5,1,2);
		INSERT INTO user_profile VALUES(4,2316,'female',23,'浙江大学',3.6,5,1,2);
		INSERT INTO user_profile VALUES(5,5432,'male',25,'山东大学',3.8,20,15,70);
		INSERT INTO user_profile VALUES(6,2131,'male',28,'山东大学',3.3,15,7,13);
		INSERT INTO user_profile VALUES(7,4321,'male',28,'复旦大学',3.6,9,6,52);
		INSERT INTO question_practice_detail VALUES(1,2138,111,'wrong','2021-05-03');
		INSERT INTO question_practice_detail VALUES(2,3214,112,'wrong','2021-05-09');
		INSERT INTO question_practice_detail VALUES(3,3214,113,'wrong','2021-06-15');
		INSERT INTO question_practice_detail VALUES(4,6543,111,'right','2021-08-13');
		INSERT INTO question_practice_detail VALUES(5,2315,115,'right','2021-08-13');
		INSERT INTO question_practice_detail VALUES(6,2315,116,'right','2021-08-14');
		INSERT INTO question_practice_detail VALUES(7,2315,117,'wrong','2021-08-15');
		INSERT INTO question_practice_detail VALUES(8,3214,112,'wrong','2021-05-09');
		INSERT INTO question_practice_detail VALUES(9,3214,113,'wrong','2021-08-15');
		INSERT INTO question_practice_detail VALUES(10,6543,111,'right','2021-08-13');
		INSERT INTO question_practice_detail VALUES(11,2315,115,'right','2021-08-13');
		INSERT INTO question_practice_detail VALUES(12,2315,116,'right','2021-08-14');
		INSERT INTO question_practice_detail VALUES(13,2315,117,'wrong','2021-08-15');
		INSERT INTO question_practice_detail VALUES(14,3214,112,'wrong','2021-08-16');
		INSERT INTO question_practice_detail VALUES(15,3214,113,'wrong','2021-08-18');
		INSERT INTO question_practice_detail VALUES(16,6543,111,'right','2021-08-13');
		INSERT INTO question_detail VALUES(1,111,'hard');
		INSERT INTO question_detail VALUES(2,112,'medium');
		INSERT INTO question_detail VALUES(3,113,'easy');
		INSERT INTO question_detail VALUES(4,115,'easy');
		INSERT INTO question_detail VALUES(5,116,'medium');
		INSERT INTO question_detail VALUES(6,117,'easy');

```
## 解答 ##

```

		select
		difficult_level,
		(sum(if(result = 'right' , 1 ,0))/count(up.question_id)) as Rate
		from
		(select * from question_practice_detail where device_id in (select device_id from user_profile where university = "浙江大学")) as up left join question_detail on up.question_id = question_detail.question_id group by difficult_level order by Rate;

```
- 优化方法
- 把中间筛选浙江大学的地方换成链接 ，并且在链接的时候进行筛选 ， 就会快上给 5-6ms
- 如下


```

		SELECT qd.difficult_level,(
		SUM(CASE WHEN qpd.result='right' THEN 1
			ELSE 0
			END
		)/COUNT(qpd.result)
		) correct_rated
		FROM user_profile up,
		     question_practice_detail qpd,
		     question_detail qd
		WHERE     up.university='浙江大学'
		      AND up.device_id=qpd.device_id
		      AND qpd.question_id=qd.question_id
		GROUP BY  qd.difficult_level
		order by correct_rated asc

```
## 现在运营想要查看用户在某天刷题后第二天还会再来刷题的平均概率。请你取出相应数据。 ##
- 数据源
```

		drop table if exists `user_profile`;
		drop table if  exists `question_practice_detail`;
		drop table if  exists `question_detail`;
		CREATE TABLE `user_profile` (
		  `id` int(11) NOT NULL,
		  `device_id` int(11) NOT NULL,
		  `gender` varchar(14) NOT NULL,
		  `age` int(11) DEFAULT NULL,
		  `university` varchar(32) NOT NULL,
		  `gpa` float DEFAULT NULL,
		  `active_days_within_30` int(11) DEFAULT NULL,
		  `question_cnt` int(11) DEFAULT NULL,
		  `answer_cnt` int(11) DEFAULT NULL
		) ENGINE=InnoDB DEFAULT CHARSET=utf8;
		CREATE TABLE `question_practice_detail` (
		`id` int NOT NULL,
		`device_id` int NOT NULL,
		`question_id`int NOT NULL,
		`result` varchar(32) NOT NULL,
		`date` date NOT NULL
		);
		CREATE TABLE `question_detail` (
		`id` int NOT NULL,
		`question_id`int NOT NULL,
		`difficult_level` varchar(32) NOT NULL
		);
		
		INSERT INTO user_profile VALUES(1,2138,'male',21,'北京大学',3.4,7,2,12);
		INSERT INTO user_profile VALUES(2,3214,'male',null,'复旦大学',4.0,15,5,25);
		INSERT INTO user_profile VALUES(3,6543,'female',20,'北京大学',3.2,12,3,30);
		INSERT INTO user_profile VALUES(4,2315,'female',23,'浙江大学',3.6,5,1,2);
		INSERT INTO user_profile VALUES(5,5432,'male',25,'山东大学',3.8,20,15,70);
		INSERT INTO user_profile VALUES(6,2131,'male',28,'山东大学',3.3,15,7,13);
		INSERT INTO user_profile VALUES(7,4321,'male',28,'复旦大学',3.6,9,6,52);
		INSERT INTO question_practice_detail VALUES(1,2138,111,'wrong','2021-05-03');
		INSERT INTO question_practice_detail VALUES(2,3214,112,'wrong','2021-05-09');
		INSERT INTO question_practice_detail VALUES(3,3214,113,'wrong','2021-06-15');
		INSERT INTO question_practice_detail VALUES(4,6543,111,'right','2021-08-13');
		INSERT INTO question_practice_detail VALUES(5,2315,115,'right','2021-08-13');
		INSERT INTO question_practice_detail VALUES(6,2315,116,'right','2021-08-14');
		INSERT INTO question_practice_detail VALUES(7,2315,117,'wrong','2021-08-15');
		INSERT INTO question_practice_detail VALUES(8,3214,112,'wrong','2021-05-09');
		INSERT INTO question_practice_detail VALUES(9,3214,113,'wrong','2021-08-15');
		INSERT INTO question_practice_detail VALUES(10,6543,111,'right','2021-08-13');
		INSERT INTO question_practice_detail VALUES(11,2315,115,'right','2021-08-13');
		INSERT INTO question_practice_detail VALUES(12,2315,116,'right','2021-08-14');
		INSERT INTO question_practice_detail VALUES(13,2315,117,'wrong','2021-08-15');
		INSERT INTO question_practice_detail VALUES(14,3214,112,'wrong','2021-08-16');
		INSERT INTO question_practice_detail VALUES(15,3214,113,'wrong','2021-08-18');
		INSERT INTO question_practice_detail VALUES(16,6543,111,'right','2021-08-13');
		INSERT INTO question_detail VALUES(1,111,'hard');
		INSERT INTO question_detail VALUES(2,112,'medium');
		INSERT INTO question_detail VALUES(3,113,'easy');
		INSERT INTO question_detail VALUES(4,115,'easy');
		INSERT INTO question_detail VALUES(5,116,'medium');
		INSERT INTO question_detail VALUES(6,117,'easy');







```
## 解答 ##
```


		select avg(if(datediff(date2, date1)=1, 1, 0)) as avg_ret
		from (
		    select
		        distinct device_id,
		        date as date1,
		        lead(date) over (partition by device_id order by date) as date2
		    from (
		        select distinct device_id, date
		        from question_practice_detail
		    ) as uniq_id_date
		) as id_last_next_date
```