# Ejercicio 01

Si no hemos instalado minikube en local, podemos pasar al [punto 4](#4-lanzar-minikube-desde-la-nube)

### 1. Comprobación de kubectl y minikube

Para comprobar si tenemos instalado kubectl correctamente, ejecutar lo siguiente para mostrar la versión que tenemos instalada:

```
kubectl version
```

Luego comprobamos **minikube** con el comando:

```
minikube version
```

### 2. Desplegar el clúster de minikube

Para arrancar minikube en nuestra máquina virtual hay que ejecutar (la primera vez se descargará la imagen de minikube, por lo que tardará un rato):

```
minikube start --vm-driver=none
```
**Nota:** en este caso, al lanzar minikube dentro de una VM, no debemos usar ningún driver de virtualización, ya que la virtualización anidada no es posible. Por esto se lanza minikube directamente en el host de la VM.

Comprobar el estado de minikube:

```
minikube status
```

Para poder interactuar con el demonio de docker en nuestra máquina, hay que usar el comando docker-env en nuestro terminal:

```
eval $(minikube docker-env)
```

Ahora ya podemos usar docker en la línea de comandos de nuestra máquina para que se comunique con el demonio de docker dentro de la máquina virtual de minikube:

```
docker ps
```

### 3. kubectl

Para no tener que estar especificando el contexto cada vez que usamos el comando **kubectl**, debemos configurar el contexto por defecto:

```
kubectl config use-context minikube
```

### 4. Lanzar minikube desde la nube

Si hemos instalado minikube en local saltar este paso. Abrir el terminal en https://goo.gl/GFpLrE

### 5. Dashboard

Para acceder al Dashboard de Kubernetes, ejecutar este comando en un terminal después de haber iniciado Minikube para obtener la dirección:

```
minikube dashboard
```
**Solo para entorno cloud (terminal web):** En la parte superior del terminal web, pulsar sobre el signo mas y luego pulsar en "Select port to view on Host 1". Escribir 30000, y luego pulsar "Display Port".
                         
### 6. Hello Minikube

Ahora usaremos el comando `kubectl run`  para realizar un despliegue sencillo con un único pod.
```    
kubectl run hello-node --image=gcr.io/hello-minikube-zero-install/hello-node --port=8080
```
Comprobamos el despliegue:
```
kubectl get deployments
```
Deberiamos ver:
```
NAME         DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
hello-node   1         1         1            1           1m
```
Comprobamos el pod:
```
kubectl get pods
```
Deberiamos ver:
```
NAME                          READY     STATUS    RESTARTS   AGE
hello-node-5f76cf6ccf-br9b5   1/1       Running   0          1m
```
Comprobamos los eventos del clúster:
```
kubectl get events
```
Comprobamos los nodos del clúster:
```
kubectl get nodes -o wide
```
Comprobamos la configuración de 'kubectl':
```
kubectl config view
```
**Nota:** Para mas información sobre los comandos de `kubectl` ir al [manual](https://kubernetes.io/docs/user-guide/kubectl-overview/) online.

Exponer el Pod al exterior (internet) usando el comando "kubectl expose":
```
kubectl expose deployment hello-node --type=LoadBalancer
```
El parámetro `--type=LoadBalancer` indica que queremos exponer nuestro servicio fuera del cluster.

Comprobamos el servicio que acabamos de crear:
```
kubectl get services
```
Salida:
```
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
hello-node   LoadBalancer   10.108.144.78   <pending>     8080:30369/TCP   21s
kubernetes   ClusterIP      10.96.0.1       <none>        443/TCP          23m
```
En los proveedores cloud que admiten balanceadores de carga, se asignará una dirección IP externa para acceder al Servicio. En Minikube, el tipo LoadBalancer hace que el Servicio sea accesible a través del comando `minikube service`.

Ejecutar el comando:
```
minikube service hello-node
```
**Solo para entorno cloud (terminal web):** En la parte superior del terminal web, pulsar sobre el signo mas y luego pulsar en "Select port to view on Host 1". Escribir el segundo puerto después del 8080, y luego pulsar "Display Port". Esto abrirá una ventana del navegador con nuestra app mostrando el mensaje "Hello World".

### 7. Habilitar addons

Minikube posee una lista de addons que pueden ser habilitados, deshabilitados y ejecutados en el entorno local de Kubernetes.

Para mostrar la lista de los addons soportados:
```
minikube addons list
```
Para habilitar un addon, por ejemplo `heapster`:
```
minikube addons enable heapster
```
Salida:
```
heapster was successfully enabled
```
Comprobar el Pod y el Service recién creados:
```
kubectl get pod,svc -n kube-system
```
Para deshabilitar `heapster`:
```
minikube addons disable heapster
```
Salida:
```
heapster was successfully disabled
```
### 8. Limpieza

Ahora ya podemos eliminar los recursos que hemos creado en nuestro clúster:
```
kubectl delete service hello-node
kubectl delete deployment hello-node
```

### 9. Parar minikube

Para parar el clúster de minikube debemos ejecutar (no ejecutar aun ;)
```
minikube stop
```
Opcionalmente, podemos borrar la VM de minikube:
```
minikube delete
```
**Nota:** esto eliminará todo los objetos desplegados y lo que hayamos hecho en el clúster de minikube, ya que no es un despliegue persistente.

Volver al [Indice](../README.md)

Continuar al [Ejercicio 02](../02%20Pods/README.md)