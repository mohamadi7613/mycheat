
# Postgresql

<!-- writing : mohamadi_arch -->


### Naming conventions

| Common Name               | Postgresql Name            |
| ------------------ | --------------------------------- | 
|table or Indexes    | Relation                             |
|Row                 | Tuple                                |
|Column              | Attribute                            |
|Data Block          | Page(when block is in disk)          |
|Page                | Buffer(when block is in memory)      |

### Page
Page: smallest unit of storage. Every table and index is stored as an array of pages of fixed sizes. default size= 8kb
All pages are logicaly equivalent and any row can be stored in any page. and we cant choose which row go to which page

| Page component                  | Description    | 
|-------------------------------- |  ------------------------------------------ |
| PageHeader Data                 | Genaral info like free space pointer, page size, version    | 
| ItemId Data                    | Pointer to a Tuple     | 
| Tuple                          | Actual items     | 
|  Free space                    | New pointers insert from top, Tupels Insert from bottom     | 
| special space                   | Index access method specific data     | 


## Postgresql architecture

poastmaster: 
the first process that get startted: 1. start proccess 2. assing buffers 3. check ip and password
when user send a requet. postmaster starts a new process (a new postgres per each client)to handle that request.

shared buffer:
when user send an sql request the result will load in shared data.(temporary location)
User can not access data files directly to read or write data
controlling by a parameter: shared_buffer located in postgresql.conf

dirty data:
the value in memory(not in disk) or uncommited data

commited transaction:
the transaction that is completed


Wal buffer:
if user send a transaction, a copy of transactions will be stored in wal buffer. When the user commit the changes the wall buffer
will involke the wal writer, which will write all the changes to the wal files on disk.



checkpointer:
checks every 5 minutes will involke the Bgwriter to save all changes (dirty buffers in shared buffer) into data files
we can change default time
it also involk when the wal buffer is full(1GB)

Archiver process: (optioal)
checks the wall file and if it is full, Archiver process copies the wal file to archive file
in test systems we dont enable it bcz it needs lot of disk space. useful for recovery. (imp for production)

CLOG buffer:
all commited transactions are marked in this buffer. So if system crashes this buffer referred to check all the commited transactions
is an area in RAM that holds the state of all transactions

stat collector:
collect and report information about server activity then update this information to the database dictionary
so db dictioanry will be updated with the latest happening information(pg_catalog)

Work memory buffer:
is memory reserved for eighter a single sort of hash table (sort of commnads) [work_mem parameter]

Maintenance Work memory:
for maintenance work [maintenance_work_mem parameter]

Temp buffers:
used for temporary tables in a user session during large sort and hash table [parameter temp_buffers]
as soon as the session ends this memory will be freed automatically

Auto vaccum launcher:
vacuum daemon to carry out the vacuum operations on fragmented tables

log collector or log writer:
ensure all logging and tracing information in the error log. exp: Postgresql does not start.

BgWriter or Writer:
Preodicaly writes the dirty buffer to a data file
(make changes to a data by transaction)

Wal writer:
writes the wal buffer to a wal file 
this happens when we do a commit




## Physical Files 


data file:
final location, pure data


wal data:
metadata information about changes to the acutal data and it is sufficent for recovery 
all transactions are written first before commit happens
wal data is stored in a physical disk in location: "WAL segment"
wal writer flushed the wal data from the buffer to wal segment
wal files: Write Ahead Log files


log files:
all server messages: stderr,csvlog,syslog


archive files(optional):
data from wal segments are writtern into archive files used for recovery


## folder layout
```bash
bin                    #
data                   #
debug_symbols          # for developers debugging
doc                    # doc of postgresql
include                # header files
installer               # for server installer
pgAdmin                 # for server admin in gui interface
share                  # sample of all config files for rebuilding
```

