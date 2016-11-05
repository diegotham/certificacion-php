El **protocolo HTTP** (_Hypertext Transfer Protocol_) es la base de la web. Se utiliza cada vez que se transfiere un documento o se hace un AJAX request. 

**REST** es una forma simple de organizar interacciones entre sistemas independientes. REST te permite trabajar de forma sencilla con clientes con diferentes sistemas operativos y plataformas como smartphones. En principio no está atado a la web, pero casi siempre se implementa en ella, ya que se fundamenta en HTTP.

Existen alternativas como SOAP pero son más complejas ya que hay que aprender nuevas convenciones y no se exprime al máximo la capacidad de HTTP. **REST está inspirado en HTTP** y utiliza todo su potencial, siendo una buena forma de ver y **entender como funciona HTTP**.

**Indice de contenido**

| | |
| -------- | -------- |
| 1. Introducción a HTTP | 5. Librerías HTTP client |
| 2. URLs | 6. Códigos de respuesta |
| 3. Métodos HTTP | 7. Ejemplo de aplicación en REST |
| 4. Representación de resources | | 

### 1. Introducción a HTTP

**HTTP** es un **protocolo** que permite el **envío y recibo de documentos a través de la web**. Un protocolo es un conjunto de normas que determina qué mensajes se pueden intercambiar y cómo deben ser. 

En HTTP hay dos roles diferentes: **servidor** y **cliente**. En general, el cliente siempre inicia la conversación y el servidor responde. **HTTP se basa en texto**, los mensajes son básicamente bits de texto, aunque el body del mensaje puede contener otros formatos. El hecho de que sea textual lo hace **fácil de monotorizar**.

Los **mensajes HTTP** están compuestos de _**headers**_ y de un _**body**_. El body puede ir vacío, contiene datos que se pueden transmitir por la red en función de las instrucciones de los headers. Los headers contienen metadatos, como información sobre la codificación de los mensajes. En el caso de un request, también contiene **métododos HTTP**.

En REST, los _headers_ son más importantes que el propio _body_.

Con los navegadores como **Chrome** (con las herramientas de desarrollo) o **Firefox** (con la extensión _firebug_), se pueden ver los detalles de los mensajes HTTP mientras navegas.

![Headers HTTP](/uploads/articulos/headers-http.png)

Otra forma de familiarizarse con HTTP es utilizar un cliente dedicado, como cURL. **cURL es una herramienta de línea de comandos** disponible en la mayoría de sistemas operativos:
```
curl -v google.com
```

Esto muestra la **conversación HTTP**. Los requests van precedidos de > y los responses de <.

### 2. URLs

Las **URLs** son la forma de identificar los recursos sobre los que se quiere operar. Cada URL identifica un _**resource**_. Son las mismas URLs asignadas a las páginas web, de hecho, una página web es un tipo de _**resource**_. Vamos a considerar una URL del ejemplo que vendrá después:
```
/clientes
```

Identificará una lista de clientes, y 
```
/clientes/marta
```

Identificará un cliente, llamado Marta, suponiendo que es la única con ese nombre en la lista de clientes.

En estos ejemplos no se incluye el **hostname** en la **URL**, pues es irrelevante para ver **cómo se organiza la interfaz**. Es importante en cambio para asegurar que el **identificador del resource** es **único en toda la web**. El **host** se incluye en el header de forma separada a la ruta del _**resource**_, que viene en la parte de arriba del _request_:
```
GET /clientes/marta HTTP/1.1

Host: ejemplo.com
```

Los _**resources**_ son entendidos como nombres. Lo siguiente no sería **RESTful**:
```
/clientes/anadir
```

Ya que utiliza una **URL para describir una acción**. Este sería un punto fundamental en la distinción entre sistemas **RESTful** y **no-RESTful**.

Las URLs deben ser tan precisas como sea posible. Cualquier cosa necesaria para **identificar de forma única un resource debería estar en la URL**. No se necesita incluir datos para identificar el resource en el request, de esta forma las URLs actúan como un mapa de todos los datos que la aplicación maneja.

Para especificar la acción, en lugar de emplear la URL se emplean los **métodos HTTP**.

