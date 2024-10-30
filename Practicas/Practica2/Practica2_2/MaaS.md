## Instalación MaaS
En la maquina virtual hemos instalado MaaS para ello hemos seguido el siguiente tutorial: https://maas.io/docs/how-to-install-maas.

Para instalarlo lo hemos echo from packages y la version 3.4:
sudo apt-add-repository ppa:maas/3.4
sudo apt update
sudo apt-get -y install maas

Luego, hemos echo Post-install setup (production):
student@vbox:~$ sudo systemctl disable --now systemd-timesyncd
Unit /etc/systemd/system/systemd-timesyncd.service is masked, ignoring.
student@vbox:~$ sudo apt install -y postgresql
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
postgresql is already the newest version (14+238).
postgresql set to manually installed.
0 upgraded, 0 newly installed, 0 to remove and 57 not upgraded.
student@vbox:~$ export DBUSER="user1"
student@vbox:~$ export DBPASS="1234"
student@vbox:~$ sudo -i -u postgres psql -c "CREATE USER \"$DBUSER\" WITH ENCRYPTED PASSWORD '$DBPASS'"
CREATE ROLE
student@vbox:~$ export DBNAME="dbname"
student@vbox:~$ sudo -i -u postgres createdb -O "$DBUSER" "$DBNAME"
student@vbox:~$ export DBUSER="user1"
student@vbox:~$ export DBPASS="1234"
student@vbox:~$ sudo -i -u postgres psql -c "CREATE USER \"$DBUSER\" WITH ENCRYPTED PASSWORD '$DBPASS'"
CREATE ROLE
student@vbox:~$ export DBNAME="dbname"
student@vbox:~$ sudo -i -u postgres createdb -O "$DBUSER" "$DBNAME"
student@vbox:/etc/postgresql/14/main$ sudo nano pg_hba.conf
y he modificado la ultima fila con : 
host    dbname    user1    0/0     md5
student@vbox:/etc/postgresql/14/main$ sudo maas init
Configuring authentication
URL for the Canonical RBAC service (leave blank if not using the service): 
Path of the Candid authentication agent file (leave blank if not using the service): 
Create first admin account
Username: admin
Password: 
Again: 
Email: admin@maas.com
Import SSH keys [] (lp:user-id or gh:user-id): 
para entrar: http://localhost:5240/MAAS/r/intro
