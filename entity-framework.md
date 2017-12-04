## EF 6 migrate from cmd

SET AssemblyName=MyProject
SET StartUpDirectory=C:\PROJECTS\MyProject\bin
SET ConnectionString=Data Source=.\SQLEXPRESS; Database=mydb; Integrated Security=True;
SET ConnectionStringProvider=System.Data.SqlClient
SET ConfigFilePath=C:\PROJECTS\MyProject\Web.config
SET MigrateExe=C:\PROJECTS\MyProject\packages\EntityFramework.6.1.1\tools\migrate.exe

%MigrateExe% %AssemblyName%.dll /startUpDirectory:%StartUpDirectory% /startUpConfigurationFile:"%ConfigFilePath%" /connectionProviderName:"%ConnectionStringProvider%" /connectionString:"%ConnectionString%" /verbose
pause
