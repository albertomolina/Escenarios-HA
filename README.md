# Curso-SAD

Este repositorio contiene los escenarios para los ejercicios de clústeres de
alta disponibilidad del curso "Seguridad y Alta Disponibilidad" impartido en el
CEP de Lora del Río en Septiembre de 2013

Cada directorio contiene un escenario para montar algún tipo de escenario de
alta disponibilidad o balanceo de carga de forma sencilla y automática.

Para poder desplegar los diferentes clústeres, basta con acceder a cada
directorio y ejecutar las siguientes instrucciones:
```
$ vagrant up
$ ansible-playbook escenario.yaml
```

## Requisitos

Para poder utilizar los escenarios es necesario tener instalados vagrant,
virtualbox y ansible

### Instalación de vagrant

En versiones anteriores era posible instalar vagrant como una gema de ruby, pero
en las versiones actuales es necesario descargarlo directamente de
http://vagrantup.com

La versión utilizada en este caso ha sido la 1.3.1

#### Boxes de vagrant

Se ha utilizado sólo el box oficial de precise32 disponible en http://files.vagrantup.com/precise32.box

### Instalación de virtualbox

Vagrant puede utilizar diferentes proveedores (hipervisores para ejecutar las
máquinas virtuales), pero el más habitual es virtualbox. En este caso hemos
utilizado la versión 4.2.18 descargada directamente desde http://virtualbox.org/

### Instalación de ansible

La forma más sencilla de instalar ansible es como paquete python con pip:
```
# apt-get install python-pip
# pip install ansible
```
Los escenarios están configurados con la versión 1.2.3 de ansible