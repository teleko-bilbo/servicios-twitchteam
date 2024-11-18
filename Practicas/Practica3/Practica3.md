# PRÁCTICA 3

1. Instalar el cliente Juju:
```bash
sudo snap install juju --classic
juju help
juju help commands
```

2. Registrar la nube MaaS:
```bash
juju add-cloud maas-cloud maas-cloud.yaml
juju add-credential maas-cloud
juju clouds
juju credentials
```

3. Crear el controlador:
```bash
juju bootstrap maas-cloud --bootstrap-constraints "tags=juju"
```

4. Añadir un modelo a la nube:
```bash
juju add-model my-model
```

5. Añadir una máquina a juju desde la nube de MaaS:
```bash
juju add-machine
```

## APARTADO 2

## APARTADO 3

