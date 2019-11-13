![CLEVER DATA GIT REPO](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/0-clever-data-github.png "李聪明的数据库")

# 使用触发器自动创建LiteSpeed备份作业
### Automatically Create LiteSpeed Backup Job With Trigger
****发布-日期: 2008年02月07日 (评论)**

![##############](images/image0012.png?raw=true "#######")

## Contents

- [中文](#中文)
- [English](#English)
- [SQL Logic](#Logic)
- [Build Info](#Build-Info)
- [Author](#Author)
- [License](#License) 


## 中文
为刚创建的每个新数据库自动创建一个LiteSpeed备份作业。
在主表上创建一个触发器（在sql 2005中禁止），它只在使用新数据库名称更新时运行以下过程。

## English
Automatically create a LiteSpeed backup job for each new database created just
create a trigger on the master table (forbidden in sql 2005) which simply runs the following procedure
upon updated with a new database name.


---
## Logic
```SQL
 CREATE PROCEDURE [dbo].[sp_createbackupjob]
AS
  /****** Object: Job [LITESPEED FULL BACKUP @newdb]  ******/
  BEGIN TRANSACTION
  DECLARE @ReturnCode INT
  SELECT @ReturnCode = 0
  /****** Object: JobCategory [[Uncategorized (Local)]]]  ******/
  IF NOT EXISTS
  (
         SELECT NAME
         FROM   msdb.dbo.syscategories
         WHERE  NAME=N'[Uncategorized (Local)]' AND category_class=1) BEGIN EXEC @ReturnCode = msdb.dbo.sp_add_category @class=N'JOB', @type=N'LOCAL', @name=N'[Uncategorized (Local)]'
         IF (@@ERROR 0
         OR
         @ReturnCode 0) GOTO quitwithrollback
       END
       declare @newdb varchar(50)
       SET @newdb =
       (
                SELECT TOP 1
                         [name]
                FROM     master..sysdatabases
                ORDER BY dbid DESC)
       DECLARE @desc VARCHAR(100)
       SET @desc = 'litespeed full BACKUP ' + @newdb
       DECLARE @mycommand VARCHAR(300)
       SET 
       	@mycommand = 'exec master.dbo.xp_backup_database @database = ' + @newdb + ',
         @filename = N'E:\BACKUP\'                                        + @newdb + ' litespeed_full.bkp",
         @backupname = n"'                                                + @newdb + ' backup",
         @init = 1,
         @with = n"skip",
         @with = n"stats = 10"'
       DECLARE @jobId BINARY(16)
       EXEC @ReturnCode
         = msdb.dbo.sp_add_job @job_name=@desc,
         @enabled=1,
         @notify_level_eventlog=0,
         @notify_level_email=0,
         @notify_level_netsend=0,
         @notify_level_page=0,
         @delete_level=0,
         @description=@desc,
         @category_name=N'[Uncategorized (Local)]', @owner_login_name=N'sa', @job_id = @jobId OUTPUT IF (@@ERROR 0 OR @ReturnCode 0) GOTO QuitWithRollback /****** Object: Step [Full Backup] Script Date: 04/14/2008 14:06:22 ******/ EXEC @ReturnCode = msdb.dbo.sp_add_jobstep @job_id=@jobId, @step_name=N'Full Backup', @step_id=1, @cmdexec_success_code=0, @on_success_action=1, @on_success_step_id=0, @on_fail_action=2, @on_fail_step_id=0, @retry_attempts=0, @retry_interval=0, @os_run_priority=0, @subsystem=N'TSQL', @command=@MyCommand, @database_name=@newdb, @flags=2 IF (@@ERROR 0 OR @ReturnCode 0) GOTO QuitWithRollback EXEC @ReturnCode = msdb.dbo.sp_update_job @job_id = @jobId, @start_step_id = 1 IF (@@ERROR 0 OR @ReturnCode 0) GOTO QuitWithRollback EXEC @ReturnCode = msdb.dbo.sp_add_jobschedule @job_id=@jobId, @name=N'Backup', @enabled=1, @freq_type=4, @freq_interval=1, @freq_subday_type=1, @freq_subday_interval=0, @freq_relative_interval=0, @freq_recurrence_factor=0, @active_start_date=20050419, @active_end_date=99991231, @active_start_time=10000, @active_end_time=235959 IF (@@ERROR 0 OR @ReturnCode 0) GOTO QuitWithRollback EXEC @ReturnCode = msdb.dbo.sp_add_jobserver @job_id = @jobId, @server_name = N'(local)'
       IF (@@ERROR 0
       OR
       @ReturnCode 0) GOTO quitwithrollback
       COMMIT TRANSACTION GOTO endsave QUITWITHROLLBACK:
       IF (@@TRANCOUNT > 0)
       ROLLBACK TRANSACTION
       ENDSAVE: 

```



[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

- **李聪明的数据库 Lee's Clever Data**
- **Mike的数据库宝典 Mikes Database Collection**
- **李聪明的数据库** "Lee Songming"

[![Gist](https://img.shields.io/badge/Gist-李聪明的数据库-<COLOR>.svg)](https://gist.github.com/congmingshuju)
[![Twitter](https://img.shields.io/badge/Twitter-mike的数据库宝典-<COLOR>.svg)](https://twitter.com/mikesdatawork?lang=en)
[![Wordpress](https://img.shields.io/badge/Wordpress-mike的数据库宝典-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

---
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Lee Songming](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/1-clever-data-github.png "李聪明的数据库")

