## EF 6 migrate from cmd

SET AssemblyName=DskJalbi
SET StartUpDirectory=C:\PROJECTS\DskJalbi\dsk-jalbi\trunk\DskJalbi\bin
SET ConnectionString=Data Source=.\SQLEXPRESS; Database=dskclaims; Integrated Security=True;
SET ConnectionStringProvider=System.Data.SqlClient
SET ConfigFilePath=C:\PROJECTS\DskJalbi\dsk-jalbi\trunk\DskJalbi\Web.config
SET MigrateExe=C:\PROJECTS\DskJalbi\dsk-jalbi\trunk\packages\EntityFramework.6.1.1\tools\migrate.exe

%MigrateExe% %AssemblyName%.dll /startUpDirectory:%StartUpDirectory% /startUpConfigurationFile:"%ConfigFilePath%" /connectionProviderName:"%ConnectionStringProvider%" /connectionString:"%ConnectionString%" /verbose
pause
