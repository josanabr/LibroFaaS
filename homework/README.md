# Asignación FaaS, K8S, Inlets

## Objetivo

El propósito de esta asignación es validar que el estudiante ha adquirido unas competencias mínimas en cuanto a la gestión y uso de las tecnologías FaaS (*Function as a Service*), K8S (Kubernetes) e Inlets.

## Resultado esperado

El estudiante deberá proveer una plataforma computacional que soporte el servicio FaaS sobre un ambiente de Kubernetes. 
Este ambiente de Kubernetes se encuentra desplegado en la red privada del estudiante y será accesible a través de Inltes. 

El servicio que se debe desplegar dentro de la plataforma FaaS debe ser algunas de las siguientes opciones:

* Una función que haga uso de la librería Pandas.

* Una función que haga uso de la librería TensorFlow ([enlace](https://www.tensorflow.org/api_docs/python/tf)).

* Una función que haga el procesamiento de un video o de una imágen y obtenga los números de placa dado un archivo multimedia ([enlace](https://github.com/openalpr/openalpr)).

* Otra[^otra].

[^otra]: Enviar un correo al profesor (john.sanabria@correounivalle.edu.co) y proponer su idea de proyecto.

## Términos de la práctica

El estudiante 

## Guía

A continuación se describen los pasos para poner a punto todas las tecnologías que el estudiante requiere para llevar a cabo el presente proyecto.

### Instalacion Kubernetes

Para llevar a cabo el despliegue de un nodo de k8s usará el código de [este repositorio](https://github.com/josanabr/ansible-k8s).
Clone el repositorio, ingrese al directorio donde se clonó el repositorio y crea el cluster ejecutando el siguiente comando:

```
vagrant up k8s-master k8s-worker1
```

Esta ejecución construye un cluster con dos nodos.

### `kubectl`

`kubectl` es la herramienta que permite la gestión de un cluster de k8s. 
Para instalar la herramienta visitar [este enlace](https://kubernetes.io/docs/tasks/tools/install-kubectl/).
Una vez instalada la herramienta debe obtener el archivo de configuración del cluster creado en el punto de [instalación de Kubernetes](#instalacion-kubernetes). 
Diríjase al directorio donde instaló k8s con Vagrant y ejecute los siguientes comandos:

```
vagrant ssh k8s-master
```

Una vez dentro de la máquina ejecute lo siguiente:

```
cp .kube/config /vagrant
```

Salga del nodo maestro (`exit`) y en el directorio donde está ubicado usted encontrará un archivo llamado `config`.
Ese archivo es el insumo de `kubectl` para interactuar con su cluster virtual.
Ejecute el siguiente comando:

```
kubectl --kubeconfig config get nodes
```

Deberá obtener una salida similar a la siguiente:

```
NAME         STATUS   ROLES    AGE   VERSION
k8s-master   Ready    master   32m   v1.18.3
node-1       Ready    <none>   30m   v1.18.3
```

De ser así, ya tiene configurada su herramienta para acceder al cluster de  k8s.

### OpenFaaS

Para instalar OpenFaaS en un cluster de Kubernetes debe hacer lo siguiente:

* Ingresar al nodo maestro del cluster de k8s:
```
vagrant ssh k8s-master
```
* Instalar la herramienta para la gestión de paquetes en k8s llamada `arkade`:
```
curl -SLsf https://dl.get-arkade.dev/ | sudo sh
```
Deberá obtener algo como lo siguiente:
```
Get Kubernetes apps the easy way

Version: 0.3.3
Git Commit: 519c056683d63bb90cfae60b83c8e136138a7644
```
* Instalar `openfaas`:
```
sudo arkade install openfaas
```
Deberá obtener algo como lo siguiente:
```
# Get the faas-cli
curl -SLsf https://cli.openfaas.com | sudo sh

# Forward the gateway to your machine
kubectl rollout status -n openfaas deploy/gateway
kubectl port-forward -n openfaas svc/gateway 8080:8080 &

# If basic auth is enabled, you can now log into your gateway:
PASSWORD=$(kubectl get secret -n openfaas basic-auth -o jsonpath="{.data.basic-auth-password}" | base64 --decode; echo)
echo -n $PASSWORD | faas-cli login --username admin --password-stdin

faas-cli store deploy figlet
faas-cli list

# For Raspberry Pi
faas-cli store list \
 --platform armhf

faas-cli store deploy figlet \
 --platform armhf

# Find out more at:
# https://github.com/openfaas/faas

Thanks for using arkade!
```

En la siguiente sección se describe como hacer el despliegue de `faas-cli`, la herramienta para la gestión de funciones en un ambiente de cómputo.

### `faas-cli`

En el ítem anterior se evidenció como instalar `openfaas` en un cluster de k8s.
La salida del comando que instala `openfaas` indica los pasos para instalar `faas-cli` y luego como acceder a él. 

* Instalación de `faas-cli`
```
curl -SLsf https://cli.openfaas.com | sudo sh
```
* Puesta a punto de `faas-cli` para acceder a él.
Para lograr este paso se deben llevar algunos pasos adicionales:

  * Obtener el password asignado al servicio OpenFaaS en k8s:
```
PASSWORD=$(kubectl --kubeconfig config get secret -n openfaas basic-auth -o jsonpath="{.data.basic-auth-password}" | base64 --decode; echo)
```

  * Crear una redirección de puertos para el servicio de OpenFaaS instalado en (OpenFaas)[#openfaas].
```
kubectl --kubeconfig config port-forward svc/gateway -n openfaas 31112:8080 &
```
   
  * Conectarse via `faas-cli` al servicio de OpenFaaS en el cluster de k8s.
```
export OPENFAAS_URL="http://127.0.0.1:31112"
echo -n $PASSWORD | faas-cli login --username admin --password-stdin
```
