Django dumpdata and loaddata
=

## dumpdata for all database
```
./manage.py dumpdata > db.json
```
## dumpdata for backup specific app
```
./manage.py dumpdata admin > admin.json
```

## dumpdata for backup specific table
```
./manage.py dumpdata admin.logentry > logentry.json
./manage.py dumpdata auth.user > user.json
```

## dumpdata (--exclude)
```
./manage.py dumpdata --exclude auth.permission > db.json
```

## dumpdata (--indent)
```
./manage.py dumpdata auth.user --indent 2 > user.json
```

## dumpdata (--format)
- json
- xml
- yaml
```
./manage.py dumpdata auth.user --indent 2 --format xml > user.xml
```

# Loaddate
```
./manage.py loaddata user.json
```

# Restore fresh database

```
./manage.py dumpdata --exclude auth.permission --exclude contenttypes > db.json
./manage.py loaddata db.json
```
