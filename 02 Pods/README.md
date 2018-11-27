Lanzar el pod.yaml

`kubectl create -f pod.yaml`

Ver el despliegue en el dashboard, comprobar yaml, logs y exec:

Eliminar pod por referencia al archivo:

`kubectl delete -f pod.yaml`

Eliminar pod por nombre del pod:

`kubectl delete pod myapp-pod`

Eliminar pod a la fuerza:

`kubectl delete pod myapp-pod --force --grace-period=0`