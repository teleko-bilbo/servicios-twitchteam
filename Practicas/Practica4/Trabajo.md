## Zuhaitz:


## Rubén:
Ayer (03/12/2024) estuvimos intentando realizar la parte correspondiente a la parte grupal de la práctica 4 de Kubernetes, pero tuvimos problemas en el despliegue de un pod, ya que nos saltaba un error de "***FailedCreatePodSandBox***". Estuvimos mirando como poder solucionarlo, pero no conseguimos desplegarlo al final.

## Olatz:
El 03/12/2024, intentamos realizar la parte grupal de la práctica 4 de Kubernetes, pero no logramos desplegar un pod debido a un error con Calico. Intentamos eliminarlo, pero el sistema lo volvía a generar automáticamente. Además, nos dimos cuenta de que Rubén, quien creó el controlador, podía acceder a las máquinas mediante SSH (juju), pero los demás miembros del equipo no tenían acceso.

En la clase del 04/12/2024, Gorka nos ayudó a resolver el problema con Calico, que tenía asignadas IPs en el mismo rango que las nuestras. También solucionamos el problema con SSH: como ya tenía la clave SSH privada id_rsa, generé la clave pública correspondiente y la registré en Juju, lo que era el paso que nos faltaba para conseguir que todos tuviéramos acceso. Despueés de haber registrado los otros dos clientes pudimos acceder mediante SSH.
