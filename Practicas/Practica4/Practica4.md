# PRÁCTICA 4

## APARTADO 1

## APARTADO 2

## APARTADO 3

## Comandos

### Cliente Kubectl
- **Ayuda:**
```bash
kubectl help
kubectl help <comando>
kubectl <comando> --help
kubectl options
```

- **Autocompletado:**
```bash
source <(kubectl completion bash)
echo "source <(kubectl completion bash)" >> ${HOME}/.bashrc
```

### Espacios de nombres (Namespaces)

- **Formato de comandos:**
```bash
kubectl <verbo> <tipo> <nombre>
```

- **Crear un namespace propio:**
```bash
kubectl create namespace <nombre>
```

- **Indicar el namespace:**
```bash
kubectl -n <nombre>
```

- **Seleccionar todos los namespaces:**
```bash
kubectl -all-namespaces
kubectl -A
```

- **Configurar un namespace permanente (usando contexto):**

Para este caso, en vez de usar comandos, es preferible editar la configuración directamente.

- **Indicar en el `metadata` del YAML:**
```bash
metadata:
    namespace: <nombre>
```

### Operaciones

- **Consulta:**
```bash
kubectl get <resource-name> <obj-name>
kubectl describe <resource-name> <obj-name>
```

- **Modificación:** (DECLARATIVA)
```bash
kubectl apply -f myobj.yaml
```

### Etiquetas (Labels)

- **CLI:** (IMPERATIVA)
```bash
kubectl label pods <nombre> color=red
kubectl label pods bar color-
kubectl run ... --labels="ver=1,app=alpaca,env=prod"
```

- **YAML:** (DECLARATIVA)
```bash
metadata:
    labels:
        app: kuard
        version: "2"
```

- **Consultas:**
```bash
kubectl get ... --show-labels   # Muestra todas
kubectl get ... -L canary       # Muestra solo una
```

- **Selectores desde CLI:** (IMPERATIVA)
```bash
kubectl get pods --selector="app=bandicoot,ver=2" # AND
kubectl get pods --selector="app in (alpaca,bandicoot)" # OR
# Para negar se usa: !, notin
```

- **Selectores desde YAML:** (DECLARATIVA)
```bash
# Forma tradicional
selector:
    app: alpaca
    ver: 1
```

```bash
# Forma nueva (permite expresiones más complejas)
selector:
    matchLabels:
        app: alpaca
        matchExpressions:
            - {key: ver, operator: In, values: [1, 2]}
```

### Workloads
Un Worload es una aplicación en ejecución en Kubernetes, que permite gestionar un conjunto de pods y hay diferentes tipos:

#### 1. Pod
Un pod es un grupo de contenedores que comparten recursos y se ejecutan en la misma máquina o nodo. Es la unidad mínima de despliegue en Kubernetes.

Para desplegar un pod:
- Solicitud al **API Server**, este registra la configuración del recurso en `etcd` y el **scheduler** revisa el estado actual y decide en qué nodo del clúster debe ejecutarse el recurso.

- El **scheduler** decide el nodo según los recursos requeridos y estado del nodo.

- El **pod** se ejecuta sin moverse del nodo (salvo que se use un ReplicatSet).

Ejemplo de YAML:
```bash
apiVersion: v1                  # Versión de la API para el recurso Pod
kind: Pod                       # Tipo de recurso: Pod
metadata:                       # Metadatos del Pod
    name: kuard                 # Nombre del Pod
spec:                           # Especificación del Pod
    containers:                 # Lista de contenedores dentro del Pod
        - image: gcr.io/kuar-demo/kuard-amd64:blue # Imagen del contenedor
        name: kuard             # Nombre del contenedor
        ports:                  # Lista de puertos expuestos por el contenedor
            - containerPort: 8080 # Puerto expuesto por el contenedor dentro del Pod
            protocol: TCP       # Protocolo del puerto, en este caso TCP
```

- **Desplegar el pod:**
```bash
kubectl apply -f kuard-pod.yaml
```

- **Consulta:**
```bash
kubectl get pods
kubectl describe pods kuard
kubectl logs kuard
kubectl logs -f kuard
```

