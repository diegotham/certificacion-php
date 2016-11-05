**HTTP** soporta el uso de varios **mecanismos de identificación para controlar el acceso a páginas u otros _resources_**. Estos mecanismos se basan en el uso del **código de estado 401** y el **response header _WWW-Authenticate_**.

*   Basic. El cliente envía el nombre de usuario y la contraseña sin encriptar en **codificación textual base64**. Sólo debe usarse con **HTTPs**, ya que la contraseña puede ser fácilmente capturada.
*   Digest. El cliente envía la contraseña en forma de **hash**. Es mucho más difícil de obtener los valores de usuario y contraseña.

### Basic Authentication

Si un servidor recibe un **HTTP request** sin identificar para un _**resource**_ protegido, puede forzar a usar **Basic authentication** rechazando el request con un a respuesta con **código de estado 401** (_Access Denied_) y establecer el response header WWW-Authenticate:
```
HTTP/1.1 401 Access Denied
WWW-Authenticate: Basic realm="Mi servidor"
Content-Lenght: 0
```

La palabra Basic en WWW-Authenticate selecciona el mecanismo de identificación que el cliente HTTP debe usar para acceder el _**resource**_. El atributo realm se puede establecer a cualquier valor para identificar el área segura, y puede usarse por los clientes HTTP para manejar las contraseñas.

La mayoría de navegadores mostrarán una caja de login cuando se reciba la respuesta anterior, permitiendo al usuario introducir un usuario y una contraseña. Esta información se usa para reintentar el request con un header Authentication:
```
GET /areasegura/ HTTP/1.1
Host: www.ejemplo.com
Authorization: Basic bXl1aefi128djGFzcw==
```

La autenticación especifica el mecanismo de autenticación, en este caso **Basic**, seguido de un usuario y contraseña. Parece encriptada, pero simplemente es una **codificación en base64**, bXl1aefi128djGFzcw== es 'myuser:mypass'. Cualquiera que pueda interceptar el request puede averiguar el usuario y la contraseña.

### Digest Authentication

**Digest Authentication** es un método que utiliza el password y otros bits de información para crear un hash que se envía al servidor para identificar. Enviar un hash evita el principal problema de la **autenticación Basic**, que es enviar los datos sin encriptar.

El estándar de los métodos **Basic y Digest Authentication** se puede ver en el [RFC 2617](http://tools.ietf.org/html/rfc2617). Anteriormente era el RFC 2069 para definir el digest. Primero vamos a ver el funcionamiento según el 2069 y después una introducción al 2617.

El servidor genera un valor, llamado **_nonce_**, que ha de ser único por cada request. El nonce se usará por el cliente para generar el hash que será enviado al servidor.

En **PHP** puede emplearse _md5()_ para generar el string:

```
$nonce = md5(uniqid());
```

El string que necesita el cliente se denomina **_opaque_**. Es otro string único generado por el servidor y que se espera que sea devuelto por el cliente sin alterarse:

```
$opaque = md5(uniqid());
```

El último string necesario es **_realm_**, una frase que informa sobre el área de acceso al que se intenta acceder. También se usa por el cliente para generar un hash para enviarlo de vuelta al servidor:

```
$realm = 'Acceso privado a ejemplo.com/privado';
```

Todos estos valores se emplean para crear la directiva **WWW-Authenticate** y enviarla como respuesta al cliente:

```
if(empty($_SERVER['PHP_AUTH_DIGEST'])){
    header('HTTP/1.1 401 Unauthorized');
    header(sprintf('WWW-Authenticate: Digest realm="%s", nonce="%s", opaque="%s"', $realm, $nonce, $opaque));
    header('Content-Type: text/html');
    echo '<p>Necesitas autenticarte.</p>';
    exit;
}
```

Cuando el cliente recibe esta respuesta, tiene que computarla y devolver un hash. Lo hace devolviendo el nombre de usuario, el realm y el password agrupados en un hash de la siguiente forma:
```
A1 as md5("username:realm:password")
A2 as md5("requestMethod:requestURI")
El útlimo hash, conocido como "response", md5("A1:nonce:A2")
```

El cliente envía en el header _**Authorization**_:
```
Authorization: Digest username="%s", realm="%s", nonce="%s", opaque="%s", uri="%s", response="%s"'
```

Cuando el servidor recibe la respuesta, se toman los mismos pasos para comprobar la versión del hash del servidor con la recibida en la respuesta. Si coinciden, el request se considera autorizado.

```
$A1 = md5("$username:$realm:$password");
$A2 = md5($_SERVER['REQUEST_METHOD'] . ":$uri");
$response = md5("$A1:$nonce:$A2");
```

Para proceder con la identificación a través de una base de datos, el valor A1 puede guardarse. Si se cambia el realm el hash resultará inválido.

En el **RFC 2617** se incluyeron nuevas características: qop, cnonce y nc.

*   **qop: quality protection**. Especificado en el header WWW-Authenticate, puede tener un valor de "auth" o "auth-int". Si no se especifica ninguno, por defecto es "**auth**", Digest Authentication para autenticación de un cliente. Si se especifica "**auth-int**" se incluye un nivel de protección de integridad en la respuesta, y el cliente debe incluir también el request body como parte del mensaje digest. Esto permite al servidor saber si el request ha sido manipulado entre el cliente y el servidor.
*   **cnonce: client nonce**. Similar a nonce pero es generado por el cliente, que envía en la respuesta al servidor para poder comparar digests. Esto proporciona integridad en la respuesta y autenticación mutua. Cuando la directiva qop se envía por parte del servidor, el cliente debe incluir un valor cnonce.
*   **nc: nounce count**. Es una cuenta hexadecimal del número de requests que el cliente ha enviado con el valor de un nonce. De esta forma el servidor puede protegerse frente a ataques de repetición.

Ahora analizar el A2 quedaría de la siguiente forma:

```
if($qop == 'auth-int'){
    $A2 = md5($_SERVER['REQUEST_METHOD'] . ":$uri:" . md5($respBody));
} else {
    $A2 = md5($_SERVER['REQUEST_METHOD'] . ":$uri");
}
$response = md5("$A1:$nonce:$nc:$cnonce:$qop:$A2");
```

Digest Access emplea un valor hashed y no el password legible, por lo que es mucho más seguro que Basic Auth. El nonce del servidor, que ha de ser único por cada request, cambiará el hash computado en cada request nuevo, y el valor nc que proporciona la RFC 2617 ayuda a prevenir **ataques de repetición**, que ocurren cuando un atacante intercepta los datos del request e intenta repetirlos como si fuera su propio request.

MD5 no es un algoritmo especialmente seguro, con la tecnología moderna no es difícil obtener los valores de un hash md5\. Es preferible Bcrypt.

No hay forma de que el servidor pueda verificar la identidad del cliente que solicita acceso con Digest Access, lo que deja abierta la posibilidad de ataques "man in the middle", donde un cliente cree que va a enviar los datos de autenticación al servidor pero envía la información a otra entidad desconocida.

La mejor seguridad para la autenticación es utilizar **SSL y encriptar las contraseñas con Bcrypt**. Se puede utilizar Basic Authentication con SSL, pero en situaciones donde SSL no es posible, Digest Access es mucho mejor que Basic Auth.