## data directory folder layout
```bash
base                    # a directory for each database
global                  # cluster wide tables such: pg database, pg tablespace,pg indexes,
log                      # all logs
pg_commit_ts             # commit timestamp data
pg_dynshmem             # dynamic shared memory subsystem
pg_logical              # all logical decoding,snapshots,mappings 
pg_multixact            # multi transaction staus data
pg_notify                 # notifications status data
pg_replslot               # when we enable replication slots
pg_serial                  # commited serialiezed transaction
pg_snapshots                # exported snapshots
pg_stat                    #permanent files for stat subsystem
pg_stat_tmp                # temporary files for stat subsystem
pg_subtrans                # subtransaction status data
pg_tblspc                  # contain symbolic links to the tablespace data
pg_twophase              #state files which is for prepaid transactions
pg_wal                   # write ahead log files
archive_status             # if we enable archiver
pg_xact                    # transaction commit data, all your transactions metadat logs
pg_hba.conf                # postgresql authentification (Host Base Authentication)
pg_ident.conf              # when we map user data to postgresql user
pg_version                 # postgresql version
postgresql.conf            # postgresql server config
postgresql.auto.conf        # alter system commands will be stored here (override the postgresql.conf)
```
## postgresql default databaes
```bash
postgres                  # if you login with postgres user you will connect to this db
template0                # a pristine copy of template for new databaes ( no connection allowed) 
template1                # a pristine copy of template for new databaes ( connection allowed) 
CREATE DATABASE newbd TEMPLATE template0;
select datname,oid from pg_database;      # no datanem becarful, oid= object identifier, pg_database inside global folder
```

## cluster
db cluster: is a collection of databaes that is managed by a single instance on a server
Initdb ===> creates a new  postgresql db cluster
data directory= creating a data cluster consist of creating the directories in which data is store
we have to first initilize the storge area on the disk before we any operation on the database


## STOP, START, PostgreSQL
```bash
sudo service start postgresql      # stop, restart, status in linux
sudo systemctl start postgresql     # stop, restart, status in linux
# WINDOWS ===> go to services or:
net stop postgresql-x64-16                 # 16 is version
net start postgresql-x64-16               # windows cmd
```

## windows vs linux
in windows we should use "" double cotation but in linux not
in windows we should use `-U user` but in linux we should login as this user


#################################################################################################

## login
```bash
psql -U postgres                                # -U for user. default user is postgres, default table is postgres
psql -U username -d database                   # for non-superusers enter database
```
# psql commands
```bsah
\?                                          # help
\! echo 'hello world'                        # terminal command by \!
exit or \q                                   # exit psql
\l                                          # list databases
\conninfo                                   # information about current database
\c postgres                                 # connect to database default database 
\c postgres ali                             # connect to db postgres as user ali
\du                                         # describe list all users
\dn                                         # describe list all schemas
\di                                         # describe list all indexes
\dt                                         # list all tables
\dt+                                        # list all tables with detail
\d customers                                  # describe table columns
\df \dv \ds                                          # list all f:functions v:views s:sequences
\g                                         # execute previous successful command
\e                                         # edit previos command
\ef inc                                          # edit function inc
\timing                                        # timing on
\o outfile.txt                                 # redirect output to outfile.txt
\set AUTOCOMMIT off                            # disable autocommit after transactions. use [rollback] or [commit] command after that
\i 1.sql                                       # import sql file
```


## start cluster in linux
```bash
su -u postgres                                         # change user
sudo -u postgres psql
ps -ef | grep postgres                                # check if postgres is running
initdb -D /usr/local/psql/data                  # initilize a new db -D refer to data directory location(if folder is empty we should init cluster)
# -W we can use -W to force super user to provide password before initialize db
pg_ctl -D /usr/local/pslq/data initdb     # initilize second syntax databse
pg_ctl -D /usr/local/pslq/data -p 5432     # we should set a a new  port to new created cluster
systemctl start postgresql-12              # start cluster in linuc
systemctl stop postgresql-12                # stop cluster in linux
systemctl enable postgresql-12              # enable cluster to automatically start after reboot
system reload postgresql-11                     # reload cluster in linuc
systemctl restart postgresql-11                   # restart cluster in linux
```

## start cluster in windows
```bash
pg_ctl start                                  # start cluster in windows if set in environment variable
pg_ctl stop                                    # stop cluster in windows
pg_ctl -D "C:\Program Files\PostgreSQL\12\data" start     #  start cluster in windows if you do not set the path in environment variable
pg_ctl stop -D "C:\Program Files\PostgreSQL\12\data" -m        # stop cluster in windows
ppg_ctl reload                                   # reload cluster in windows
pg_ctl restart                                  # restart cluster in windows
pg_controldata                                # information about current cluster
```
## psql

```bash
psql -U postgres -p 5432 -h localhost -d postgres     # connect to cluster -p: port -h: host -d: database
psql -U postgres                                # -U for user. default user is postgres
psql -U postgres -d customers -f 1.sql                     # import data from a file(write some insert sql in that file) or \i 1.sql
show data_directory                              # show data directory
show port                                       # show port
show work_mem                                   # show work memory
show max_connections                             # show something inside postgres.conf, no need to open file manually
show search_path                                 # sort of searching namespaces
select name,boot_val,source from pg_settings    # see something in postgres.conf
select * fomr pg_file_settings                   # data dictionary view
\d pg_settings                                   # all columns in postgres.conf
alter system set work_mem='64MB'                 # set work memory in postgresql.auto.conf
alter system reset work_mem                      # reset work memory
alter system reset all                           # reset all properties in postgresql.auto.conf
select pg_reload_conf();                          # reload cluster in pqsl
```

