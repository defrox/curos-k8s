# Ejercicio 03

En este ejercicio crearemos varios tipos de servicios y veremos sus particularidades.

**Solo para entorno cloud (terminal web):** recargar la ventana del terminal para evitar que expire.

### 1. Crear un servicio del tipo ClusterIP

En este caso necesitaremos crear un pod además del servicio. Con el editor de texto o directamente desde el terminal, crear un archivo `pod.yaml` con el siguiente contenido:
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
    - name: nginx-container
      image: nginx:1.7.9
      ports:
      - containerPort: 80
```
y un servicio `service.yaml`
```
kind: Service
apiVersion: v1
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx # Pod al que enrutará este servicio
  ports:
  - name: http
    protocol: TCP
    port: 80
```
Ahora desplegamos el pod y el servicio que acabamos de crear:

```
kubectl create -f pod.yaml -f service.yaml
```
Comprobamos lo que acabamos de desplegar:
```
kubectl get pods
```
y
```
kubectl get svc
```
Una vez se ha desplegado, podemos acceder al servicio `nginx-service` a traves de su `CLUSTER_IP` y puerto con:
```
curl <CLUSTER_IP>:80
```
Y veremos por pantalla el html que devuelve el pod `nginx`

Comprobemos nuestro servicio en detalle:
```
kubectl describe svc nginx-service
```
o también:
```
kubectl get svc nginx-service -o yaml
```
Ahora borramos el servicio del cluster:
```
kubectl delete svc nginx-service
```
Si volvieramos a ejecutar el comando curl anterior, veríamos que ya no tenemos acceso al pod.

### 2. Crear un servicio del tipo NodePort

En este caso usaremos el mismo pod creado previamente, y crearemos un nuevo servicio con el contenido:

```
kind: Service
apiVersion: v1
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx # Pod al que enrutará este servicio
  type: NodePort
  ports:
  - name: http
    protocol: TCP
    port: 8080
    targetPort: 80
```
Ahora desplegamos el servicio que acabamos de crear:

```
kubectl apply -f service.yaml
```
Comprobamos el servicio que acabamos de desplegar:
```
kubectl get svc
```
Anotamos los dos puertos de nuestro servicio. De nuevo accedemos al servicio `nginx-service` a traves de su `CLUSTER_IP` y puerto con:
```
curl <CLUSTER_IP>:8080
```
Ahora obtenemos la dirección IP del cluster con el comando:
```
kubectl cluster-info
```
Si probamos acceder a la IP que acabamos de obtener a través del segundo puerto de nuestro servicio, veremos que también accedemos al pod:
```
curl <K8S_CLUSTER_IP>:<NODE_PORT>
```

### 3. Crear un servicio del tipo LoadBalancer

Vamos a editar nuestro pod con el contenido:

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  containers:
    - name: nginx-container
      image: nginx:1.7.9
      ports:
      # Ahora los nombramos
      - name: http
        hostPort: 80
        containerPort: 80
      - name: https
        hostPort: 443
        containerPort: 80
```
y el servicio:
```
kind: Service
apiVersion: v1
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx # Pod al que enrutará este servicio
  type: LoadBalancer
  ports:
  - name: http
    protocol: TCP
    port: 80
    targetPort: http # nombre del puerto en el pod
  - name: https
    protocol: TCP
    port: 443
    targetPort: https
```
actualizamos ambos objetos desplegados:
```
kubectl apply -f service.yaml -f pod.yaml
```
Comprobamos lo que acabamos de desplegar:
```
kubectl get svc && kubectl get pod
```
De nuevo accedemos al servicio `nginx-service` a traves de su `CLUSTER_IP` y los dos puertos definidos en el servicio con:
```
curl <CLUSTER_IP>:80
curl <CLUSTER_IP>:443
```
Para exponer los puntos de acceso `ENDPOINTS` en minikube, ejecutar el comando:
```
minikube service nginx-service
```
Y tendremos acceso desde el exterior. 

**Solo para entorno cloud (terminal web):** En la parte superior del terminal web, pulsar sobre el signo mas y luego pulsar en "Select port to view on Host 1". Escribir el segundo puerto después del 80 (repetir también para el 443), y luego pulsar "Display Port". Esto abrirá una ventana del navegador con nuestro despliegue.

- En los proveedores cloud que admiten balanceadores de carga, se asignará una dirección IP externa para acceder al Servicio.

Ya podemos limpiar nuestro cluster:
```
kubectl delete -f service.yaml -f pod.yaml
```

Volver al [Ejercicio 02](../02%20Pods/README.md)

Continuar al [Ejercicio 04](../04%20Deployments/README.md)