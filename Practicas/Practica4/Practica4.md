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

#### 3. Deployment

#### 4. StatefulSet

#### 5. DaemonSet

#### 6. Job

#### 7. CronJob