### 3. Métodos HTTP

Cada request especifica un verbo o método HTTP en el header. Este es la primera palabra en mayúsculas que aparece en cualquier header:
```
GET / HTTP/1.1
```

utiliza el método GET, mientras que
```
DELETE /clientes/marcos HTTP/1.1
```

utiliza el método DELETE.

Los **verbos HTTP** le dicen al servidor **qué hacer con los datos identificados en la URL**. El request puede opcionalmente contener información adicional en el body, que podría requerir realizar alguna operación, como guardar datos en el _**resource**_. Se pueden proporcionar estos datos en cURL con la opción -d.

Para construir una API RESTful los verbos más importantes son: GET, POST, PUT y DELETE. Hay otros métodos disponibles, como HEAD y OPTIONS, pero no son muy utilizados.

#### **Método GET**

GET es el método más simple de HTTP. Es el que utilizan los navegadores cada vez que se hace click en un enlace o se escribe una URL en la barra de navegación. Le dice al servidor que transmita los datos identificados por la URL al cliente. Los datos nunca deberían ser modificados en el lado del servidor con un GET request. Un GET request es sólo de lectura, pero una vez que el cliente recibe los datos, puede hacer cualquier operación, como darle formato para mostrarlo.

#### **Método PUT**

Un **PUT request** se utiliza cuando se quiere crear o editar el _resource_ identificado por la URL:
```
PUT /clientes/maria
```

podría crear un cliente llamado María en el servidor. No hay nada en el request que informe al servidor como se deben crear los datos, sólo que debe crearlos. Esto permite poder **cambiar la tecnología del backend** si en algún momento es necesario. Los **PUT requests** contienen los datos a usar a la hora de actualizar o crear un _resource_ en el body. En **cURL** se pueden **añadir datos al request con -d**:
```
curl -v -X PUT -d "Texto"
```

#### **Método DELETE**

El método DELETE hace lo contrario que PUT, se usa cuando se quiere eliminar un _resource_ identificado por la **URL del request**.
```
curl -v -X DELETE /clientes/maria
```

Esto eliminará todos los datos asociados con el _resource_.

#### **Método POST**

POST se emplea cuando el proceso que se quiere que ocurra pueda ser repetido si el POST se repite (esto es, que no es **idempotente**). Además los **POST requests** deben procesar el request body como subordinado de la URL a la que se está enviando:
```
POST /clientes/
```

no debería modificar el recurso _/clientes/_ sino un recurso cuya URL contiene _/clientes/_. Por ejempo, pueden añadir un nuevo cliente a la lista, con un id generado por el servidor:
```
/clientes/id-unico
```

Los PUT request pueden usarse en lugar de los POST y viceversa. Algunos sistemas utilizan sólo uno, y otros los dos, incluso para emplear POST para actualizar y PUT para crear.

A menudo, los POST requests se usan para disparar operaciones en el servidor que no cuajan con el paradigma Create/Update/Delete, aunque esto va más allá de REST. En este ejemplo vamos a emplear PUT.

#### **Clasificación de métodos HTTP**

*   **Métodos seguros e inseguros**: los métodos seguros son los que nunca modifican _resources_. El único seguro listado en este artículo es GET.
*   **Métodos idempotentes**: obtienen el mismo resultado independientemente del número de veces que se repita el request: GET, PUT y DELETE. El único método no idempotente es POST. PUT y DELETE parece raro que lo sean pero aquí está la explicación: repetir un método PUT con exactamente el mismo body modifica un _resource_ de forma que permanece idéntico al descrito en el PUT request anterior, por lo que nada cambia. De la misma forma, no tiene sentido con DELETE eliminar un _resource_ dos veces. Dicho esto, no importa cuantas veces se utilice PUT y DELETE, el resultado será el mismo como si sólo se hubiera hecho una vez.

Es importante utilizar el método correcto en la situación adecuada, lo que depende totalmente del desarrollador.

### 4. Representación de _resources_

El **cliente HTTP** y el **servidor HTTP** intercambian información sobre los _resources_ identificados por las URLs.

