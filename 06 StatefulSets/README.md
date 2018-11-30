# Ejercicio 06

En este ejercicio crearemos varios pods y veremos sus particularidades.

**Solo para entorno cloud (terminal web):** recargar la ventana del terminal para evitar que expire.

### 1. Crear un StatefulSet

Con el editor de texto o directamente desde el terminal, crear un archivo `nginx.yaml` con el siguiente contenido:
```
apiVersion: v1
kind: Service
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  ports:
  - port: 80
    name: web
  clusterIP: None
  selector:
    app: nginx
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  selector:
    matchLabels:
      app: nginx # debe coincidir con .spec.template.metadata.labels
  serviceName: "nginx"
  replicas: 3 # por defecto es 1
  template:
    metadata:
      labels:
        app: nginx # debe coincidir con .spec.selector.matchLabels
    spec:
      terminationGracePeriodSeconds: 60
      containers:
      - name: nginx
        image: k8s.gcr.io/nginx-slim:0.8
        ports:
        - containerPort: 80
          name: web
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html/test
        readinessProbe:
          httpGet:
            port: 80
          initialDelaySeconds: 30
          periodSeconds: 10
      volumes:
      - name: www
        emptyDir: {}
```
Y desplegamos lo que acabamos de crear:
```
kubectl create -f nginx.yaml
```
Como podemos comprobar en este caso hemos declarado dos objetos en un solo archivo, lo que nos permite desplegarlos juntos.

Ahora comprobemos como se despliegan secuencialmente:
```
watch kubectl get pods
```
Para entrar al terminal de un pod:
```
kubectl exec -it web-0 bash
```
Una vez dentro creamos un archivo `web-0.txt` y salimos del pod:
```
touch /usr/share/nginx/html/test/web-0.txt && exit
```
entramos en el pod y comprobamos si existe el archivo `web-0.txt`
```
kubectl exec -it web-0 -- ls /usr/share/nginx/html/test
```
y comprobamos en otro pod:
```
kubectl exec -it web-0 -- ls /usr/share/nginx/html/test
```

### 2. Aumentando el escalado (Scaling Up)

Para aumentar el escalado:
```
kubectl scale sts web --replicas=5
```
y comprobamos como escala secuencialmente:
```
watch kubectl get pods
```

### 3. Disminuyendo el escalado (Scaling Down)

Para disminuir el escalado:
```
kubectl scale sts web --replicas=2
```
y comprobamos como escala secuencialmente:
```
watch kubectl get pods
```

### 4. Eliminación no en cascada

Ademas de la eliminación tradicional en cascada, también podemos eliminar el objeto principal manteniendo sus hijos. Probemos con el StatefulSet que hemos creado:
```
kubectl delete statefulset web --cascade=false
```
y comprobamos como no ha eliminado los pods:
```
watch kubectl get pods
```
eliminamos un solo pod y comprobamos de nuevo como se mantienen los demás y no se crean o reinician nuevos
```
kubectl delete pod web-0 && watch kubectl get pods
```
ya podemos eliminar los demás
```
kubectl delete pods -l app=nginx
```

---


Volver al [Ejercicio 05](../05%20ReplicaSets/README.md)

Continuar al [Ejercicio 07](../07%20Volumes/README.md)