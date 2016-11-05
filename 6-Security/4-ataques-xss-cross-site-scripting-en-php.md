**Indice de contenido**

1.  ¿Qué es Cross-Site Scripting?
2.  Ejemplo de ataque XSS
3.  Tipos de ataques XSS
4.  Prevenir ataques XSS
5.  Ejemplo de prevención contra ataques XSS

### 1. ¿Qué es Cross-Site Scripting?

**XSS** ocurre cuando un atacante es capaz de inyectar un **script**, normalmente **Javascript**, en el _**output**_ de una aplicación web de forma que se ejecuta en el **navegador del cliente**. Los ataques se producen principalmente por **validar incorrectamente datos de usuario**, y se suelen inyectar mediante un **formulario web** o mediante un **enlace alterado**. 

Los desarrolladores muchas veces subestiman el potencial peligro que conlleva este tipo de ataques. Si un atacante puede inyectar Javascript en el output de una aplicación web y ejecutarlo, podrá **ejecutar cualquier código Javascript en el navegador de un usuario**. Algunos de los **objetivos que se quieren conseguir con ataques XSS** son: robar cookies y sesiones de usuarios, modificar el sitio web, realizar HTTP requests con la sesión del usuario, redireccionar a usuarios a sitios dañinos, atacar al navegador o instalar malware, reescribir o manipular extensiones de navegador, etc. 

Desde la **perspectiva del navegador**, el script es originado por la aplicación web por lo que se asume como fuente fiable. Esta es una de las causas que permiten que estos ataques se puedan llevar a cabo.

### 2. Ejemplo de ataque XSS

Supongamos por ejemplo el siguiente **formulario**:

```
<form action="post.php" method="post">
    <input type="text" name="comment" value="">
    <input type="submit" name="submit" value="Submit">
<form>
```

En el formulatio hay una **caja de texto** para datos de entrada y un **botón de enviar**. Una vez que el formulario es enviado, enviará los datos a _post.php_ para procesarlos. Supongamos que lo único que se hará con los datos en post.php será mostrarlos con un echo:

```
echo $_POST['comment'];
```

Sin ningún tipo de **filtrado**, el atacante puede enviar el siguiente script a través del formulario, lo que generará un popup en el navegador con el mensaje "Hackeado":

```
<script>alert("hacked")</script>
```

El script anterior tan sólo envía un mensaje de alerta, pero con Javascript ya hemos visto que se pueden hacer multitud de cosas que pueden dañar al usuario, especialmente relacionadas con el **robo de cookies y sesiones**.

### 3, Tipos de ataques XSS

Los **ataques Cross-Site Scripting** pueden agruparse en dos grandes categorías, dependiendo de **cómo envían el código malicioso**: non-persistent XSS y persistent XSS. 

*  Non-persistent XSS

Los ataques **non-persistent XSS** o **reflected XSS** no almacenan el código malicioso en el servidor sino que lo pasan y presentan directamente a la víctima. Es el método más popular de ataque XSS. El ataque se lanza desde una fuente externa, mediante email o un sitio de terceros.

```
// Palabra de búsqueda
echo "Has buscado la palabra: " . $_GET["query"];
// Resultados de búsqueda ...
// ...código...
```

El código anterior sería de un **motor de búsquedas**, donde la palabra que se ha buscado se devuelve a presentar al usuario. El problema es que la variable **$_GET["query"]** no es validada o escapada, por lo que un atacante podría enviar el siguiente link a la víctima:

```
http://example.com/search.php?query=<script>alert("hackeado")</script>
```

*  Persistent XSS

El **código malicioso** ya ha superado la barrera del proceso de validación y está almacenado en un **almacén de datos**. Puede ser un **comentario**, un archivo **log**, un mensaje de **notificación**, o cualquier otro tipo de sección del sitio web que solicita algún _**input**_ al usuario. Cuando esta información en particular se presenta en el sitio web, el código malicioso se ejecuta.

Si suponemos un sistema de comentarios en el que los comentarios se almacenan en un archivo llamado comentarios.txt:

```
file_put_contents("comentarios.txt", $_POST["comentario"], FILE_APPEND);
```

En cualquier parte del sitio web donde se vayan a mostrar los comentarios:

```
echo file_get_contents("comentarios.txt");
```

Cuando el atacante envía un comentario, inserta código malicioso en comentarios.txt. Cuando los comentarios se muestran, éste código se ejecutará sin ser validado o escapado de ninguna forma.

### 4. Prevenir ataques XSS

