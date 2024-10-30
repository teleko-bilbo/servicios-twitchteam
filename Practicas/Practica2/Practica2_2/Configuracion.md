## Distribución de los equipos
| ID       | Num. Red | Posición     | VMs                            | Dirección IP |
|----------|----------|--------------|--------------------------------|--------------|
| U110243  | Z4       | Fila 1 – 3   | MaaS, Juju                     |              |
| U110242  | Z33      | Fila 1 – 2   | easyrsa, etcd, kubeapi_load_balancer, kubernetes_control_plane |              |
| U110241  | Z31      | Fila 1 - 1   | VWorker1                       |              |
| U110253  | Z37      | Fila 3 – 1   | VWorker2                       |              |
| U110254  | Z39      | Fila 3 – 2   | VWorker3                       |              |
| U110255  | Z16      | Fila 3 – 3   | VWorker4                       |              |
| U110256  | Z15      | Fila 3 – 4   | VWorker5                       |              |
| U110257  | Z14      | Fila 3 – 5   | VWorker6                       |              |
| U110258  | Z13      | Fila 3 - 6   | VWorker7                       |              |
| U110246  | Z1       | Fila 1 – 6   | Client 1                       |              |
| U110245  | Z2       | Fila 1 – 5   | Client 2                       |              |
| U110244  | Z3       | Fila 1 – 4   | Client 3                       |              |


## Caracteristicas de los VM

| VM                         | Num. CPU | RAM  | Disco |
|----------------------------|----------|------|-------|
| MaaS                       | 1        | 8 GB |  50GB |
| Juju                       |          |      |       |
| easyrsa                    | 1        | 4 GB | 30 GB |
| etcd                       | 2        | 4 GB | 30 GB |
| kubeapi_load_balancer      | 1        | 4 GB | 30 GB |
| kubernetes_control_plane   | 2        | 4 GB | 30 GB |
| VWorker1                   | 2        | 8 GB | 30 GB |
| VWorker2                   | 2        | 8 GB | 30 GB |
| VWorker3                   | 2        | 8 GB | 30 GB |
| VWorker4                   | 2        | 8 GB | 30 GB |
| VWorker5                   | 2        | 8 GB | 30 GB |
| VWorker6                   | 2        | 8 GB | 30 GB |
| VWorker7                   | 2        | 8 GB | 50 GB |
| Client 1                   |          |      |       |
| Client 2                   |          |      |       |
| Client 3                   |          |      |       |
