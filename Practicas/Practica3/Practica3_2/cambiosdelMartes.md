Para acceder por ssh a los clientes: El fichero id_rsa tiene que ser exportado y no tiene que ser copiado. Y una vez que esta exportado, hay que cambiarle los permisos que solo el propietario pueda tener permisos de lectura y escritura pero los group o otros no tengas ningun permis. Entonces hemos utilizado el comando: chmod 600 ~/.ssh/id_rsa.
Errores para solucionar:
- El vworker1 la configuracion IP esta en unconfigured y los otros tienen autoassigned. No deja hacer deploy.
- El DHCP que hemos configurado no respeta las IP reservadas que hemos configurado (Por ello nos salia lo del TFTP, asignaba el IP de uno de los clientes)
