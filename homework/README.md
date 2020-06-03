# Asignación FaaS, K8S, Inlets

## Objetivo

El propósito de esta asignación es validar que el estudiante ha adquirido unas competencias mínimas en cuanto a la gestión y uso de las tecnologías FaaS (*Function as a Service*), K8S (Kubernetes) e Inlets.

Este documento consta de las siguientes secciones:

* [Resultado esperado](#resultado-esperado).

* [Guía](#guia) en esta sección se presentan algunos tips respecto a las tecnologías que se deben usar en el proyecto.

* [Términos de la entrega](#terminos-de-la-entrega) en esta sección se dan detalles de que es lo que debe entregar el estudiante como informe final.

* [Enlaces de interés](#enlaces-de-interes) en esta sección se presentan algunos enlaces que pueden servir de interés para llevar a cabo el proyecto.

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
En la sección 5.5 del [libro compartido](../book/output/book.pdf) en clase también se da una explicación de este proceso de instalación.

El ambiente de Kubernetes con OpenFaaS instalado deberá ser expuesto hacia Internet usando la tecnología de Inlets. 
En la sección [Enlaces de Interés](#enlaces-de-interes) se brinda un enlace al repositorio que se compartió en clase respecto al tema de Inlets.

En este ambiente de Kubernetes + OpenFaaS deberá desplegarse una función que deberá usar algunas de las siguientes funciones y/o librerías:

* Una función que haga uso de la librería Pandas. 

* Una función que haga uso de la librería TensorFlow ([enlace](https://www.tensorflow.org/api_docs/python/tf)).

* Una función que haga el procesamiento de un video o de una imágen y obtenga los números de placa dado un archivo multimedia ([enlace](https://github.com/openalpr/openalpr)).

* Otra. Enviar un correo al profesor (john.sanabria@correounivalle.edu.co) y proponer su idea de proyecto.

**En cualquiera de las opciones que seleccione deberá proveer el Dockerfile con el que se creó la imagen de contenedor donde reside la función y un caso de uso de dicha función.**

A continuación se da una guía o pequeños tips que debe tener en cuenta el estudiante a la hora de llevar a cabo esta asignación.

## Guia

En la sección anterior se explicó acerca de que es lo que se espera el estudiante lleve a cabo. 
A continuación se dan algunos *tips* respecto de la mayoría de tecnologías que el estudiante requiere para llevar a cabo el presente proyecto.

### Instalacion Kubernetes

Para llevar a cabo el despliegue de Kubernetes se dan las siguientes opciones:

* [k3s](https://k3s.io/) Este proyecto permite la ejecución de Kubernetes en ambientes basados en sistemas ARM, IoT, entre otros. Existen en Internet sitios que hablan de como instalar k3s sobre máquinas virtuales basadas en VirtualBox.

* [Kubernetes en VirtualBox](https://github.com/josanabr/ansible-k8s) En este repositorio se pueden crear hasta 3 máquinas virtuales en las cuales se instala Kubernetes original. La última vez que se probó fue el día 2020-06-02 con todo éxito.

* [Minikube](https://kubernetes.io/docs/setup/learning-environment/minikube/) Este proyecto permite tener todo un ambiente de Kubernetes operacional en una sola máquina.


### `kubectl`

`kubectl` es la herramienta que permite la gestión de un cluster de k8s. 
Para instalar la herramienta visitar [este enlace](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

`kubectl` se puede instalar dentro del *master* del cluster de Kubernetes o fuera de él.
**Es decisión del estudiante si decide hacer la instalación dentro o fuera del** *master*.

### OpenFaaS

Para instalar OpenFaaS en un cluster de Kubernetes debe acceder al nodo *master* y se sugieren los siguientes pasos:

* Instalar la herramienta para la gestión de paquetes en Kubernetes llamada `arkade`:

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

En la siguiente sección se describe como instalar `faas-cli`.

### `faas-cli`

En el ítem anterior se mostró como instalar `OpenFaaS` en un cluster de Kubernetes.
La salida del comando que instala `OpenFaaS` indica los pasos para instalar `faas-cli` y luego como acceder a él. 
`faas-cli` se puede instalar en el *master* o en algún otro nodo que tenga acceso al *master* del cluster de Kubernetes.

Para la instalación de `faas-cli` se sugieren los siguientes pasos:

* Instalación de `faas-cli`
```
curl -SLsf https://cli.openfaas.com | sudo sh
```

Si `faas-cli` se instaló por fuera del *master* se requieren algunos pasos adicionales. 
Aquí se dan estos pasos:

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

## Terminos de la entrega

La entrega de este proyecto debe constar de lo siguiente:

* Un informe escrito que brinde un contexto del problema a abordar (algo similar a lo que se indica en la sección [Resultado esperado](#resultado-esperado)). Así mismo el desarrollo o los pasos seguidos para llegar a la conclusión del proyeto.

* Un repositorio en GitHub donde se de evidencia de todos los scripts y archivos que permitieron llegar a la solución del problema, ejemplo: Dockerfiles, scripts de isntalación. Todos los insumos que en el informe se presenten deberán ser puestos en el repositorio de GitHub y debidamente referenciados dentro del informe.

* Videos en [asciinema](https://asciinema.org) y que evidencien:

  * El acceso al cluster de Kubernetes via `kubectl`.
  * La construcción, despliegue y publicación de la función que se usó en el proyecto via `faas-cli`.
  * La ejecución de la función desde Internet y al cluster de Kubernetes que se encuentra dentro de la red privada via `faas-cli`.

## Enlaces de interes

En esta sección se brindan algunos enlaces que puede ser de utilidad para el desarrollo del proyecto.

* [Como desplegar Inlets en Google Cloud Platform](https://github.com/josanabr/tunneling-inlets)
* [Kubernetes en máquinas virtuales de VirtualBox usando Vagrant](https://github.com/josanabr/ansible-k8s)
* [Libro OpenFaaS, Kubernetes](../book/output/book.pdf).
