USAMOS EL SIGUIENTE VAGRANTFILE:

# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.define :mysqlrouter do |mysqlrouter|
    mysqlrouter.vm.box = "generic/centos8"
    mysqlrouter.vm.network :private_network, ip: "192.168.50.2"
    mysqlrouter.vm.provision "shell", path: "aprovision.sh.txt"
    mysqlrouter.vm.hostname = "mysqlrouter"
  end
  config.vm.define :mysql1 do |mysql1|
    mysql1.vm.box = "generic/centos8"
    mysql1.vm.network :private_network, ip: "192.168.50.3"
    mysql1.vm.provision "shell", path: "aprovision.sh.txt"
    mysql1.vm.hostname = "mysql1"
  end
   config.vm.define :mysql2 do |mysql2|
     mysql2.vm.box = "generic/centos8"
     mysql2.vm.network :private_network, ip: "192.168.50.4"
     mysql2.vm.provision "shell", path: "aprovision.sh.txt"
     mysql2.vm.hostname = "mysql2"
   end
   config.vm.define :mysql3 do |mysql3|
     mysql3.vm.box = "generic/centos8"
     mysql3.vm.network :private_network, ip: "192.168.50.5"
     mysql3.vm.provision "shell", path: "aprovision.sh.txt"
     mysql3.vm.hostname = "mysql3"
   end
   config.vm.define :cliente do |cliente|
     cliente.vm.box = "generic/centos8"
     cliente.vm.network :private_network, ip: "192.168.50.6"
     cliente.vm.provision "shell", path: "aprovision.sh.txt"
     cliente.vm.hostname = "cliente"
   end
end

CON ESTE VAGRANTFILE CENTOS 8 GENERIC YA VIENE INSTALADO EL VIM.

======================================================================================================

LO PRIMERO QUE DEBEMOS HACER AL INICIAR LAS MAQUINAS ES DESACTIVAR EL SELINUX EN CADA MAQUINA:

sudo vim /etc/selinux/config
SELINUX=disabled

SIEMPRE QUE INICIEMOS LAS MAQUINAS RECORDAR DESACTIVAR EL SERVICIO FIREWALLD EN TODAS LAS MAQUINAS!!!

==============================================================================================

INSTALACIÓN DE MYSQL (MAQUINAS MYSQLROUTER, MYSQL1, MYSQL2, MYSQL3) Y MYSQL SHELL (MYSQL1, MYSQL2, MYSQL3)

sudo yum update -y
sudo wget https://dev.mysql.com/get/mysql80-community-release-el7-7.noarch.rpm
sudo rpm -Uvh mysql80-community-release-el7-7.noarch.rpm
sudo yum install mysql-server -y
sudo yum install mysql-shell -y
sudo systemctl start mysqld
sudo systemctl status mysqld

mysql --version
mysqlsh --version


cat /var/log/mysqld.log   ---


*Deshabilitar el módulo AppStream de MySQL*:
   sh
   sudo dnf module disable mysql
======================================================================================================
EMPEZAMOS POR LA CONFIGURACIÓN DE CADA MAQUINA:

==================EN LA MAQUINA MYSQL1===============================

En estas rutas vamos a asignar las IP y las maquinas las cuales van a estar comunicadas en el cluster:

vim /etc/hosts
192.168.50.3 mysql1 mysql1
192.168.50.4 mysql2 mysql2
192.168.50.5 mysql3 mysql3

vim /etc/my.cnf
report_host = mysql1
report_host = mysql2
report_host = mysql3

reiniciamos la maquina para efectuar cambios:
sudo reboot

como reiniciamos la maquina ya sabemos que debemos desactivar el firewall de nuevo:
service firewalld stop

iniciamos mysql:
sudo systemctl start mysqld

entramos a mysql shell con el usuario root sin contraseña:
mysqlsh --uri root@localhost (SIN CONTRASEÑA)

Configuramos el cluster con los siguientes pasos:
dba.configureInstance()
2 (la opción que debemos elegir)
innodbcluster (como se llamara la instancia)
Autonoma123* (la contraseña de la instancia)
y
y

