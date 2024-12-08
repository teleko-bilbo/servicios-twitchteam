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

Una vez cofigurado se ha desplegado el YAML: 
```bash
kubectl apply -f metallb.yaml
```

En esta imagen se aprecían los elementos que se han desplegado con el nameservice `metallb`:

![elementos metallb](images/image1.PNG)


### Cambiar el servicio kuard de la práctica anterior a tipo LoadBalancer
Para probar el funcionamiento del LoadBalancer, hemos reutilizado el despliegue (`deployment`) y el servicio (`service`) de la práctica anterior. Sin embargo, en esta práctica hemos modificado el tipo de servicio a `LoadBalancer` para aprovechar la funcionalidad proporcionada por MetalLB.


#### Archivo `deployment.yaml`:
```yaml
apiVersion: apps/v1   # Versión de la API para el recurso Deployment
kind: Deployment      # Tipo de recurso: Deployment
metadata:             # Metadatos del Deployment
  name: streamflix-deployment         # Nombre del Deployment
spec:                 # Especificación del Deployment
  replicas: 3         # Número deseado de réplicas (pods)
  selector:           # Selector para gestionar los pods de este Deployment
    matchLabels:      # Define etiquetas que los pods deben tener para ser gestionados por este Deployment
      app: kuard      # Etiqueta que deben tener los pods para ser gestionados por este Deployment
  template:           # Template del pod, describe cómo serán los pods del Deployment
    metadata:         # Metadatos del pod template
      labels:         # Etiquetas aplicadas a los pods creados por el Deployment
        app: kuard    # Etiqueta utilizada para identificar los pods
    spec:             # Especificación del pod
      containers:     # Lista de contenedores dentro del pod
        - image: gcr.io/kuar-demo/kuard-amd64:blue # Imagen del contenedor
          name: streamflix-container # Nombre del contenedor
          ports:      # Lista de puertos expuestos por el contenedor
            - containerPort: 8080 # Puerto expuesto por el contenedor dentro del pod
              protocol: TCP       # Protocolo del puerto, en este caso TCP  
```
#### Características destacables del `deployment.yaml`:
- **Nombre**: El Deployment se llama `streamflix-deployment`.
- **Réplicas**: Se crean 3 réplicas de los pods.
- **Selector**: Se asegura de que el Deployment administre únicamente los pods etiquetados con `app: kuard`.
- **Contenedor**: Usa la imagen `gcr.io/kuar-demo/kuard-amd64:blue` y expone el puerto interno `8080` con protocolo TCP.


#### Archivo `services.yaml`:
```yaml
apiVersion: v1       # Versión de la API
kind: Service        # Tipo de objeto
metadata:            # Metadatos del objeto
  name: streamflix-service        # Nombre del servicio
spec:                # Especificaciones del servicio
  ports:             # Puertos del servicio
  - port: 80      # Puerto expuesto
    targetPort: 8080 # Puerto al que se redirige
    protocol: TCP    # Protocolo utilizado
  selector:          # Selección de pods
    app: kuard       # Etiqueta para seleccionar los pods
  type: LoadBalancer    # Tipo de servicio
```
#### Características destacables del `services.yaml`:
- **type**: El servicio se ha configurado como `LoadBalancer`.
- **name**: El servicio se llama `streamflix-service`.
- **app**: Selecciona los pods que tienen la etiqueta `app: kuard`.
- **port**: Expone el puerto `80` para las conexiones externas.
- **targetPort**: Redirige las solicitudes al puerto interno `8080` del pod.

> [!NOTE]
> Al no indicar el `namespace`, tanto el Deployment como el Service se desplegarán en el namespace configurado por defecto, que en este caso es `streamflix`.

#### Comandos para desplegar:
Para desplegar los recursos, utilizamos los siguientes comandos:
```bash
kubectl apply -f deployment.yaml
kubectl apply -f services.yaml
```
![servicio LoadBalancer](images/image2.PNG)

En la imagen anterior, se observa cómo el servicio ha sido asignado al rango de IPs configurado en MetalLB. En este caso, la IP externa asignada es 192.168.1.50. Esto confirma que el servicio está listo para recibir tráfico externo.

> [!NOTE]
> A lo largo de la práctica, se han realizado actualizaciones en los archivos YAML para corregir algunas configuraciones. Como resultado, la configuración mostrada en los archivos y la imagen pueden diferir ligeramente. Un ejemplo de esto es el puerto en el que se expone el servicio.

### Acceder al servicio mediante IP externa y comprobar que se balancea

Para completar este apartado, se accede a la IP externa asignada al servicio. Cada vez que se realiza una conexión, el LoadBalancer distribuye la carga entre los tres pods desplegados. Los pods devuelven información detallada sobre su estado, incluyendo la dirección IP interna que tienen dentro de la red Kubernetes.

Al conectar repetidamente, se obtiene la información de los tres pods, lo que permite verificar que el LoadBalancer está funcionando correctamente. Cada conexión es redirigida a un pod diferente, confirmando que la carga se balancea correctamente entre ellos.

Esto se puede observar en las siguientes imágenes, donde se muestra la información de los tres pods:


![info POd1](images/image3.PNG)

![info POd2](images/image4.PNG)

![info POd3](images/image5.PNG)

De este modo, se comprueba que el LoadBalancer está equilibrando eficazmente el tráfico entre los pods, asegurando la alta disponibilidad del servicio.

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
