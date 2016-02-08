# HA con pacemaker y corosync. IP Failover + Apache

Partiendo del ejercicio anterior, vamos a agregar un sistema de
replicación de dispositivos de bloques conocido como DRBD, que nos
permitirá añadir posteriormente un recurso más al clúster de
pacemaker, en este caso el directorio de datos del sitio web, que se
mantendrá replicado y operativo en modo maestro/esclavo gracias a
DRBD.

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
$ ansible-playbook site.yaml
```

## Finalización de la configuración

Este escenario se ha dejado sin terminar de forma intencionada porque
pensamos que es más interesante ir haciendo paso a paso la
configuración de DRBD. Los pasos a seguir serían los que aparecen en
"Clusters from Sratch", a partir de la sección [7.3.3](http://clusterlabs.org/doc/en-US/Pacemaker/1.1-plugin/html-single/Clusters_from_Scratch/index.html#_initialize_and_load_drbd)

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
IPCluster, WebSite, WebData y WebFS están funcionando correctamente en uno de ellos. En esta 
configuración se ha forzado que todos los recursos se ejecuten siempre en un
solo nodo, que será el maestro de todos los recursos
* Utiliza el navegador y accede a la dirección www.example.com. Recarga la página
y comprueba que siempre responde el mismo nodo (nodo maestro)
* Entra en el nodo maestro por ssh y para el servicio apache. Comprueba que
transcurridos unos instantes el servicio vuelve a estar levantado en ese nodo
(pacemaker se encarga de volver a levantarlo). ¿Qué diferencias encuentras entre
esta configuración y la del ejercicio de balanceo DNS?
* Para el nodo maestro con vagrant y comprueba el estado del clúster con crm_mon
en el otro nodo. Verifica que es posible acceder con el navegador al sitio
www.example.com, pero que ahora el contenido lo sirve el otro nodo. ¿Piensas que
esta configuración es suficiente para ejecutar contenido web dinámico?
* Realiza alguna modificación en el directorio que se sirve por web,
arranca el nodo apagado y comprueba los cambios que se producen.

