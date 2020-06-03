# Asignación FaaS, K8S, Inlets

## Objetivo

El propósito de esta asignación es validar que el estudiante ha adquirido unas competencias mínimas en cuanto a la gestión y uso de las tecnologías FaaS (*Function as a Service*), K8S (Kubernetes) e Inlets.

## Resultado esperado

El estudiante deberá proveer una plataforma computacional que soporte el servicio FaaS sobre un ambiente de Kubernetes. 
Este ambiente de Kubernetes se encuentra desplegado en la red privada del estudiante y será accesible a través de Inlets. 
La figura abajo muestra lo que se desea se despliegue por parte del estudiante.

![](../book/figures/FaaSK8SInletsHomework.png)

En la figura se muestra que el estudiante debe desplegar un cluster de Kubernetes. 
Este cluster puede ser virtual o físico. 
En la sección [Instalación Kubernetes](#instalacion-kubernetes) se dan algunos ambientes que se sugieren puedan ser instalados por el estudiante para el desarrollo de esta tarea.
**El estudiante deberá indicar y documentar el ambiente que seleccionó y como llevó a cabo el despliegue del ambiente de Kubernetes**.

Este cluster de Kubernetes debe tener instalado OpenFaaS, [aquí](#openfaas) se indica como hacer la instalación de OpenFaaS en Kubernetes.

El ambiente de Kubernetes con OpenFaaS instalado deberá ser expuesto hacia Internet usando la tecnología de Inlets. 
En la sección [Enlaces de Interés](#enlaces-de-interes) se brinda un enlace al repositorio que se compartió en clase respecto al tema de Inlets.

En este ambiente de Kubernetes + OpenFaaS deberá desplegarse una función que debe ser capaz de usar algunas de las siguientes funciones y/o librería:

* Una función que haga uso de la librería Pandas. 

* Una función que haga uso de la librería TensorFlow ([enlace](https://www.tensorflow.org/api_docs/python/tf)).

* Una función que haga el procesamiento de un video o de una imágen y obtenga los números de placa dado un archivo multimedia ([enlace](https://github.com/openalpr/openalpr)).

* Otra. Enviar un correo al profesor (john.sanabria@correounivalle.edu.co) y proponer su idea de proyecto.

**En cualquiera de las opciones que seleccione deberá proveer el Dockerfile con el que se creó la imagen de contenedor donde reside la función y un caso de uso de dicha función.**

## Términos de la práctica

El estudiante 

## Guía

A continuación se describen los pasos para poner a punto todas las tecnologías que el estudiante requiere para llevar a cabo el presente proyecto.

### Instalacion Kubernetes

Para llevar a cabo el despliegue de Kubernetes se dan las siguientes opciones:

* [k3s](https://k3s.io/) Este proyecto permite la ejecución de Kubernetes en ambientes basados en sistemas ARM, IoT, entre otros. Existen en Internet sitios que hablan de como instalar k3s sobre máquinas virtuales basadas en VirtualBox.

* [Kubernetes en VirtualBox](https://github.com/josanabr/ansible-k8s) En este repositorio se pueden crear hasta 3 máquinas virtuales en las cuales se instala Kubernetes original. La última vez que se probó fue el día 2020-06-02 con todo éxito.

* [Minikube](https://kubernetes.io/docs/setup/learning-environment/minikube/) Este proyecto permite tener todo un ambiente de Kubernetes operacional en una sola máquina.


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

De ser así, ya tiene configurados su cluster y su herramienta de gestión para acceder al cluster de k8s.

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

Asegúrese de ejecutar este comando dentro de la máquina virtual:

```
kubectl port-forward -n openfaas svc/gateway 8080:8080
```

Salga de la máquina virtual (`exit`) y ejecute el siguiente comando:

VBoxManage controlvm k8s_k8s-master_1591044207003_35102 natpf1 "k8s,tcp,,8080,,8080"


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

---

Pasos que seguí una vez instalado openfaas en 'k8s-master'

1- [k8s-master] kubectl port-forward svc/gateway -n openfaas --address 0.0.0.0 8080:8080
2- [laptop] VBoxManage controlvm k8s-master natpf1 "k8s,tcp,,8080,,8080"
3- [laptop] PASSWORD=$(kubectl --kubeconfig config get secret -n openfaas basic-auth -o jsonpath="{.data.basic-auth-password}" | base64 --decode; echo)
4- [laptop] echo -n $PASSWORD | faas-cli login --username admin --password-stdin

## Enlaces de interes

* [Inlets](https://github.com/josanabr/tunneling-inlets)
* [k8s](https://github.com/josanabr/ansible-k8s)
