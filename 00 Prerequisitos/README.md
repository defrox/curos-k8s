# Instalación de requisitos

## Contraseña de usuario y root de la VM
`osboxes.org`

## Configuración del teclado en Español
`sudo setxkbmap -layout es`

## Configurar proxy en la VM
Para establecer la configuración del proxy de la red orange en la VM, hay que editar el archivo **/etc/environment** y añadir al final
```
export {HTTP,HTTPS}_PROXY=http://10.113.55.38:8080/
export {http,https}_proxy=http://10.113.55.38:8080/
export {NO_PROXY,no_proxy}=localhost,127.0.0.1,::1

export {HTTP,HTTPS}_PROXY=http://proxydsi:8080/
export {http,https}_proxy=http://proxydsi:8080/
export {NO_PROXY,no_proxy}=localhost,127.0.0.1,::1
```

## Instalar Virtual Box (Linux)
Para instalar VirtualBox en linux, ejecutar:

`sudo apt install virtualbox virtualbox-ext-pack virtualbox-qt`


## Instalar docker
https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce

Para comprobar si hemos instalado docker correctamente, ejecutar:

`docker version`

## Instalar kubectl
[**kubectl**](https://kubernetes.io/docs/reference/kubectl/overview) es la herramienta de línea de comandos de Kubernetes. Con ella podremos inspeccionar los recursos, componentes y objetos del clúster de kubernetes; crear, eliminar y editar componentes y objetos; visualizar el nuevo clúster y ejecutar comandos en los contenedores desplegados en el clúster.

https://kubernetes.io/docs/tasks/tools/install-kubectl/

**Nota:** si da error al ejecutar `apt update` ejecutar solo `sudo apt update` y aceptar la nueva clave.

Para comprobar si hemos instalado kubectl correctamente, ejecutar:

`kubectl version`



## Instalar Minikube

[**Minikube**](https://github.com/kubernetes/minikube/) es una herramienta que facilita la ejecución local de Kubernetes. Minikube ejecuta un clúster de Kubernetes de un solo nodo dentro de una máquina virtual en su ordenador para aquellos usuarios que buscan probar Kubernetes o realizar algún desarrollo sencillo.

https://github.com/kubernetes/minikube/#linux

Para comprobar si hemos instalado minikube correctamente, ejecutar:

`minikube version`
