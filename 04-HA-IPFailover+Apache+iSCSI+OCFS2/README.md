# HA con pacemaker y corosync. IP Failover + Apache + OCFS2

En este caso vamos a añadir un volumen adicional al nodo dns, que se servirá
a los otros dos nodos del cluster vía iSCSI, se formateará con OCFS2.

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

Por hacer