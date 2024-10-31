# PRÁCTICA 2_2

## MAAS
**Metal as a Service (MAAS)** es una solución de administración de infraestructura física que convierte servidores físicos en recursos virtualizados fáciles de gestionar y usar. MAAS permite a los administradores del sistema y desarrolladore tratar servidores físicos de la misma manera que se tratan las MV en la nube, proporcionando una plataforma automatizada para la implementación y gestión de hardware. Este concepto introducido por Canonical nos permite tener una serie de beneficios al usarlo:
- **Eficiencia**: Automatiza tareas repetitivas y complejas de despliegue de hardware, ahorrando tiempo y reduciendo errores humanos.
- **Escalabilidad**: Facilita el escalado horizontal al permitir la rápida implementación de nuevos servidores físicos según sea necesario.
- **Flexibilidad**: Soporta múltiples sistemas operativos y configuraciones de hardware, adaptándores a diversas necesidades empresariales y de desarrollo.
- **Visibilidad y control**: Proporciona una vista unificada de todos los recursos físicos disponibles y su estado, mejorando la gestión y utilización de los activos de hardware.

### Instalación MaaS
En la maquina virtual hemos instalado MaaS para ello hemos seguido el siguiente [tutorial](https://maas.io/docs/how-to-install-maas)

#### Paso 1: Instalación de MAAS desde paquetes
La vesión utilizada es la 3.4:

```bash
sudo apt-add-repository ppa:maas/3.4
sudo apt update
sudo apt-get -y install maas

### Configuración posterior a la instalación (Producción)

Después de la instalación hemos realizado la configuración de producción 
#### Paso 2: Desabilitar ```systemd-timesyncd```
```bash
student@vbox:~$ sudo systemctl disable --now systemd-timesyncd
```
#### Paso 3: Instalación de PostgreSQL
```bash
sudo apt install -y postgresql
```
Y entonces nos saldra:
```bash
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
postgresql is already the newest version (14+238).
postgresql set to manually installed.
0 upgraded, 0 newly installed, 0 to remove and 57 not upgraded.
```

#### Paso 4: Configuración de la base de datos y usuario
Configura el usuario y la base de datos en PostgreSQL
```bash
export DBUSER="user1"
export DBPASS="1234"
sudo -i -u postgres psql -c "CREATE USER \"$DBUSER\" WITH ENCRYPTED PASSWORD '$DBPASS'" 
export DBNAME="dbname"
sudo -i -u postgres createdb -O "$DBUSER" "$DBNAME"
```

#### Paso 5: Configuración del archivo ```pg_hba.conf```
He editado el archivo de configuración de PostgreSQL y he añadidola siguiente linea al final, para ello:
```bash
sudo nano pg_hba.conf
```
y he modificado la ultima fila con : 
```bash
host    dbname    user1    0/0     md5
```

### Inicialización del MAAS

Para inicializar MAAS, hemos utilizado el siguiente comando (no nos dejaba utilizar el del tutorial):
```bash
sudo maas init
```
Durante la configuración de la autenticación:
- URL del servicio Canonical RBAC (dejamos en blanco porque no lo usamos)
- Ruta del archivo de agente de autenticación Candid (dejamos en blanco porque no lo usamos)
- Creamos nuestro username: admin
- Y la contraseña: maas
- email: admin@maas.com
- Import SSH keys [] (lp:user-id or gh:user-id) (lo dejamos en blanco también)

**Para entrar:** http://localhost:5240/MAAS/r/intro
  

------------------------------------------------------------------------------------------------------------------------------------------------

## Prueba de conectividad entre los equipos:
He realizado una prueba de conectividad entre estos equipos que están ya en nuestra propia red. Estas direcciones IP han sido obtenidas de forma dinámica con el protocolo DHCP. He realizado un ping entre ellas y todas tienen conectividad entre todas:
- Cliente 1:  169.254.20.178
- Cliente 2:  169.254.91.148
- Cliente 3:  169.254.29.200
- MaaS:       169.254.91.29
- Kubernetes: 169.254.230.138
- Worker 1:   169.254.216.76
- Worker 2:   169.254.
- Worker 3:   169.254.
- Worker 4:   169.254.
- Worker 5:   169.254.
- Worker 6:   169.254.
- Worker 7:   169.254.

### Datos de la base de datos
- Username: admin
- Password: maas
- Email: admin@maas.com


## APARTADO 3