Tan fácil como un atacante puede atacar un sitio web no protegido contra **ataques Cross-Site Scripting**, un desarrollador puede defenderse de éstos. La prevención ha de tenerse siempre en cuenta incluso antes de escribir el propio código.

La regla o política más básica que ha de tenerse siempre en cuenta es simple: **NUNCA confíes de datos que vienen de usuarios o de cualquier otra fuente externa**. Cualquier dato debe ser validado o escapado para su _**output**_. 

Las medidas a tomar se pueden dividir en tres: **data validation**, **data sanitization** y **output escaping**.

*  Data validation

La **validación de datos** es el proceso de asegurarse que tu aplicación analiza el tipo de datos correctos. Si tu script PHP espera un _**integer**_ de un input, cualquier otro tipo de dato debe de rechazarse. Cada dato debe ser validado cuando se recibe para asegurarse que es del **tipo correcto**, y rechazado si no pasa ese **proceso de validación**.

Si quieres validar un número de teléfono, por ejemplo, deberás rechazar cualquier string que contenga letras, porque sólo consistirá en dígitos. También puedes tener en cuenta la longitud que deberán tener estos dígitos. Siendo más permisivo, se pueden aceptar algunos otros símbolos como +, (), y - que a veces se utilizan al indicar números de teléfono:

```
// Comprobar un número de teléfono en Estados Unidos:
$telefono = '1-909-466-4344';
if (preg_match('/^((1-)?\d{3})-\d{3}-\d{4}/', $telefono)){
    echo "El teléfono $telefono es válido";
} else {
    echo "El teléfono $telefono NO es válido";
}
```

*  Data sanitization

La **sanitización de datos** se centra en manipular los datos para asegurarse que son seguros, eliminando cualquier parte indeseable y normalizándolos en la forma correcta. Por ejemplo, si se espera un texto _**string**_ de los usuarios, puedes querer evitar cualquier tipo de markup HTML:

```
// Sanitizar comentario de usuario
$comentario = strip_tags($_POST["comentario"]);
```

A veces la **validación de datos** y su **sanitización** pueden ir de la mano.

```
$telefono = "1234567";
$telefono = preg_replace('/[^\d]/', "", $telefono);
$length = strlen($telefono);
if ($length = 7 || $length = 10 || $length = 11){
    echo "$telefono es un formato válido";
}
```

*  Output escaping

Para proteger la integridad de los **datos que se devuelven**, el _**output data**_, se debe **escapar** cualquier dato que se devuelve al usuario. Esto evita que el navegador malinterprete alguna **secuencia especial de caracteres**: 

```
echo "Has buscado la palabra: " . htmlspecialchars($_GET["query"]);
```

Puede emplearse también la función _**htmlentities()**_. La diferencia entre ambas es que _**htmlspecialchars()**_ sólo traduce los símbolos **&**, **""**, **''**, **<** y **>** en **entidades HTML**, en cambio _htmlentities()_ traduce todos los caracteres posibles que tengan su equivalencia en HTML. Normalmente vale con _htmlspecialchars()_ a no ser que uses algún tipo de codificación diferente a **ISO-8859-1** o **UTF-8**. 

### 5. Ejemplo de prevención contra ataques XSS

Mezclando un poco las tres **formas de prevenir ataques XSS**, vamos a ver un sencillo sistema de comentarios:

```
// Validar el comentario
$comentario = trim($_POST["comentario"]);
if(empty($comentario)){
    exit("Debes proporcionar un comentario");
}
// Sanitizar comentario
$comentario = strip_tags($comentario);
// El comentario ya se puede guardar de forma segura
file_put_contents("comentarios.txt", $comentario, FILE_APPEND);
// Escapar comentarios antes de mostrarlos
$comentarios = file_get_contents("comentarios.txt");
echo htmlspecialchars($comentarios);
```

Primero nos aseguramos de que no se guardan **comentarios vacíos**. Después se sanitizan los datos eliminando cualquier posible **etiqueta HTML** que pudiera contener. Finalmente, los comentarios se devuelven **filtrados**. La función _strip_tags_ hace que no sea posible insertar enlaces en los comentarios, ya que éstos utilizan una etiqueta <a> que será eliminada. Para que puedan insertarse se puede utilizar _htmlentities_ o _htmlspecialchars_ en su lugar.

Hay que tener en cuenta que ninguna solución es fiable al 100%, y que es conveniente estar al tanto de novedades respecto a los **ataques Cross-Site Scripting** ya que van evolucionando a medida que lo hacen las plataformas que los facilitan (navegadores, HTML...).