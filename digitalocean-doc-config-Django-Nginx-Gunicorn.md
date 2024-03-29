Configurar Django, Nginx y Gunicorn en DigitalOcean
==========

por mi parte instale Ubuntu 12.04.3 x32

---

**1. "Actulizar y bajar paquetes linux"**
```
sudo apt-get update && upgrade
```

**2. "Instalar [pip](http://lcaballero.wordpress.com/2013/03/20/instalacion-de-paquetes-python-con-distribute-y-pip/)"**
```
sudo apt-get install python-pip
```

**3."Install [virtualenvwrapper](http://virtualenvwrapper.readthedocs.org/en/latest/)"**
```
pip install virtualenvwrapper
```

**3.1"Configurar Virtualenvwrapper"**
```
export WORKON_HOME=/opt/Envs
mkdir -p $WORKON_HOME
source /usr/local/bin/virtualenvwrapper.sh
mkvirtualenv env1
```

***3.1.1 "Opcional pero recomendado"***

Dejar éstos comando que salen en el punto 3.1 en el siguiente archivo, (también recomendado para las variables de entorno)
```
vim ~/.profile
```

Pegar comandos 
```
WORKON_HOME=/opt/Envs
source /usr/local/bin/virtualenvwrapper.sh
```
Guardar los cambios y luego

```
source ~/.profile
source ~/.bashrc
```

**3.2"Activar y descativar Virtualenvwrapper"**

* Activar
```
workon env1
```
* Desactivar
```
deactivate
```

