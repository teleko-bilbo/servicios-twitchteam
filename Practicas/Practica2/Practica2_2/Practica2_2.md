# PRÁCTICA 2_2

## Metal as a Service (MaaS)
MaaS es una solución de administración de infraestructura física que convierte servidores físicos en recursos virtualizados fáciles de gestionar y usar. MaaS permite a los administradores del sistema y desarrolladore tratar servidores físicos de la misma manera que se tratan las MV en la nube, proporcionando una plataforma automatizada para la implementación y gestión de hardware. Este concepto introducido por Canonical nos permite tener una serie de beneficios al usarlo:

- **Eficiencia**: Automatiza tareas repetitivas y complejas de despliegue de hardware, ahorrando tiempo y reduciendo errores humanos.

- **Escalabilidad**: Facilita el escalado horizontal al permitir la rápida implementación de nuevos servidores físicos según sea necesario.

- **Flexibilidad**: Soporta múltiples sistemas operativos y configuraciones de hardware, adaptándores a diversas necesidades empresariales y de desarrollo.

- **Visibilidad y control**: Proporciona una vista unificada de todos los recursos físicos disponibles y su estado, mejorando la gestión y utilización de los activos de hardware.

### Instalación MaaS
En una de las máquinas virtuales hemos instalado MaaS. Para ello, hemos seguido el siguiente [*tutorial*](https://maas.io/docs/how-to-install-maas).

#### Paso 1: Instalación de MAAS desde paquetes
La versión que se ha utilizado ha sido la 3.4:

```bash
sudo apt-add-repository ppa:maas/3.4
sudo apt update
sudo apt-get -y install maas
```

Después de la instalación hemos realizado la configuración de producción.

#### Paso 2: Desabilitar ```systemd-timesyncd```
```bash
student@vbox:~$ sudo systemctl disable --now systemd-timesyncd
```

#### Paso 3: Instalación de PostgreSQL
```bash
sudo apt install -y postgresql
```
Y entonces nos saldrá:
```bash
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
postgresql is already the newest version (14+238).
postgresql set to manually installed.
0 upgraded, 0 newly installed, 0 to remove and 57 not upgraded.
```

#### Paso 4: Configuración de la base de datos y del usuario
Se ha configurado el usuario y la base de datos en PostgreSQL:
```bash
export DBUSER="user1"
export DBPASS="1234"
sudo -i -u postgres psql -c "CREATE USER \"$DBUSER\" WITH ENCRYPTED PASSWORD '$DBPASS'" 
export DBNAME="dbname"
sudo -i -u postgres createdb -O "$DBUSER" "$DBNAME"
```

#### Paso 5: Configuración del archivo ```pg_hba.conf```
Se ha editado el ficehro de configuración de PostgreSQL y se ha añadido la siguiente línea al final, para ello:
```bash
sudo nano pg_hba.conf
```

Y se ha modificado la última fila con lo siguiente: 
```bash
host    dbname    user1    0/0     md5
```

### Inicialización del MaaS
Para inicializar MaaS, hemos utilizado el siguiente comando (no nos dejaba utilizar el del tutorial):
```bash
sudo maas init
```

Durante la configuración de la autenticación:
- URL del servicio Canonical RBAC (dejamos en blanco porque no lo usamos)

- Ruta del archivo de agente de autenticación Candid (dejamos en blanco porque no lo usamos)

- Hemos creado el usuario administrador con los siguientes datos:
    - Username: ***admin***
    - Password: ***maas***
    - Email: ***admin@maas.com***

- Import SSH keys [] (lp:user-id or gh:user-id) (lo dejamos en blanco también)

Para entrar a la interfaz gráfica desde el navegador del ***equipo local***: http://localhost:5240/MAAS/r/intro

Para entrar a la interfaz gráfica desde el navegador de un ***equipo remoto***: http://direccionIP:5240/MAAS/r/intro

## Distribución de los equipos
Los equipos se han distribuido y organizado de la siguiente forma, donde cada equipo cuenta con VMs para diferentes roles, incluyendo clientes, nodos de Kubernetes y workers:

| ID       | Num. Red | Posición     | VMs                                      |  Dirección IP         |
|----------|----------|--------------|------------------------------------------|-----------------------|
|  U110246 | Z1       | Fila 1 – 6   | Client1                                  |  192.168.1.1          |
|  U110245 | Z2       | Fila 1 – 5   | Client2                                  |  192.168.1.2          |
|  U110244 | Z3       | Fila 1 – 4   | Client3                                  |  192.168.1.3          |
|  U110243 | Z4       | Fila 1 – 3   | MaaS, Juju                               |  192.168.1.10-11      |
|  U110242 | Z33      | Fila 1 – 2   | K8s_easyrsa, K8s_etcd, K8s_control_plane |  192.168.1.12-13-14   |
|  U110241 | Z31      | Fila 1 - 1   | VWorker1                                 |  192.168.1.21         |
|  U110253 | Z37      | Fila 3 – 1   | VWorker2                                 |  192.168.1.22         |
|  U110254 | Z39      | Fila 3 – 2   | VWorker3                                 |  192.168.1.23         |
|  U110255 | Z16      | Fila 3 – 3   | VWorker4                                 |  192.168.1.24         |
|  U110256 | Z15      | Fila 3 – 4   | VWorker5                                 |  192.168.1.25         |
|  U110257 | Z14      | Fila 3 – 5   | VWorker6                                 |  192.168.1.26         |
|  U110258 | Z13      | Fila 3 - 6   | VWorker7                                 |  192.168.1.27         |

Para configurar las direcciones IP de los clientes y del servidor MaaS en Kubuntu hay que ir a Network / Connections y seleccionar la Wired Ethernet a configurar:
- Cambiar el ***method*** a *manual*.
- Añadir los servidores DNS de la universidad: 
```
10.10.13.107
10.10.13.108
```
- Añadir address, netmask y gateway:
    - Address: 192.168.1.1
    - Netmask: 255.255.255.0
    - Gateway: 192.168.1.10

## Características de las VM
Cada VM tiene las siguientes configuraciones específicas de CPU, RAM y disco duro:

| VM                    | Num. CPU | RAM  | Disco |
|-----------------------|----------|------|-------|
| Client 1              |          |      |       |
| Client 2              |          |      |       |
| Client 3              |          |      |       |
| MaaS                  | 1        | 8 GB | 50 GB |
| Juju                  |          |      |       |
| K8s_easyrsa           | 1        | 4 GB | 30 GB |
| K8s_etcd              | 2        | 4 GB | 30 GB |
| K8s_control_plane     | 2        | 4 GB | 30 GB |
| VWorker1              | 2        | 8 GB | 30 GB |
| VWorker2              | 2        | 8 GB | 30 GB |
| VWorker3              | 2        | 8 GB | 30 GB |
| VWorker4              | 2        | 8 GB | 30 GB |
| VWorker5              | 2        | 8 GB | 30 GB |
| VWorker6              | 2        | 8 GB | 30 GB |
| VWorker7              | 2        | 8 GB | 50 GB |

## Prueba de conectividad entre los equipos
Hemos realizado una prueba de conectividad previa entre los equipos de nuestra propia red. Para esta primera prueba, todavía no se han configurado las direcciones IP de la tabla anterior. Estas direcciones IP han sido obtenidas de forma dinámica con el protocolo DHCP. Para esta prueba de conectividad se realizado un ping entre los distintos equipos para verificar la conectividad entre todos ellos:
| Equipo host | Dirección IP       |
|-------------|--------------------|
| Client1     | 169.254.20.178     |
| Client2     | 169.254.91.148     |
| Client3     | 169.254.29.200     |
| MaaS        | 169.254.91.29      |
| Kubernetes  | 169.254.230.138    |
| VWorker1    | 169.254.216.76     |
| VWorker2    | 169.254.           |
| VWorker3    | 169.254.           |
| VWorker4    | 169.254.           |
| VWorker5    | 169.254.           |
| VWorker6    | 169.254.           |
| VWorker7    | 169.254.           |
