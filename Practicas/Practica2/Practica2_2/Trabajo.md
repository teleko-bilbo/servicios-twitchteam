## Zuhaitz:
He trabajado sobretodo configurando los equipos (VM). Hemos configurado 8 equipos para que puedan ser arrancados y controlados por el MaaS, y he documentado la configuración.

## Rubén:
He configurado, junto con Zuhaitz, las máquinas virtuales (VMs) para nuestra red siguiendo el esquema previsto. Hemos creado una VM para cada cliente (Clientes 1, 2 y 3), cada una alojada en un equipo físico diferente, luego hemos creado tres VMs destinadas a los componentes de Kubernetes en un único equipo y, para los Workers, hemos configurado una VM en equipos independientes, con un Worker por cada máquina física. Además, he redactado la documentación de la Práctica 2_2, detallando cada apartado de configuración y describiendo las pruebas realizadas, incluyendo instrucciones para la instalación y configuración del MaaS controller, la configuración de las máquinas virtuales y el paso a paso de las pruebas finales realizadas.

## Olatz
He creado dos VMs uno para Maas y otro para juju, en un mismo equipo local y he echo la instalación del maas. Para la instalación he utilizado apt y he seguido el tutorial que nos proporciono el profesor sus diapositivas. He tenido un problema, en el "post install setup (production)" al inicializar maas con la base de datos; porque no me dejaba poner el comando del tutorial, seguramente por la version del maas que es el 3.4 y el del tutorial no lo pone. Al final después de probar, con hacer "sudo maas init" era sufiente y hemos podido inicializarlo y crear el admin user.
