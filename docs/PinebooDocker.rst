PinebooDocker HowTo
============

.. toctree::
   :maxdepth: 2


Instalar docker y compose
-------------------------

    #. Instalar docker (puede diferir en otros sistemas no Ubuntu, ver https://docs.docker.com/install/)::

        sudo apt-get update

        sudo apt-get install apt-transport-https ca-certificates curl software-properties-common

        curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

        sudo LC_ALL=C.UTF-8 add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

        sudo apt-get update

        sudo apt-get install docker-ce

        sudo chmod 777 /var/run/docker.sock

    #. Instalar compose (puede diferir en otros sistemas no Ubuntu)::

        sudo curl -L "https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

        sudo chmod +x /usr/local/bin/docker-compose


Configurar entorno
------------------

Si vamos a utilizar una serie de **bases de datos externas** al docker, necesitaremos configurar los servidores de bases de datos (en principio de postgres) de cada una de las máquinas donde se encuentren. Para ello seguimos los siguientes pasos:

    - Escuchar desde cualquier dirección. Esto lo haremos desde el fichero /etc/postgres/.../postgres.conf (hay que tener cuidado de no sobreexponer el servidor)::

        listen_addresses = '*'

    - Permitir peticiones desde la dirección IP del docker. Esto lo haremos desde el fichero /etc/postgres/.../pg_hba.conf:

        - Si la base de datos está en el mismo servidor::

            host    all    all    172.55.0.0/24    md5

        - Si no::

            host    db_name    db_user    db_server_ip/24    md5

    - Si hemos hecho cambios, reiniciar el servicio de postgres::

        sudo service postgresql restart



Descargar Docker de producción
------------------------------

Bajamos los ficheros del docker de producción, para ello nos vamos a la carpeta donde vamos a alojar la aplicación y lanzamos::

    git clone https://github.com/yeboyebo/pinebooapi.git

Posteriormente tenemos que dar permisos a la carpeta para poder ejecutar la aplicacion:

    chmod -R 775 pinebooapi


Adaptar a entorno
-----------------

Para ello cambiaremos la configuración del fichero .env (oculto). Si no existe será necesario crear uno nuevo

Lo modificaremos conforme a estas reglas:

    - PORT: Indicamos el puerto por el cual nuestra aplicacion se desplegara, normalmente 8005
    - DBHOST: IP de la maquina donde esta alojada la base de datos.
    - DOCKER_IP: Se utiliza para dar una salida al docker creando una red virtual dentro de tu ordenador, esta ip seria la ip para acceder a esa red (172.DOCKER_IP.0.1), ejempl: 55
    - DBNAME: Nombre de la base de datos
    - DBUSER: Usuario que se conectara con la base de datos y realizara todas las operaciones.
    - DBPASSWORD: Contraseña del usuario.
    - DBPOST: El puerto de conexion con la base de datos
    - PINEBOODIR: /ruta/pineboo, Ej: /home/usuario/Pineboo/
    - MODULESDIR: /ruta/modules donde queremos la carga estatica Ej: /home/usuario/funcional/
    - TEMPDIR: Ruta para poder cachear los qs traducidos, normalmente: /pineboo/Pineboo/tempdata
    - STATIC_LOADER_DIRS: Scripts sobre los queremos activar la carga estatica, siempre comenzaran con /pineboo/modules/ seguido de la funcionalidad/modulo que queramos cargar. Ej: /pineboo/modules/fun_elganso/nuevo/sistema/libreria/scripts/

Adaptar a aplicación
--------------------

Para ello cambiaremos la configuración del fichero app/AQNEXT/local.py

Aquí configuramos si queremos carga estatica:

    - StaticLoader: Si la dejamos como True estamos indicando que queremos activada la carga estatica, seria recomendable desactivarla cuando no se este desarrollando


Comandos
--------

    - Levantar el docker::

        docker-compose up &

    - Tirar el docker::

        docker-compose down

    - Subir version::
        docker-compose build

    - Ver los nombres de los servicios (tiene que estar levantado)::

        docker ps

    - Entrar en docker (tiene que estar levantado)::

        docker-compose exec django bash

    - Lanzar comando en docker (tiene que estar levantado)::

        docker-compose exec django comando

    - Ver que muestra por consola un servicio (tiene que estar levantado)::

        docker-compose logs django
