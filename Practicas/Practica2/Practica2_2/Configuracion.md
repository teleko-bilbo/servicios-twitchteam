## Distribución de los equipos:
Los equipos se han distribuido y organizado de la siguiente forma, donde cada equipo cuenta con VMs para diferentes roles, incluyendo clientes, nodos de Kubernetes y workers:

| ID       | Num. Red | Posición     | VMs                                      |  Dirección IP  |
|----------|----------|--------------|------------------------------------------|----------------|
|  U110246 | Z1       | Fila 1 – 6   | Client1                                  |  192.168.1.1  |
|  U110245 | Z2       | Fila 1 – 5   | Client2                                  |  192.168.1.2  |
|  U110244 | Z3       | Fila 1 – 4   | Client3                                  |  192.168.1.3  |
|  U110243 | Z4       | Fila 1 – 3   | MaaS, Juju                               |  192.168.1.10  |
|  U110242 | Z33      | Fila 1 – 2   | k8s_easyrsa, k8s_etcd, k8s_control_plane |  192.168.1.21  |
|  U110241 | Z31      | Fila 1 - 1   | VWorker1                                 |  192.168.1.13  |
|  U110253 | Z37      | Fila 3 – 1   | VWorker2                                 |  192.168.1.11  |
|  U110254 | Z39      | Fila 3 – 2   | VWorker3                                 |  192.168.1.14  |
|  U110255 | Z16      | Fila 3 – 3   | VWorker4                                 |  192.168.1.16  |
|  U110256 | Z15      | Fila 3 – 4   | VWorker5                                 |  192.168.1.18  |
|  U110257 | Z14      | Fila 3 – 5   | VWorker6                                 |  192.168.1.22  |
|  U110258 | Z13      | Fila 3 - 6   | VWorker7                                 |  192.168.1.19  |

## Características de las VM:
Cada VM tiene las siguientes configuraciones específicas de CPU, RAM y disco duro:

| VM                    | Num. CPU | RAM  | Disco |
|-----------------------|----------|------|-------|
| Client 1              |          |      |       |
| Client 2              |          |      |       |
| Client 3              |          |      |       |
| MaaS                  | 1        | 8 GB | 50 GB |
| Juju                  |          |      |       |
| k8s_easyrsa           | 1        | 4 GB | 30 GB |
| k8s_etcd              | 2        | 4 GB | 30 GB |
| k8s_control_plane     | 2        | 4 GB | 30 GB |
| VWorker1              | 2        | 8 GB | 30 GB |
| VWorker2              | 2        | 8 GB | 30 GB |
| VWorker3              | 2        | 8 GB | 30 GB |
| VWorker4              | 2        | 8 GB | 30 GB |
| VWorker5              | 2        | 8 GB | 30 GB |
| VWorker6              | 2        | 8 GB | 30 GB |
| VWorker7              | 2        | 8 GB | 50 GB |

