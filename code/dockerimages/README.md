# Dockerfiles para Raspberry Pi 32bits

En este directorio se encuentran los `Dockerfile`s que permirten la creación de las siguientes imágenes:

* [Python con Flask](python-flask)

* [Flask con Aplicación](flask-app) del sitio [Get started with Docker Compose](https://docs.docker.com/compose/gettingstarted/)

* [Pandas](pandas)

---

Para crear las imágenes se hace de la forma usual.
Ubicarse en el directorio que tiene el `Dockerfile` y ejecutar el comando:

```
docker build -t <su_usuario_en_docker_hub>/<nombre_de_la_imagen>
```

Donde:

* `<su_usuario_en_docker_hub>` corresponde a su usuario en la plataforma Docker Hub.

* `<nombre_de_la_imagen>` corresponde al nombre que le quiere dar a la imagen creada en Docker.
