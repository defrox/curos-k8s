## Configurar proxy en la VM
**NOTA:** No configurar el proxy, por el momento no lo ha funcionado.

Para establecer la configuración del proxy de la red orange en la VM, hay que editar el archivo **~/.profile** y añadir al final
```
export {HTTP,HTTPS}_PROXY=http://proxydsi:8080/
export {http,https}_proxy=http://proxydsi:8080/
export {NO_PROXY,no_proxy}=localhost,127.0.0.1,::1
```
También tenemos que configurar el proxy en la configuración de docker, para ello crear el archivo de configuración y añadir la configuración del proxy:
```
sudo mkdir -p /etc/systemd/system/docker.service.d && \
sudo touch /etc/systemd/system/docker.service.d/http-proxy.conf && \
echo "[Service]" | sudo tee -a /etc/systemd/system/docker.service.d/http-proxy.conf
echo "Environment=\"HTTP_PROXY=http://proxydsi:8080/\" \"HTTPS_PROXY=http://proxydsi:8080/\" \"NO_PROXY=localhost,127.0.0.1,::1\"" | sudo tee -a /etc/systemd/system/docker.service.d/http-proxy.conf
```
reiniciar los servicios:
```
sudo systemctl daemon-reload && sudo systemctl restart docker
```
comprobar los cambios:
```
systemctl show --property=Environment docker
```
si ejecutamos `docker pull hello-world` se debería descargar correctamente.

Para que minikube se quede configurado con el proxy, debemos arrancar por lo menos una vez minikube con la siguiente configuracion:
```
sudo minikube start --vm-driver=none \
     --docker-env HTTP_PROXY=http://10.38.32.9:10808/ \
     --docker-env HTTPS_PROXY=http://10.38.32.9:10808/ \
     --docker-env NO_PROXY=index.docker.io,\
 registry.hub.docker.com,\
 registry-1.docker.io,\
 registry.docker-cn.com,\
 registry-mirror-cache-cn.oss-cn-shanghai.aliyuncs.com,\
 192.168.99.100\
     --registry-mirror https://registry.docker-cn.com
```
De esta manera, minikube se quedará configurado y solo necesitaremos ejecutar el comando `minikube start` para arrancar minikube con el proxy.



https://blog.alexellis.io/minikube-behind-proxy/

https://codefarm.me/2018/08/09/http-proxy-docker-minikube/


sudo mkdir -p /etc/systemd/system/docker.service.d && \
sudo touch /etc/systemd/system/docker.service.d/http-proxy.conf && \
sudo echo "[Service]" > /etc/systemd/system/docker.service.d/http-proxy.conf
sudo echo "Environment=\"HTTP_PROXY=http://proxydsi:8080/\" \"HTTPS_PROXY=http://proxydsi:8080/\" \"NO_PROXY=localhost,127.0.0.1,::1\"" > /etc/systemd/system/docker.service.d/http-proxy.conf

sudo mkdir -p /etc/systemd/system/docker.service.d && \
sudo touch /etc/systemd/system/docker.service.d/http-proxy.conf && \
echo "[Service]" | sudo tee -a /etc/systemd/system/docker.service.d/http-proxy.conf
echo "Environment=\"HTTP_PROXY=http://proxydsi:8080/\" \"HTTPS_PROXY=http://proxydsi:8080/\" \"NO_PROXY=localhost,127.0.0.1,::1\"" | sudo tee -a /etc/systemd/system/docker.service.d/http-proxy.conf


sudo minikube start --vm-driver=none \
     --docker-env HTTP_PROXY=http://10.38.32.9:10808/ \
     --docker-env HTTPS_PROXY=http://10.38.32.9:10808/ \
     --docker-env NO_PROXY=index.docker.io,\
 registry.hub.docker.com,\
 registry-1.docker.io,\
 registry.docker-cn.com,\
 registry-mirror-cache-cn.oss-cn-shanghai.aliyuncs.com,\
 192.168.99.100\
     --registry-mirror https://registry.docker-cn.com

sudo minikube start --vm-driver=none \
     --docker-env HTTP_PROXY=http://proxydsi:8080/ \
     --docker-env http_proxy=http://proxydsi:8080/ \
     --docker-env HTTPS_PROXY=http://proxydsi:8080/ \
     --docker-env https_proxy=http://proxydsi:8080/ \
     --docker-env NO_PROXY=192.168.99.0/24 \
     --docker-env no_proxy=192.168.99.0/24
