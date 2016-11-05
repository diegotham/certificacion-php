**Indice de contenido**

1.  ¿Qué es un socket?
2.  Conectar a un servidor
3.  Hacer una petición HTTP con una conexión socket
4.  Crear un servidor socket

### 1. ¿Qué es un Socket?

La **dirección IP** es la identificación de un aparato en una red, y el **puerto** es la identificación de una aplicación dentro de ese aparato. Estos dos conceptos identifican a una aplicación en un ordenador o servidor, y junto con el **protocolo** a emplear, se les denomina **socket**.

Cuando enviamos un _request_ a la ip 127.0.0.1 puerto 80, se envía al **servidor** 127.0.0.1 (localhost) y el **sistema operativo** busca si tiene una aplicación con el puerto 80 asignado. Si existe, el sistema operativo reenvía el _request_ a la aplicación en concreto. Es entonces cuando la aplicación puede aceptar la conexión, que conecta con la **ip** y **puerto** que el cliente envió con el request.

Existen dos posibilidades para **programar con sockets en PHP**, con la [extensión Socket](http://php.net/manual/es/intro.sockets.php), con funciones muy similares a las de C, y con **stream sockets**, mediante la [extensión Stream](http://php.net/manual/es/book.stream.php), que permite enlazar y conectar a sockets de red. Vamos a analizar esta segunda forma.

Los _**recursos socket**_ creados con la **extensión Stream** se pueden usar con casi todas las funciones de stream relacionadas, como _fgets()_ o _fread()_, por lo que proporcionan acceso a streams fácilmente. Todas estas funciones comienzan con _**stream_socket_***_.

### 2. Conectar a un servidor

**Conectarse a un servidor** se hace mediante la función _**stream_socket_client()**_. El único argumento obligatorio es especificar el socket al que te quieres conectar, y devuelve un _**recurso**_ si conecta correctamente o **false** en caso de error. La **especificación de un socket** es de la forma _**$protocolo://$host:$port**_, donde protocolo puede ser:

*   **tcp**, usado por casi todos los protocolos de internet, como HTTP, FTP o SMTP.
*   **upd**
*   **unix**, que conecta con un socket Unix, un tipo especial de socket de red interna, que forma parte de la red del sistema operativo. Es más eficiente porque no hay una red externa de por medio.

### 3. Hacer una petición HTTP con una conexión socket

Para **hacer una petición HTTP** muy simple se puede emplear el siguiente script:

```
// Obtenemos la ip del host de www.example.com
$direccion = gethostbyname("www.example.com");
// Creamos la conexión socket:
$cliente = stream_socket_client("tcp://$direccion:80", $errno, $errorMessage);
if ($cliente === false) {
    throw new UnexpectedValueException("Failed to connect: $errorMessage");
}
// Escribimos en el socket la petición HTTP:
fwrite($cliente, "GET / HTTP/1.0\r\nHost: www.example.com\r\nAccept: */*\r\n\r\n");
echo stream_get_contents($cliente);
fclose($cliente);
```

La función _**stream_socket_client()**_ permite pasar referencias para el segundo y tercer argumento, que indican el **código de error** y el **mensaje**, respectivamente. 

Las conexiones creadas con _stream_socket_client()_ son streams, al igual que lo son los archivos abiertos mediante _**fopen()**_. Esto significa que podemos usar _**fwrite()**_ para escribir **bytes** al socket, y que podremos usar la función _**stream_get_contents()**_ para leer la **respuesta** entera. De la misma forma, los sockets stream se pueden cerrar, como los archivos, con _**fclose()**_.

### 4. Crear un socket de servidor

La **extensión Stream** también proporciona una forma fácil de **crear un socket de servidor** con la función _**stream_socket_server()**_. Esta función también toma como primer argumento una especificación socket, de la misma forma a como se ha hecho con _stream_socket_client()_.

Para **crear un socket de servidor** son necesarias las siguientes cosas:

1.  **Enlazar un socket**, le dice al sistema operativo que queremos que lleguen paquetes a la red indicada por el socket.
2.  Comprueba si está disponible una conexión de entrada.
3.  **Acepta** la conexión de entrada (con la función [stream_socket_accept()](http://php.net/stream_socket_accept)).
4.  **Envía** alguna **información** al cliente.
5.  **Cierra la conexión**, o deja que el cliente la cierre.
6.  Vuelta al punto 2.

Antes de poder **escribir en un servidor**, primero hay que **aceptar la operación el en socket de servidor** mediante la función _**stream_socket_accept()**_. Esta función bloquea el socket hasta que un cliente conecta con el servidor o cuando el tiempo de espera se agota.

**Ejemplo**:

```
$servidor = stream_socket_server("tcp://127.0.0.1:1339", $errno, $errorMessage);
if ($servidor === false) {
    throw new UnexpectedValueException("No se ha podido enlazar el socket: $errorMessage");
}
for (;;) {
    $cliente = @stream_socket_accept($servidor);
    if ($cliente) {
        stream_copy_to_stream($cliente, $cliente);
        fclose($cliente);
    }
}
```

Para probarlo, abre una ventana en la **terminal** y escribe:

```
php server.php
```

_server.php_ es el archivo donde has guardado el código. Una vez iniciado, abre una nueva ventana en la terminal y escribe lo siguiente:

```
echo "Hola que tal" | nc 127.0.0.1 1337
// Devuelve Hola que tal
```

Si tienes el puerto 1337 ocupado dará un error **Address already in use**, puedes probar con cualquier otro, como 9002, por ejemplo.

El **proceso** que sigue es el siguiente:

Primero enlaza con **tcp** el socket en la **dirección** 127.0.0.1 y el **puerto** 1337\. Este es el **socket de red**, que puede usarse por los clientes para conectar con nuestro servidor. Al igual que ocurría con la función _stream_socket_client()_, dos argumentos se pueden pasar por referencia, el **código de error** y su **mensaje**. Si ocurre cualquier error, como que el puerto ya esté en uso, podemos ver el mensaje gracias a la condición _if $servidor === false_.

Cuando se usa la **estructura de control for** sin ninguna expresión se produce un **loop infinito**. Esto es necesario para que el servidor siga funcionando hasta que decidamos pararlo.

_$cliente = stream_socket_accept($servidor)_ bloquea el socket hasta que un cliente conecta con el socket o se agota el tiempo de espera. Se añade el **@** para evitar que la función devuelva **warnings**. _stream_socket_accept()_ **devuelve una conexión** o **false**, por lo que con _if ($client)_ sólo haremos algo si el cliente está conectado. 

La función _stream_copy_to_stream($cliente, $cliente)_ **copia bytes** desde el stream dado en el primer argumento al stream dado en el segundo argumento, por lo que básicamente devuelve todo lo que el cliente envía.

Finalmente se cierra la conexión con _fclose($cliente)_. Esto no siempre es necesario, por ejemplo en una conexión FTP donde se quiere que el cliente la cierre.