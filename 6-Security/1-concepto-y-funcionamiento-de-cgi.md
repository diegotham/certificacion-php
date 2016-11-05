**Indice de contenido**

1.  ¿Qué es CGI?
2.  Cómo funciona CGI
3.  Datos de entrada o input
4.  Datos de salida o output

### 1. ¿Qué es CGI?

**CGI** significa _**Common Gateway Interface**_ ("Interfaz de Entrada Común"), o lo que es lo mismo, _**Application Programming Interface**_. CGI no es ningún lenguaje de programación, sino una **API** de servidor web. Se trata de un **sistema de comunicación** que le dice al servidor web cómo enviar y recibir datos de una **aplicación de servidor** a un **cliente**. Esto permite a los servidores usar **aplicaciones de servidor** para realizar funciones concretas que añaden mayor interactividad a los sitios web, como formularios, acceso a bases de datos, login de usuarios, chats, etc. Fue una de las primeras formas realizar sitios web dinámicos en Internet (actualmente se utiliza una variante mucho más rápida, [Fast-CGI](http://www.fastcgi.com/drupal/)).

La mayoría (o todos) los servidores web se pueden configurar para **ejecutar una aplicación o programa CGI** (_utilizo programa o aplicación CGI indistintamente_). Esto significa que al recibir una petición, el servidor reenviará los datos a un programa CGI específico, configurando algunas variables de entorno y ordenando los parámetros para que el programa pueda saber dónde y qué buscar. Los **programas CGI** se ejecutan en el servidor web en cualquier lenguaje que pueda procesar **variables de entorno** y **standard input** (**_stdin_**) y escribir en **standard output** (_**stdout)**_.

El servidor web interactuará con los programas CGI utilizando el estándar **Common Gateway Interface** establecido en el [RFC 3875](http://www.ietf.org/rfc/rfc3875). Estos programas pueden ser interpretados o compilados. Si son **interpretados**, como **Ruby**, **Perl**, **Python** o **PHP**, el código escrito ha de **traducirse por un intérprete** para que el servidor lo entienda **cada vez que se ejecute el programa**, instrucción por instrucción. Si son **compilados**, como **C** o **Visual Basic**, el código fuente se traduce por un compilador a un **archivo ejecutable** que el servidor entiende. Con ese archivo se puede ejecutar el programa las veces que sea necesario sin tener que repetir el proceso (lo que reduce el tiempo de espera entre ejecución y ejecución). Otros como **Java** son casos particulares ya que utilizan una máquina virtual para traducir el código fuente, por lo que se le podría considerar un "híbrido". Los **lenguajes interpretados** son más comunes en el **desarrollo web** y los compilados más en el **software de escritorio**, ya que estos últimos requieren mayores recursos.

### 2. Cómo funciona CGI

Un **servidor** espera a que un cliente solicite un archivo mediante un _**request**_. Este archivo puede ser un **documento HTML**, una **imagen**, o cualquier otro tipo de archivo. A pesar de que los sitios web suelen consistir en varios archivos juntos, como por ejemplo imágenes, el servidor envía sólo un archivo durante una sesión. El cliente debe iniciar **una nueva sesión por cada archivo que solicite**, por lo que los servidores y clientes están preparados para manejar varias sesiones de vez.

No es necesario que sea un archivo lo que se solicita mediante el _**request**_. Todo lo que se transfiere son datos, por lo que puede ser cualquier tipo de [_resource_](https://en.wikipedia.org/wiki/Web_resource).

El servidor y el cliente normalmente funcionan en diferentes **computadoras**, y es probable que también lo hagan con diferentes **sistemas operativos**. El cliente simplemente solicita un _**resource**_ y no le preocupa de dónde consigue los datos el servidor. Éste tan sólo lee los datos que recibe y los envía al cliente, por lo que sólo envía **datos estáticos**, el servidor no modifica dinámicamente los datos.

**CGI** está diseñado precisamente para poder tratar con **datos dinámicos** extendiendo la funcionalidad del servidor, de la misma forma que una extensión de PHP extiende la funcionalidad del **lenguaje PHP**. De esta forma puedes escribir un programa que genera **datos dinámicos** que irán directamente al cliente en lugar de un **archivo simple**.

A la hora de escribir un programa, tan sólo hay que escribir en **standard output** (_**stdout**_), y el servidor se encargará de hablar con el navegador, sin que el servidor tenga que saber de que tipo de datos se trata.

### 3. Datos de entrada o input

**La web no es interactiva**, esto es, el programa CGI no puede solicitar al cliente input, procesarlo, enviar output, pedir de nuevo más input... Por eso CGI es simple: recibe _**input**_ del cliente y envía _**output**_ como respuesta. 

El navegador recibe input mediante **formularios HTML**, que pueden realizarse mediante los métodos **GET** y **POST**. Con el método GET se envía información mediante URL, el método POST envía mediante _**stdin**_, lo cual tiene varias ventajas respecto a GET:

*   Puedes enviar **más cantidad de datos** (URL está limitado).
*   Los datos no se registran durante el proceso, como por ejemplo _**passwords**_. Si es mediante URL se deja rastro en los sistemas por los que vaya pasando la información.
*   Los datos no aparecen en la **barra del navegador**, como ocurre con las URL, por lo que no son visibles.

Antes de que el servidor cargue el programa CGI, establece varias **variables de entorno** que pueden analizarse para ver cuánto **input** se recibe y de dónde viene (si de _**URL**_ o _**stdin**_). Una de estas variables de entorno es **REQUEST_METHOD**, y su valor puede ser **GET** o **POST**, entre otros.

En el caso de **POST**, la variable **CONTENT_LENGTH** indica cuantos **bytes de datos** se leen desde _**stdin**_. La variable **CONTENT_TYPE** indica si los datos vienen desde un **formulario** u otra fuente. Una lista con las variables disponibles además de mucha más información sobre CGI puede encontrarse [aquí](http://www.mnuwer.dbasedeveloper.co.uk/dlearn/web/session03.htm).

### 4. Datos de salida o output

Una vez que se han leído los datos, se pueden procesar y enviar el output a _**stdout**_. Estos datos de salida pueden ser en formato **HTML** o cualquier otro, como **GIF**. Los programas CGI pueden devolver cualquier tipo de output. Es por esto por lo que es necesario decirle al cliente que tipo de datos se van a enviar. Si son en formato HTML, se hace mediante el string _**Content-type: text/html**_.

Puede utilizarse PHP como CGI pero lo normal es hacerlo mediante **módulo de servidor**, que es más rápido y más seguro.