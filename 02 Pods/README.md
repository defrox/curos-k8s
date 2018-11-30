# Ejercicio 02

En este ejercicio crearemos varios tipos de pods y veremos sus particularidades.

**Solo para entorno cloud (terminal web):** recargar la ventana del terminal para evitar que expire.

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

### 2. Pod con sondas de estado

Muchas aplicaciones que se ejecutan durante largos períodos de tiempo eventualmente hacen la transición a estados fallidos, y no pueden recuperarse excepto si son reiniciadas. Kubernetes proporciona sondas para detectar y remediar tales situaciones. Para controlar los casos en los cuales se deben reiniciar se establece a través del valor del parámetro `restartPolicy` (`Alawys` y `OnFailure` harán que se puedan reiniciar, `Never` lo impedirá siempre)

Volvamos a editar el archivo pod.yaml (o podemos crear otro nuevo) con el siguiente contenido:

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-exec
spec:
  containers:
  - name: liveness
    image: k8s.gcr.io/busybox
    args:
    - /bin/sh
    - -c
    - touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600
    livenessProbe:
      exec:
        command:
        - cat
        - /tmp/healthy
      initialDelaySeconds: 5
      periodSeconds: 5
```
Desplegamos nuestro nuevo pod:

```
kubectl apply -f pod.yaml
```

Y comprobamos el despliegue:

```
watch kubectl get pod liveness-exec -o wide
```

Veremos que los primeros 30 segundos, el pod devolverá un estado correcto ('healthy'), pero al cabo de 35 segundos devolverá un código de error.

Podemos ver la evolución del pod con el comando:

```
kubectl describe pod liveness-exec
```

Si ejecutáramos el comando y esperáramos unos minutos, comprobaríamos como aumenta el número de reinicios del pod.

**Nota para nerds:** flapping es cuando un pod se reinicia continuamente a causa de un error en el pod.

```
watch kubectl get pod liveness-exec -o wide
```

Borramos el pod:

```
kubectl delete pod liveness-exec
```

### 3. Sondas HTTP

Otro tipo de sondas usan solicitudes HTTP GET.

Pasemos a crear o editar nuestro archivo `pod.yaml` con el contenido

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-http
spec:
  containers:
  - name: liveness
    image: k8s.gcr.io/liveness
    args:
    - /server
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
        httpHeaders:
        - name: X-Custom-Header
          value: Awesome
      initialDelaySeconds: 3
      periodSeconds: 3
```
y desplegamos de nuevo el pod:

```
kubectl apply -f pod.yaml
```
 
En el caso de las sondas HTTP, cualquier código mayor o igual a 200 y menor de 400 indicará un estado correcto o exitoso. Cualquier otro valor indicará un estado erróneo o fallido.
 
En este ejemplo durante los primeros 10 segundos el pod estará 'vivo', `/healthz` devolverá un código 200. Después de esto devolverá un código de estado 500.

Comprobamos:
```
watch kubectl get pod liveness-http -o wide
```
y también
```
kubectl describe pod liveness-http
```

Las sondas HTTP poseen campos adicionales que pueden ser establecidos en `httpGet`:

`host`, `scheme`, `path`, `httpHeaders`, `port`

Una vez hemos terminado, eliminamos el pod:

```
kubectl delete pod liveness-http
```
### 4. Sondas TCP

Un tercer tipo de sondas utilizan el socket TCP. Con este tipo de configuración, el kubelet (agente del nodo) intentará abrir un socket a su contenedor en el puerto especificado. Si puede establecer una conexión, el contenedor se considera en buen estado, si no se puede lo considerará fallido.

Pasemos a crear o editar nuestro archivo `pod.yaml` con el contenido

```
apiVersion: v1
kind: Pod
metadata:
  name: goproxy
  labels:
    app: goproxy
spec:
  restartPolicy: OnFailure
  containers:
  - name: goproxy
    image: k8s.gcr.io/goproxy:0.1
    ports:
    # Nota: puertos del pod con nombre
    -  name: liveness-port
       containerPort: 8080
       hostPort: 8080
    # Nota: este pod tiene los dos tipos de sondas
    readinessProbe:
      tcpSocket:
        port: liveness-port
      initialDelaySeconds: 5
      periodSeconds: 10
    livenessProbe:
      tcpSocket:
        port: 8080
      initialDelaySeconds: 15
      periodSeconds: 20
```
y desplegamos de nuevo el pod:

```
kubectl apply -f pod.yaml
```
 
Como se puede ver, la configuración para la comprobación TCP es bastante similar a la comprobación HTTP. Este ejemplo usa tanto las sondas `readiness` como `liveness`. El `kubelet` enviará la primera sonda `readiness` 5 segundos después de que se inicie el contenedor. En concreto, intentará conectarse al contenedor goproxy en el puerto 8080. Si la sonda tiene éxito, el pod se marcará como listo (ready). El kubelet continuará ejecutando esta comprobación cada 10 segundos.

Además de la sonda `readiness`, esta configuración incluye una sonda `liveness`. El kubelet ejecutará la primera sonda `liveness` 15 segundos después de que se inicie el contenedor. Al igual que la sonda `readiness`, `kubelet` intentará conectarse al contenedor goproxy en el puerto 8080. Si la sonda falla, el contenedor se reiniciará.

Comprobamos:
```
watch kubectl get pod goproxy -o wide
```
y también
```
kubectl describe pod goproxy
```
Una vez hemos terminado, eliminamos el pod:

```
kubectl delete pod goproxy
```
Además de lo visto en este ejemplo (puertos con nombre, sondas `readiness` y `livenes`), existen otros elementos que nos permiten configurar las sondas:

`initialDelaySeconds` , `periodSeconds`, `timeoutSeconds`, `successThreshold`, `failureThreshold`

### 5. Init Containers

Los initContainer son contenedores que se ejecutan de forma secuencial y hasta terminación antes de ejecutar los contenedores del pod.

Pasemos a crear o editar nuestro archivo `pod.yaml` con el contenido

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
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox
    command: ['sh', '-c', 'until nslookup myservice; do echo waiting for myservice; sleep 2; done;']
  - name: init-mydb
    image: busybox
    command: ['sh', '-c', 'until nslookup mydb; do echo waiting for mydb; sleep 2; done;']
```
y desplegamos de nuevo el pod:

```
kubectl apply -f pod.yaml
```
Comprobamos:
```
watch kubectl get pods -o wide
```
y vemos que no termina de desplegar el pod, ya que el initContainer esta esperando a tener conexión con `myservice` y con `mydb`.
```
NAME        READY     STATUS     RESTARTS   AGE
myapp-pod   0/1       Init:0/2   0          6m
```
Creamos un nuevo archivo `service.yaml` con el contenido:
```
kind: Service
apiVersion: v1
metadata:
  name: myservice
spec:
  ports:
  - protocol: TCP
    port: 80
    targetPort: 9376
---
kind: Service
apiVersion: v1
metadata:
  name: mydb
spec:
  ports:
  - protocol: TCP
    port: 80
    targetPort: 9377
```
y lo desplegamos
```
kubectl apply -f service.yaml
```
y volvemos a comprobar:
```
watch kubectl get pods -o wide
```
también podemos comprobar los servicios en otro terminal:
```
watch kubectl get svc -o wide
```
Veremos que una vez los servicios se despliegan, el pod consigue levantarse correctamente.
```
NAME        READY     STATUS    RESTARTS   AGE
myapp-pod   1/1       Running   0          9m
```
---

Volver al [Ejercicio 01](../01%20Cluster/README.md)

Continuar al [Ejercicio 03](../03%20Services/README.md)