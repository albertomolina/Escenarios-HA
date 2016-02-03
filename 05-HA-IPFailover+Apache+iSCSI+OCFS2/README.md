# HA con pacemaker y corosync. IP Failover + Apache + OCFS2

En este caso vamos a añadir un volumen adicional al nodo dns, que se servirá
a los otros dos nodos del cluster vía iSCSI, se formateará con
OCFS2. La idea es proporcionar un mecanismo externo (iSCSI) que
gestione el almacenamiento compartido y un sistema de ficheros tipo
cluster (OCFS2) para que ambos nodos puedan leer y escribir sobre el
mismo volumen simultáneamente y poder proporcionar el servicio en modo
activo-activo si se desea.

* nodo1: 10.1.1.101 <- Servidor web
* nodo2: 10.1.1.102 <- Servidor web
* dns: 10.1.1.103 <- Servidor DNS
* www.example.com: 10.1.1.100

## Levantar el escenario

Simplemente ejecutamos la instrucción:

```
$ vagrant up
```

Que levanta y configura la red en los tres nodos.

## Configurar el escenario

Se incluyen los "playbooks" necesarios para la configuración con ansible de los
dos servidores web y el servidor dns. Realizamos la configuración completa del
escenario con:

```
$ ansible-playbook escenario.yaml
```

## Prueba de funcionamiento

* Edita el fichero /etc/resolv.conf de tu equipo y añade como servidor DNS
primario el nodo "dns" que tiene la dirección IP 10.1.1.103
* Comprueba la conectividad con los nodos del cluster con ping
* Utiliza dig para resolver el nombre www.example.com

$ dig @10.1.1.103 www.example.com

* Comprueba que la dirección www.example.com está asociada a la dirección IP
10.1.1.100, que en este escenario es la IP virtual que estará asociada en todo
momento al nodo que esté en modo maestro
* Accede a uno de los nodos del clúster y ejecuta la instrucción
crm_mon. Comprueba que los dos nodos están operativos y que los recursos
IPCluster y WebSite están funcionando correctamente en uno de ellos. En esta
configuración se ha forzado que todos los recursos se ejecuten siempre en un
solo nodo, que será el maestro de todos los recursos
* Accede al nodo "dns" y comprueba que se ha exportado correctamente el disco adicional por iSCSI con la instrucción:

    $ sudo tgtadm --lld iscsi --op show --mode target

* Accede a uno de los nodos del cluster y comprueba que se ha inicializado la conexión con el target iSCSI:

    $ sudo iscsiadm -m session -P 3

* 

Entra en e


Por hacer