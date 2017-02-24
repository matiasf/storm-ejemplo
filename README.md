Storm

¿Que es?

Actualmente vivimos un crecimiento importante en lo que son los sistemas con grandes volúmenes de datos, los cuales están muchas veces desestructurados, o requieren de algún tipo de procesamientos específico para poder resolver un problema, o inferir un nuevo dato a partir de ellos. 

Big Data, Machine Learning, Business Intelligence, entre otros son términos que nos empiezan a resultar más familiares, pero que también hacen que los sistemas que debemos construir tengan requerimientos más complejos, haciendo que las arquitecturas y tecnologías “tradicionales” no sean una solución eficaz para algunos problemas que podemos comenzar a encontrar.

Bajo este abanico de tecnologías muchas veces no tenemos claro qué problema podemos atacar. Es bajo este contexto que en este artículo haremos una breve reseña sobre Storm, para entender qué hace, cómo lo hace y cómo se para con respecto a otros productos de similares características.

Según su sitio Storm es un sistema de computación en tiempo real distribuido [1]. Si yo tuviera que definirlo, podría decir que es un sistema de procesamiento distribuido, orientado a ejecución de tareas paralelas, procesando los datos como flujos.

Si buscamos por internet también podemos definirlo en función de cómo se compara con Hadoop (en el sitio marca algo como “Storm hace en tiempo real, lo que Hadoop hace en batch”), o con Spark, incluso Kafka. Esto se debe a que muchos problemas de los que atacan este tipo de frameworks son similares, o incluso se complementan para resolver mas problemas complejos.

¿Como lo hace?

Para entender cómo Storm hace lo que dice debemos entender algunos conceptos como el de Topología.

Una topología representa la lógica de una aplicación distribuida en Storm como un grafo de Bolts y Spouts conectados a través del agrupamiento de Streams. En la siguiente imagen los Spouts quedan representados por las canillas y los Bolts por las gotas. 

![alt tag](http://storm.apache.org/images/storm-flow.png)

Topología Storm

Los Spouts representados por canillas son los nodos que producen los datos de la Topología. Sea conectándose a una base de datos, tomando mensajes de una cola de mensajería, u obteniendo datos desde un servicio REST, los Spouts emitirán las tuplas (tipo de dato a través de donde se envían los mensajes dentro de la Topología). Los Spouts pueden ejecutar de forma paralela entre sí y de manera distribuida.

Los Bolts representados por gotas realizara algún procesamiento sobre las tuplas recibidas, emitiendo nuevas tuplas para la Topología. Estos también pueden ejecutar de forma paralela entre sí y distribuida.

Todos estos nodos del grafo se conectan a través de Streams. Un Stream es una secuencia desordenada de tuplas emitidas y procesadas de manera paralela y distribuida.

Estos Streams pueden agruparse para poder manipular como las tuplas son distribuidas entre las Tasks de un Bolt. Estas Tasks representan los hilos de ejecución de un Bolt dado definido por su nivel de paralelismo en la Topología, por lo que el agrupamiento de Streams define como las tuplas de un Stream viajan de un conjunto de Tasks a otro grupo de Tasks.

Las Topologías ejecutan a través de uno o más Workers los cuales representan un JVM que ejecuta una o más Tasks de la Topología. Storm intentará distribuir las Tasks entre los distintos Workers del sistema de la mejor forma posible según se lo configure.

Todo esto no tiene tanto atractivo si no se garantiza al menos el procesamiento de todos los mensajes de la Topología, considerando un mensaje procesado cuando a recorrido todo el grafo de cómputo de la Topología según se haya definido. 

Storm garantiza esto a través de un mecanismo de confirmación de los mensajes, la definición de timeouts de mensaje y la manipulación de mensajes repetidos, pudiendo configurar todo este comportamiento en la Topología. Queda a criterio del lector profundizar el tema ya que existen muchas implementaciones y mecanismos para manipular el comportamiento de un Stream.

Ejemplo: Contar palabras en Twitter.

Se han manejado una cantidad importante de conceptos que pueden ser confusos, por lo que aplicaremos los conceptos anteriormente descritos en un ejemplo. En este repositorio encontraremos un ejemplo que pueden ejecutar.

EXPLICAR EJEMPLO

Kafka, Spark, Hadoop, ¿Son lo mismo que Storm?

No, pero están relacionados, tanto Spark como Hadoop realizan paralelismo a nivel de procesamiento de datos, en cambio Storm realiza paralelismo a nivel de ejecución de tareas. Entre Spark y Storm existen de “sabores” que realizan un trabajo muy similar. 

Spark Streaming y Storm Trident son casi productos equivalentes que difieren en el enfoque, Spark Streaming le da las posibilidad a este de construir aplicaciones para procesar Streams de datos, creando micro transacciones de batches Spark. En tanto Storm Trident ayuda a la ejecución de Streams transaccionales muy parecidos a los Streams de Spark.

Kafka también se confunde con Storm por la similitud entre Stream y una cola distribuida. En verdad una cola distribuida es solo eso, no implica un procesamiento paralelo en sí. Muchas soluciones con Storm utilizan Kafka como fuentes de datos Spout para poder alimentar de manera paralela a la Topología Storm, el cual cuenta con Spouts para conexiones con Kafka y con otros motores de colas de mensajes.


Referencias

[1] http://storm.apache.org/


