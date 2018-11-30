# Ejercicio 04

En este ejercicio crearemos varios deployments y veremos sus particularidades.

**Solo para entorno cloud (terminal web):** recargar la ventana del terminal para evitar que expire.

### 1. Mi primer deployment

Con el editor de texto o directamente desde el terminal, crear un archivo `deployment.yaml` con el siguiente contenido:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2 # le dice al deployment que ejecute 2 pods como se define en template
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```
Ahora desplegamos el deployment que acabamos de crear:
```
kubectl create -f deployment.yaml  --save-config
```
Comprobamos el despliegue por terminal:
```
watch kubectl get deployment
```
Cuando este desplegado correctamente (2 réplicas disponibles), para ver la información del deployment ejecutamos:
```
kubectl describe deployment nginx-deployment
```
Listamos los pods creados por nuestro deployment
```
kubectl get pods -l app=nginx
```
Y mostramos la información de un pod:
```
kubectl describe pod <nombre_del_pod>
```
donde `<nombre_del_pod>` es el nombre de uno de nuestros pods.

### 2. Actualización del deployment

Se puede actualizar un deployment si desplegamos un nuevo YAML con:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.8 # Actualizamos la versión de nginx de 1.7.9 a 1.8
        ports:
        - containerPort: 80
```
Para actualizar el deployment:
```
kubectl apply -f deployment.yaml
```
Y comprobamos como el deployment crea nuevos pods con los nuevos nombres y elimina los antiguos:
```
watch kubectl get pods -l app=nginx
```

### 3. Escalado del deployment

Podemos aumentar el número de réplicas en nuestro deployment, si modificamos el valor y aplicamos el YAML. En nuestro caso aumentaremos el valor de `replicas` a 4:
```
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 4 # Update the replicas from 2 to 4
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.8
        ports:
        - containerPort: 80
```
Actualizamos
```
kubectl apply -f deployment.yaml
```
y comprobamos
```
watch kubectl get pods -l app=nginx
```

### 4. Eliminación del deployment

Para eliminar el deployment debemos ejecutar:
```
kubectl delete deployment nginx-deployment
```

---


Volver al [Ejercicio 03](../03%20Services/README.md)

Continuar al [Ejercicio 05](../05%20ReplicaSets/README.md)