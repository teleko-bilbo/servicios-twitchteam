# PRÁCTICA 3
## JUJU
Juju es una herramienta de código abierto desarrollada por Canonical, la misma empresa detrás de Ubuntu. Facilita la implementación, gestión y escalado de aplicaciones en la nube o en entornos locales mediante un modelo de administración basado en aplicaciones. Esto resulta útil en nuestro caso para automatizar tareas y orquestar servicios. A continuación, describimos los pasos que seguimos para utilizar Juju:

### 1. Instalar cliente de Juju
1. Instalar el cliente Juju:
```bash
sudo snap install juju --classic
juju help
juju help commands
```
El comando juju help proporciona información básica, mientras que juju help commands detalla las funcionalidades disponibles.
### 2. Dar de alta la nube MaaS en Juju
2. Registrar la nube MaaS:
```bash
juju add-cloud maas-cloud maas-cloud.yaml
juju add-credential maas-cloud
juju clouds
juju credentials
```
En este apartado tuvimos que cambiar el yaml y poner nuestro IP. Y una vez que añadimos el cloud con el yaml, hay que añadir las credenciales. Cada cliente le pondrá su nombre y después con el juju credentials podrás verlo.

### 3. Crear controlador
3. Crear el controlador:
```bash
juju bootstrap maas-cloud --bootstrap-constraints "tags=juju"
```

4. Añadir un modelo a la nube:
```bash
juju add-model my-model
```
### 4. Solicitar máquinas al MaaS (estado ready) y despegar Ubuntu 22.04
5. Añadir una máquina a juju desde la nube de MaaS:
```bash
juju add-machine
```
### 5. Conectarse por SSH a las maquinas de Juju
Para conectarnos por SSH, exportamos nuestra clave privada `id_rsa` a las máquinas clientes. Esto permite el acceso remoto seguro a las máquinas gestionadas por Juju.

### 6. Liberar máquinas al MaaS 
### 7.Desplegar dashboard
### Conclusión
El uso de Juju simplifica considerablemente la gestión de infraestructuras, permitiendo automatizar tareas complejas. Su integración con MaaS nos ha permitido desplegar y gestionar máquinas fácilmente.
## APARTADO 3

