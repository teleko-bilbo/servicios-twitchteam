# PRÁCTICA 3
## JUJU
### Instalar cliente de Juju

1. Instalar el cliente Juju:
```bash
sudo snap install juju --classic
juju help
juju help commands
```
### Dar de alta la nube MaaS en Juju
2. Registrar la nube MaaS:
```bash
juju add-cloud maas-cloud maas-cloud.yaml
juju add-credential maas-cloud
juju clouds
juju credentials
```
En este apartado tuvimos que cambiar el yaml y poner nuestro IP. Y una vez que añadimos el cloud con el yaml, hay que añadir las credenciales. Cada cliente le pondrá su nombre y después con el juju credentials podrás verlo.

### Crear controlador
3. Crear el controlador:
```bash
juju bootstrap maas-cloud --bootstrap-constraints "tags=juju"
```

4. Añadir un modelo a la nube:
```bash
juju add-model my-model
```
### Solicitar máquinas al MaaS (estado ready) y despegar Ubuntu 22.04
5. Añadir una máquina a juju desde la nube de MaaS:
```bash
juju add-machine
```
### Conectarse por SSH a las maquinas de Juju
Tuvimos que exportar la id_rsa a nuestras maquinas clientes para después poder conectarnos por SSH
## APARTADO 2

## APARTADO 3

