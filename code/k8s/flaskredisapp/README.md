# Aplicación Flask + Redis 

Para ejecutar esta aplicación se asume que la herramienta `kubectl` se encuentra propiamente configurada y es capaz de acceder a un cluster de Kubernetes.
Para validar que hay acceso a un cluster de k8s, ejecutar lo siguiente:

```
kubectl get nodes
```

Este comando deberá mostrar los nodos que tiene el cluster al cual se puede acceder.

---

Para lanzar la aplicación sobre el cluster de k8s basado en Raspberry Pi, ejecutar lo siguiente:

```
kubectl apply -f flaskapp.yaml
```

Para acceder a la aplicación ejecute el siguiente comando: 

```
kubectl get ingress.extensions
```

Un número IP deberá ser presentado y desde su navegador se podrá acceder a la aplicación a través de dicho número IP.

Para detener la ejecución de la aplicación se ejecuta:

```
kubectl delete -f flaskapp.yaml
```
