# Ejemplo del concepto de Pod

En este directorio se encuentran los archivos necesarios para correr un ejemplo donde se evidencie la operación de un objeto *Pod* en Kubernetes.
Este ejemplo fue tomando de este enlace [Recursos de Kubernetes: Pods](https://www.josedomingo.org/pledin/2018/06/recursos-de-kubernetes-pods/). 

Para llevar a cabo este ejemplo se requiere:

* Tener acceso a un cluster de Kubernetes y gestionarlo a través de la herramienta `kubectl`.

* Descargar el archivo [nginx.yaml](nginx.yaml) y guardarlo localmente.

En el directorio donde descargó el archivo se podrán ejecutar los siguientes comandos:

* `kubectl create -f nginx.yaml` este comando crea un *pod* donde se ejecutará un contenedor que tiene el servidor web `nginx`.

* `kubectl get pods` nos permite ver todos los *pods* que están corriendo en el sistema.

* `kubectl get pod -o wide nginx` permite ver en que equipo se está ejecutando el *pod*.

* `kubectl describe pod nginx` permite ver información más detallada del *pod*.

* `kubectl logs nginx` permite ver eventos generados por el servidor `nginx`.

* `kubectl exec -it nginx -- /bin/bash` permite acceder al contenedor a través de una terminal remota.

* `kubectl port-forward nginx 8080:80` permite redireccionar el puerto `8080` del computador donde se ejecuta este comando contra el puerto `80` del *pod* que corre el servidor web `nginx`. 

* `kubectl delete -f nginx.yaml` permite la detención y borrado del *pod* que ejecuta `nginx`. 
