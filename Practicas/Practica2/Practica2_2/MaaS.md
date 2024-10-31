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