- **Eliminación:**
```bash
kubectl delete pods/kuard
kubectl delete -f kuard-pod.yaml
```

- **Utilización:**
```bash
kubectl port-forward kuard 8080:8080
kubectl exec -it kuard -- /bin/sh
kubectl cp kuard:/captures/capture3.txt ./capture3.txt
```

#### 2. ReplicaSet
Actúa como un gestor de pods a nivel de cluster: se encarga de que haya siempre un determinado número de réplicas de pod corriendo.
Desacoplamiento:
    - No es dueño de los pods que gestiona, simplemente se fija en sus etiquetas
    - Las podemos incluso asignar (o quitar) a posteriori a un pod que ya este corriendo

Ejemplo de YAML:
```bash
apiVersion: apps/v1   # Versión de la API para el recurso ReplicaSet
kind: ReplicaSet      # Tipo de recurso: ReplicaSet
metadata:             # Metadatos del ReplicaSet
  name: kuard         # Nombre del ReplicaSet
spec:                 # Especificación del ReplicaSet
  replicas: 3         # Número deseado de réplicas (pods)
  selector:           # Selector para gestionar los pods de este ReplicaSet
    matchLabels:      # Define etiquetas que los pods deben tener para ser gestionados por este Replicaset 
      app: kuard      # Etiqueta que deben tener los pods para ser gestionados por este ReplicaSet
  template:           # Template del pod, describe cómo serán los pods del ReplicaSet
    metadata:         # Metadatos del pod template
      labels:         # Etiquetas aplicadas a los pods creados por el ReplicaSet
        app: kuard    # Etiqueta utilizada para identificar los pods
    spec:             # Especificación del pod
      containers:     # Lista de contenedores dentro del pod
        - image: gcr.io/kuar-demo/kuard-amd64:blue # Imagen del contenedor
          name: kuard # Nombre del contenedor
          ports:      # Lista de puertos expuestos por el contenedor
            - containerPort: 8080 # Puerto expuesto por el contenedor dentro del pod
              protocol: TCP       # Protocolo del puerto, en este caso TCP
```
**CLI:** (IMPERATIVA)
- **Creación y consulta**
```bash
kubectl apply -f kuard-rs.yaml
kubectl get pods
kubectl describe rs kuard
```
- **Escalado manual**
```bash
kubectl scale replicasets kuard --replicas=4
```
- **Eliminación**
```bash
kubectl delete rs kuard
kubectl delete rs kuard --cascade=false
```

#### 3. Deployment
Pods y Replicasets trabajan sobre una imagen del contenedor que no cambiar. Los Deployment permiten gestionar cambios de versión de la aplicación. Permiten hacer un rollout gradual (desplegar gradualmente) sin detener el servicio y sin errores. 
De la misma manera que los Replicasets gestionan Pods, los Deployment gestionan Replicasets. Desacoplamiento mediante etiquetas.

Ejemplo de YAML (Es igual que el de replicasets, cambiando el "kind"):
```bash
apiVersion: apps/v1   # Versión de la API para el recurso Deployment
kind: Deployment      # Tipo de recurso: Deployment
metadata:             # Metadatos del Deployment
  name: kuard         # Nombre del Deployment
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
          name: kuard # Nombre del contenedor
          ports:      # Lista de puertos expuestos por el contenedor
            - containerPort: 8080 # Puerto expuesto por el contenedor dentro del pod
              protocol: TCP       # Protocolo del puerto, en este caso TCP
```
- **Rollout**
Cambio de la imagen del contenedor en el YAML y luego:
```bash
kubectl apply -f
```
Puedes indicar la descri`ción del cambio mediante una **anotación** en el YAML (Las anotaciones son como labels pero indicando en el change-cause y lo puedes consultar en el historial)
```bash
# Dentro de spec: template: metadata: annotations:
kubernetes.io/change-cause: "Update to green kuard"
```
Se puede ver el historial del rollouts y colver a una revisión pasada:
```bash
spec:
  revisionHistoryLimit: 10 # Podemos limitar el historial
