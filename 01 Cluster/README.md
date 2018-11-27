# Ejercicio 1

### Comprobación de kubectl y minikube

Para comprobar si tenemos instalado kubectl correctamente, ejecutar lo siguiente para mostrar la versión que tenemos instalada:

```
kubectl version
```

Luego comprobamos **minikube** con el comando:

```
minikube version
```

### Desplegar el clúster de minikube

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

### kubectl

Para no tener que estar especificando el contexto cada vez que usamos el comando **kubectl**, debemos configurar el contexto por defecto:

```
kubectl config use-context minikube
```

### Dashboard

Para acceder al Dashboard de Kubernetes, ejecutar este comando en un terminal después de haber iniciado Minikube para obtener la dirección:

```
minikube dashboard
```

### Parar minikube

Para parar el clúster de minikube debemos ejecutar:
```
minikube stop
```
**Nota:** esto eliminará todo los objetos desplegados y lo que hayamos hecho en el clúster de minikube, ya que no es un despliegue persistente.