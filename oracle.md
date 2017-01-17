#Data pump
**check DATA_PUMP_DIR**

```select * from dba_directories where DIRECTORY_NAME = 'DATA_PUMP_DIR';```

```create or replace directory data_pump_dir as 'D:\orabcp';```

```grant read,write on directory DATA_PUMP_DIR to <user>;```

**export**

```expdp c##test/test@ORCL schemas=c##test directory=DATA_PUMP_DIR dumpfile=testdump.dmp logfile=expdpTest.log```

**import**

```impdp c##test/test@ORCL schemas=c##test directory=DATA_PUMP_DIR dumpfile=testdump.dmp logfile=impdpTest.log```

#Clear schema
```select 'drop '||object_type||' '|| object_name||';' from user_objects;```

#Regex split
```
  with test as 
  (
    select 'ABC;DEF;GHI;JKL;MNO' str from dual  
  )  
  select regexp_substr (str, '[^;]+', 1, rownum) split  
  from test  
  connect by level <= length (regexp_replace (str, '[^;]+'))  + 1;
```
#Create tablespace and user/schema
```
CREATE BIGFILE TABLESPACE myuser_ts
  DATAFILE 'myuser_ts.dat'
  SIZE 20M AUTOEXTEND ON;

CREATE USER myuser IDENTIFIED BY myuser;
GRANT CONNECT TO myuser;
GRANT RESOURCE TO myuser;

ALTER USER myuser QUOTA UNLIMITED ON myuser_ts;

--revert
drop user myuser cascade;
drop tablespace myuser_ts including contents and datafiles;
```
