# Escenarios-HA

Este repositorio contiene los escenarios para los ejercicios de clústeres de
alta disponibilidad del curso "Seguridad y Alta Disponibilidad" impartido en el
CEP de Lora del Río en Septiembre de 2013, pero actualizados en 2016
con versiones más actuales de software.

Cada directorio contiene todos los ficheros necesarios para montar algún tipo de
escenario de alta disponibilidad o balanceo de carga de forma sencilla y
automática.

Para poder desplegar los diferentes clústeres, basta con acceder a cada
directorio y ejecutar las siguientes instrucciones:
```
$ vagrant up
$ ansible-playbook escenario.yaml
```
Nota: Debido a que git modifica los permisos de todos los ficheros a 644, una
vez clonado el repositorio es necesario modificar a mano los permisos de la
clave privada del usuario vagrant, en caso contrario no funcionarán los
playbooks de ansible:
```
$ chmod 400 vagrant_private_key
```

## Requisitos

Para poder utilizar los escenarios es necesario tener instalados vagrant,
virtualbox y ansible

### Instalación de vagrant

En versiones anteriores era posible instalar vagrant como una gema de ruby, pero
en las versiones actuales es necesario descargarlo directamente de
http://vagrantup.com

La versión utilizada en este caso ha sido la 1.6.5

#### Boxes de vagrant

Se ha utilizado sólo el box oficial de Ubuntu 14.04 LTS disponible en https://atlas.hashicorp.com/ubuntu/boxes/trusty64

```
$ vagrant box add ubuntu/trusty64
```

### Instalación de virtualbox

Vagrant puede utilizar diferentes proveedores (hipervisores para ejecutar las
máquinas virtuales), pero el más habitual es virtualbox. En este caso hemos
utilizado la versión 4.3.32 

### Instalación de ansible

Ansible se puede instalar de diferentes modos, en este caso hemos utilizado la versión 1.7.2 disponible como paquete en Debian 8 (Jessie)