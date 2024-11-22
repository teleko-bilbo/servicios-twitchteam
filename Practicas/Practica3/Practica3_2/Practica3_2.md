# PRÁCTICA 3_2

El objetivo de esta práctica es instalar un clúster Kubernetes production-grade con Juju, gestionar aplicaciones con Helm y configurar un sistema de almacenamiento distribuido con Longhorn.

## Kubernetes
### Charmed Kubernetes
Instalación sencilla de un cluster Kubernetes production-grade con Juju:
1. Crear un modelo en Juju para Kubernetes:
```bash
juju add-model k8s maas-cloud
```

2. Desplegar Charmed Kubernetes usando un fichero de configuración (`k8s-overlay.yaml`):
```bash
juju deploy charmed-kubernetes --overlay k8s-overlay.yaml
```

### Escalado horizontal
Para aumentar o reducir la capacidad del clúster:
- Añadir un nuevo nodo:
```bash
juju add-unit kubernetes-worker
```

- Añadir un nodo en una máquina específica:
```bash
juju add-machine
juju add-unit kubernetes-worker --to <machine-id>
```

- Eliminar un nodo existente:
```bash
juju remove-unit kubernetes-worker/<unit-id>
```

### Configuración de kubectl
Configurar el acceso al clúster con kubectl:
- Obtener el fichero de configuración:
```bash
mkdir ~/.kube
juju ssh kubernetes-control-plane/1 -- cat config > ~/.kube/config
```

- Instalar ***kubectl*** y verificar la versión:
```bash
sudo snap install kubectl --classic
kubectl version
```

### Acesso al dashboard de Kubernetes
Habilitar el acceso al dashboard web:
```bash
kubectl port-forward service/kubernetes-dashboard -n kubernetes-dashboard 8443:443
```

Acceder desde un navegador: https://localhost:8443.

### Añadir Kubernetes como cloud en Juju
Configurar Kubernetes como un cloud para gestionar despliegues:
```bash
juju add-k8s k8s-cloud
juju bootstrap k8s-cloud
```

## Helm
### Repositorios de charts
Helm es un gestor de paquetes (charts) para Kubernetes.

- Añadir un repositorio:
```bash
helm repo add <name> <url>
helm repo update
```

- Buscar charts en un repositorio:
```bash
helm search repo <name>  # vs helm search hub
```

### Gestión de releases
Un *chart* puede instalarse múltiples veces como una *release*:
- Instalar una release:
```bash
helm install <release-name> <repo/chart>
```

O generar un nombre automáticamente:
```bash
helm install <repo/chart> --generate-name
```
- Consultar estado de una release:
```bash
helm status <release-name>
helm list
```

- Desinstalar una release:
```bash
helm uninstall <release-name>
```

### Ayuda
Consultar comandos y opciones de Helm:
```bash
helm help 
helm <comando> -h
```

## Longhorn
### Recomendaciones HW:
- 3 nodos

- 4 vCPUs y 4 GiB RAM por nodo

- Almacenamineto SSD

### Instalación con Helm
1. Añadir el repositorio de Longhorn:
```bash
helm repo add longhorn https://charts.longhorn.io
helm repo update
```

2. Instalar Longhorn:
```bash
helm install longhorn longhorn/longhorn --namespace longhorn-system --create-namespace --version 1.6.2
```

3. Acceder a la interfaz de Longhorn:
```bash
kubectl port-forward service/longhorn-frontend -n longhorn-system 8001:80
```

Acceder desde un navegador: http://localhost:8001.

## Problemas y pruebas realizadas
Durante la práctica, hemos tenido problemas a la hora de arrancar el Juju controller, ya que al iniciar la MV de este, el MaaS controller le asignaba una dirección IP dentro del rango de direcciones configurado (192.168.1.3, concretamente), pero luego en el paso de TFTP el Juju controller realizaba un TFTP Request al MaaS controller, pero este último no respondía, provocando un error ***PXE-E32: TFTP open timeout***.

Para ver de donde podía venir el fallo, hemos revisado lo siguiente:

1. **Configuración del NAT:**

Hemos comprobado que la configuración del NAT estuviese bien configurada. 

- Hemos asegurado que la línea `net.ipv4.ip_forward=1` en el fichero `/etc/sysctl.conf` estuviera descomentada.

- Hemos aplicado el comando `sudo iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE` (recordando que esta configuración no es persistente tras un reinicio).

- Hemos probado la conectividad desde distintos equipos de la red, verificando la comunicación tanto entre máquinas como acceso a Internet.

2. **Estado del firewall:**

Hemos comprobado que el firewall estuviese desactivado. Para ello, primero hemos comprobado si el firewall estaba desactivado con (***sudo ufw status***) y en caso de no estar desactivado, hay que ejecutar (***sudo ufw disable***). De esta forma, nos aseguramos que el firewall, esté desactivado.

3. **Configuración de los switches:**

Hemos probado en dos switches diferentes (***switch2*** y ***switch3***) para ver si el problema podía estar ahí y al cambiar de switch ya no saltaba el TFTP timeout, pero aún así no terminaba de cargar del todo el Juju controller. Hemos mirado también la configuración de los swicthes con el siguiente comando:

```bash
ssh switch2@u017633.ehu.es
Password: switch2-xx

ssh switch3@u017633.ehu.es
Password: switch3-xx
```

4. **Estado del MaaS Controller:**

Hemos verificado el estado del MaaS controller con el siguiente comando, para asegurarnos de que servicios como ***maas-regiond*** y ***maas-rackd*** estuvieran activos.
```bash
sudo maas status
```

Hemos probado también a reiniciarlos con los siguientes comandos:
```bash
sudo systemctl restart maas-regiond
sudo systemctl restart maas-rackd
```

De esta forma, no hemos conseguido solucionar nada.

5. **Prueba del servidor TFTP:**

También hemos probado el funcionamiento del servidor TFTP manualmente desde uno de los clientes:

- Hemos instlado el cliente TFTP (`sudo apt install tftp-hpa`) y hemos comprobado el estado del servicio con `sudo systemctl status tftpd-hpa`.

- Después, hemos probado a transferir un fichero básico (pxelinux.0) al cliente de la siguiente manera:
```bash
tftp 192.168.1.10
tftp> get pxelinux.0
```

De esta forma, nos ha saltado un error de "***pxelinux.0: Permission denied***". Esto se supone que es por temas de permisos del fichero `pxelinux.0`, por lo que habría que mirar que permisos tiene configurados.

Para ver los logs, hemos utilizado el siguiente comando:
```bash
sudo journalctl -u tftpd-hpa
```

6. **Problemas adicionales al desplegar un Worker:**

Al final, hemos probado a desplegar otro equipo diferente (un ***Worker***) y hemos tenido problemas, ya que no nos dejaba realizar el deploy ya tampoco. El MaaS controller mediante DHCP le asignaba un dirección IP (192.168.1.38) al Worker, pero luego esa dirección que tendría que aparecer en el dashboard del MaaS controller, no aparecía.