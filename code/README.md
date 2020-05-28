# code

En este directorio se encuentra el código que se usan en las diferentes actividades dentro del libro que se encuentra en [este directorio](../book/).

* [dockerimages](dockerimages) En este directorio se encuentran los `Dockerfile`s de los diferentes contenedores que se usan en las prácticas del libro.
  * Contenedor con Python + Flask para Raspberry Pi (Raspbian) - [enlace](dockerimages/python-flask/).
  * Contenedor con aplicación similar a la que se aborda en [Get started with Docker Compose](https://docs.docker.com/compose/gettingstarted/) - [enlace](dockerimages/flask-app/).
  * Contenedor con Pandas para Raspberry Pi (Raspbian) - [enlace](dockerimages/pandas/).

* [examples](examples) En este directorio se encuentran algunos ejemplos de como interactuar con un cluster de Kubernetes.

* [k8s](k8s) En este directorio se encuentran los códigos que permiten el despliegue en Kubernetes de la aplicación desarrollada en [Get started with Docker Compose](https://docs.docker.com/compose/gettingstarted/) a través de la herramienta `kubectl`.
