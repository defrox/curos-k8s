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

### 2. Crear un pod con un volumen compartido persistente

Los volumenes creados con `hostPath` se crean en el propio sistema de archivos del nodo y son persistentes. Probemos a desplegar un pod con un volumen de este tipo: 
```
kubectl create -f https://raw.githubusercontent.com/defrox/curso-k8s-orange/master/07%20Volumes/pod-hp.yaml
```
Si comprobamos el estado veremos que falla a causa de no encontrar un directorio en el host
```
kubectl describe pod test-pd
```
Pero si creamos el directorio
```
mkdir /root/data
```
y volvemos a comprobar pasados unos segundos, veremos que ya ha conseguido montar el volumen
```
kubectl describe pod test-pd
```
Para comprobar la persistencia, accedemos al pod y creamos un archivo
```
kubectl exec -it test-pd -- touch /test-pd/iwashere.txt
```
y comprobamos en el host:
```
ls /root/data
```
ahora borramos el pod
```
kubectl delete pod test-pd
```
y comprobamos de nuevo en el host:
```
ls /root/data
```

Mas información sobre [volumenes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) y [aprovisionamiento dinámico de volumenes](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)

Volver al [Ejercicio 06](../06%20StatefulSets/README.md)

Continuar al [Ejercicio 08](../08%20Secrets%20y%20ConfigMaps/README.md)