Configurar Django, Nginx y Gunicorn en Amazon
==========

Por mi parte instalé Ubuntu Server 14.04 LTS

---

**1. "Conectarse al servidor Amazon"**
```
ssh -i "llave.pem" ubuntu@ec2-XX-XX-X-XXX.compute-X.amazonaws.com
```

**2. "Actulizar y bajar paquetes linux"**
```
sudo apt-get update
sudo apt-get upgrade
```

solo para mac (por si no puedes instalar packetes)

```
export LC_ALL="en_US.UTF-8"
export LC_CTYPE="en_US.UTF-8"
sudo dpkg-reconfigure locales
```
**3. "Instalar packetes"**
```
sudo apt-get install python-pip
sudo apt-get install python-dev
sudo apt-get install build-essential
```
**4. "Entorno virtual"**
```
sudo pip install virtualenvwrapper
```

**5. "Configuración del entorno virtual"**
```
vim ~/.profile
```

se agrega los siguientes código en profile
```
export WORKON_HOME=$HOME/.virtualenvs
source /usr/local/bin/virtualenvwrapper.sh
```

una vez agregado lo anterior
```
source ~/.profile
```

**6. "Craer un entorno con workon"**
```
mkvirtualenv appname
workon appname
```

**7."Instalar [GIT](https://github.com/)"**
```
sudo apt-get install git
```
**7.1 "opcional"**
**7.1.1 "Generar la llave"**
```
ssh-keygen
cat ~/.ssh/id_rsa.pub
```
la llave que generas al hacer cat debes ponerlo en [github>account-settings>ssh-key](https://github.com/settings/ssh) luego el botón Add SSH key pegas el código y haces clic en el botón Add Key

**7.1.2 "Clonar tu repositorio"**
```
cd /home/
git clone git@github.com:rulz/myproject.git
```

**8."Instalar tus requerimientos del proyecto"**

debes ingresar a tu proyecto
```
cd miproyecto
pip install -r requirements.txt
```

**9."Instalar [nginx](http://blog.desdelinux.net/nginx-una-interesante-alternativa-a-apache/)"**

```
sudo apt-get install nginx

sudo service nginx start
sudo service nginx stop
sudo service nginx restart

sudo vim /etc/nginx/nginx.conf
```

agregarlo en la primera linea
```
user ubuntu ubuntu;
```

editamos el archivo
```
sudo vim /etc/nginx/sites-enabled/default
```

agregar dentro del server {}
```
access_log  /home/ubuntu/myproject/nginx-access.log;
error_log  /home/ubuntu/myproject/nginx-error.log info;
```

**10."Instalar [gunicorn](http://blog.manuelviera.es/gunicorn/)"**

```
pip install gunicorn
```

**11. "creamos el script .sh"**

```
cd ~/myproject

vim start_gunicorn.sh

```
agregar

```
APPNAME=myproject
APPDIR=/home/ubuntu/$APPNAME/

LOGFILE=$APPDIR'gunicorn.log'
ERRORFILE=$APPFIR'gunicorn-error.log'

NUM_WORKERS=3

ADDRESS=0:8000

cd $APPDIR

source ~/.profile
workon $APPNAME

exec gunicorn $APPNAME.wsgi:application \
-w $NUM_WORKERS --bind=$ADDRESS \
--log-level=debug \
--log-file=$LOGFILE 2>>$LOGFILE  1>>$ERRORFILE &
```

permisos
```
chmod +x start_gunicorn.sh
./start_gunicorn.sh
```

***12."hacer un collectstatic"***
```
cd /home/myproject
python manage.py collectstatic
```

***13."Configurar nginx"***
```
sudo vim /etc/nginx/sites-available/myproject
```

dentro debe ir este código
```
server {
        server_name 123.123.123.123;

        access_log off;

        location /static/ {
            alias /home/ubuntu/myproject/staticfiles/;
        }

        location / {
                proxy_pass http://123.123.123.123:8000;
                proxy_set_header X-Forwarded-Host $server_name;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header Host 123.123.123.123;
                proxy_set_header REMOTE_ADDR $remote_addr;
                add_header P3P 'CP="ALL DSP COR PSAa PSDa OUR NOR ONL UNI COM NAV"';
        }
}
```

```
cd /etc/nginx/sites-enabled
sudo ln -s ../sites-available/myproject
sudo service nginx restart
```

***14."Instalar PostgreSQL"***
```
sudo apt-get install postgresql postgresql-contrib
```

luego debes desinstalar y volver a instalar
```
sudo apt-get remove postgresql postgresql-contrib
sudo apt-get install postgresql postgresql-contrib
sudo apt-get install libpq-dev python-dev
```

***15."configurar PostgreSQL"***

```
sudo su postgres
psql
CREATE DATABASE mydb;
CREATE USER usermydb WITH PASSWORD 'passmydb123';
GRANT ALL PRIVILEGES ON DATABASE mydb TO usermydb;

export DATABASE_URL=postgres://usermydb:passmydb123@localhost:5432/mydb

cambair clave a usuario
alter user usermydb with password 'newpass1234'; 
```

fuentes
```
http://agiliq.com/blog/2014/08/deploying-a-django-app-on-amazon-ec2-instance/

https://platzi.com/blog/django-aws/

https://www.youtube.com/watch?v=Kg29bvYyLSM
```






