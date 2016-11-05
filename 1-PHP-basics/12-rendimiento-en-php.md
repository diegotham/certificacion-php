Hay dos factores fundamentales a la hora de mejorar el rendimiento de los scripts en PHP: **reducir el uso de memoria** y **reducir el tiempo de carga**. Existen diferentes formas para mejorarlos, vamos a ver algunas:

| | |
| -------- | -------- |
| 1. Garbage collection | 5. Desplazar tareas a un segundo plano |
| 2. OPCache | 6. Aprovechar el HTTP caching |
| 3. Optimizar las sesiones | 7. Perfilar el código |
| 4. Optimizar la base de datos |

### 1. Garbage collection

El mecanismo **garbage collection** tiene un impacto tanto en el **uso de memoria** como en el **tiempo de ejecución**.

*  Reducción en el uso de memoria con Garbage Collection

El objetivo del recolector de basuras es reducir el uso de memoria limpiando las variables de referencias circulares. En PHP esto ocurre cuando el **root buffer** está lleno o cuando se llama a la función [gc_collect_cycles()](http://php.net/manual/es/function.gc-collect-cycles.php). El siguiente es un ejemplo con diferentes usos de memoria en función de las variables creadas:

*   FASE 1: PHP consume un mínimo de memoria al principio, aunque no se haga nada:

```
echo "Fase 1, el uso de memoria es de: ", round(memory_get_usage()/1024, 2), "KB";
// DEVUELVE : 248,79KB
```

*   FASE 2: Se crea un loop con un array de 10.000 de valores de números aleatorios:

```
$array = array();
for ($i = 0; $i < 10000; ++$i){
  $array[] = rand();
}
echo "Fase 2, el uso de memoria es de: ", round(memory_get_usage()/1024, 2), "KB";
// DEVUELVE 1705.32 KB. Gran aumento por la cantidad de elementos del array
```

*   FASE 3: Se crean 2 variables:

```
$var1 = 1;
$var2 = 2;
echo "Fase 3, el uso de memoria es de: ", round(memory_get_usage()/1024, 2), "KB";
// DEVUELVE 1705.6 KB. Se produce un ligero aumento por los dos nuevos zval
```

*   FASE 4: Se asignan los símbolos de las variables anteriores (var1,var2) a $array:

```
$var1 = $array;
$var2 = $array;
echo "Fase 4, el uso de memoria es de: ", round(memory_get_usage()/1024, 2), "KB";
// DEVUELVE 1705.51 KB. El uso de memoria es prácticamente el mismo, var1 y var2
// tan sólo son símbolos del mismo zval. Esto es gracias a la copia en escritura
```

*   FASE 5: Se asigna el símbolo array a un array vacío:

```
$array = array();
echo "Fase 5, el uso de memoria es de: ", round(memory_get_usage()/1024, 2), "KB";
// DEVUELVE 1705.64 KB. Aumenta un poco el uso de memoria por el nuevo array (nuevo zval)
```

*   FASE 6: Se añade un valor al array de var2:

```
$var2[] = "Hola!";
echo "Fase 6, el uso de memoria es de: ", round(memory_get_usage()/1024, 2), "KB";
// DEVUELVE 2693.33 KB. Aquí se produce un aumento notable. PHP hace de nuevo una copia en escritura, var2 hace una copia del array que apuntaba antes y añade el nuevo elemento
```

En este momento, var1 y var2 apuntan a arrays diferentes y $array apunta a un array vacío

*   FASE 7: Se crea un array vacío en el símbolo de var1

```
$var1 = array();
echo "Fase 7, el uso de memoria es de: ", round(memory_get_usage()/1024, 2), "KB";
// DEVUELVE 1705.91 KB. Notable disminución. A var1 se le asigna el valor de un array vacío, cuando antes tenía un array muy grande. Aqui es donde se produce una limpieza
```

Aunque a var2 ya no se le hace referencia en ningún momento, no se añade a la **colección de basuras**. PHP lo guarda en memoria hasta que finaliza el script.

**¿Qué conclusiones se sacan de éste ejemplo?**

*   Si quieres que una **variable en ámbito global libere su memoria**, utiliza la función _unset()_ o asígnale un valor diferente. Sino, PHP continuará guardándola en memoria hasta el final.
*   La copia en escritura es muy útil, y los arrays se copian por referencia de la misma forma que lo hacen los objetos. Pero hay que tener en cuenta que un cambio en el array hace una copia en la memoria.
*   En el momento en que haces _unset()_ o reasignas una variable, **PHP libera su memoria**. Liberar memoria no es gratis (especialmente con grandes cantidades) en términos de **tiempo de procesamiento**, lo que significa que si quieres que tu **script se ejecute lo más rápido posible a costa de gastar RAM**, deberías evitar usar **garbage collection** en grandes variables, y dejar que PHP lo haga sólo al final del script.

Veamos ahora un ejemplo con la creación de una **clase**:

```
class Clase
{
    public $var = '3.141592';
}

$baseMemory = memory_get_usage();

for ( $i = 0; $i <= 1000000; $i++ )
{
    $x = new Clase;
    $x->self = $x;
    if ( $i % 500 === 0 )
    {
        echo sprintf( '%8d: ', $i ), memory_get_usage() - $baseMemory, "<br>";
    }
}
```

En este ejemplo se crea un objeto en el que una propiedad enlaza de nuevo al mismo objeto. Cuando $x se reasigna en la siguiente iteración del loop, antes de PHP 5.3 se produciría una **fuga de memoria**. 

Después de 10.000 iteraciones, el **buffer** se llena y lanza el mecanismo recolector de basura y libera memoria asociada con esas posibles raíces. 

*  Aumento en el tiempo de ejecución con Garbage Collection

El **garbage collection** también influye en el tiempo de ejecución por el tiempo que lleva a éste a liberar la memoria fugada. Si partimos del siguiente script:

```
$start = microtime(true);
class Clase
{
    public $var = '3.14159265359';
}

for ( $i = 0; $i <= 100000; $i++ )
{
    $x = new Clase;
    $x->self = $x;
}

echo round(memory_get_peak_usage()/1024, 2), "<br>";

$end = microtime(true);
$time = number_format(($end - $start), 2);
echo $time;
```

Y lo ejecutamos una primera vez con **garbage collection** habilitado y en otra deshabilitado, obtenemos los siguientes tiempos:

**Tiempo con GC habilitado:** 0,23
**Tiempo con GC deshabilitado:** 0,17

La diferencia de tiempo con este script es de sólo 0,06 segundos, en cambio el uso de memoria ha sido:

**Memoria usada con GC:**  5815.88 KB
**Memoria usada sin GC:**  54470.23 KB

La diferencia en el consumo de memoria es enorme.

En general el **recolector de basuras en PHP** sólo provoca un retraso cuando el algoritmo recolector de ciclos se activa (más de 10.000 roots), sino no hay impacto en el rendimiento.

### 2. OPCache

**OPCache mejora el rendimiento de PHP** almacenando el código de **bytes** de un script precompilando en la memoria compartida, eliminando la necesidad de que PHP cargue y analice los scripts en cada petición.

Cada script PHP se compila en tiempo de ejecución, parte de ese tiempo se dedica a traducir el código legible para los humanos en código que la máquina puede entender. Un **motor de cache bytecode** como [OPCache](http://www.sitepoint.com/understanding-opcache/), APC o Xcache lo hace sólo una vez durante la primera ejecución de un archivo PHP. Después el script precompilado se guarda en la memoria, lo que aumentará notablemente el rendimiento en una **aplicación PHP**.

La **implementación de OPCache** permite recibir más requests por segundo y devolver respuestas más rápido. [Este artículo](https://blog.appdynamics.com/php/why-every-php-application-should-use-an-opcache/) plantea un ejemplo de un **e-commerce** construído en **Symfony2** donde analiza más profundamente el impacto de OPCache en el código. El artículo concluye con un 14% de reducción en el tiempo de respuesta en general, pero hay otros análisis que concluyen una mejora muy superior, como el de [fideloper](http://fideloper.com/install-zend-opcache). 

### OPCache VS APC cache

*   **APC cache** no funciona correctamente en las últimas versiones de PHP, resultando en errores de ejecución.
*   **OPCache** está ligado (_bundled_) a PHP a partir de su versión 5.5, y también puede ser instalado en versiones anteriores.
*   OPCache era conocido anteriormente como **Zend Optimizer+**, hasta que Zend decidió hacerlo open source e incluirlo en el núcleo de PHP.

La conclusión es que se debería usar OPCache, y si se está usando APC cache en un proyecto, lo ideal sería migrarlo a OPCache.

### Como saber si OPCache está cacheando los archivos

Si ya tienes instalado y configurado **OPCache** puede ser importante controlar qué archivos PHP están siendo cacheados. El **motor de la cache** funciona en segundo plano pero es transparente y se puede comprobar su estado, principalmente mediante las funciones [opcache_get_configuration()](http://www.php.net/manual/en/function.opcache-get-configuration.php) y [opcache_get_status()](http://www.php.net/manual/en/function.opcache-get-status.php).

También existen scripts disponibles que emplean la configuración y los datos de estado y los muestra de forma más amigable, como [opcache-status](https://github.com/rlerdorf/opcache-status), de **Rasmus Lerdorf** o el [OPCacheGUI](https://github.com/PeeHaa/OpCacheGUI).

### Tips de configuración de OPCache

En cada proyecto hay que asegurarse que _**opcache.use_cwd**_ está establecido como **true**. Activando esta configuración **OPCache** buscará en todos los directorios para diferenciar entre archivos con los mismos nombres. Si se establece como **false**, puede conllevar a colisiones entre archivos con el mismo nombre base.

En **frameworks** que usan anotaciones, como **Doctrine** o **PHP Unit**, hay que establecer _**opcache.load_comments**_ y _**opcache.save_comments**_ a **true**. Así los comentarios de los archivos también en incluyen en el código precompilado de OPCache.

Se pueden [excluir ciertos archivos de ser cacheados](http://www.php.net/manual/en/opcache.configuration.php#ini.opcache.blacklist-filename), como los que tienen datos de configuración. Es necesario activar la opción _**opcache.blacklist_filename**_ con el directorio donde está la lista que archivos que quieres excluir. 

Para **desactivar OPCache en entornos de desarrollo o testing**, se suele mantener globalmente OPCache con **_opcache.enabl_e** en el archivo de configuración php.ini, y si no se quiere usar la cache en algún entorno o proyecto, se desactiva con la función [ini_set()](http://www.php.net/manual/en/function.ini-set.php). 

### 3. Optimizar las sesiones

Aunque **HTTP** es un **protocolo sin estado**, la mayoría de **aplicaciones web** requieren una forma de manejar los datos de usuarios. En **PHP** se controlan mediante sesiones. 

La mejor opción para guardar las sesiones es mediante una base de datos y emplear una cache **LRU (Least Recently Used**) como [Memcached](http://memcached.org/) o [Redis](http://redis.io/). Lo ideal es limitar el tamaño de datos de sesión a unos 4096 bytes y guardar todos los datos de sesión en una **cookie encriptada**. 

### 4. Opmitizar la base de datos

Normalmente las aplicaciones hacen uso de las bases de datos para organizar el contenido. Dependiendo de la cantidad de datos y como se accede a ellos, puede costar muchos recursos y tiempo hacer peticiones. Una forma fácil de solventarlo es utilizar una cache como **Memcached** o **Redis** que permita guardar peticiones e invalidarlas y obtener peticiones actualizadas cuando éstas quedan obsoletas.

Una buena forma de hacer todo esto automáticamente es empleando un mapa de objetos relacional ORM como Doctrine, que tiene soporte incorporado para Memcached y Redis.

### 5. Desplazar tareas a un segundo plano

Hay ciertas tareas en un script que pueden llevar cierto tiempo. En muchos casos no es necesario que el usuario tenga que esperar a que ciertas tareas se completen, por ejemplo cuando se rellena un formulario y es necesario enviar un email de respuesta al usuario. 

La solución es apartar el código del flujo principal y ponerlo en una cola o sistema de mensajes, para que luego se maneje mediante un sistema paralelo. Dos notables **beneficios** derivan de este sistema: experiencia de usuario y escalar el trabajo de procesos largos añadiéndolos a la cola. 

Existen diferentes herramientas que permiten realizar estas tareas, algunas de las más populares son [php-resque](https://github.com/chrisboulton/php-resque), [RabbitMQ](http://www.rabbitmq.com/) y [ActiveMQ](http://activemq.apache.org/).

### 6. Aprovechar el HTTP caching

Es conveniente leer la [especificación de la caché de HTTP](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html), si se usa correctamente puede ahorrar mucha memoria. [Este](https://www.mnot.net/cache_docs/) es un tutorial muy bueno (y bastante largo) sobre la **cache web**. El libro **HTTP: The Definitive Guide** también tiene una sección muy interesante dedicada al tema.

Para proyectos y aplicaciones con alto tráfico, es muy recomendable emplear una cache proxy como [Varnish](https://www.varnish-cache.org/) para aliviar la carga de los servidores.

### 7. Perfilar el código

Lo mejor es aplicar el mayor número de métodos para mejorar el rendimiento, en la medida de lo posible.

**Perfilar el código** permite recopilar **estadísticas de ejecución de un script**, como ver el nombre de las funciones ejecutadas, número de veces que se han ejecutado, etc. Analizar estos datos nos permite ver qué se puede mejorar en nuestro código. 

[Xdebug](http://www.xdebug.org/docs/profiler) es una extensión PHP para **depurar el código**. Permite seguir el recorrido del script y recopilar información de perfil y de memoria.

Existen aplicaciones frontend para **Xdebug** como [WebGrind](https://github.com/jokkedk/webgrind), y también es fácilmente adaptable a **IDEs** como **PHPStorm**.