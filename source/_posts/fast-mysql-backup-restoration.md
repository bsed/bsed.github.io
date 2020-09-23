---
title: Fast MySQL Backup Restoration
date: 2015-04-05 21:46:00
updated: 2015-04-22 21:34:19
tags: 
categories: 
- default

---
## What’s the Problem?

If you take regular backups using mysqldump, you’ll know that backing up a large database can often take a few minutes to complete. What most people don’t think about when creating these type of backups, is that restoring your database from mysqldump backup files takes much much longer.
<!--more-->
## How Can I Speed Up This Process?

By setting a couple of flags before you import your database dumps, you can dramatically speed up the restore process:
```
SET autocommit=0;
SET unique_checks=0;
SET foreign_key_checks=0;
```
Since we disabled auto-commit, we’ll also need to manually commit at the end of the restore:

｀COMMIT;｀
Since we are restoring an entire database, we can speed things up by disabling unique checks and foreign key checks. Also, by committing everything at the end of the restore, rather than as the restore is in progress we get significant additional speed increases.

## When Should I do This?

You could manually set these flags when restoring your database, but I prefer to add these lines into the backup as it is created.

For example, this bash script will add the speedup lines straight into your backup .sql file:
```
#!/bin/bash
echo "SET autocommit=0;
SET unique_checks=0;
SET foreign_key_checks=0;" > backup.sql
mysqldump -u myuser --password=mypassword mydatabase >> backup.sql
echo "COMMIT;" >> backup.sql
```
I hope this is useful to someone! Let me know in the comments if you have any further optimisations.