```
Estrategia RollingUpdate
```bash
# Dentro de spec: strategy:
type: RollingUpdate
rollingUpdate:
  maxUnavailable: 1 # Pods (tb %) no disponibles durante rollout
  maxSurge: 1       # Pods (tb %) extra durante rollout
```
**CLI:** (IMPERATIVA)
- **Creación y consulta**
```bash
kubectl apply -f kuard-rs.yaml
kubectl get deployments
kubectl get all
kubectl describe deployments kuard
```
- **Escalado manual**
```bash
kubectl scale replicasets kuard-d94fcfd7 --replicas=2 # ¿Qué ocurre?
kubectl scale deployments kuard --replicas=2 # ¿Qué ocurre?
```
- **Rollout**
```bash
kubectl rollout status deployments kuard
kubectl rollout pause deployments kuard
kubectl rollout resume deployments kuard
kubectl rollout history deployment kuard
kubectl rollout history deployment kuard --revision=2
kubectl rollout undo deployments kuard
kubectl rollout undo deployments kuard --to-revision=3
```
- **Eliminación**
```bash
kubectl delete deployments kuard
kubectl delete deployments kuard --cascade=false
```

#### 4. Servicios
- Descubimiento de servicios:
Encontrar qué procesos están escuchando en que direcciones para qué servicios.
- Objeto Service de Kubernetes
Utiliza **selector** para identificar los pods a los que redrigir el tráfico
Emplea una IP fija (**Cluster IP**: que es una IP interna "no es la real del equipo") y un nombre **DNS** estables:
    - Se puede acceder al servicio aunque la IP de los pods cambie
    - Únicamente accesibles desde dentro del cluster (el cluster IP hará el balanceo)
#DNS automático: kuard.dyd.svc.cluster.local
    -kuard: nombre del servicio
    -dyd: espacio de nombres
    -svc: servicio (podría exponer otro tipo de objetos)
    -cluster.local: configurar si conectar varios clusters
Ejemplo de YAML:
```bash
apiVersion: v1       # Versión de la API
kind: Service        # Tipo de objeto
metadata:            # Metadatos del objeto
  name: kuard        # Nombre del servicio
spec:                # Especificaciones del servicio
  ports:             # Puertos del servicio
  - port: 8080       # Puerto expuesto
    targetPort: 8080 # Puerto al que se redirige
    protocol: TCP    # Protocolo utilizado
  selector:          # Selección de pods
    app: kuard       # Etiqueta para seleccionar los pods
  type: ClusterIP    # Tipo de servicio
```
**CLI:** (IMPERATIVA)
- **Creación**
```bash
kubectl apply -f kuard-service.yaml   # Declarativa
kubectl expose deployment kuard       # Imperativa
```
- **Consulta**
```bash
kubectl get all
kubectl get deployments
kubectl describe deployment kuard
kubectl get endpoints kuard
kubectl describe endpoints kuard
kubectl get endpoints kuard --watch   # Ver cambios en tiempo real
```
- **Acceso**
```bash
kubectl port-forward pod/kuard-598d54d9c6-jsxtd 1234:8080 # Sin balanceo
ssh -N -L 1234:10.152.183.102:8080 ubuntu@192.168.13.30   # Balanceo por ClusterIP
```
- **Eliminación**
```bash
kubectl delete service kuard
kubectl delete services,deployments -l app
```
#NodePort
-Se configura un puerto (elegido automático/manual) en cada nodo para poder exponer el servicio al exterior. 
-kube-proxy intercepta las peticiones a ese puerto y las redirige al servicio
-Podemos hacer un port-forward para así acceder desde el exterior al servicio a través de un nodo específico
-Si el nodo cae perdemos la conexión al servicio

```bash
# Cambiar de ClusterIP a NodePort en spec: type:
kubectl edit service kuard

# Consultamos puerto asignado
kubectl describe service kuard

# Acceso a través del nuevo puerto en un nodo cualquiera
# http://192.168.13.26:32060
```
#### 4. StatefulSet

#### 5. DaemonSet

#### 6. Job

#### 7. CronJob
