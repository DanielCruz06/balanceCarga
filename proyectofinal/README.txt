#----------Proyecto Balanceo de carga Bases de datos MySQL y MySQL Router
#-------------Como primer paso se crean las maquinas en este caso puntual se crean 4 la caracteristicas de cada maquina 
# se encuentra dentro del archivo VagrantFile
#-------------Se aprovisionan las maquinas con el archivo de aprovisionamiento
#==========================================================================
#-------Maquina mysqlrouter--Desactivamos el selinux utilizando:------------------
vim /etc/selinux/config
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=disabled
# SELINUXTYPE= can take one of three values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
TEST8
==================Instalación de mysql==============================
sudo yum update -y
wget https://repo.mysql.com//mysql80-community-release-el9-1.noarch.rpm
sudo dnf install mysql80-community-release-el9-1.noarch.rpm
sudo yum install mysql-server -y
sudo yum install mysql-shell -y
#----------mysql-server y mysql-shell
#----------mysql-server: Instala todo lo necesario para tener un servidor de base de datos #MySQL funcionando en tu sistema.
#mysql-shell: Proporciona una herramienta de línea de comandos avanzada para interactuar y #gestionar bases de datos MySQL, pero no instala el servidor de base de datos en sí.
#----------------comandos de instalación de mysql------------centos9s----------
sudo yum update
wget https://repo.mysql.com//mysql80-community-release-el9-1.noarch.rpm
sudo dnf install mysql80-community-release-el9-1.noarch.rpm
sudo dnf install mysql-community-server

#================================================================================
#==================================mysql1========================================
/*
*sudo dnf update ---> Primero, asegúrate de que tu sistema esté completamente actualizado:

*sudo dnf install https://dev.mysql.com/get/mysql80-community-release-el8-1.noarch.rpm ---> A continuación, instala los repositorios de MySQL:

*sudo dnf repolist enabled | grep mysql ---> Confirma que el repositorio de MySQL se ha añadido correctamente:

*sudo dnf module disable mysql ----> Deshabilita el módulo AppStream de MySQL si es necesario:

*sudo dnf install mysql-community-server ---> instalacion mysql server

* Inicia y habilita el servicio de MySQL:--->
sudo systemctl start mysqld
sudo systemctl enable mysqld


* sudo mysql_secure_installation ---Asegúrate de ejecutar mysql_secure_installation para configurar la seguridad de MySQL:


*sudo dnf install mysql-shell  --->Para instalar MySQL Shell, simplemente usa:


*/



vim /etc/hosts
192.168.50.3 mysql1 mysql1
192.168.50.4 mysql2 mysql2
192.168.50.5 mysql3 mysql3
#Se debe de reemplazar estos host para que se pueda realiza el direccionamiento #correctamente entre las maquinas.

#vim /etc/my.cnf
report_host = mysql1
report_host = mysql2
report_host = mysql3

#vim /etc/my.cnf es para abrir y editar el archivo de configuración de MySQL.
#Añadir report_host = mysql1 en ese archivo configura el nombre del host que el servidor #MySQL reportará en entornos de replicación.

sudo reboot

#================================================================================
service firewalld stop
systemctl disable firewalld
sudo systemctl start mysqld

#cat /var/log/mysqld.log   BpiZxp3Wle*a  --- mysql2
 1fE2G+PxRWP= --- mysql1
#Contraseña vm mysql2 --->?Thga2htz?Pw

mysqlsh --uri root@localhost (AkoDjIq)h4_?) -- contraseña arrojada con el comando 

Agregando el comando ALTER USER  ‘root’@’localhost’ IDENTIFIED BY ‘Autonoma123*’;

#------------------------------------------------------------------------------

dba.configureInstance() ----> Configuración de toda la instancia
2
projectFinal            ----> Nuevo usuario
Autonoma123*            ----> Contraseña
y                       ----> Guardado de contraseña 
y                       ----> Guardado de contraseña 

#-------------Configuració Myysql-----------------------------------------------
dba.checkInstanceConfiguration('projectFinal@mysql1') ---> configuración delhost
Autonoma123*
Y
#==============Tiene que salir el siguiente mensaje=============================
Validating local MySQL instance listening at port 3306 for use in an InnoDB cluster...

