# Como crear y cargar base de datos por terminal

```
dropdb -U postgres -p 5432 -h localhost -i -e database
```
```
createdb -U postgres -p 5432 -h localhost -E UTF8 -e database
```
```
pg_restore -U postgres -h localhost -p 5432 -d database < /Users/rulz/Proyectos/SiteRulz/staging.dump
```