El _**request**_ y el _**response**_ contienen una **representación del _resource_**. Por representación se entiende **información en un formato específico**, sobre el estado del _resource_ o sobre cómo estará el estado en el futuro. Tanto los headers como el body son parte de esa representación.

Los **headers HTTP**, que contienen **metadatos**, están definidos por el **HTTP spec**. Sólo pueden contener texto y están formateados de una manera específica.

El _**body**_ puede contener datos en cualquier formato, y aquí es donde se puede ver el poder de **HTTP**. Se pueden enviar textos, imágenes, HTML y XML en cualquier idioma. A través de los **metadatos del request de diferentes URLs**, se puede elegir entre diferentes representaciones del mismo _resource_. Por ejemplo se puede enviar una página web a los navegadores y un JSON a las aplicaciones.

La **respuesta HTTP** debería especificar el _**content type**_ del _**body**_. Esto se realiza en el header, en el campo Content-Type:
```
Content/Type: application/json
```

Por simplicidad, en el ejemplo de este artículo sólo envía y recibe **JSON**, pero la aplicación debería estar estructurada de forma que sea posible cambiar el formato de los datos para ajustarlo a **plataformas** diferentes o a la **preferencia de los clientes**.

### 5. Librerías HTTP client

Para **experimentar con los diferentes métodos _request_**, es necesario un cliente que permita especificar que método usar. Los formularios HTML sólo permiten hacer GET y POST requests, por lo que no sirven para probar los métodos. En un entorno real se accede a las APIs de forma programada a través de una aplicación cliente separada, o a través de **JavaScript** en el navegador.

Esta es la razón por la que, además de conocer el lado del **servidor HTTP**, es esencial tener un conocimiento de las **capacidades del cliente HTTP** en el lenguaje que se vaya a utilizar.

Una librería HTTP client muy popular es cURL. Además de proporcionar una herramienta en la línea de comandos, se puede usar en diferentes lenguajes, y suele ser la opción elegida entre los **desarrolladores PHP**. Otros lenguajes como **Python** ofrecen **librerías HTTP client nativas**.

### 6. Códigos de respuesta

La función _header()_ envía los **headers HTTP** y asegura que estén formateados adecuadamente. Los headers han de ser lo primero en el response, no se ha de escribir nada antes de un header. A veces el HTTP server puede estar configurado para incluir otros headers además de los que se puedan especificar en el código.

Los headers contienen todo tipo de metadatos, como la codificación de texto utilizada en el mensaje del body, o el MIME type del contenido del body. En este caso estamos especificando explícitamente los códigos de respuesta HTTP. Los códigos de respuesta estandarizan una forma de informar al cliente acerca del resultado de su _**request**_. Por defecto, PHP devuelve un código de respuesta 200, que significa que ha sido satisfactoria.

El servidor debe enviar el **código de respuesta HTTP más apropiado**. De esta forma el cliente puede intentar reparar sus errores, suponiendo que haya alguno. 

El significado de un código de respuesta HTTP no es muy preciso, por eso HTTP es considerado algo genérico, pero se debe intentar usar el código de respuesta que más concuerde con la situación.

Los códigos de respuesta más comúnmente utilizados con REST son:

*   **200 OK**. Satisfactoria.
*   **201 Created**. Un _resource_ se ha creado. Respuesta satisfactoria a un _request_ POST o PUT.
*   **400 Bad Request**. El _request_ tiene algún error, por ejemplo cuando los datos proporcionados en POST o PUT no pasan la validación.
*   **401 Unauthorized**. Es necesario identificarse primero.
*   **404 Not Found**. Esta respuesta indica que el _**resource**_ requerido no se puede encontrar (La URL no se corresponde con un _resource_).
*   **405 Method Not Allowed**. El **método HTTP** utilizado no es soportado por este _resource_. 
*   **409 Conflict**. Conflicto, por ejemplo cuando se usa un PUT request para crear el mismo resource dos veces.
*   **500 Internal Server Error**. Un error 500 suele ser un error inesperado en el servidor.

### 7. Ejemplo de aplicación en REST

Este ejemplo de aplicación en **REST** es muy sencillo, en un servidor con **PHP5** (versión mínima 5.2 para las funciones _json_encode()_ y _json_decode()_).