This instance reports its own address as mysql2:3306
Clients and other cluster members will communicate with it through this address by default. If this is not correct, the report_host MySQL system variable should be changed.

Checking whether existing tables comply with Group Replication requirements...
No incompatible tables detected

Checking instance configuration...
Instance configuration is compatible with InnoDB cluster

The instance 'mysql1:3306' is valid to be used in an InnoDB cluster.

{
    "status": "ok"
}

#------Create cluster
\c projectFinal@mysql1:3306
Autonoma123*
y
y

#-----Comando en caso de que se encuentre lleno el los metadatos del cluster
\sql
SELECT * FROM mysql_innodb_cluster_metadata.v2_clusters;
\js
dba.dropMetadataSchema();


#-----------Se debe hacer desde el usuario mysql1
mysqlsh --uri projectFinal@mysql1:3306

#-----variable para la creación del cluster
var mycls=dba.createCluster('projectFinal')

#------Prueba de mycls
mycls.status()

#------Describir
mycls.describe()

#---añadiendo instancias
mycls.addInstance('mysql2:3306')


#================================================================================
#==================================mysql2========================================

#cat /var/log/mysqld.log-------es donde se encuentra la pwd default
#Contraseña vm mysql2 --->,!aGM-x3a0tS
#----------Posterior se debe cambiar la contraseña --------------------------
Agregando el comando ALTER USER  ‘root’@’localhost’ IDENTIFIED BY ‘Autonoma123*’;
#==========Configuración de instancias dentro de Mysql=======================
dba.configureInstance() ----> Configuración de toda la instancia para el caso
2                       ----> Opcion de usuario admin
projectFinal ---> Nombre instancia
Autonoma123* ---> Contraseña 
y            ---> Guardado de contraseña   
y            ---> Guardado de contraseña   
#------------------------------------------------------------------------------
dba.checkInstanceConfiguration('projectFinal@mysql2') ---> 
Autonoma123*
Y
#=============Tiene que salir el siguiente mensaje=============================
Validating local MySQL instance listening at port 3306 for use in an InnoDB cluster...

This instance reports its own address as mysql2:3306
Clients and other cluster members will communicate with it through this address by default. If this is not correct, the report_host MySQL system variable should be changed.

Checking whether existing tables comply with Group Replication requirements...
No incompatible tables detected

Checking instance configuration...
Instance configuration is compatible with InnoDB cluster

The instance 'mysql2:3306' is valid to be used in an InnoDB cluster.

{
    "status": "ok"
}

#================================================================================
#==================================mysql3========================================

#cat /var/log/mysqld.log-------es donde se encuentra la pwd default
#Contraseña vm mysql2 --->;jHt391uawsq
#-------------------------------------------------------------------------------
mysqlsh --uri root@localhost
\sql
#----------Posterior se debe cambiar la contraseña --------------------------
Agregando el comando ALTER USER  ‘root’@’localhost’ IDENTIFIED BY ‘Autonoma123*’;

#==========Configuración de instancias dentro de Mysql=======================
dba.configureInstance() ----> Configuración de toda la instancia para el caso
2                       ----> Opcion de usuario admin
projectFinal ---> Nombre instancia
Autonoma123* ---> Contraseña 
y            ---> Guardado de contraseña   
y            ---> Guardado de contraseña   
#------------------------------------------------------------------------------
dba.checkInstanceConfiguration('projectFinal@mysql3') ---> 
Autonoma123*
Y
#=============Tiene que salir el siguiente mensaje=============================
Validating local MySQL instance listening at port 3306 for use in an InnoDB cluster...

This instance reports its own address as mysql2:3306
Clients and other cluster members will communicate with it through this address by default. If this is not correct, the report_host MySQL system variable should be changed.

Checking whether existing tables comply with Group Replication requirements...
No incompatible tables detected

Checking instance configuration...
Instance configuration is compatible with InnoDB cluster

The instance 'mysql3:3306' is valid to be used in an InnoDB cluster.

{
    "status": "ok"
}


#================================================================================
#==============================mysqlrouter1======================================

sudo yum update
wget https://repo.mysql.com//mysql80-community-release-el9-1.noarch.rpm
sudo dnf install mysql80-community-release-el9-1.noarch.rpm
sudo dnf install mysql-community-server
sudo yum install mysql-router -y
sudo yum install mysql-server -y