## sql in command line
```bash
createdb -U ali -p 5432 -h localhost -d customers   # create new database for customers
createdb -U ali customers                            # create new database for customers
dropdb -U ali customers     # drop database
createuser -U postgres -P -s ali                       # create new user -P(password) -s(not super user) -S(super user)
createuser -U postgres --interactive                       # create new user with asking questions
dropuser -U postgres ali                                    # drop user ali
```

## sql inside psql
```bash
# do not forget semicolon at the end;
create database customers owner alice;            # create new database
drop database customers;                          # drop database
create user ali login superuser password '123';    # create new user
revoke connect on database customers from public;      # revoke all permissions from public(not super uers)
drop user ali;                                    # drop user (if uesr created table we get error)
create schema fincance;                           # create new schema
create schema authorization ali;                       # create schema belong to user ali (sql selects with same table names will search inside this schema) ==> search_path
create table fincance.customer(id int);                  # create table in fincance scheama
select * from pg_tables where tablename='customer';      # check all tables in diff schemas with this name
drop schema fincance;                            # drop schema
drop schema fincance cascade;                    # drop schema with all dependencies (force)
```

### types of shotdown
1. Smart ===>disallows new connection but let existing setions work noramally
2. Fast(default) ===> kill all current transactions
3. Immediate ====> abort all current transactions and lead to recovery on next startup

## postgres.conf
```bash
initdb                                               # installs a defaul copy of postgres.conf located in data directory

```

### pg_hba.conf
```bash
listen_addresses = '*' ==> postgresql.conf              # '*' means all ip address in file postgresql.conf (we can set multiple ip address)
host all all 0.0.0.0/0 md5 | crypt                             # 0.0.0.0/0 means all ip address and md5 means password required, crypt means encrypt
host all all ::0/0 reject                                # ::0/0 means all ip address and reject means denie access
host all all 127.0.0.1/32 trust                           # 127.0.0.1/32 means localhost and md5 means trust means password not required 
```

## privliges
privileges are used to control what users can do with what database
there are two types of privileges
1. cluster level: granted by super user
2. object level: granted by super user or owner


```bash
alter user ali with nosuperuser;                            # change user privileges (\du)
alter user ali createdb;                                    # cluster privileges
grant select on customers to ali;                           # grant or rovoke object level privilage to other user
grant connect on database customers to ali;                 # grant cluster level privilage to other user
grant usage on schema fincance to ali;                      # grant cluster level privilage to other user
grant all privileges on all tables in schema fincance to ali;             # grant cluster level privilage to other user
grant select (col1), insert (col2) on customers to ali;    # grant object level privilage to other user
revoke select on customers from public;                        # revoke object level privilage from all users
```

## pg_catalog
PostgreSQL stores metadata information about the database and cluster in the pg_catalog schema
| system schemas  | Descrtiption   |
|-------------- | -------------- | 
| pg_database    | stores general database info like \l     |
|pg_tables|all tables in  postgresql|
|pg_stat_database| contains stat information, how many transactions is currently run |
|pg_tablespace| tablespace info|
|pg_operator| all operator information|
|pg_available_extenstion| list all available extenstions|
|pg_shadow|all the users in the system like \du |
|pg_timezone_names|list of timezones|
|pg_logs|if transaction not completed will usefull|
|pg_settings|where name='max_connections'|
|pg_indexes||
|pg_views|all schema and all views|
|current_schema()|current schema|
|current_user()||
|current_database()||
|current_setting('max_connections')||
|pg_postmaster_start_time()||

```bash
select * from pg_database                 # display all databases info like \l
select current_user()
select current_database()
```

## time
```bash
select now() as current;                  # set alias
select( now() + interval '1 hour') an_hour_later    # one hour later [1 day, 2 hours 30 minutes] [+ -]

```

# SQL
-----------------------------------------------------------
## data types
Boolean
serial
Character Types [ such as char, varchar, and text]
Numeric Types [ such as integer and floating-point number]
Temporal Types [ such as date, time, timestamp, and interval]
UUID [ for storing UUID (Universally Unique Identifiers) ]
Array [ for storing array strings, numbers, etc.]
JSON [ stores JSON data]
hstore [ stores key-value pair]
Special Types [ such as network address and geometric data]

