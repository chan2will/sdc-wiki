FYI to all: when running the restore data to dev/staging
you’ll want to ssh into the machines and stop the `vulcan` and `celery` containers
otherwise the restore hangs because the existing connections won’t terminate
and the restore can’t drop the tables
if it’s still hanging, you can do a
```SELECT * FROM pg_stat_activity```
on the dev/staging db and figure out why
if somehow some stuff is messed up, then you might try
 ```SELECT pg_terminate_backend(pid) FROM pg_stat_activity WHERE usename='postgres' AND …```
to selectively terminate queries
for instance, today some temporary report table queries were broken
somehow the server hung on creating a temp table in about 9 or 10 different connections