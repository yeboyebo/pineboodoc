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


Adaptar a aplicación
--------------------

Para ello cambiaremos la configuración del fichero app/AQNEXT/local.py

Aquí configuramos si queremos carga estatica:

    - StaticLoader: Si la dejamos como True estamos indicando que queremos activada la carga estatica, seria recomendable desactivarla cuando no se este desarrollando
    - dirs: Indicaoms la ruta a los scripts que queremos cargar con carga estatica siempre con /pineboo/modules/ delante, por ejemplo si queremos cargar los scripts de libreria = [True, "/pineboo/modules/libreria/scripts",]



Adaptar a entorno
-----------------

Para ello cambiaremos la configuración del fichero .env (oculto). Si no existe será necesario crear uno nuevo

Lo modificaremos conforme a estas reglas:

    - PORT: Indicamos el puerto por el cual nuestra aplicacion se desplegara, normalmente 80
    - DBHOST: IP de la maquina donde esta alojada la base de datos.
    - DOCKER_IP: Se utiliza para dar una salida al docker creando una red virtual dentro de tu ordenador, esta ip seria la ip para acceder a esa red (172.DOCKER_IP.0.1)
    - DBNAME = Nombre de la base de datos
    - DBUSER = Usuario que se conectara con la base de datos y realizara todas las operaciones.
    - DBPASSWORD = Contraseña del usuario.
    - DBPOST = El puerto de conexion con la base de datos
    - PINEBOODIR = /ruta/pineboo
    - MODULESDIR = /ruta/modules donde queremos la carga estatica


Comandos
--------

    - Levantar el docker::

        docker-compose up &

    - Tirar el docker::

        docker-compose down

    - Ver los nombres de los servicios (tiene que estar levantado)::

        docker ps

    - Entrar en docker (tiene que estar levantado)::

        docker-compose exec django bash

    - Lanzar comando en docker (tiene que estar levantado)::

        docker-compose exec django comando

    - Ver que muestra por consola un servicio (tiene que estar levantado)::

        docker-compose logs django
