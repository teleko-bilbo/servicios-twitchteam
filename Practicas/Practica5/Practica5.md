# PRÁCTICA 5

## APARTADO 1: LoadBalancer
Un LoadBalancer (balanceador de carga) es un componente esencial en entornos de red que distribuye el tráfico de red entre varios servidores o servicios. Su objetivo principal es garantizar la alta disponibilidad y optimizar el uso de recursos al equilibrar la carga de trabajo.

En Kubernetes el tipo LoadBalancer asigna una IP a un balanceador de carga externo, es decir, no viene inlcuido en Kubernetes y lo proporciona el proveedor de nube. 

### Instalar MetalLB
MetalLB es una implementación de LoadBalancer para clústeres de Kubernetes en entornos donde no se dispone de un balanceador de carga nativo, como en instalaciones on-premise o en máquinas virtuales locales. MetalLB proporciona soporte para los tipos de servicios `LoadBalancer`, permitiendo asignar direcciones IP externas a servicios dentro del clúster.

En entornos locales (bare-metal), Kubernetes no ofrece un LoadBalancer nativo como en proveedores cloud. MetalLB resuelve este problema al proporcionar un balanceador de carga configurable que puede integrarse en estos entornos.

Para instalar MetalLB, utilizamos Helm, un gestor de paquetes para Kubernetes que simplifica la instalación y gestión de aplicaciones en el clúster.
> [!NOTE]
> La instalación de Helm se hizó en la Práctica 3

 Los comandos ejecutados son los siguientes:

```bash
helm repo add bitami https://charts.bitnami.com/bitnami
helm repo update
helm install -n metallb metallb bitnami/detallb --create-namespace
```
#### Explicación de los comandos:
- **`helm repo add`**: Añade el repositorio de charts de Bitnami.
- **`helm repo update`**: Actualiza la lista de charts disponibles en el repositorio.
- **`helm install`**: Instala MetalLB en el espacio de nombres `metallb`, creando el namespace si no existe.

En esta imagen se aprecían los elementos que se han desplegado con el nameservice 'metallb':  
![elementos metallb](Images/image1.png)


#### Configuración de MetalLB
Una vez instalado MetalLB, es necesario configurarlo para asignar rangos de direcciones IP disponibles para los servicios tipo LoadBalancer. Esto se hace mediante un archivo de configuración YAML. El archivo `metallb.yaml` contiene la siguiente configuración:

```yaml
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: metallb-name
  namespace: metallb
spec:
  ipAddressPools:
  - metallbpool
---
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: metallbpool
  namespace: metallb
spec:
  addresses:
  - 192.168.1.50-192.168.1.60
```
#### Características importantes del archivo de configuración:
- **Namespace**: El namespace especificado es `metallb`.
- **L2Advertisement**: Define cómo se anunciarán las direcciones IP disponibles.
- **IPAddressPool**: Especifica el rango de direcciones IP (en este caso, de `192.168.1.50` a `192.168.1.60`) que se asignarán a los servicios tipo LoadBalancer. 
> [!NOTE]  
> Este rango de direcciones está reservado desde el controlador MaaS, para que no se puedan asignar dinámicamente.


### Cambiar el servicio kuard de la práctica anterior a tipo LoadBalancer
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