Observamos la configuración de la instancia:
dba.checkInstanceConfiguration('innodbcluster@mysql1')
Autonoma123*
Y

Entramos a la shell depende de la maquina:
\c innodbcluster@mysql1:3306
Autonoma123*
Y

creamos nuestro cluster con un nombre:
var mycls= dba.createCluster('asesoftware')

creamos una variable para obtener el cluster y con la cual obtendremos los datos:
var cls=dba.getCluster('asesoftware')
cls.status()
cls.describe()

{
comandos que nos pueden servir:
dba.getCluster()
dba.dropMetadataSchema()
dba.rebootClusterFromCompleteOutage()
}

{
Para cuando volvamos a iniciar las maquinas y queramos obtener el cluster que creamos antes:
dba.rebootClusterFromCompleteOutage()
dba.getCluster()
var cls=dba.getCluster('asesoftware') 
}

ahora agregamos las otras dos instancias, es decir, las otras dos maquinas:
cls.addInstance('mysql2:3306')
Autonoma123*
Y
(default clone): LE DAMOS ENTER

cls.addInstance('mysql3:3306')
Autonoma123*
Y
(default clone): LE DAMOS ENTER

Ahora vemos el estado del cluster:
cls.status() (deberiamos ver las tres maquinas, la primera con privilegios R/W y las demas R/O)

Asigmanos la instancia que tendra el mysql router:
cls.setupRouterAccount('mysqlrouter')
Autonoma123*

======================================================================================================

==================EN LA MAQUINA MYSQL2===============================


En estas rutas vamos a asignar las IP y las maquinas las cuales van a estar comunicadas en el cluster:

vim /etc/hosts
127.0.0.1 mysql1 mysql1 reemplazar por 192.168.50.3 mysql1 mysql1
127.0.0.1 mysql2 mysql2 reemplazar por 192.168.50.4 mysql2 mysql2
127.0.0.1 mysql3 mysql3 reemplazar por 192.168.50.5 mysql3 mysql3

vim /etc/my.cnf
agregar report_host = mysql1
agregar report_host = mysql2
agregar report_host = mysql3

reiniciamos la maquina para efectuar cambios:
sudo reboot

como reiniciamos la maquina ya sabemos que debemos desactivar el firewall de nuevo:
service firewalld stop

iniciamos mysql:
sudo systemctl start mysqld

entramos a mysql shell con el usuario root sin contraseña:
mysqlsh --uri root@localhost (SIN CONTRASEÑA)

Configuramos el cluster con los siguientes pasos:
dba.configureInstance()
2 (la opción que debemos elegir)
innodbcluster (como se llamara la instancia)
Autonoma123*(la contraseña de la instancia)
y
y

Observamos la configuración de la instancia:
dba.checkInstanceConfiguration('innodbcluster@mysql2')
Autonoma123*
Y

=====================================================================================================

==================EN LA MAQUINA MYSQL3===============================

En estas rutas vamos a asignar las IP y las maquinas las cuales van a estar comunicadas en el cluster:

vim /etc/hosts
192.168.50.3 mysql1 mysql1
192.168.50.4 mysql2 mysql2
192.168.50.5 mysql3 mysql3

vim /etc/my.cnf
agregar report_host = mysql1
agregar report_host = mysql2
agregar report_host = mysql3

reiniciamos la maquina para efectuar cambios:
sudo reboot

como reiniciamos la maquina ya sabemos que debemos desactivar el firewall de nuevo:
service firewalld stop

iniciamos mysql:
sudo systemctl start mysqld

entramos a mysql shell con el usuario root sin contraseña:
mysqlsh --uri root@localhost (SIN CONTRASEÑA)

Configuramos el cluster con los siguientes pasos:
dba.configureInstance()
2 (la opción que debemos elegir)
innodbcluster (como se llamara la instancia)
Autonoma123* (la contraseña de la instancia)
y
y

Observamos la configuración de la instancia:
dba.checkInstanceConfiguration('innodbcluster@mysql3')
Autonoma123*
Y

=====================================================================================================