## types of constraint
NOT NULL Constraint [Ensures that a column cannot have NULL value.] 
UNIQUE Constraint [Ensures that all values in a column are different.] 
PRIMARY Key [Not NULL Constraint + UNIQUE Constraint] 
FOREIGN Key [ Constrains data based on columns in other tables.]
CHECK Constraint [ The CHECK constraint ensures that all values in a column satisfy certain conditions.]
EXCLUSION Constraint[ensures that if any two rows are compared on the specified column(s) or expression(s) using the specified operator(s), not all of these comparisons will return TRUE.]

## tanble inheritance
child table inherits all columns from parent table

```bash
CREATE TABLE child_table (col1, col2) INHERITS (parent_table);
SELECT * FROM parent_table;                                       # display all columns from parent table and child records
SELECT * FROM ONLY parent_table;                                       # display columns of only parent
UPDATE parent SET col1 = 'val1';                                     # update parent table and child tables also
UPDATE ONLY parent SET col1 = 'val1';                                     # update only parent table
DROP TABLE parent;                                               # we can not delete parent table if it has child
DROP TABLE parent cascade;                                               # delete parent table and its child forcefully
```

### psql syntax only
```sql
SELECT ... LIMIT [num] OFFSET [num];       # limit and offset
```

-----------------------------------------------------
## table partitioning
splitting tables into smaller pieces (performance)
postgresql supports table partitioning via table inheritance and also with range, list and hash partitioning methods
master table is better to do not hold any data
we should create a trigger function to do the partitioning to run before inserting data

```bash
CREATE TABLE child_table (CHECK (col1 > 0 and COL1 <100)) INHERITS (parent_table); 
CREAT INDEX index_name ON table_name using btree(col1)                         # btree is defulat [optional]

```

## copy table
```bash
CREATE TABLE new_table AS old_table;                       # copy structure and the data
CREATE TABLE new_table AS SELECT * FROM old_table;
CREATE TABLE new_table AS old_table WITH NO DATA;                       # copy structure and no data
```

## TableSpace
postgresql stores data logically in tablespace and physicaly in data files, so tablespace does not hold any data (just a pointer)
postgresql uses tablespace for map a logical name to physical location on disk
default location of tablespace is data directory 
```bash
pg_default                                                  #stores all user data
pg_global                                                   #stores all global data
select * from pg_tablespace;                                # display all tablespaces 
CREATE TABLESPACE tablespace_name LOCATION '/path/to/tablespace';   # create a new tablespace
CREATR TABLE table_name(col1 , col2) TABLESPACE tablespace_name;    # create a table inside tablespace
ALTER TABLE table_name SET TABLESPACE tablespace_name;             # change tablespace of a table
SELECT pg_relation_filepath('table_name');    # where is table located
############
CREATE TABLESPACE tablespace_temp_name OWNER username LOCATION '/path/to/tablespace';    # create a temporary tablespace
set temp_tablespaces = 'tablespace_temp_name';          # set in postgresql.conf, postgresql will create this folder automatically and delete after shutdown the database
############
```

## Backup
we do not have a specific command to backup a database
1. logical backup: simple textual representation ==> save into sql script file
2. physical backup : in binary format and not human readable
    - online backup: the backup is taken when the system in up
    - offline backup: the backup is taken when the system in down

#### logical backup
databases less than 100GB are good for logical backups
```bash
pg_dump -U user -d database > ./backup.sql                # dump a database (textaul by default)
pg_dump -U user -Fc > ./backup.dump                      # create a custome dump -F (format) -c(custome dump so non textaul and human readable)
pg_dumpall -U user > ./backup.sql                         # dump all databases (cluster) [user should be superuser]
pg_dumpall -U user -f ./backup.sql                        # dump all databases -f for file
pg_dumpall | gzip > /opt/user_backups/clusterall_bkz.gz   # dump cluster and compress it
pg_dumpall | split -b 100m - /opt/backup/cluster_bkz                                # 100g (GB) 100k(KB)
###########----Backup---#############
CREATE DATABASE mydatabase;                    # for backup we should first create that database
psql -U postgres -d mydatabase < ./backup.sql   # restore backup
###########----Backup---#############
# pg_restore can not restore from textaul file but can restore from custome file backup
# we can restore a table or whole db
pg_restore -U user -d database_name -t table_name ./backup.dump            # restore table from backup.
```

