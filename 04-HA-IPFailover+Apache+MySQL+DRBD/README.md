# HA con pacemaker y corosync. IP Failover + Apache

Partiendo del ejercicio de IP Failover, vamos a agregar el recurso apache al
sistema de gestión del cluster. De esta forma el clúster controlará que el
servicio esté siempre operativo en el nodo maestro, además como tenemos asociada
la dirección www.example.com a la IP virtual 10.1.1.100, accederemos siempre al
servidor web del nodo maestro al poner en el navegador la dirección
www.example.com

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
* Levanta el nodo que estaba parado y accede a él por ssh. Cambia manualmente los
recursos a este nodo con la instrucción:

```
# crm resource move [Recurso] [nodo]
```

Esto es muy útil por ejemplo para realizar tareas de mantenimiento en uno de los
nodos
* Una vez realizadas las tareas que mantenimiento se devuelve el control de los
recursos a crm para que los siga gestionando:

```
# crm resource unmove [Recurso]
```