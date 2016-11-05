**Indice de contenido**

*   1. ¿Qué es un stream?
*   2. Stream Contexts
*   3. Stream metadata
*   4. Stream filters
*   5. Stream transports

### 1. ¿Qué es un stream?

Un **stream** es un objeto de tipo _**resource**_ que puede ser leído o escrito de una forma lineal, y con el que se puede usar la función [_fseek()_](http://php.net/manual/es/function.fseek.php) para **buscar posiciones** dentro del mismo. Los **streams sirven para generalizar operaciones** que comparten una funcionalidad común. Son una característica de PHP que fue introducida en PHP 4.3 para unificar los métodos de trabajar con archivos, sockets y otros recursos similares.

Ejemplos de streams: un archivo en el disco duro, una conexión HTTP a un sitio web, una conexión UDP a un servidor, un archivo ZIP, un tunel SSH.

Las operaciones comunes a todos los ejemplos anteriores es que comparten la habilidad de poder ser leídos y escritos. El **poder de los streams en PHP** es que puedes acceder a todos ellos usando el mismo conjunto de funciones, y si quieres emplear streams en algo, puedes **crear tu propio stream wrapper**.

Un _**wrapper**_ o _envoltura_ es un **código adicional** que le indica al stream cómo manejar protocolos o codificaciones específicas. Por ejemplo, el wrapper** http** permite **acceso de lectura** a archivos y recursos mediante HTTP utilizando **GET**, pudiendo traducir así una URL. Puedes ver el **listado completo de wrappers en PHP** [aquí](http://php.net/manual/es/wrappers.php).

Un _**stream**_ está descrito por un **esquema** (_scheme_) y un **objetivo** (_target_): _**scheme://target**_. El **scheme** es el **nombre del wrapper** que se va a usar (_file_, _http_, _https_, _ftp_, _ftps_, _php_...). Si no se especifica una se usa la función predeterminada, que suele ser _file://_. El **target** depende del wrapper usado. Para streams relacionados con el sistema de archivos (_**filesystem**_), normalmente es una **ruta** y un **nombre de archivo**. Para streams relacionados con redes, normalmente es un nombre de **host**, y con frecuencia con una **ruta** añadida al final.

Para **usar streams**, hay que llamar a una función PHP que los utilice. Ejemplos de **funciones que soportan streams** son _fopen()_, _file_get_contents()_, o _file()_. Si has empleado algunas de estas funciones alguna vez, es que has estado utilizando streams. **PHP** abre conexiones para el **manejo de archivos** o para abrir **conexiones de redes externas** mediante **_fopen()_** porque trata todas las fuentes de datos como _streams_. Es posible crear un _**stream handler**_ para **trabajar con nuevos protocolos** y luego poder manejarlos con funciones como _fopen()_ y _file_get_contents()_.

En el caso del _wrapper_ **_file://_**, usamos un _stream_ cada vez que accedemos al **sistema de archivos**. Al utilizar, por ejemplo, _**readfile()**_, podemos hacerlo así _readfile('/directorio/archivo.txt')_ o así _readfile('file:///directorio/archivo.txt')_. Si indicamos una URL _readfile('http://google.com')_, PHP utilizará el **wrapper http**. Si se quiere trabajar con un stream concreto, se debe especificar en el URI _wrapper://_, seguido de lo que se quiera abrir. Por ejemplo:

```
echo file_get_contents("http://www.ejemplo.com");
```

Como hemos especificado _http://_ que hace referencia a sitios web, añadiremos el resto de la URL. Otros ejemplos:

```
zip:///directorio/carpeta/archivo.zip
file:///directorio/carpeta/archivo.txt
ftp://user:password@ejemplo.com/archivo.txt
ssh2.tunnel://user:password@ejemplo.com:22/192.168.0.1:15
```

### 2. Stream Contexts

Un _**context**_ es un conjunto de opciones o parámetros para _**streams**_ que modifican y mejoran el funcionamiento de un _**wrapper**_. Un uso habitual de un context es modificar el wrapper **HTTP**, lo que nos evita tener que usar cURL para operaciones simples de red. 

**Ejemplo**:

```
echo file_get_contents('http://www.ejemplo.com'); 
```

Hemos proporcionado la URL pero no hay un mecanismo para enviar otros **headers HTTP**. Los stream contexts permiten añadir **opciones adicionales**. 

**​**Un _**context**_ se crea con la función _stream_context_create()_ y se pueden pasar a la mayoría de las funciones de creación de streams relacionados con **sistemas de archivos** (_fopen()_, _file()_, _file_get_contents()_...).
```
string file_get_contents ( string $filename [, int $flags = 0 [, resource $context [, int $offset = -1 [, int $maxlen = -1 ]]]] )
```

Vamos a pasar un **array de opciones** a la función _stream_context_create()_:

```
$opciones = array(
    'http' => array(
        'method' => "GET",
        'header' =>
            "Cookie: foo=bar\r\n"
    ));
$context = stream_context_create($opciones);
// Ahora podemos usar el context:
echo file_get_contents("http://www.example.com", 0, $context);
// Para ver las opciones del context:
var_dump(stream_context_get_options($context));
/*
 Devuelve:
array (size=1)
  'http' =>
    array (size=2)
      'method' => string 'GET' (length=3)
      'header' => string 'Cookie: foo=bar
' (length=38)
 */
```

Existen otras funciones básicas más en la utilización de **contexts**:

*  stream_context_set_option
```
bool stream_context_set_option (resource $stream_or_context, string $wrapper, string $option, mixed $value)
```

o escrita utilizando un **_array_** (que es como se pasarán las opciones normalmente):
```
bool stream_context_set_option (resource $stream_or_context, array $options)
```

Permite establecer opciones en el _**context**_ especificado. Siguiendo el ejemplo anterior:

```
$nuevosHeaders = array('http'=> array(
    'header' =>
        "Content-Type: application/x-www-form-urlencode\r\n" .
        "Cookie: foo=bar\r\n"
));
stream_context_set_option($context, $nuevosHeaders);
var_dump(stream_context_get_options($context));
/*
array (size=1)
  'http' =>
    array (size=2)
      'method' => string 'GET' (length=3)
      'header' => string 'Content-Type: application/x-www-form-urlencode
Cookie: foo=bar
' (length=86)
 */
```

*  stream_context_set_params
```
bool stream_context_set_params (resource $stream_or_context, array $params)
```

Permite establecer parámetros en el contexto especificado (también permite establecer opciones). Suele ser un callback.

```
$params = ['notification' => 'este_es_el_callback'];
stream_context_set_params($context, $params);
var_dump(stream_context_get_params($context));
/*
 Devuelve:
array (size=2)
  'notification' => string 'este_es_el_callback' (length=19)
  'options' =>
    array (size=1)
      'http' =>
        array (size=2)
          'method' => string 'GET' (length=3)
          'header' => string 'Content-Type: application/x-www-form-urlencode
Cookie: foo=bar
' (length=65)
 */
```

*  stream_context_get_options
```
array stream_context_get_options (resource $stream_or_context)
```

Devuelve un array con las opciones en el context especificado.

Puedes ver más acerca de las **opciones y parámetros de contexts** que se pueden utilizar con los sistemas de archivos [aquí](http://us.php.net/manual/en/context.php).

### 3. Stream metadata

Tras haber podido **configurar los headers enviados en una petición HTTP** con los stream contexts, ahora vamos a ver como poder extraer esos datos. Hemos podido ver el sitio web _example.com_ pero no sus **headers**.

```
$opciones = array(
    'http' => array(
        'method' => "GET",
        'header' =>
            "Content-Type: application/x-www-form-urlencode\r\n" .
            "Cookie: foo=bar\r\n"
    ));
$context = stream_context_create($opciones);
// Ahora podemos usar el context:
echo file_get_contents("http://www.example.com", 0, $context);
```

Esos **headers son parte de los metadatos** (_metadata_), y existe una función para poder extraerlos: [_stream_get_meta_data()_](http://us.php.net/manual/en/function.stream-get-meta-data.php). _file_get_contents()_ devuelve un string con la respuesta, pero al no poder extraer metadatos de ese string, debemos emplear otra función.

_fopen()_ devuelve un _stream resource_. Vamos a convertir el ejemplo anterior para utilizar esta función:

```
$opciones = array('http' => array(
'method' => "GET",
'header' => "Accept-language: en\r\n" .
"Cookie: foo=bar\r\n"
));
$context = stream_context_create($opciones);
$fp = fopen("http://www.example.com/", "rb", false, $context);
// Podemos ver el contenido con stream_get_contents
echo stream_get_contents($fp);
```

Para obtener los metadatos de este request, se emplea _stream_get_meta_data()_ sobre el resource:

```
var_dump(stream_get_meta_data($fp));
/*
array (size=10)
  'wrapper_data' =>
    array (size=9)
      0 => string 'HTTP/1.0 200 OK' (length=15)
      1 => string 'Date: Wed, 25 Nov 2015 19:13:50 GMT' (length=35)
      2 => string 'Server: Apache/2.4.12 (Ubuntu)' (length=30)
      3 => string 'Cache-Control: private' (length=22)
      4 => string 'pragma: no-cache' (length=16)
      5 => string 'expires: -1' (length=11)
      6 => string 'Vary: Accept-Encoding' (length=21)
      7 => string 'Connection: close' (length=17)
      8 => string 'Content-Type: text/html; charset=UTF-8' (length=38)
  'wrapper_type' => string 'http' (length=4)
  'stream_type' => string 'tcp_socket/ssl' (length=14)
  'mode' => string 'rb' (length=2)
  'unread_bytes' => int 1215
  'seekable' => boolean false
  'uri' => string 'http://www.diego.com.es' (length=23)
  'timed_out' => boolean false
  'blocked' => boolean true
  'eof' => boolean false
*/
```

### 4. Stream filters

Los **filtros** permiten transformar los datos cuando se leen o escriben. Las funciones _**stream_filter_append()**_ y _**stream_filter_prepend()**_ unen un **filtro** a un **stream** (importa el orden en el que se registran):

*  stream_filter_append
```
resource **stream_filter_append (resource $stream, string $filtername [, int $read_write [, mixed $params ]] )
```

Por defecto, la función unirá el filtro en modo de lectura si el archivo se abrió para lectura (r, r+), o se unirá al filtro de escritura si el archivo fue abierto para escritura (w, a, w+, a+). El parámetro _$read_write_ puede sobreescribir este comportamiento con las constantes **STREAM_FILTER_READ**, **STREAM_FILTER_WRITE** y **STREAM_FILTER_ALL**. 

Vamos a ver un ejemplo con [ROT13](https://en.wikipedia.org/wiki/ROT13) como _$filtername_:

```
$opciones = array(
'http' => array(
'method' => "GET",
'header' => "Accept-language: en\r\n" .
"Cookie: foo=bar\r\n"
)
);

$context = stream_context_create($opciones);

$fp = fopen("http://www.example.com/", "rb", false, $context);
stream_filter_append($fp, "string.rot13", STREAM_FILTER_READ);
echo stream_get_contents($fp);
```

Existen filtros ya preparados para hacer transformaciones simples, encoding y decoding, compresión e incluso encriptado. Puedes ver una lista con los filtros disponibles [aquí](http://php.net/manual/en/filters.php).

### 5. Stream transports

Existe una **clase especial de streams**, los _**socket transports**_, que son algo diferentes a los ya mencionados _stream wrappers_. Puede verse la lista de socket transports disponibles en el servidor con _stream_get_transports()_. Algunos ejemplos: tcp, udp, unix, ssl.

Todos trabajan con **redes**, y hay que usar funciones de _**stream sockets**_ como _fsockopen()_ para usar dichos transports. No es posible usar funciones como _file()_ o _file_get_contents()_.

Aquí se puede ver una lista de [transports disponibles en PHP](http://php.net/manual/en/transports.php).