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

select * from z1;
select * from z2;
quit
```

## Sqoop import query
### Task 2
```
sqoop import --connect jdbc:mysql://localhost/zeyodb --username root --password cloudera --m 1  --delete-target-dir --target-dir /user/cloudera/joindir --query "select a.*,b.product from z1 a join z2 b on a.id=b.id where \$CONDITIONS"

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
# Local Cloudera To S3 bucket
## Mysql Commands
### Task 5

```
hdfs dfsadmin -safemode leave
mysql -uroot -pcloudera
create database if not exists zdb;
use zdb;
drop table cust;
create table cust(id int,name varchar(100));
insert into cust value(1,'zeyo');
insert into cust value(2,'analytics');
select * from cust;
quit
```

## SQOOP Command
### Task 5
```
sqoop import -Dfs.s3a.access.key=<accessKey> -Dfs.s3a.secret.key=<secretKey> -Dfs.s3a.endpoint=<endpoint> --connect jdbc:mysql://localhost/zdb --username root --password cloudera --table cust --m 1 --target-dir  s3a://<s3BucketName>/<folderName>
```

# Cloudera -SERIALIZATION
## Mysql Commands
### Task 6
```
hdfs dfsadmin -safemode leave
mysql -uroot -pcloudera
create database if not exists zdb;
use zdb;
drop table cust;
create table cust(id int,name varchar(100));
insert into cust value(1,'zeyo');
insert into cust value(2,'analytics');
select * from cust;
quit
```

## Sqoop Import
### Task 6
```
sqoop import --connect jdbc:mysql://localhost/zdb --username root --password cloudera --table cust --m 1 --delete-target-dir  --target-dir  /user/cloudera/pardir --as-parquetfile
```
## Validate Data Import
### Task 6
```
hadoop   fs   -ls    /user/cloudera/pardir
hadoop   fs    -cat   /user/cloudera/pardir/*
```

# 2 Mapper 
## Mysql Commands
### Task 7
```
mysql -uroot -pcloudera
create database mcheck;
use mcheck;
drop table if exists mtab;
create table mtab(id int,name varchar(100),amount int);
insert into mtab values(1,'zeyo',40);
insert into mtab values(2,'vasu',50);
insert into mtab values(3,'rani',70);
insert into mtab values(4,'vani',40);
insert into mtab values(5,'hema',50);
insert into mtab values(6,'hari',70);
insert into mtab values(7,'raki',40);
insert into mtab values(8,'vira',50);
insert into mtab values(9,'rupa',70);
insert into mtab values(10,'Siji',40);
insert into mtab values(11,'rapo',50);
insert into mtab values(12,'visu',70);
select * from mtab;

quit
```
## Sqoop Import
### Task 7
```
sqoop import --connect jdbc:mysql://localhost/mcheck --username root --password cloudera --table mtab --m 2 --split-by id --delete-target-dir --target-dir /user/cloudera/mdata2

hadoop fs -ls /user/cloudera/mdata2/ 

hadoop fs -cat  /user/cloudera/mdata2/part-m-00000 

hadoop fs -cat  /user/cloudera/mdata2/part-m-00001
```
