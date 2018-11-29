# Ejercicio 07

En este ejercicio crearemos varios tipos de volúmenes y veremos sus particularidades. Dadas las limitaciones es conveniente usar el entorno cloud.

**Solo para entorno cloud (terminal web):** recargar la ventana del terminal para evitar que expire.

### 1. Crear un pod sencillo

Con el editor de texto o directamente desde el terminal, crear un archivo `pod.yaml` con el siguiente contenido:
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv0003
spec:
  capacity:
    storage: 100Mi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: standard
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



Volver al [Ejercicio 06](../06%20StatefulSets/README.md)

Continuar al [Ejercicio 08](../08%20Secrets%20y%20ConfigMaps/README.md)