==================EN LA MAQUINA MYSQLROUTER=========================

para entrar al bash de mysql shell:
mysqlsh (CTRL + D para salir)

instalamos mysql router:
sudo yum install mysql-router -y

mysqlrouter --version

En estas rutas vamos a asignar las IP y las maquinas las cuales van a estar comunicadas con mysql router:

vim /etc/hosts
192.168.50.2 mysqlrouter mysqlrouter
192.168.50.3 mysql1 mysql1
192.168.50.4 mysql2 mysql2
1192.168.50.5 mysql3 mysql3

vim /etc/my.cnf
report_host = mysqlrouter
report_host = mysql1
report_host = mysql2
report_host = mysql3

iniciamos mysql:
sudo systemctl start mysqld

iniciamos mysql router:
mysqlrouter --bootstrap innodbcluster@mysql1 --user mysqlrouter (abrira puertos de lectura y escritura)
Autonoma123*

systemctl start mysqlrouter 

======================================================================================================

==================EN LA MAQUINA MYSQL1===============================

Maquina Master! con privilegios R/W (Read and Write)

Ahora vamos a crear una base de datos y una tabla para comprobar el funcionamiento del cluster en mysql:

mysqlsh --uri root@localhost (enter. sin contraseña)

\sql

select @@hostname; (debera salir mysql1)

CREATE DATABASE UAO;
USE UAO;
CREATE TABLE if not exists UAO.estudiantes(id int primary key auto_increment, nombre varchar(100), carrera varchar(100), semestre int);
INSERT UAO.estudiantes(nombre, carrera, semestre) values ('Jose Daniel','Ing.Informatica', 8);
INSERT UAO.estudiantes(nombre, carrera, semestre) values ('Angie','Ing.Informatica', 6);
INSERT UAO.estudiantes(nombre, carrera, semestre) values ('Victor Manuel','Ing.Informatica', 10);
INSERT UAO.estudiantes(nombre, carrera, semestre) values ('Juan camilo','Ing.Informatica', 7);
SELECT * FROM UAO.estudiantes;

=====================================================================================================

==================EN LA MAQUINA MYSQL2===============================

Maquina Esclava! con privilegios R/O (Read Only)

Comprobamos en lectura la creación de lo que hizo la maquina master con privilegios de escritura y lectura:

mysqlsh --uri root@localhost

\sql

select @@hostname; (debera salir mysql2)

SELECT * FROM UAO.estudiantes;

INSERT UAO.estudiantes(nombre, carrera, semestre) values ('Prueba','Ing.Informatica', 5); (NO DEBERIA PORQUE LA MAQUINA TIENE PRIVILEGIOS R/O)

=======================================================================================================================================================

==================EN LA MAQUINA MYSQL3===============================

Maquina Esclava! con privilegios R/O (Read Only)

Comprobamos en lectura la creación de lo que hizo la maquina master con privilegios de escritura y lectura:

mysqlsh --uri root@localhost

\sql

select @@hostname; (debera salir mysql3)

SELECT * FROM UAO.estudiantes;

INSERT UAO.estudiantes(nombre, carrera, semestre) values ('Prueba','Ing.Informatica', 5); (NO DEBERIA PORQUE LA MAQUINA TIENE PRIVILEGIOS R/O)

========================================================================

#=====Cuando se inician de nuevo las maquinas===========================

INICIAR LAS MAQUINAS

En las maquinas mysql1 2 y 3

service firewalld stop
sudo systemctl start mysqld
mysqlsh --uri root@localhost (SIN CONTRASEÑA)
dba.checkInstanceConfiguration('innodbcluster@mysql1') (cambia de numero dependiendo de la maquina)
Autonoma123*
Y

\c innodbcluster@mysql1:3306 (cambia de numero dependiendo de la maquina)
Autonoma123*
Y
TENER TODAS LAS MAQUINAS PRENDIDAS CON LOS SERVICIOS PRENDIDOS!!

para la maquina master:
dba.rebootClusterFromCompleteOutage()
dba.getCluster()
var cls=dba.getCluster('asesoftware')
cls.status()

