# HA con pacemaker y corosync. IP Failover

El caso más sencillo de cluster de alta disponibilidad es utilizar dos nodos que
funcionen en modo maestro esclavo y que ofrezcan como recurso de alta
disponibilidad una dirección IP, que se denomina en algunos casos IP virtual.

Cada nodo del clúster posee su propia dirección IP y uno de ellos posee además
la dirección IP virtual. El software de alta disponibilidad está monitorizando
ambos nodos en todo momento y en el caso de que el nodo que ofrece el recurso
tenga algún problema, el recurso (la dirección IP en este caso) pasa al nodo que
esté en modo esclavo.

Vamos a utilizar la misma configuración de equipos que en el primer ejercicio,
salvo que en esta ocasión utilizaremos la dirección IP 10.1.1.100 como IP
virtual asociada a www.example.com

* nodo1: 10.1.1.101
* nodo2: 10.1.1.102
* dns: 10.1.1.103 <- Servidor DNS

## Levantar el escenario

Simplemente ejecutamos la instrucción:

```
$ vagrant up
```

Que levanta y configura la red en los tres nodos.

## Configurar el escenario

Se incluyen los "playbooks" necesarios para la configuración con ansible de los
dos nodos y el servidor dns. Realizamos la configuración completa del escenario
con:

```
$ ansible-playbook site.yaml
```

## Prueba de funcionamiento

* Edita el fichero /etc/resolv.conf de tu equipo y añade como servidor DNS primario el nodo "dns" que tiene la dirección IP 10.1.1.103
* Comprueba la conectividad con los nodos del cluster con ping Utiliza dig para resolver el nombre www.example.com:
```
$ dig @10.1.1.103 www.example.com
```
* Comprueba que la dirección www.example.com está asociada a la dirección IP
10.1.1.100, que en este escenario es la IP virtual que estará asociada en todo
momento al nodo que esté en modo maestro
* Accede a uno de los nodos del clúster y ejecuta la instrucción
crm_mon. Comprueba que los dos nodos están operativos y que el recurso
IPCluster está funcionando correctamente en uno de ellos
* Haz ping a www.example.com desde la máquina anfitriona y comprueba la tabla
arp. Podrás verificar que la dirección MAC asociada a la dirección IP
10.1.1.100 coincide con la del nodo maestro en estos momentos.
* Para el nodo maestro (supongamos que es node2):
```
$ vagrant halt node2
```
* Haz ping a www.example.com y comprueba que la tabla arp ha cambiado. Ahora la dirección MAC asociada a la dirección IP 10.1.1.100 es la del otro nodo
* Entra en el nodo maestro y comprueba el estado del clúster con crm_mon
* Levanta de nuevo el nodo que estaba parado. Los recursos no van a volver a él porque en la configuración se ha penalizado el movimiento de los recursos, estos tienden a quedarse en el nodo en el que se están ejecutando, no a volver al nodo que era maestro