# PRÁCTICA 5

## APARTADO 1: LoadBalancer
### a.Instalar MetalLB
```bash
helm repo add bitami https://charts.bitnami.com/bitnami
helm repo update
helm install -n metallb metallb bitnami/detallb --create-namespace
```
**imagen metallb
#### Configurar metallb
Hay que desplegar el metallb.yaml (subir al repo) con nuestras IP's reservadas.
- namespace metallb
- name: metallb-name
- IPs: 50-60
### b.Cambiar el servicio kuard de la práctica anterior a tipo LoadBalancer
Hemos desplegados los yml de Deployment y Serivce de la practica anterior. "deployment.yaml" y "service.yaml" (hay que subir al repo): --
Serivce.yaml:
- namespace streamflix
- name: streamflix-service
- serivce: LoadBalancer
**imagen LoadBalancer 
### c. Acceder al servicio mediante IP externa y comprobar que se balancea
** imagenes servicios LoadBalancer_Pod123

## APARTADO 2: Ingress
### a.Exponer un servico ingress-nginx de tipo LoadBalancer entre los pods ingress-nginx-kubernetes-worker
**imagen: get all -n nginx
Para crear el yaml hemos tenido que consultar las etiquetas que tenian estos pods con el comando: 
```bash
kubectl get pods -n ingress-nginx-kubernetes-worker -o wide --show-labels
```
** imagen: nginx labels
hemos construido el service-nginx.yaml (hay que subir al repo) 
** imagen: ningx expuesto
### b.Hacer un segundo Deployment y servicio similar al de la práctica anterior
deploy-nginx.yaml con nombre diferente
service2.yaml con nobmre diferente
### c.Dar de alta en el /etc/hosts del cliente dos nombres diferentes asociados a la IP del servicio ingress-nginx
hemos definido las siguientes entradas asociadas a la ip 192.168.1.51 en /etc/hosts/: "streamflix1.dyd.eus" "streamflix2.dyd.eus". 
**imagen etc/hosts
### d.Crear un Ingress que en función del nombre del host dirija la petición a uno u otro servicio
hay que poner en el ingress.yaml:
 - cambiamos nombbre
 - primer servicio, name: streamflix-service , host: streamflix1.dyd.eus", port: 80
 - segudno servicio, name: streamflix-service , host: streamflix1.dyd.eus", port: 80

poner los servicios tambien a puerto 80
### e.Conectarse desde el navegador web empleando los diferentes nombres y comprobar que responde el servicio asociado a ese nombre y que se balancea entre los pods del servicio

## APARTADO 3
