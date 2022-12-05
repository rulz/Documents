Configurar Django en Heroku
=========

[Fuente](https://devcenter.heroku.com/articles/getting-started-with-django)


lo primiero que se debe hacer es clonar el proyecto creado en Heroku

comando para clonar
```git clone git@heroku.com:miproyecto.git```

una vez clonado el repositorio se debe poner tu proyecto en la carpeta clonada

---

Para iniciar este pequeño toturial, debemos ordenar los requerimientos que nos pide Heroku
para que pueda ser instalado con pip



**1. "Ordenar los requerimientos en requirements.txt"**

Recomiendo generar 2 archivos requirements.txt distintos y nombrarlos de la siguiente forma:
```
a) requirements.txt: en este archivo poner todos los requerimientos que Heroku pide para que corra Django en sus servidores.

    -r requirements_base.txt
    psycopg2==2.5.1
    gunicorn==18.0
    wsgiref==0.1.2
```
```
b) requirements_base.txt: en este archivo poner todos los requerimientos que necesita tu proyecto para que corra.

    Django==1.6.1
    Fabric==1.8.1
    django-extensions==1.3.3
    django-debug-toolbar==1.0.1
    Werkzeug
    dj-static==0.0.5
    dj-database-url==0.2.2
    
los 2 últimos requerimientos son los que pide Heroku, el primero es para servir los archivos estaticos y el otro es para poder nombrar que base de datos ocupar según una URL, y se pone en el base porque es un requerimientos que debe llevar el proyecto si o si
```

se preguntarán porque genero 2 archivos, es simple la respuesta, no es necesario instalar los requerimientos que están en requirements.txt en el tu computador (local), por esa razón hago la separación.

***2. "Crear el archivo Procfile"***

[Procfile](https://devcenter.heroku.com/articles/getting-started-with-django#procfile) es un archivo de text sin formato, que debe ir en la raíz de la aplicación, dentro del archivo debe ir un comando que ejecuta el proyecto (lo hace correr), acá dejo el comando:
```
web: gunicorn nombredelproyecto.wsgi
```

si quieres saber que es gunicorn haga click [acá](http://gunicorn.org/)

***3. "Modificar [settings.py](https://devcenter.heroku.com/articles/getting-started-with-django#settings-py)"***

se debe modificar la variable DATABASE de la siguiente forma

```
import dj_database_url
DATABASES = {
        'default': dj_database_url.config(default='sqlite:///' + os.path.join(BASE_DIR, 'db', 'db.sqlite3'))
}
```

***4. "Modificar [wsgi.py](https://devcenter.heroku.com/articles/getting-started-with-django#wsgi-py)"***

modificar las 3 últimas lineas de esta forma

```
from django.core.wsgi import get_wsgi_application
from dj_static import Cling

application = Cling(get_wsgi_application())
```

***5. Hacer un commit***

ya teniendo todo modificado para que nos funcione nuestro proyecto en Heroku,
debemos hacer un commit 

```
git add .
git commit -am "mi primer commit para subir mi proyecto"
git push heroku master
```

se sube el proyecto, Heroku identifica que lenguaje estamos ocupando y empieza a instalar lo que está en requirements.txt las cosas necesaria para que corra nuestro proyecto en sus servidores.

***6. Como hacer un syncdb en Heroku***

```
heroku run ./manage.py syncdb
```

***7. Como entrar al shell en Heroku***

```
heroku run ./manage.py shell
```

***8. Update DB Plan Heroki***

a). crea la db nueva 

b). Información de las db instaladas
```
heroku pg:info --app example-app

=== HEROKU_POSTGRESQL_WHITE_URL
Plan:                  Basic
Status:                Available
Connections:           0/20
PG Version:            14.6

```

c). poner en matención la app
```
heroku maintenance:on --app example-app
```

d). copiar la db a new db
```
heroku pg:copy DATABASE_URL HEROKU_POSTGRESQL_PINK --app example-app
```

e). actulizar la db que voy a ocupar nueva
```
heroku pg:promote HEROKU_POSTGRESQL_PINK --app example-app

###monitoriar
heroku pg:wait --app example-app
```

f). sacar de mantencion
```
heroku maintenance:off --app example-app
```