**4."Instalar [GIT](https://github.com/)"**
```
sudo apt-get install git
```
**4.1 "opcional"**
**4.1.1 "Generar la llave"**
```
ssh-keygen
cat ~/.ssh/id_rsa.pub
```
la llave que generas al hacer cat debes ponerlo en [github>account-settings>ssh-key](https://github.com/settings/ssh) luego el botón Add SSH key pegas el código y haces clic en el botón Add Key

**4.1.2 "Clonar tu repositorio"**
```
cd /home/
git clone git@github.com:rulz/myproject.git
```
**5."Instalar [GCC](http://iie.fing.edu.uy/~vagonbar/gcc-make/gcc.htm)"**
```
sudo apt-get install gcc
```

**6."Instalar tus requerimientos del proyecto"**

debes ingresar a tu proyecto
```
cd miproyecto
pip install -r requirements.txt
```

**7."Instalar [nginx](http://blog.desdelinux.net/nginx-una-interesante-alternativa-a-apache/)"**
```
sudo apt-get install nginx
```

**8."Instalar [gunicorn](http://blog.manuelviera.es/gunicorn/)"**
```
pip install gunicorn
```

**8.1"Configurar gunicorn"**
```
gunicorn_django --bind domini.com:8001
gunicorn_django --workers=3 --bind domini.com:8001
cd /opt/Envs/env1
sudo vim gunicorn_config.py
```

**8.1.1"Información que debe tener gunicorn_config.py"**
```
command = '/opt/Envs/env1/bin/gunicorn'
pythonpath = '/home/myproject'
bind = '127.0.0.1:8001'
workers = 3
user = 'nobody'
```

***8.1.2"Verificar gunicorn_config.py y tu proyecto"***
```
cd /home/myproject
/opt/Envs/env1/bin/gunicorn -c /opt/Envs/env1/gunicorn_config.py myproject.wsgi
ctrl+c
```

***9."hacer un collectstatic"***
```
cd /home/myproject
python manage.py collectstatic
```

***10."Configurar nginx"***
```
sudo vim /etc/nginx/sites-available/myproject
```

dentro debe ir este código
```
server {
        server_name dominio.com;

        access_log off;

        location /static/ {
            alias /home/myproject/staticfiles/;
        }

        location / {
                proxy_pass http://127.0.0.1:8001;
                proxy_set_header X-Forwarded-Host $server_name;
                proxy_set_header X-Real-IP $remote_addr;
                add_header P3P 'CP="ALL DSP COR PSAa PSDa OUR NOR ONL UNI COM NAV"';
        }
    }
************
otra opcion
upstream app_server {
    server 127.0.0.1:9000 fail_timeout=0;
}

server {
    listen 80 default_server;
    listen [::]:80 default_server ipv6only=on;

    root /usr/share/nginx/html;
    index index.html index.htm;

    client_max_body_size 4G;
    server_name _;

    keepalive_timeout 5;

    # Your Django project's media files - amend as required
    location /media  {
        alias /home/django/django_project/django_project/media;
    }

    # your Django project's static files - amend as required
    location /static {
        alias /home/django/django_project/django_project/static;
    }

    location / {
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_redirect off;
        proxy_pass http://app_server;
    }
}

```

```
cd /etc/nginx/sites-enabled
sudo ln -s ../sites-available/mysite
sudo service nginx restart
```

```
/opt/Envs/env1/bin/gunicorn -c /opt/Envs/env1/gunicorn_config.py myproject.wsgi
```

***11."Instalar PostgreSQL"***
```
sudo apt-get install postgresql postgresql-contrib
```


***11.1"MAC"***

si aparece un error como este
```
perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings:
        LANGUAGE = (unset),
        LC_ALL = (unset),
        LC_TIME = "es_ES.utf-8",
        LC_MONETARY = "es_ES.utf-8",
        LC_CTYPE = "es_ES.utf-8",
        LC_COLLATE = "es_ES.utf-8",
        LC_MESSAGES = "es_ES.utf-8",
        LC_NUMERIC = "es_ES.utf-8",
        LANG = "C"
    are supported and installed on your system.
perl: warning: Falling back to the standard locale ("C").
```
debes poner los siguientes códigos en el terminal [ver](http://javierin.com/2010/04/27/problema-lc_ctype-lc_messages-lc_all-ubuntu/)
```
LANG=en_GB.UTF-8
LC_ALL=en_GB.UTF-8
LANGUAGE=en_GB.UTF-8
```
luego debes desinstalar y volver a instalar
```
sudo apt-get remove postgresql postgresql-contrib
sudo apt-get install postgresql postgresql-contrib
sudo apt-get install libpq-dev python-dev
```

***12."configurar PostgreSQL"***
```
sudo su - postgres
createdb mydb
createuser -P
psql
GRANT ALL PRIVILEGES ON DATABASE mydb TO myuser;

cambair clave a usuario
alter user usuario with password '1234'; 
```

luego en el settings.py de tu proyecto
```
DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.postgresql_psycopg2', # Add 'postgresql_psycopg2', 'mysql', 'sqlite3' or 'oracle'.
            'NAME': 'mydb',                      # Or path to database file if using sqlite3.
            # The following settings are not used with sqlite3:
            'USER': 'myuser',
            'PASSWORD': 'password',
            'HOST': 'localhost',                      # Empty for localhost through domain sockets or           '127.0.0.1' for localhost through TCP.
            'PORT': '',                      # Set to empty string for default.
        }
    }
```
***12."Install psycopg2"***
```
pip install psycopg2
```

***13."syncdb django"***
```
python manage.py syncdb
```

###Ubuntu
para puerto
```
netstat -tulpn | grep 8001
kill -9 13928(PID)

fuser -k 80/tcp 
```

iniciar postgresql
```
/etc/init.d/postgresql start
```

```
chmod -R 777 /home/myproject/media/
```

Librerías de imágenes (para poder instalar PIL o PILLOW)
```
sudo apt-get install libjpeg-dev

sudo apt-get install libmemcached-dev zlib1g-dev

```
ir al proyecto Workon
```
echo `pwd` | cat > $VIRTUAL_ENV/.project 
```


2022

https://www.digitalocean.com/community/tutorials/how-to-set-up-django-with-postgres-nginx-and-gunicorn-on-ubuntu-20-04

```
server {
    listen 80;
    server_name 137.184.154.86;

    location = /favicon.ico { access_log off; log_not_found off; }
    location /static {
        #root /home/rulz/mineducEDE;
        alias /home/rulz/mineducEDE/staticfiles;
    }

    location /media {
        #root /home/rulz/mineducEDE;
        alias /home/rulz/mineducEDE/mediafiles;
    }

    location / {
        include proxy_params;
        proxy_pass http://unix:/run/gunicorn.sock;
    }
}
```

