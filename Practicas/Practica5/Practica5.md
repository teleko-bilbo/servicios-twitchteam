# PRÁCTICA 5

## APARTADO 1: LoadBalancer
### a.Instalar MetalLB
```bash
helm repo add bitami https://charts.bitnami.com/bitnami
helm repo update
helm install -n metallb metallb bitnami/detallb --create-namespace
```
**imagen metallb
#### Configurar metallb
Hay que desplegar el metallb.yaml (subir al repo) con nuestras IP's reservadas.
- namespace metallb
- name: metallb-name
- IPs: 50-60
### b.Cambiar el servicio kuard de la práctica anterior a tipo LoadBalancer
Hemos desplegados los yml de Deployment y Serivce de la practica anterior. "deployment.yaml" y "service.yaml" (hay que subir al repo): --
Serivce.yaml:
- namespace Streamflix
- name: streamflix-service
- serivce: LoadBalancer
**imagen LoadBalancer 
** imagenes servicios LoadBalancer_Pod123
### c. Acceder al servicio mediante IP externa y comprobar que se balancea
## APARTADO 2

## APARTADO 3
