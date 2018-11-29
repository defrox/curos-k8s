# Ejercicio 04

En este ejercicio crearemos varios pods y veremos sus particularidades.

### 1. Crear un pod sencillo

Con el editor de texto o directamente desde el terminal, crear un archivo `pod.yaml` con el siguiente contenido:
```
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
    - name: myapp-container
      image: busybox
      command: ['sh', '-c', 'echo All you need is Love! && sleep 3600']
```
Ahora desplegamos el pod que acabamos de crear:

```
kubectl create -f pod.yaml
```

o también:

```
kubectl apply -f pod.yaml
```

**Nota:** normalmente para crear un objeto en Kubernetes se usa el comando `create` de `kubectl`, pero también se puede usar `apply` que además sirve para actualizar elementos existentes.

Comprobamos el despliegue del pod en el dashboard (yaml, logs y exec). También podemos hacerlo por terminal:

```
watch kubectl get pods
```

Para eliminar un pod haciendo referencia al archivo:

```
kubectl delete -f pod.yaml
```

Para eliminar un pod haciendo nombre del pod:

```
kubectl delete pod myapp-pod
```

Para eliminar un pod a la fuerza:

```
kubectl delete pod myapp-pod --force --grace-period=0
```


Volver al [Ejercicio 03](../03%20Services/README.md)

Continuar al [Ejercicio 05](../05%20ReplicaSets/README.md)