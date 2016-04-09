# Mysql


DDL(数据定义语言):

	1.创建数据库：     create database dbname;
	2.删除数据库：     drop database dbname;

	use dbname   #进入数据库

	3.创建表：         create table tablename(

						column_name_1 column_type_1 constraints,
						column_name_2 column_type_2 constraints,
						......

						);

	4.查看表的定义：	desc tablename;
						show create table tablename \G;     #"\G"选项的含义是使得记录能够按照字段竖向排列，以便更好的显示

	5.删除表： 			drop table tablename;

	6.修改表:			alter table tablename modify [column] column_definition [first|after column_name];    # first|after column_name  用于控制修改字段的排序顺序

		(1)增加表字段:	alter table tablename add [column] column_definition [first|after column_name];

		(2)删除表字段： alter table tablename drop [column] column_name;

		(3)字段改名:	alter table tablename change [column] old_column_name column_definition [first|after column_name];


	#change和modify 都可以用于修改表的定义，不同的是change后面需要写两次列名，不方便。但是change的优点是可以修改列名称，modify则不能
		(4)修改表名：	alter table tablename rename [to] new_tablename;




DML(数据操纵语言): INSERT UPDATE DELETE SELECT

	1.插入记录：		INSERT into tablename (field1,field2,...fieldn) values (value1,value2,...valuen);
						#若不指明字段名称，那么values后面的顺序应该和字段的排列顺序一致

						INSERT into tablename (field1,field2,...fieldn) 
						values 
						(record1_value1,record1_value2,...record1_valuen),
						(record2_value1,record2_value2,...record2_valuen),
						....
						(recordn_value1,recordn_value2,...recordn_valuen);

	2.更新记录：		UPDATE tablename set field1=value1,field2=value2,...fieldn=valuen [where condition];

	3.删除记录：		DELETE from tablename [where condition];

	4.查询记录：		SELECT *from tablename [where condition];

		(1)查询不重复记录： SELECT distinct column_name from tablename ;

		(2)条件查询：		SELECT *from tablename where condition;

		(3)排序和限制：		SELECT *from tablename [where condition] [order by field1 [DESC\ASC],field2 [DESC\ASC],...fieldn [DESC\ASC]] [limit offset_start,row_count];
							#order by 用于数据库的排序操作  DESC（降序） ASC（升序） 是排序关键字 ，默认升序。  
							#对于排序后的记录，如果希望只显示一部分而不是全部，就可以使用limit关键字来实现。   offset_start为起始偏移量，默认为0。row_count表示显示的行数

		(4)聚合：		SELECT [field1,field2,...fieldn] fun_name
						from tablename
						[where condition]
						[group by field1,field2,...fieldn [with rollup]]
						[having condition]


						#fun_name 表示要做的聚合操作，也就是聚合函数，常用的有sum(求和),count(*)(记录数),max(最大值),min(最小值)
						#group by 关键字表示要进行分类聚合的字段，比如要按照部门分类统计员工数量，部门就应该写在group by 后面
						#with rollup 是可选语法，表明是否对分类聚合后的结果进行再汇总
						#having 关键字表示对分类后的结果再进行条件的过滤

						#注意：having 和 where的区别在于，having是对聚合后的结构进行条件的过滤，而where是在聚合前就对记录进行过滤，如果逻辑允许，我们尽可能用where先过滤记录，这样因为结果集减小，将对聚合的效率大大提高，最后再根据逻辑看是否用having进行再过滤

		(5)表连接:		内连接和外连接
						它们之间最大的区别是：内连接仅选出两张表中互相匹配的记录，而外连接会选出其他不匹配的记录。我们最常用的是内连接。

						(1)内连接：	SELECT column_name1,column_name2 from tablename1,tablename2 where tablename1.column_name1 = tablename2.column_name2;

						(2)外连接：分为左连接和右连接

									左连接：包含所有的左边表中的记录甚至是右边表中没有和它匹配的记录
									右连接：包含所有的右边表中的记录甚至是左边表中没有和它匹配的记录

									SELECT column_name1,column_name2 from tablename1 left join tablename2 where tablename1.column_name1 = tablename2.column_name2;


		(6)子查询：某些情况下，当进行查询的时候，需要的条件是另一个 SELECT 语句的结果，这个时候就要用到子查询。用于子查询的关键字主要包括 in,not in,=,!=,exists,not exists等

					SELECT *from tablename1 where column_name in (SELECT column_name from tablename2)


		(7)记录联合：我们经常会碰到这样的应用，将两个表的数据按照一定的查询条件查询出来后，将结果合并到一起显示出来。这个时候就需要用 union 和 union all 关键字来实现这样的功能

					SELECT *from tablename1
					union\union all
					SELECT *from tablename2
					...
					union\union all
					SELECT *from tablenamen;

					# union 和 union all 的主要区别就是 union all 是把结果集直接合并在一起，而union 是将union all 的结果进行一次 distinct ，取出重复记录后的结果


DCL(数据控制语言): grant revoke 

	grant SELECT,INSERT on databasename.* to 'user'@'localhost' identified by 'password';
	revoke SELECT on databasename from 'user'@'localhost';
