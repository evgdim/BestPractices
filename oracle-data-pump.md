# check DATA_PUMP_DIR
select * from dba_directories where DIRECTORY_NAME = 'DATA_PUMP_DIR';
create or replace directory data_pump_dir as 'D:\orabcp';

grant read,write on directory DATA_PUMP_DIR to \<user\>;

#export
expdp c##test/test@ORCL schemas=c##test directory=DATA_PUMP_DIR dumpfile=testdump.dmp logfile=expdpTest.log

#import
impdp c##test/test@ORCL schemas=c##test directory=DATA_PUMP_DIR dumpfile=testdump.dmp logfile=impdpTest.log