El servidor es **Apache** (aunque puede elegirse otro), y se ha de tener activado _mod_rewrite_, además de implementar una configuración que podrá incluirse en el archivo de configuración o mediante _.htaccess_. De esta forma el archivo _server.php_ responderá a todos los _requests_ que vengan del servidor.

Todos los _requests_ de las URL que comienzan con /clients/ deben ir enrutadas en el archivo _server.php_.

#### **Funcionamiento de la aplicación**

La aplicación se encuentra en un archivo _server.php_, tan sólo es una clase con 8 métodos y un array. También se incluye la **configuración de Apache** en un archivo _apache.conf_. Se puede descargar de [aquí](http://cdn.tutsplus.com/net/uploads/legacy/851_rest/code.zip). El archivo de configuración sólo es un redirect de la home a _server.php_, que puede también configurarse con un archivo _.htaccess_. En el enlace de descarga el archivo de configuración son dos líneas, pero no funcionará a no ser que lo cambies por esto:

```
RewriteEngine On
RewriteBase /
options +FollowSymLinks
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule .* server.php/$0 [L]
```

Existen dos aspectos fundamentales para procesar los requests de forma **REST**. El primero es iniciar un proceso diferente en función del **método HTTP**, incluso cuando las URLs son las mismas. En **PHP** está el array global **$_SERVER**, que determina que método se ha utilizado para el request.

```
$_SERVER['REQUEST_METHOD']
```

Esta variable contiene el nombre del método en un string, ya sea GET, PUT...

La otra clave es saber que URL se ha solicitado. Para ello se utiliza otra variable:

```
$_SERVER['REQUEST_URI']
```

Esta variable contiene la URL desde la primera /. Si el host es 'ejemplo.com', 'http://ejemplo.com/' devolverá '/', y 'http://ejemplo.com/prueba/' devolverá '/prueba/'.

Primero se averigua **que URL ha sido solicitada**, considerando sólo las URL que empiezan con _clients_.

```
$resource = array_shift($paths);
if($resource == 'clients'){
    $name = array_shift($paths);
    if(empty($name)){
        $this->handle_base($method);
    } else {
        $this->handle_name($method, $name);
    }
} else {
    // Sólo se aceptan resources desde 'clients'
    header('HTTP/1.1 404 Not Found');
}
```

Sólo hay dos posibilidades:

1.  Si el resource es _clients_, se devuelve una lista de los clientes.
2.  Si se proporciona un identificador más, se supone que es el **nombre del cliente**, por lo que se envía a una función en concreto **dependiendo del método HTTP**. Para ello utilizamos un switch (que no se recomienda en una aplicación real):

```
switch($method){
    case 'PUT':
        $this->create_contact($name);
        break;
    case 'DELETE':
        $this->delete_contact($name);
        break;
    case 'GET':
        $this->display_contact($name);
        break;
    default:
        header('HTTP/1.1 405 Method not allowed');
        header('Allow: GET, PUT, DELETE');
        break;
}
```

#### **Utilizando la aplicación**

Empleamos **cURL** para todas las operaciones. 

Si queremos los **detalles del cliente** 'jim', enviamos un GET request a la URL de este _resource_:
```
curl -v http://localhost:80/clients/jim
```

Esto mostrará los _headers_. La última línea de respuesta será el _body_. En este caso será en **JSON** que contiene la dirección de Jim.

Para **obtener la información de todos los clientes** de vez:
```
curl -v http://localhost:80/clients/
```

Para **crear un nuevo cliente** Marta:
```
curl -v -X PUT http://localhost:80/clients/marta -d '{"address":"Calle Hispanidad" }
```

Y finalmente para **eliminar un cliente**:
```
curl -v -X DELETE http://localhost:80/clients/anne
```

Si se solicita un cliente que no existe, devuelte un **error 404**, en cambio, si se intenta crear un cliente que ya existe devolverá un **error 409**.

#### **Conclusión**

**HTTP** se creó para la comunicación entre sistemas que no comparten nada mas que un entendimiento del **protocolo**. En general, cuantas menos suposiciones se hagan en relación a HTTP, mejor, será más probable que otros programas y APIs puedan acceder a la aplicación.