# MARTES 26 DE NOVIEMBRE  

## Avances del día  

### Problema inicial con el DHCP y las IPs duplicadas  
- Detectamos que el servidor DHCP asignaba una IP ya configurada manualmente en uno de los clientes. Esto causaba un error TFTP al intentar comunicarse.  
- **Solución:** Cambiamos la IP del cliente afectado para resolver el conflicto. Ahora el *juju* funciona correctamente.  
- **Nota:** Debemos revisar la configuración de las IP reservadas del DHCP, ya que no están siendo respetadas.  

### Configuración de clientes en *juju*  
- Configuramos el cliente *juju* en dos dispositivos.  
- El cliente que creó el controlador generó un token para realizar la configuración.  

### Conexión SSH y manejo de claves  
- Detectamos que copiar y pegar el contenido del `id_rsa` no funcionaba.  
- **Solución:** Exportamos la clave privada de forma segura (por correo en lugar de usar repositorio).  
- **Configuración adicional:**  
  - Cambiamos los permisos de la clave privada para que solo el propietario tenga acceso, usando:  
    ```bash
    chmod 600 ~/.ssh/id_rsa
    ```  

---

## Errores pendientes por solucionar  

### Problema con la configuración de IP en *vworker1*  
- Actualmente aparece como "unconfigured", mientras que los otros clientes tienen "autoassigned".  
- Esto impide realizar despliegues.  

### Problemas con el DHCP  
- El servidor no respeta las IP reservadas configuradas.  
- Este problema fue la causa del error inicial de TFTP, al asignar una IP ya utilizada por un cliente.  

---

## Recomendaciones y próximos pasos  

1. Reconfigurar el DHCP para garantizar que respete las IP reservadas.  
2. Verificar y corregir la configuración de red en *vworker1*.  



