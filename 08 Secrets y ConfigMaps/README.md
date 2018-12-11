# Ejercicio 08

En este ejercicio crearemos varios ConfigMaps y Secrets y veremos sus particularidades.

**Solo para entorno cloud (terminal web):** recargar la ventana del terminal para evitar que expire.

### 1. Crear un configmap y un pod que acceda a él

Con el editor de texto o directamente desde el terminal, creamos un archivo de texto:
```
echo "All you need is Love!" > slogan.txt
```
y un archivo `configmap.yaml` con ese archivo de texto:
```
kubectl create configmap orange --from-file=slogan.txt
```
Comprobamos lo que hemos creado
```
kubectl describe configmap orange
```
```
Name:           orange
Namespace:      default
Labels:         <none>
Annotations:    <none>

Data
====
slogan.txt:
----
All you need is Love!

Events: <none>
```
Ya podemos crear un `pod.yaml` con el siguiente contenido:
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
      command: ['sh', '-c', 'echo I am ready && sleep 3600']
      env:
        # Define the environment variable
        - name: ORANGE
          valueFrom:
            configMapKeyRef:
              name: orange
              key: slogan.txt
```
desplegamos el pod que acabamos de crear:
```
kubectl create -f pod.yaml
```
y comprobamos que la variable de entorno creada a partir del configmap existe en el pod
```
kubectl exec -it myapp-pod env | grep ORANGE
```
```
ORANGE=All you need is Love!
```
Ya podemos eliminar el pod:
```
kubectl delete -f pod.yaml
```

### 2. Crear un secret y un pod que acceda a él

Con el editor de texto o directamente desde el terminal, creamos un archivo de texto:
```
echo "this is my new password" > password.txt
```
y un archivo `secret.yaml` con ese archivo de texto:
```
kubectl create secret generic newpass --from-file=password.txt
```
Comprobamos lo que hemos creado
```
kubectl describe secret newpass
```
```
Name:           newpass
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
password.txt:  24 bytes
```
Si visualizamos el secret veremos que el valor esta encriptado en base64
```
kubectl get secret newpass -o yaml
```
Ya podemos crear un `pod.yaml` con el siguiente contenido:
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
    image: centos:7
    command: ['sh', '-c', 'echo I am ready && sleep 3600']
    volumeMounts:
      - name: passvol
        mountPath: "/tmp/newpass"
        readOnly: true
  volumes:
  - name: passvol
    secret:
        secretName: newpass
```
desplegamos el pod que acabamos de crear:
```
kubectl create -f pod.yaml
```
comprobamos que ha arrancado correctamente
```
kubectl logs myapp-pod
```
y comprobamos que el archivo creado a partir del secret existe en el pod
```
kubectl exec -it myapp-pod cat /tmp/newpass/password.txt
```
```
this is my new password
```
Ya podemos eliminar el pod:
```
kubectl delete -f pod.yaml
```

---

Volver al [Ejercicio 07](../07%20Volumes/README.md)

Ir al [Extras 99](../99%20Extras/README.md)