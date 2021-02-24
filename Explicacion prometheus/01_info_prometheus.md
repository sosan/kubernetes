# Informacion e instalacion Prometheus Parte 1

**Photo by [Luke Chesser](https://unsplash.com/@lukechesser) on [Unsplash](https://unsplash.com/photos/JKUTrJ4vK00?utm_source=unsplash&utm_medium=referral&utm_content=creditShareLink)**


Prometeus es un sistema de monitorizacion en tiempo real muy modular. Soundcloud cedio el proyecto a la CNCF.

Se puede desplegar a traves terraform, cloudformation, helm, etc...

Utiliza metricas con timeseries y se usa para monitorizar el entorno. Segun a ido creciendo prometheus, se ha integrando muy bien en kubernetes. Utilizando prometeus para monitorizar el cluster kubernetes, podemos sacar metricas del uso del cluster kubernetes (capacidad, que esta haciendo cada pod, servicios, etc...)

Sustituye a una parte muy especifica de datadog / newrelic: monitorizar los recursos. Datadog / newrelic tienen muchos mas servicios.


# Que es monitorizacion?

La monitorizacion son las herramientas y procesos para medir y gestionar temas tecnologicos.


# Que ofrece Prometheus?

Tus mediciones se van a transformar en metricas que una persona pueda evaluar. Esto provee feedback al negocio, etc...

Muchos pueden tener nagios como referente, pero con nagios vas a buscar el estado de un recurso/servicio, esta arriba / abajo, estado booleano, aunque nagios ofrece plugins para mejorarlo.

Prometeus permite recoger metricas (porcentaje de uso cpu, porcentaje uso memoria, bytes en un interface de red, etc...) en base a esas metricas pueda lanzar alertas. Prometheus esta mas pensado en sistemas mas dinamicos ya que agrupa las metricas sin necesidad de detalles concretos, por ejemplo: conocer la ip.

Prometheus esta mas indicado para ver la raiz del error en entornos mas complicados como kubernetes, donde la falla de un proceso puede estar asociado a otro proceso que es el realmente falla.


# Monitorizacion push vs pull

La diferencia basica entre estos dos sistemas es que el sistema push va a buscar el dato y el sistema de monitorizacion pull reciben el dato.

Los sistemas basados en pull garantizan que ciertas mediciones van a estar enviando el dato aunque no se puedan enviar entre si, menos uso de los recursos. Por contra, por ejemplo, el sistema pull no puede comprobar si tu instancia sigue viva, porque no llega a ella.

Los sistemas basados en push las aplicaciones emiten eventos que son recibidos por el sistema de monitorizacion.

Los dos acercamientos tienen aspectos positivos y negativos. El debate sigue abierto.

Prometheus es un sistema basado en pull pero tambien soporta recibir eventos(push) gracias a un pushgateway que envia los eventos a prometheus. Prometheus es un poco hibrido en este aspecto.

# Datos que usaremos

- Metricas que tienen un timeseries.
- Logs (es costoso estar parseando los logs en busca de patrones). Se suelen utilizar otros sistemas mas especializados en este ambito.
- Chequeo del estado, aunqe tambien puede considerarse una metrica.

Las metricas seran el elemento principal que vamos a usar para generar alarmas, el log vamos a convertir en una metrica, es decir, sacamos un patron de errores del log y ese patron lo convertimos como metrica. El log nos va apoyar para generar las alarmas.

Como hemos comentado antes, muchos frameworks de monitorizacion utilizan un estado boleano para lanzar alertas y se quedan cortos al usarse en entornos tan complejos como kubernetes.

Prometheus cambia la idea anterior de chequear el estado del servicio por chequear las metricas.

La metrica nos ayuda a adelantarnos al error, podemos adelantarnos a los errores. Ejemplo de situacion, la carga de cpu empieza a subir, igual 5 minutos antes de que la web caiga, podemos reaccionar.

A que se llama timeseries? se le llama timeseries a un dato con un indice timestamp junto con un dato de cualquier tipo. Por ejemplo, numero de visitas en una pagina web, alertar al minado de criptomonedas en nuestra infraestructura, etc...

# Granularidad

Cuando almacenas un dato tienes que definir la granularidad, es decir, cada cuando vas a almacenar el dato, si es cada segundo, cada 60 segundos, etc...

Elegir la granularidad correcto es critico porque con ello nos conlleva a adelantarnos a un error critico.


# Tipos de metricas

Hay una variedad de diferentes tipos de metricas que vamos a etener:

- Gauge (calibre): son valores que pueden subir o bajar. Esencialmente una foto fija de una medida en concreto. La clasica metrica de cpu, disco, uso de memoria, numero concurrente de visitas en un momento dado,  etc...

- Contador: son numeros que se incrementen durante un tiempo, pero que nunca bajan, esencialmente son incrementales. Algunas veces los counters se pueden resetar a 0 e incrementarse de nuevo. Ejemplos: uptime del sistema, numero de bytes recibidos en un interfaz de red, numero de logins, etc... Los counter tienen utilidad porque te permiten calcular el ratio de cambio. El ratio de crecimiento en N minutos.

- Histograma: es metrica que muestrea observaciones. Agrupa datos juntos, y representa los datos en un grupo. El resultado es varias metricas agrupadas. Visualizacion comun es una grafica de barras.

- Transformaciones matematicas: ocasasionalmente el valor de una sola metrica no nos sirve para nada, en su lugar necesitamos una visualizacion con transformaciones matematicas, por ejemplo, funciones estadisticas, las mas comunes son contar / suma / media / percentils / desviacion estandard / ratio de cambio.

- Agregacion de metricas: podemos agregar metricas para multiples recursos. Por ejemplo: uso de disco desde diferentes servidores. No agregas datos simplemente pones los datos uno encima de otro. El valor agregado lo visualiza grafana mejor.

- Media: metodo de facto de las metricas. La media de un rango concreto.

Por ejemplo: un plan de capacidad a largo plazo, necesitaremos transformaciones matematicas, funciones mas avanzadas de estadistica.

# Metodologia de monitorizacion

Hacemos un uso de una combinacion de diferentes metodologias de monitorizacion encima de nuestras metricas. Vamos a combinar elementos de dos metodos diferentes. El uso del metodo USE (Utilizacion, Saturacion, Errores) y el metodo "Google's Four Golden Signal" que se centra en la monitorizacion a nivel de aplicacion. Estas son las dos metodos que combinan para diseñar sistemas de monitorizacion.

Cuando estan combinadas uno se centra en la perfomance del servidor y el otro en el performance a nivel de aplicacion. 

- El metodo USE, desarrollado por netflix, propone la creacion de un checklist para el analisis de los servidores que permite identificacion rapido de problemas. Con este identificas problemas comunes de perfomance con los datos que has recolectado. Muy util para monitorizar recursos(cpu, disco, etc...) que sufren problemas de performance sobre grandes cargas(saturacion).

  * Utilizacion es el tiempo medio que el recurso esta ocupado realizando procesos expresado como porcentaje sobre tiempo.

  * Saturacion es el numero de trabajos que no ha podido procesar y por tanto se han quedado en cola, se expresa en el tamaño de la cola y contador de errores.

  * Errores.
  
    Ejemplo: tenemos un serio problema de perfomance, nuestro checklist contiene vario elementos y los tenemos que recorrer en busca de anomalias. Mirariamos el uso/saturacion/errores de la cpu, uso/saturacion/errores de la memoria, etc...

- El metodo "Google's Four Golden Signal" viene del "google sre book" [link libro](https://sre.google/sre-book/monitoring-distributed-systems/) Latencia / Trafico / Errores / Saturacion.

  * Latencia: es el tiempo que le toma servir una peticion. Hay que distinguir entre latencia cuando algo ha salido bien y latencia cuando ha salido mal, por ejemplo, una peticion fallida puede tener una latencia muy baja, porque simplemente retorna el error muy rapido.

  * Trafico: es la demanada que tiene tu sistema en un momento dado. Por ejemplo, peticiones HTTP por segundo.

  * Errores: es el numero de errores en un tiempo dado.
  * Saturacion: es como de ocupado esta tu aplicacion o los recursos que estan haciendo que la aplicacion vaya mal, por ejemplo, memoria o entrada/salida.








