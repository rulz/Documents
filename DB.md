# Como crear, eliminar y cargar base de datos por terminal

DROP Database
```
dropdb -U postgres -p 5432 -h localhost -i -e database
```
Create Database
```
createdb -U postgres -p 5432 -h localhost -E UTF8 -e database
```
Load Database
```
pg_restore -U postgres -h localhost -p 5432 -d database < /Users/rulz/Proyectos/SiteRulz/staging.dump
```
