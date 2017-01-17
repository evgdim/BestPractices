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
#Generate Java class and Spring row mapper from trables

```
DECLARE
  TARGET_SCHEMA VARCHAR2(20) := 'MYUSER';
  NL char := chr(10);
  CLASS_STR_TMP VARCHAR2(32767) := '@Data'||NL||'public class ';-- lombok @Data
  ROWMAPPER_STR_TMP VARCHAR2(32767) := 'public class ';
  JAVA_DATA_TYPE VARCHAR2(100);
  JAVA_CLASS_NAME VARCHAR2(100);
  JAVA_PROP_NAME VARCHAR2(100);
BEGIN
   FOR t IN (SELECT * FROM ALL_TABLES WHERE owner = TARGET_SCHEMA) LOOP
    JAVA_CLASS_NAME := replace(initcap(t.table_name),'_');
    CLASS_STR_TMP := CLASS_STR_TMP || JAVA_CLASS_NAME || ' {'|| NL;
    ROWMAPPER_STR_TMP := ROWMAPPER_STR_TMP || JAVA_CLASS_NAME || 'RowMapper implements RowMapper<' || JAVA_CLASS_NAME ||'> {' || NL ||
      '   @Override' || NL ||
      '   public ' || JAVA_CLASS_NAME || ' mapRow(ResultSet rs, int i) throws SQLException { ' || NL ||
      '      ' || JAVA_CLASS_NAME || ' ' || lower(JAVA_CLASS_NAME) || ' = new ' || JAVA_CLASS_NAME || '();' || NL;
    
    FOR c IN (SELECT * FROM ALL_TAB_COLS WHERE TABLE_NAME = t.table_name ORDER BY COLUMN_ID) LOOP
      JAVA_DATA_TYPE := CASE c.DATA_TYPE
        WHEN 'VARCHAR2' THEN 'String'
        WHEN 'NUMBER' THEN 'BigDecimal'
        WHEN 'INT' THEN 'Integer'
        WHEN 'DATE' THEN 'Timestamp'
        WHEN 'TIMESTAMP' THEN 'Timestamp'
        ELSE '<Unknown>'
      END;
      JAVA_PROP_NAME := replace(initcap(c.COLUMN_NAME),'_');
      CLASS_STR_TMP := CLASS_STR_TMP || '   private ' || JAVA_DATA_TYPE || ' ' || JAVA_PROP_NAME || ';' || NL;
      ROWMAPPER_STR_TMP := ROWMAPPER_STR_TMP || '      ' ||
        lower(JAVA_CLASS_NAME) || '.set' || initcap(JAVA_PROP_NAME) || '(rs.get' || JAVA_DATA_TYPE || '("'||c.COLUMN_NAME||'"));' || NL;
    END LOOP;
    CLASS_STR_TMP := CLASS_STR_TMP || NL || '}';
    ROWMAPPER_STR_TMP := ROWMAPPER_STR_TMP || '      return ' || lower(JAVA_CLASS_NAME) || ';' || NL ||
      '   }' || NL ||
    '}';
    
     dbms_output.put_line('===================================');
     dbms_output.put_line(CLASS_STR_TMP);
     dbms_output.put_line('');
     dbms_output.put_line('...................................');
     dbms_output.put_line('');
     dbms_output.put_line(ROWMAPPER_STR_TMP);
     dbms_output.put_line('===================================');
     CLASS_STR_TMP := '';
     ROWMAPPER_STR_TMP := '';
   END LOOP;  
EXCEPTION
   WHEN others THEN
      dbms_output.put_line('asd');
END;
```