\c innodbcluster@mysql1:3306 (cambia de numero dependiendo de la maquina)
\sql
SELECT * FROM UAO.estudiantes;

MYSQL ROUTER ========================================

service firewalld stop
sudo systemctl start mysqld
sudo systemctl start mysqlrouter

mysqlrouter --bootstrap innodbcluster@mysql1 --user mysqlrouter
Autonoma123*


#=======================================================================
#========CONFIGURACIÓN SYSBENCH=================

sudo yum install sysbench -y

sysbench --version

sysbench --test=cpu --cpu-max-prime=20000 --num-threads=1 run

MODOS DE USAR SYSBENCH:

oltp_read_only
port = 3306
port router = 6446
--tables=10 
--table-size=100
--events=0 
--rate=5000
--time=30 
--threads=10


#======CREACIÓN AMBIENTE PRUEBAS SYSBENCH
#======Desde Localhost
CREATE DATABASE sbtest;
CREATE USER 'sysbench'@'localhost' IDENTIFIED BY 'Autonoma123*';
GRANT ALL PRIVILEGES ON sbtest.* TO 'sysbench'@'localhost';
FLUSH PRIVILEGES;
#=====Preparar ambiente de pruebas
sysbench /usr/share/sysbench/oltp_read_write.lua --mysql-db=UAO --mysql-user=root --mysql-password=Autonoma123* --db-driver=mysql --tables=50 --table-size=100 prepare

#========Pruebas 
sysbench /usr/share/sysbench/oltp_read_write.lua --mysql-db=UAO --mysql-user=root --mysql-password=Autonoma123* --db-driver=mysql --tables=50 --table-size=100 --threads=10 --time=60 --report-interval=10 --rand-type=uniform run

#Limpiar despues de pruebas 
#Comando para limpiar
sysbench oltp_read_write --tables=50 --table-size=100 --db-driver=mysql --mysql-host=localhost --mysql-db=sbtest --mysql-user=sysbench --mysql-password=Autonoma123* --mysql-port=6446 --time=60 --threads=10 cleanup

============================= MAS PRUEBAS ========================

Pruebas de lectura intensiva:
sysbench oltp_read_only --tables=50 --table-size=5000 --db-driver=mysql --mysql-host=localhost --mysql-db=UAO --mysql-user=root --mysql-port=6446 --time=60 --threads=32 prepare

sysbench oltp_read_only --tables=50 --table-size=5000 --db-driver=mysql --mysql-host=localhost --mysql-db=UAO --mysql-user=root --mysql-port=6446 --time=60 --threads=32 run

Pruebas de escritura intensiva:
sysbench oltp_write_only --tables=100 --table-size=2000 --db-driver=mysql --mysql-host=localhost --mysql-db=UAO --mysql-user=root --mysql-port=6446 --time=120 --threads=64 prepare

sysbench oltp_write_only --tables=100 --table-size=2000 --db-driver=mysql --mysql-host=localhost --mysql-db=UAO --mysql-user=root --mysql-port=6446 --time=120 --threads=40 run

Pruebas de carga mixta (lectura y escritura):
sysbench oltp_read_write --tables=200 --table-size=10000 --db-driver=mysql --mysql-host=localhost --mysql-db=UAO --mysql-user=root --mysql-port=6446 --time=180 --threads=128 prepare

sysbench oltp_read_write --tables=200 --table-size=10000 --db-driver=mysql --mysql-host=localhost --mysql-db=UAO --mysql-user=root --mysql-port=6446 --time=180 --threads=9 run

Pruebas de concurrencia:
sysbench oltp_read_write --tables=50 --table-size=5000 --db-driver=mysql --mysql-host=localhost --mysql-db=UAO --mysql-user=root --mysql-port=6446 --time=120 --threads=64 --report-interval=5 --histogram prepare

sysbench oltp_read_write --tables=50 --table-size=5000 --db-driver=mysql --mysql-host=localhost --mysql-db=UAO --mysql-user=root --mysql-port=6446 --time=120 --threads=36 --report-interval=5 --histogram run