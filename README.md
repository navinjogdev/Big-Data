# Big-Data

## Cloudera Commands(Local Cloudera)


```
hadoop dfsadmin -safemode leave
hadoop fs -ls /user/clodera/
hadoop fs -mkdir /user/cloudera/pdir
hadoop fs -ls /user/cloudera/


echo zeyobron > /home/cloudera/zfile
cat /home/cloudera/zfile
hadoop fs -mkdir /user/cloudera/ldir
hadoop fs -put /home/cloudera/zfile /user/cloudera/ldir
hadoop fs -ls /user/cloudera/ldir/
hadoop fs -cat /user/cloudera/ldir/zfile

             source      Destination
hadoop fs -put <edge-node> <hdfs-target>
hadoop fs -get <hdfs-target> <edge-node>

====connection refuse======
sudo -u hdfs hdfs namenode -format
sudo service hadoop-hdfs-namenode start
sudo service hadoop-hdfs-datanode start
sudo service hadoop-hdfs-secondarynamenode start
hadoop fs -mkdir -p  /user/cloudera/
hadoop fs -ls  /user/cloudera
==================================

```

## Mysql Commands
### Task 1
```
mysql -uroot -pcloudera

create database if not exists zeyodb;
use zeyodb;
drop table if exists zeyotab;

create table zeyotab(id int,name varchar(100),city varchar(100));
insert into zeyotab values(1,'sai','chennai');
insert into zeyotab values(2,'ravi','hyderabad');
insert into zeyotab values(3,'rani','chennai');
insert into zeyotab values(4,'vasu','bangalore');

select * from zeyotab;
quit;

```

## Sqoop Commands
### Task 1
```
sqoop import --connect jdbc:mysql://localhost/zeyodb --username root --password cloudera --m 1 --table zeyotab --delete-target-dir --target-dir /user/cloudera/firstimport

hadoop   fs   -ls     /user/cloudera/firstimport
hadoop   fs   -cat    /user/cloudera/firstimport/part-m-00000

```
## Mysql Commands Two Table creation
### Task 2

```
mysql -uroot -pcloudera
use zeyodb;

drop table if exists z1;
create table z1 (id int,name varchar(100),city varchar(100),mode varchar(100));
insert into z1 values(1,'zeyo','chennai','cash');
insert into z1 values(2,'hema','hyderabad','credit');
insert into z1 values(3,'raji','chennai','cash');
insert into z1 values(4,'viru','delhi','credit');


drop table if exists z2;
create table z2 (id int,product varchar(100));
insert into z2 values(1,'cookies');
insert into z2 values(2,'mobile');
insert into z2 values(3,'laptop');
insert into z2 values(4,'mouse');

=====Validate Tables====
select * from z1;
select * from z2;
quit
```

## Sqoop import query
### Task 2
```
sqoop import --connect jdbc:mysql://localhost/zeyodb --username root --password cloudera --m 1  --delete-target-dir --target-dir /user/cloudera/joindir --query "select a.*,b.product from z1 a join z2 b on a.id=b.id where \$CONDITIONS"

=====Validate Data====
hadoop fs -ls /user/cloudera/joindir
hadoop fs -cat /user/cloudera/joindir/part-m-00000

```

# CLOUDERA INCREMENTAL
## Mysql Commands
### Task 3
```
mysql -uroot -pcloudera
create database if not exists zeyodb;
use zeyodb;
drop table if exists zeyotab;
create table zeyotab (id int,name varchar(100),city varchar(100),mode varchar(100));
insert into zeyotab values(1,'zeyo','chennai','cash');
insert into zeyotab values(2,'hema','hyderabad','credit');
insert into zeyotab values(3,'raji','chennai','cash');
insert into zeyotab values(4,'viru','delhi','credit');
quit
```

## Sqoop import query
### Task 3
```
sqoop import --connect jdbc:mysql://localhost/zeyodb --username root --password cloudera --table zeyotab --m 1  --delete-target-dir --target-dir /user/cloudera/indir

hadoop fs -ls /user/cloudera/indir
hadoop fs -cat /user/cloudera/indir/part-m-00000
```
## Mysql Commands
### Task 3

```
mysql -uroot -pcloudera
use zeyodb;

insert into zeyotab values(5,'rani','chennai','cash');
insert into zeyotab values(6,'rita','delhi','credit');
select * from zeyotab;
quit

```
## SQOOP Incremental Import
### Task 3

```
sqoop import --connect jdbc:mysql://localhost/zeyodb --username root --password cloudera --table zeyotab --m 1 --target-dir /user/cloudera/indir --incremental append --check-column id --last-value 4

hadoop fs -ls /user/cloudera/indir
hadoop fs -cat /user/cloudera/indir/part-m-00001

```

# Incremental Job Cloudera
## Mysql Commands
### Task 4


```
mysql -uroot -pcloudera
create database if not exists zeyodb;
use zeyodb;
drop table if exists zeyojob;
create table zeyojob (id int,name varchar(100),city varchar(100));
insert into zeyojob values(1,'zeyo','chennai');
insert into zeyojob values(2,'hema','hyderabad');
insert into zeyojob values(3,'raji','chennai');
insert into zeyojob values(4,'viru','delhi');
select * from zeyojob;
quit
```

## SQOOP JOB CREATION WITH PASSWORD FILE
### Task 4

```
cd
echo -n cloudera>passfile

sqoop job --delete zjob

sqoop job --create zjob -- import --connect jdbc:mysql://localhost/zeyodb --username root --password-file file:///home/cloudera/passfile --table zeyojob --m 1 --target-dir /user/cloudera/jobdir --incremental append --check-column id --last-value 0

```

## Execute the Job and Validate
### Task 4

```
sqoop job --exec zjob
hadoop fs -ls /user/cloudera/jobdir
hadoop fs -cat /user/cloudera/jobdir/part-m-00000
```
## Mysql Commands
### Task 4
```
mysql -uroot -pcloudera
use zeyodb;
insert into zeyojob values(5,'ramu','chennai');
insert into zeyojob values(6,'vasu','delhi');
select * from zeyojob;
quit
```

## Execute the Job and Validate
### Task 4

```
sqoop job --exec zjob
hadoop fs -ls /user/cloudera/jobdir
hadoop fs -cat /user/cloudera/jobdir/part-m-00001
```