#### physical backup 1.offline
the database should be shutdown in order to get a usable backup ==> not good for production
the database should be shutdown before restoring date
partial store or single table sotre not possible ==> entire cluster
```bash
pg_ctl stop                                           # stop cluster
tar -cvzf ./backup.tar /car/lib/psql/14/data .  # tar backup the data directly
```
#### physical backup 2.online 
continuous backup ==> good for production
PITR ==> point in time recovery

```bash
###########-----archiving wal files---- ##################
show archive_mode;                                       # show status of archive mode
# stop cluster with "pg_ctl stop;" and change below parametes in postgresql.conf 
###postgres.conf
wal_level=replica
archive_mode = on
archive_command = 'copy "%p" "C:\\archieve\\%f" '    # copy wal into archive windows  %f=name of the file
archive_command = 'cp -i %p /opt/archieve/%f'    # copy wal into archive linux
####
# select pg_start_backup('test1')                 # [you can give any name like test1] before start backup,checking wal files getting copied or not?
selct pg_stop_backup()                         # write all required wal files
# archive files work in conjunction with wal full backup inorder to resotre a database until certain time
# Base backup is critical and without that wal files are useless
######## -----1.method one: low level api backup---- #############
pg_start_backup('test1',false,false)                       # return the control back to user for firt parameter, non exclusive backup for second parameter
tar -cvzf ./backup.tar /var/lib/psql/14/data .  # tar backup the data directly, but current wal file will not archive [its ok]
select pg_stop_backup('f')                  # f for non exclusive, t for exclusive [archiveing all wal files even current wal file]
########-----2.method two: pg_base_backup------##########
# full backup:data_directory + wal files
# wen con not use this method for restoring one database or on object, we just restore the whole cluster bvz this is not a dump file, this is binary
selct pg_switch_wal()                         #   for check archive mode is working or not
pg_basebackup -U user -h host -D "C:\\path\\to\\backup" -Ft -z -P -Xs                  # -Ft(format tar), -Fp(format palin text) -z(gzip) -P (progress bar) -X(all data with all transactions during the time of backup) -s(stream)
```

## PITR (Recovery)

**Steps to perform PITR**
1. `select pg_switch_wal();`  for save all transactions after the backup and before the stopping of database [note: you do not need to do this in real time bcz there will be lots of transactions which are happening in the backend and your wal buffer will get full like 16MB and the log switch will happen automatically]
2. stop the server ==> pg_ctl stop
3. copy the whole old cluster data directory and any tablespaces into a temp file ==> mv
4. remove all directories and files from cluster data directory
5. restore the databases from backup [copy basebackup to data directory]
6. remove old files present in pg_wal directory in the file system backup [they are old]
7. if there are any unarchived Wal segment files recovered from crashed cluster, copy them to pg_wal directory
8. set postgres.conf
9. create an empty file recovery.signal in the cluster data directory [ for version 12 before its different] [tell postgres start recovery]
10. temporary modify pg_hba.conf to prevent ordinary users from connecting
11. start the server. the server will go into recovery mode and proceed to read through the archived WAl files it needs
12. `select pg_wal_reply_resume()`  for start restore
13. Upon completion of the recovery process, the server will remove recovery.siganl
14. Inspect the contents of the database to ensure the recovered database is in desired state
15. modify pg_hba.conf to allow users to connect to the database


```bash
####postgres.conf
# find the location from archive_command
restore_command = 'copy "C:\\archieve\\%f" "%p"'    # all the archive files will be retrive from this location in windows
restore_command = 'cp -i /opt/archieve/%f %p'    # copy wal into archive linux
Recovery_Target_Inclusive = specifies whether to stop the recovery just after the target is reacheed(on) or just before the recover target(off). [default is on]
# we should set one of this below recovery ways
#######--------wayas-----########---------------------------------------------
# If you set more than one way you will get error
Recovery_Target_Time = "2000-01-01 00:00:00 EDT"                  #specifies the time upto which recovery will proceed [*IMP]
###----LSN-----###
Recovery_Target_Lsn = output of up select command                     #Lsn = Log Seqence Number [Recover until this LSN]
select pg_current_wal_lsn(), pg_walfile_name(pg_current_wal_lsn())  #show current wal lsn
# LSN: whenever the logs are getting generated, a sequence number is assigned to each log
# Recovery_Target_Lsn specifies the LSN pf wrote ahead log location up to which reocvery will proceed
###----LSN-----###
Recovery_Target = immediate               # specifies the reocvery should end as soon as consistent state is reached [one value]
Recovery_Target_Name =specifies the named restore            # restore point name [point(create with pg_create_restore_point)]
Recovery_Target_Xid = specifies the transaction id upto which recovery will proceed
#######--------wayas-----########---------------------------------------------
```







