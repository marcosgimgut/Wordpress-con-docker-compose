# Wordpress-con-docker-compose

* Instalar docker-compose: Lo hacemos desde el propio archivo Vagrantfile añadiendo:

```
config.vagrant.plugins = "vagrant-docker-compose"
config.vm.provision :docker
config.vm.provision :docker_compose,  yml: "/vagrant/docker-compose.yml", rebuild: true, run: "always"
```

* Hacer docker-compose ejecutable.
  
```
sudo chmod +x /usr/local/bin/docker-compose
```

* Comprobar que ya esta en el sistema.

```
docker-compose --version
```

* Crear el archivo docker-compose.yml dentro de la máquina virtual. Con este servicio tendremos las imágenes de wordpress y mariadb para ejecutar Wordpress.

```
sudo nano /vagrant/docker-compose.yml
```

* Contenido del archivo:
  
```
    version: '3.1'
services:
  wordpress:
    container_name: servidor_wp
    image: wordpress
    restart: always
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: user_wp
      WORDPRESS_DB_PASSWORD: asdasd
      WORDPRESS_DB_NAME: bd_wp
    ports:
      - 80:80
    volumes:
      - wordpress_data:/var/www/html/wp-content
  db:
    container_name: servidor_mysql
    image: mariadb
    restart: always
    environment:
      MYSQL_DATABASE: bd_wp
      MYSQL_USER: user_wp
      MYSQL_PASSWORD: asdasd
      MYSQL_ROOT_PASSWORD: asdasd
    volumes:
      - mariadb_data:/var/lib/mysql
volumes:
    wordpress_data:
    mariadb_data:
```

* Cambiamos los puertos necesarios para que no haya conflicto con los puertos de la maquina virtual agregando al Vagrantfile las siguientes líneas:

```
config.vm.network "forwarded_port", guest: 80, host: 8080 //Wordpress lo tenemos usando el puerto 80 en la máquina virtual
config.vm.network "forwarded_port", host: 3306, guest: 3306 //La base de datos la tenemos en los dos sitios en el puerto 3306
```

* Crear los escenarios

```
docker-compose up -d
```
* Listar los contenedores para comprobar que están levantados los servicios.

```
docker-compose ps
```


![Imagen de instalación de wordpress](img/Captura1.PNG)
![Imagen del dashboard de Wordpress](img/Captura2.PNG)
![Imagen de wordpress funcionando](img/Captura3.PNG)

* Si queremos parar los contenedores:

```
docker-compose stop
```

* Si queremos borrar los contenedores:

```
docker-compose rm
```

* Si queremos eliminar el escenario (Contenedores, red y voúmenes):

```
docker-compose down -v
```