## Zuhaitz:

## Rubén:
He configurado junto con Zuhaitz los equipos (VMs) de nuestra red. Hemos creado una VM para cada uno de los clientes (1, 2 y 3) en un equipo diferente, luego otras 3 VMs para Kubernetes en un mismo equipo y luego una VM para cada Worker en un equipo diferente cada una.

## Olatz
He creado dos VMs uno para Maas y otro para juju, en un mismo equipo local y he echo la instalación del maas. Para la instalación he utilizado apt y he seguido el tutorial que nos proporciono el profesor sus diapositivas. He tenido un problema, en el "post install setup (production)" al inicializar maas con la base de datos; porque no me dejaba poner el comando del tutorial, seguramente por la version del maas que es el 3.4 y el del tutorial no lo pone. Al final después de probar, con hacer "sudo maas init" era sufiente y hemos podido inicializarlo y crear el admin user.
