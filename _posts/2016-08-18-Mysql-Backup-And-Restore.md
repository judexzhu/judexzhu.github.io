---
layout: post
title: "Mysql Backup and Restore"
description: "Mysql Backup and Restore"
tags: [Linux, Mysql]
---

### Backup

```bash
mysqldump --user=mysqluser --password=mysqluserpassword --all-databases > /backup/backupname_`date +"%Y%m%d"`.sql 
```

### Restore

```bash
# mysql -uroot -p
Enter password:
MariaDB [(none)]> use databasename;
MariaDB [databasename]>source /path/to/the/backup/sqlfile.sql;
```
