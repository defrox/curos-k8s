# Ejercicio 05

En este ejercicio crearemos varios replicasets y veremos sus particularidades.

**Solo para entorno cloud (terminal web):** recargar la ventana del terminal para evitar que expire.

### 1. Crear un replicaset

Con el editor de texto o directamente desde el terminal, crear un archivo `replicaset.yaml` con el siguiente contenido:
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: guestbook
    tier: frontend
spec:
  # modificar el número de replicas a elección
  replicas: 3
  selector:
    matchLabels:
      tier: frontend
    matchExpressions:
      - {key: tier, operator: In, values: [frontend]}
  template:
    metadata:
      labels:
        app: guestbook
        tier: frontend
    spec:
      containers:
      - name: php-redis
        image: gcr.io/google_samples/gb-frontend:v3
        resources:
          requests:
            cpu: 10m
            memory: 10Mi
        env:
        - name: GET_HOSTS_FROM
          value: dns
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            port: 80
```
y un servicio `service.yaml`
```
kind: Service
apiVersion: v1
metadata:
  name: frontend-service
spec:
  selector:
    app: guestbook # RS al que enrutará este servicio
  type: LoadBalancer
  ports:
  - name: http
    protocol: TCP
    port: 80
```
desplegamos lo que acabamos de crear:
```
kubectl create -f replicaset.yaml -f service.yaml
```
Si lo deseamos podemos comprobar que se puede acceder desde internet
```
minikube service frontend-service
```
**Solo para entorno cloud (terminal web):** En la parte superior del terminal web, pulsar sobre el signo mas y luego pulsar en "Select port to view on Host 1". Escribir el segundo puerto después del 80, y luego pulsar "Display Port". Esto abrirá una ventana del navegador con nuestro despliegue.

### 2. Comprobar el uso de recursos

Para comprobar el uso de recursos de nuestros objetos desplegados, ejecutamos:
```
kubectl describe nodes
```
Y veremos un listado con los recursos que hemos establecido en el replicaset.

### 3. Aumentar los recursos

Si se diera el caso de que nuestros pods no tienen suficientes recursos, podríamos aumentarlos como en el siguiente YAML:
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: guestbook
    tier: frontend
spec:
  # modificar el número de replicas a elección
  replicas: 3
  selector:
    matchLabels:
      tier: frontend
    matchExpressions:
      - {key: tier, operator: In, values: [frontend]}
  template:
    metadata:
      labels:
        app: guestbook
        tier: frontend
    spec:
      containers:
      - name: php-redis
        image: gcr.io/google_samples/gb-frontend:v3
        resources:
          requests:
            cpu: 100m
            memory: 100Mi
        env:
        - name: GET_HOSTS_FROM
          value: dns
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            port: 80
```
Y lo actualizamos
```
kubectl apply -f replicaset.yaml
```
Comprobamos que en efecto a aumentado los recursos
```
kubectl describe rs frontend
```
---

Volver al [Ejercicio 04](../04%20Deployments/README.md)

Continuar al [Ejercicio 06](../06%20StatefulSets/README.md)