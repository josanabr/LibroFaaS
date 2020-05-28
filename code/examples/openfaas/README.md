# OpenFaaS

OpenFaaS es la implementación del concepto FaaS en su modalidad de código abierto. 

Para operar con un ambiente de OpenFaaS se requiere de un ambiente de cómputo, un ambiente de desarrollo y un ambiente de usuario final.
El ambiente de cómputo estará conformado por equipos Raspberry Pi 4 de 2 GB cada uno.
El ambiente de desarrollo será un Raspberry Pi 3 de 1 GB de RAM y el ambiente de usuario final será un equipo que tenga instalada la herramienta `faas-cli`.
Para instalar la herramienta se ejecuta el siguiente comando:

```
curl -sLfS https://cli.openfaas.com | sudo sh
```

Se inicializa la variable de ambiente `OPENFAAS_URL` que apuntará al ambiente de cómputo.

```
export OPENFAAS_URL="http://faasserver.local:8080"
echo "bWypjU6NXlzQRbo010LqWFHUInFdQyUtlvNzO4bTZ55MkhBMtjCFQI7yK60AnYR" | faas-cli login --password-stdin
```

La cadena `bWyp...nYR` se puede acceder en el equipo ambiente de cómputo a través del siguiente comando:

```
sudo cat /var/lib/faasd/secrets/basic-auth-password
```

Una vez autenticado entonces ya se puede interactuar con la plataforma.

* `faas-cli store list --platform armhf` listar las funciones disponibles para la plataforma ARM

* `faas-cli store deploy --platform armhf figlet` desplegar la función figlet

* `echo "faasd" | faas-cli invoke figlet` invocar la función figlet

---

Está pendiente la inclusión de instrucciones para crear sus propias funciones. Pero se puede ir al libro en la sección 4.2.
