# HA con pacemaker y corosync. IP Failover

El caso más sencillo de cluster de alta disponibilidad es utilizar dos nodos que
funcionen en modo maestro esclavo y que ofrezcan como recurso de alta
disponibilidad una dirección IP, que se denomina en algunos casos IP virtual.

Cada nodo del clúster posee su propia dirección IP y uno de ellos posee además
la dirección IP virtual. El software de alta disponibilidad está monitorizando
ambos nodos en todo momoento y en el caso de que el nodo que ofrece el recurso
tenga algún problema, el recurso (la dirección IP en este caso) pasa al nodo que
esté 
está asociada sólo a uno de ellos 
Utilizando entradas tipo A duplicadas en un servidor DNS es posible
realizar de forma muy sencilla un balanceo de carga entre varios equipos, esto
se conoce como DNS round robin [1]

En este caso vamos a realizar un balanceo de carga entre dos servidores web,
para lo que creamos un escenario con tres equipos:

* nodo1: 10.1.1.101 <- Servidor web
* nodo2: 10.1.1.102 <- Servidor web
* dns: 10.1.1.103 <- Servidor DNS

## Levantar el escenario

Simplemente ejecutamos la instrucción:

```
$ vagrant up
```

Que levanta y configura la red en los tres nodos.

## Configurar el escenario

Se incluyen los "playbooks" necesarios para la configuración con ansible de los
dos servidores web y el servidor dns.

La configuración se realiza utilizando la clave privada ssh del usuario vagrant,
que es necesario que no pueda leer ningún otro usuario, por lo que hay que
protegerla adecuadamente:
```
$ chmod 600 vagrant_private_key
```
Una vez protegida la clave, podemos configurar todos los nodos:
```
$ ansible-playbook escenario.yaml
```

## Prueba de funcionamiento

Si no ha habido errores durante la ejecución de los playbooks, se puede
comprobar que se realiza el balanceo www.example.com entre nodo1 y nodo2,
repitiendo la consulta DNS con dig:
```
$ dig @10.1.1.103 www.example.com
```

También puede verse de forma mucho más clara a través del navegador, para lo
cual es necesario añadir la dirección IP como servidor DNS primario la dirección
10.1.1.103 y podremos comprobar como se balancean las peticiones entre los dos
servidores web nodo1 y nodo2 (es necesario forzar la recarga, CTRL+F5 por
ejemplo).

[1] http://en.wikipedia.org/wiki/Round-robin_DNS