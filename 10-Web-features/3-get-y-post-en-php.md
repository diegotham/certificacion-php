Existen dos métodos con los que el navegador puede enviar información al servidor: 

*   Método HTTP GET. Información se envía de forma visible
*   Método HTTP POST. Información se envía de forma no visible

Antes de que el navegador envíe la información proporcionada, la codifica mediante **URL encoding**, dando como resultado un **Query String**. Esta codificación es un esquema de _keys_ y _values_ separados por un **ampersand &**:

```
key1=value1&key2=value2&key3=value3...
```

Los **espacios** y otros **caracteres no alfanuméricos** se sustituyen. Una vez que la información es codificada, se envía al servidor.

### Método HTTP GET

El **método GET** envía la información codificada del usuario en el _header_ del **HTTP request**, directamente en la [**URL**](http://diego.com.es/introduccion-a-rest-en-php#URLs). La página web y la información codificada se separan por un interrogante ?:

```
www.ejemplo.com/index.htm?key1=value1&key2=value2&key3=value3...
```

*   El método GET envía la información en la propia URL, estando limitada a 2000 caracteres.
*   La información es visible por lo que con este método nunca se envía **información sensible**.
*   No se pueden enviar **datos binarios** (archivos, imágenes...).
*   En **PHP** los datos se administran con el array asociativo $_GET. 

Ejemplo sencillo de **formulario html** con el **método GET**:

```
<html>
<body>
<form action="formget.php" method="get">
    Nombre: <input type="text" name="nombre"><br>
    Email: <input type="text" name="email"><br>
    <input type="submit" value="Enviar">
</form>
Hola <?php isset($_GET["nombre"]) ? print $_GET["nombre"] : ""; ?><br>
Tu email es: <?php isset($_GET["email"]) ? print $_GET["email"] : ""; ?>
</body>
</html>
```

La **url** que resulta de hacer click en **submit** es de la forma:

formget.php?nombre=peter&email=peter%40ejemplo.com

En este caso @ es un **carácter especial** y se codifica.

### Método HTTP POST

Con el método **HTTP POST** también se codifica la información, pero ésta se envía a través del **body** del **HTTP Request**, por lo que no aparece en la URL.

*   El método POST no tiene **límite de cantidad de información** a enviar.
*   La información proporcionada no es visible, por lo que se puede enviar **información sensible**.
*   Se puede usar para enviar **texto normal** así como **datos binarios** (archivos, imágenes...).
*   PHP proporciona el array asociativo $_POST para acceder a la información enviada.

```
<html>
<body>
<form action="formpost.php" method="post">
    Nombre: <input type="text" name="nombre"><br>
    Email: <input type="text" name="email"><br>
    <input type="submit" value="Enviar">
</form>
Hola <?php isset($_POST["nombre"]) ? print $_POST["nombre"] : ""; ?><br>
Tu email es: <?php isset($_POST["email"]) ? print $_POST["email"] : ""; ?>
</body>
</html>
```

Se puede comprobar que la información no se muestra en la **url**.