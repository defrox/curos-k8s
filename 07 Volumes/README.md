# Ejercicio 07

En este ejercicio crearemos varios tipos de volúmenes y veremos sus particularidades.

**Solo para entorno cloud (terminal web):** recargar la ventana del terminal para evitar que expire.

### 1. Crear un pod con un volumen compartido efímero

Los volumenes creados con `emptyDir: {}` se crean en el propio sistema de archivos del nodo y solo viven mientras vive el pod (pero sobrevive a los reinicios). Probemos a desplegar un pod con un volumen de este tipo: 
```
kubectl create -f https://raw.githubusercontent.com/defrox/curso-k8s-orange/master/07%20Volumes/pod.yaml
```
y comprobamos como los dos contenedores del pod comparten el mismo volumen, entrando en el contenedor `c1`
```
kubectl exec -it sharevol -c c1 bash
```
una vez dentro, comprobamos que el directorio `/tmp/xchange` esta vacío y creamos un archivo
```
ls /tmp/xchange
touch /tmp/xchange/iwashere.txt
ls /tmp/xchange
exit
```
y comprobamos en el contenedor `c2`:
```
kubectl exec -it sharevol -c c2 -- ls /tmp/data
```
limpiamos
```
kubectl delete pod sharevol
```

### 1. Crear un pod con un volumen compartido persistente

Los volumenes creados con `hostPath` se crean en el propio sistema de archivos del nodo y son persistentes. Probemos a desplegar un pod con un volumen de este tipo: 
```
kubectl create -f https://raw.githubusercontent.com/defrox/curso-k8s-orange/master/07%20Volumes/pod-hp.yaml
```
y comprobamos como los dos contenedores del pod comparten el mismo volumen, entrando en el contenedor `c1`
```
kubectl exec -it sharevol -c c1 bash
```
una vez dentro, comprobamos que el directorio `/tmp/xchange` esta vacío y creamos un archivo
```
ls /tmp/xchange
touch /tmp/xchange/iwashere.txt
ls /tmp/xchange
exit
```
y comprobamos en el contenedor `c2`:
```
kubectl exec -it sharevol -c c2 -- ls /tmp/data
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