# PRÁCTICA 2

### Instalación de Wireshark
Para instalar Wireshark hay que ejecutar estos comando:
```bash
sudo apt-get install wireshark
sudo groupadd wireshark
sudo usermod -a -G wireshark YOUR_USER_NAME
sudo chgrp wireshark /usr/bin/dumpcap
sudo chmod 750 /usr/bin/dumpcap
sudo setcap cap_net_raw,cap_net_admin=eip /usr/bin/dumpcap
sudo getcap /usr/bin/dumpcap
```

### Instalación de Dnsmasq
He probado a instalar Dnsmasq, que es una herramienta que simplifica la configuración de DNS, DHCP y TFTP en un solo servicio.

1. Instalar Dnsmasq:
```bash
sudo apt update
sudo apt install dnsmasq
```

Al instalarlo, me ha saltado el siguiente error:
```bash
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Suggested packages:
  resolvconf
The following NEW packages will be installed:
  dnsmasq
0 upgraded, 1 newly installed, 0 to remove and 15 not upgraded.
Need to get 19,7 kB of archives.
After this operation, 97,3 kB of additional disk space will be used.
Get:1 http://es.archive.ubuntu.com/ubuntu jammy-updates/universe amd64 dnsmasq all 2.90-0ubuntu0.22.04.1 [19,7 kB]
Fetched 19,7 kB in 1s (31,9 kB/s)  
Selecting previously unselected package dnsmasq.
(Reading database ... 214701 files and directories currently installed.)
Preparing to unpack .../dnsmasq_2.90-0ubuntu0.22.04.1_all.deb ...
Unpacking dnsmasq (2.90-0ubuntu0.22.04.1) ...
Setting up dnsmasq (2.90-0ubuntu0.22.04.1) ...
Created symlink /etc/systemd/system/multi-user.target.wants/dnsmasq.service → /lib/systemd/system/dnsmasq.service.
Job for dnsmasq.service failed because the control process exited with error code.
See "systemctl status dnsmasq.service" and "journalctl -xeu dnsmasq.service" for details.
invoke-rc.d: initscript dnsmasq, action "start" failed.
× dnsmasq.service - dnsmasq - A lightweight DHCP and caching DNS server
     Loaded: loaded (/lib/systemd/system/dnsmasq.service; enabled; vendor preset: enabled)
     Active: failed (Result: exit-code) since Tue 2024-10-29 21:22:56 CET; 17ms ago
    Process: 5350 ExecStartPre=/etc/init.d/dnsmasq checkconfig (code=exited, status=0/SUCCESS)
    Process: 5358 ExecStart=/etc/init.d/dnsmasq systemd-exec (code=exited, status=2)
        CPU: 59ms

oct 29 21:22:56 vbox systemd[1]: Starting dnsmasq - A lightweight DHCP and caching DNS server...
oct 29 21:22:56 vbox dnsmasq[5358]: dnsmasq: failed to create listening socket for port 53: Address already in use
oct 29 21:22:56 vbox dnsmasq[5358]: failed to create listening socket for port 53: Address already in use
oct 29 21:22:56 vbox systemd[1]: dnsmasq.service: Control process exited, code=exited, status=2/INVALIDARGUMENT
oct 29 21:22:56 vbox dnsmasq[5358]: FAILED to start up
oct 29 21:22:56 vbox systemd[1]: dnsmasq.service: Failed with result 'exit-code'.
oct 29 21:22:56 vbox systemd[1]: Failed to start dnsmasq - A lightweight DHCP and caching DNS server.
```

Para corregirlo hay que detener y desactivar *systemd-resolverd* para liberar el puerto 53:
```bash
sudo systemctl stop systemd-resolved
sudo systemctl disable systemd-resolved
```

Ahora hay que reiniciar dnsmasq y comprobar su estado:
```bash
sudo systemctl start dnsmasq
sudo systemctl status dnsmasq
```

2. Configurar Dnsmasq para DNS, DHCP y TFTP:
Hay que editar el fichero de configuración principal de Dnsmasq que se encuentra en /etc/dnsmasq.conf:
```bash
sudo nano /etc/dnsmasq.conf
```

Pero antes hay que conocer las direcciones IP de los servidores DNS, hay que ejecutar el siguiente comando:
- Desde Windows: Hay dos opciones:
  - OPCIÓN 1:
  ```bash
  ipconfig /all
  ```

  Donde se ha obtenido el siguiente resultado sobre los servidores DNS:
  ```bash
  Servidores DNS. . . . . . . . . . . . . . : 10.10.13.107
                                              10.10.13.108
  ```
  - OPCIÓN 2:
  ```bash
  nslookup ehu.eus
  ```

  Esta ha sido la respuesta obtenida:
  ```bash
  Servidor:  ciscoumbrella1.lgp.ehu.es
  Address:  10.10.13.107

  Respuesta no autoritativa:
  Nombre:  ehu.eus
  Address:  158.227.0.65
  ```

- Desde Linux:
```bash
nmcli dev show | grep DNS
```

Se ha obtenido la siguiente respuesta:
```bash
IP4.DNS[1]:                             10.10.13.107
IP4.DNS[2]:                             10.10.13.108
IP4.DNS[3]:                             100.90.1.1
IP4.DNS[4]:                             100.100.1.1
```

Desde esta página web también se puede ver:
*https://elhacker.net/registros-dns.html?domain=ehu.eus*

Y hay que agregar o editar las siguientes líneas para configurar los servicios de DHCP, DNS y TFTP:
```bash
# Desactivar el servidor DNS integrado de dnsmasq:
port=0

# Servidores DNS:
server=10.10.13.107
server=10.10.13.108

# Rango DHCP:
dhcp-range=192.168.1.50,192.168.1.100,12h

# Configuración de TFTP y PXE:
enable-tftp
tftp-root=/srv/tftpboot

# Archivo de arranque PXE:
pxe-service=x86PC,"Network Boot",pxelinux
dhcp-boot=pxelinux.0

# Opciones de DHCP:
dhcp-option=3,192.168.1.10
dhcp-option=6,10.10.13.107
```

3. Crear el directorio TFTP:
Hay que crear el directorio donde se van a almacenar los ficheros de arranque para TFTP:
```bash
sudo mkdir -p /srv/tftpboot
```

Después, hay que copiar los ficheros de arranque necesarios para las Raspberry Pi en este directorio.

4. Reiniciar y verificar Dnsmasq:
Hay que reiniciar el servicio Dnsmasq para aplicar los cambios:
```bash
sudo systemctl restart dnsmasq
```
Después, hay que verificar que Dnsmasq esté funcionando correctamente:
```bash
sudo systemctl status dnsmasq
```

### Dnsmasq en Docker
En el caso de utilizar el contenedor de Docker que contiene el Dnsmasq configurado que ha probado Zuhaitz, se le puede incluir un volumen persistente de tal forma que haya una carpeta compartida entre el equipo host y el contenedor:

  1. Crear un volumen persistente en Docker:
  ```bash
  docker volume create tftpboot-volume
  ```
  2. Montar el volumen en el contenedor:
  ```bash
  docker run --cap-add=NET_ADMIN -it --rm --net=host -v tftpboot-volume:/tmp ferrarimarco/pxe
  ```

De esta forma, ya estaría creado el volumen persistente. Para ver los ficheros o carpetas en el equipo host hay que ir a:
```bash
sudo cd /var/lib/docker/volumes/tftpboot-volume/_data
```

Luego, para copiar ficheros en otros directorios:
```bash
cp /tmp/<nombreFichero> /var/lib/tftpboot/
```