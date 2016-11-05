**Indice de contenido**

| | |
| -------- | -------- |
| 1. Introducción | 4. Validación |
| 2. GET y POST | 5. Subida de archivos |
| 3. Tipos de elementos | 6. Subida múltiple de archivos |

### 1. Introducción

Un formulario sencillo con dos campos a rellenar y un botón para enviar en **HTML** es de la siguiente forma:

```
<html>
<body>
<form action="formpost.php" method="post">
    Nombre: <input type="text" name="nombre"><br>
    Email: <input type="text" name="email"><br>
    <input type="submit" value="Enviar">
</form>
</body>
</html>
```

Cuando un usuario rellena los datos y hace click en enviar, los datos del formulario se envían para ser procesados al archivo _formpost.php_. Los datos se envían a través de HTTP con el método **HTTP Post**.

Una vez los datos están en _formpost.php_ pueden mostrarse, guardarse en una base de datos o lo que se necesite. En _formpost.php_ vamos a mostrarlos con echo:

```
<html>
<body>
Hola <?php echo $_POST["nombre"]; ?><br>
Tu email es: <?php echo $_POST["email"]; ?>
</body>
</html>
```

Lo mismo se puede conseguir con el método **HTTP GET**:

```
<html>
<body>
<form action="formget.php" method="get">
Nombre: <input type="text" name="nombre"><br>
Email: <input type="text" name="email"><br>
    <input type="submit" value="Enviar">
</form>
</body>
</html>
```

Ahora el archivo donde recibimos los datos es _formget.php_:

```
<html>
<body>
Hola <?php echo $_GET["nombre"]; ?><br>
Tu email es: <?php echo $_GET["email"]; ?>
</body>
</html>
```

Estos han sido dos ejemplos muy sencillos para ver el **funcionamiento básico de un formulario**. Siempre que se crean formularios hay que tener muy en cuenta la **seguridad**, y para ello se utiliza la [validación de formularios](#Validacion).

Las _superglobals_ $_GET y $_POST sirven para coleccionar datos de formularios, y vamos a ver las diferencias a continuación.

### 2. GET y POST

**$_GET** y **$_POST** son variables superglobales que forman arrays de _keys_ y _values_, donde los _keys_ son los nombres del formulario (atributo "_name_") y los _values_ son los datos de entrada de los usuarios. El hecho de que sean _**superglobals**_ hace que sean accesibles desde el script independientemente del ámbito.

**$_GET** es un array de variables que se pasan al script a través de los **parámetros de URL**. La información que se envía es visible para todo el mundo y tiene limitada la cantidad de información que se puede enviar a 2000 caracteres. Las URLs con los datos enviados pueden guardarse en marcadores, lo que puede ser útil en ciertos casos. $_GET sólo se emplea para el envío de información reducida y no sensible.

**$_POST** es un array de variables que se pasan al script a través del método **HTTP POST**. La información que se envía no es visible para los demás ya que los nombres y variables van embebidas en el _**body**_ del **HTTP request**. No tiene límites en la cantidad de información a enviar. Soporta datos de entrada _**multi-part binary**_ a la hora se subir archivos al servidor (lo que permite [subir múltiples archivos a la vez](#SubidaMultipleDeArchivos)). 

### 3. Tipos de elementos

Los 10 **tipos de elementos más importantes para los formularios HTML con PHP** son:

| | | 
| -------- | -------- |
| **Elemento** | **Descripción** |
| input type="text" | Caja de texto |
| input type="password" | Caja de texto donde se muestran asteriscos en lugar de los caracteres escritos |
| input type="checkbox" | Cajas seleccionables que permite escoger múltiples opciones |
| input type="radio" | Cajas seleccionables en grupos que sólo permiten escoger una opción  |
| input type="submit" | Botón para enviar el formulario |
| input type="file" | Cajas de texto y botón que permite subir archivos |
| input type="hidden" | Elemento escondido. Especialmente útil para tokens de seguridad |
| option | Una opción posible dentro de un elemento element |
| select | Lista de opciones de elementos option |
| textarea | Texto multilínea |

### 4. Validación

La **validación de formularios** es algo fundamental ya que previene posibles ataques de intrusos, además de asegurar que los datos que se reciben son realmente del tipo deseado.

Existen **dos formas de validación de formularios**: en el lado del cliente y en el lado del servidor. En el lado del cliente la validación suele ser mediante **JavaScript**, es más rápida y evita enviar más trabajo al servidor. En el lado del servidor se emplea **PHP** para verificar que se envían valores correctos, es más seguro pero es más lento y da un poco de trabajo al servidor. En este artículo vamos a ver algún ejemplo de esta segunda forma.

El siguiente es un sencillo **formulario** algo más complejo con los siguiente datos: Nombre, Contraseña, Educacion, Nacionalidad, Idiomas, Email y Sitio web:

```
<h2>Formulario:</h2>
<form action="<?php echo htmlspecialchars($_SERVER["PHP_SELF"]); ?>" method="post">
    Nombre:
    <input type="text" name="nombre" maxlength="50"><br>
    Contraseña:
    <input type="password" name="password"><br>
    Educacion:
    <select name="educacion">
        <option value="sin-estudios">Sin estudios</option>
        <option value="educacion-obligatoria" selected="selected">Educación Obligatoria</option>
        <option value="formacion-profesional">Formación profesional</option>
        <option value="universidad">Universidad</option>
    </select> <br>
    Nacionalidad:
    <input type="radio" name="nacionalidad" value="hispana">Hispana</input>
    <input type="radio" name="nacionalidad" value="otra">Otra</input><br>
    Idiomas:
    <input type="checkbox" name="idiomas[]" value="español" checked="checked">Español</input>
    <input type="checkbox" name="idiomas[]" value="inglés">Inglés</input>
    <input type="checkbox" name="idiomas[]" value="francés">Francés</input>
    <input type="checkbox" name="idiomas[]" value="aleman">Alemán</input><br>
    Email:
    <input type="text" name="email"><br>
    Sitio Web:
    <input type="text" name="sitioweb"><br>
    // Botón de enviar
    <input type="submit" name="submit" value="Enviar">
</form>
</body>
</html>
```

Vamos a ver los **aspectos más característicos**:

*   El atributo _**maxlength**_ limita el número de caracteres para una caja de texto.
*   Contraseña es del tipo _**password**_, lo que permite ocultar los caracteres con * cuando se escribe.
*   Educación es un elemento _**select**_, que permite seleccionar _**options**_ en una lista. El texto en el atributo _**value**_ es el que se enviará a través del formulario. El atributo _**selected**_ permite mostrar el seleccionado por defecto.
*   Nacionalidad es del tipo _**radio**_, los elementos radio del mismo grupo han de tener un único atributo _**name**_ y sólo se puede seleccionar uno.
*   Los idiomas están en un elemento _**checkbox**_, que permite enviar varios datos en forma de array si se indica en el atributo _**name**_ con corchetes. Se puede añadir un atributo _**checked**_ como seleccionado por defecto.
*   En _**action**_ se ha puesto _<?php echo htmlespecialchars($_SERVER["PHP_SELF"]); ?>_. **$_SERVER["PHP_SELF"]** es una superglobal que devuelve el nombre del archivo en el que se encuentra el formulario, lo que hace que los datos se envíen al mismo archivo, en lugar de llevarlos a otro archivo para tratarlos. Si se emplea esta forma de indicar el archivo para _**action**_, es necesario usar la función _htmlespecialchars()_, que convierte caracteres especiales en entidades HTML previniendo posibles [ataques Cross-site Scripting](http://diego.com.es/ataques-xss-cross-site-scripting-en-php).

En este ejemplo todas las acciones de validar, enviar y recibir datos se hacen en el mismo archivo _form.php_.

Primero vamos a crear un **filtrado común para todos los campos** con la función _filtrado()_:

```
function filtrado($datos){
    $datos = trim($datos); // Elimina espacios antes y después de los datos
    $datos = stripslashes($datos); // Elimina backslashes \
    $datos = htmlspecialchars($datos); // Traduce caracteres especiales en entidades HTML
    return $datos;
}
```

Esta función se aplicaría a cada campo a la hora de recibir los datos:

```
if(isset($_POST["submit"]) && $_SERVER["REQUEST_METHOD"] == "POST"){
    $nombre = filtrado($_POST["nombre"]);
    $password = filtrado($_POST["password"]);
    $educacion = filtrado($_POST["educacion"]);
    $nacionalidad = filtrado($_POST["nacionalidad"]);
    // Utilizamos implode para pasar el array a string
    $idiomas = filtrado(implode(", ", $_POST["idiomas"]));
    $email = filtrado($_POST["email"]);
    $sitioweb = filtrado($_POST["sitioweb"]);
}
```

Para **aceptar los datos** se ha puesto _isset($_POST["submit"])_, que crea la _**key**_ _submit_ cuando se hace click en el botón, y _$_SERVER["REQUEST_METHOD"] == "post"_, que especifica que el **método _request_** ha de ser POST. Se puede usar una de las dos formas o las dos a la vez.

Para **mostrar los datos**:

```
<?php if(isset($_POST["submit"])): ?>
<h2>Mostrar datos enviados</h2>
Nombre : <?php isset($nombre) ? print $nombre : ""; ?> <br>
Contraseña : <?php isset($password) ? print $password : ""; ?> <br>
Educación : <?php isset($educacion) ? print $educacion : ""; ?> <br>
Nacionalidad : <?php isset($nacionalidad) ? print $nacionalidad : "";?> <br>
Idiomas : <?php isset($idiomas) ? print $idiomas : ""; ?> <br>
Email : <?php isset($email) ? print $email : ""; ?> <br>
Sitio web : <?php isset($sitioweb) ? print $sitioweb : ""; ?> <br>
<?php endif; ?>
```

Todos los campos anteriores son **opcionales**. La validación más básica consiste en **exigir que se rellene un campo**, y comprobaremos también que el email tiene un **formato de email**, que el **sitio web es una URL correcta** y que **password** tiene un mínimo de 5 caracteres. Las **validaciones de formatos** pueden realizarse mediante expresiones regulares o con **filtros**. Siempre que sea posible, es mejor utilizar los [filtros de PHP](http://diego.com.es/filtrado-de-datos-de-entrada-en-php#LaExtensionDeFiltradoDePHP).

```
if(isset($_POST["submit"]) && $_SERVER["REQUEST_METHOD"] == "POST"){
    // El nombre y contraseña son campos obligatorios
    if(empty($_POST["nombre"])){
        $errores[] = "El nombre es requerido";
    }
    if(empty($_POST["password"]) || strlen($_POST["password"]) < 5){
        $errores[] = "La contraseña es requerida y ha de ser mayor a 5 caracteres";
    }
    // El email es obligatorio y ha de tener formato adecuado
    if(!filter_var($_POST["email"], FILTER_VALIDATE_EMAIL) || empty($_POST["email"])){
        $errores[] = "No se ha indicado email o el formato no es correcto";
    }
    // El sitio web es obligatorio y ha de tener formato adecuado
    if(!filter_var($_POST["sitioweb"], FILTER_VALIDATE_URL) || empty($_POST["sitioweb"])){
        $errores[] = "No se ha indicado sitio web o el formato no es correcto";
    }
    // Si el array $errores está vacío, se aceptan los datos y se asignan a variables
    if(empty($errores)) {
        $nombre = filtrado($_POST["nombre"]);
        $password = filtrado($_POST["password"]);
        $educacion = filtrado($_POST["educacion"]);
        $nacionalidad = filtrado($_POST["nacionalidad"]);
        // Utilizamos implode para pasar el array a string
        $idiomas = filtrado(implode(", ", $_POST["idiomas"]));
        $email = filtrado($_POST["email"]);
        $sitioweb = filtrado($_POST["sitioweb"]);
    }
}
```

El **array de errores** _$errores_ guarda cada uno de los errores que se registran para luego mostrarlos. Si el array tiene algún elemento, el formulario no será aceptado. Para mostrarlos podemos hacerlo así:

```
<ul>
<?php if(isset($errores)){
    foreach ($errores as $error){
        echo "<li> $error </li>";
    }
}
?>
</ul>
```

### 5. Subida de archivos

Lo más laborioso a la hora de hacer un formulario que permita la subida de archivos es **manejarlos y organizarlos una vez están en el servidor**. Inicialmente los archivos se suben a un directorio temporal y luego se relocalizan.

**Configuración en PHP para la subida de archivos**:

*   Debe estar On la variable _file\_uploads_ en _php.ini_.
*   En _php.ini_ también se encuentra la variable _upload\_tmp\_dir_, que indica el directorio donde se dirigirán los archivos cuando se suban. El tamaño máximo viene indicado en el mismo archivo de configuración por _upload\_max\_filesize_. Para ver estos valores se puede utilizar _ini\_get('upload\_tmp\_dir')_ e _ini\_get('upload\_max\_filesize')_. _upload\_tmp\_dir_ sólo es manipulable desde el php.ini o httpf.conf, _upload\_max\_filesize_ puede también desde .htaccess o un user.ini ([tienen modo **PHP_INI_SYSTEM** y **PHP_INI_PERDIR** respectivamente](http://diego.com.es/configuracion-en-php#DondeYComoCambiarLaConfiguracionEnPHP)).
*   Es necesario que los permisos tanto del **directorio temporal** como del **directorio final** sean de escritura.
*   Para que un formulario tenga la capacidad de aceptar archivos se añade el atributo _**enctype="multipart/form-data"**_ al elemento form.

De nuevo en este ejemplo pondremos todo el contenido en un archivo _form2.php:_

```
<h2>Formulario subida de archivos</h2>
<html>
<body>
<form action="<?php echo htmlspecialchars($_SERVER["PHP_SELF"]); ?>" method="POST" enctype="multipart/form-data">
    <input type="hidden" name="MAX_FILE_SIZE" value="<?php echo $max_file_size; ?>" />
    <input type="file" name="imagen" />
    <input type="submit" name="submit" />
</form>
</body>
</html>
```

Se incluye un **input** del tipo _**hidden**_ que limita el tamaño máximo del archivo que se puede subir (en bytes).

La información sobre un archivo subido la proporciona el [array multidimensional](http://diego.com.es/arrays-en-php#ArraysMultidimensionales) **$_FILES**. Este array se crea con el _**key**_ que se indique en el input del formulario, en este caso "imagen":

*   $_FILES["imagen"]**["name"]**. Guarda el nombre original del archivo del cliente.
*   $_FILES["imagen"]**["type"]**. Guarda el MIME type del archivo.
*   $_FILES["imagen"]**["size"]**. Guarda el tamaño del archivo en bytes.
*   $_FILES["imagen"]**["tmp_name"]**. Guarda el nombre del archivo temporal.
*   $_FILES["imagen"]**["error"]**. Guarda cualquier código de error que pueda provocar la subida del archivo.

La función _move\_uploaded\_file()_ mueve un archivo subido del directorio temporal al directorio que se indique.

```
$directorioSubida = "uploads/";
$max_file_size = "51200";
$extensionesValidas = array("jpg", "png", "gif");
if(isset($_POST["submit"]) && isset($_FILES['imagen'])){
    $errores = array();
    $nombreArchivo = $_FILES['imagen']['name'];
    $filesize = $_FILES['imagen']['size'];
    $directorioTemp = $_FILES['imagen']['tmp_name'];
    $tipoArchivo = $_FILES['imagen']['type'];
    $arrayArchivo = pathinfo($nombreArchivo);
    $extension = $arrayArchivo['extension'];
    // Comprobamos la extensión del archivo
    if(!in_array($extension, $extensionesValidas)){
        $errores[] = "La extensión del archivo no es válida o no se ha subido ningún archivo";
    }
    // Comprobamos el tamaño del archivo
    if($filesize > $max_file_size){
        $errores[] = "La imagen debe de tener un tamaño inferior a 50 kb";
    }
    // Comprobamos y renombramos el nombre del archivo
    $nombreArchivo = $arrayArchivo['filename'];
    $nombreArchivo = preg_replace("/[^A-Z0-9._-]/i", "_", $nombreArchivo);
    $nombreArchivo = $nombreArchivo . rand(1, 100);
    // Desplazamos el archivo si no hay errores
    if(empty($errores)){
        $nombreCompleto = $directorioSubida.$nombreArchivo.".".$extension;
        move_uploaded_file($directorioTemp, $nombreCompleto);
        print "El archivo se ha subido correctamente";
    }
}
```

El array **$_FILES['imagen']['error']** especifica por qué no se ha podido subir el archivo, lo que permite especificar un mensaje de vuelta para cada tipo de error. Devuelve un _**integer**_ con el número de error:

| | | |
| -------- | -------- |
| **Error** | **Valor** | **Significado** |
| UPLOAD_ERR_OK | 0 | No hay errores |
| UPLOAD_ERR_INI_SIZE | 1 | Supera el tamaño máximo indicado en php.ini |
| UPLOAD_ERR_FORM_SIZE | 2 | Supera el tamaño máximo indicado en MAX_FILE_SIZE de html |
| UPLOAD_ERR_PARTIAL | 3 | Sólo se ha subido el archivo parcialmente |
| UPLOAD_ERR_NO_FILE | 4 | No se ha subido ningún archivo |
| UPLOAD_ERR_NO_TMP_DIR | 6 | Falta la carpeta temporal |
| UPLOAD_ERR_CANT_WRITE | 7 | No se puede escribir en el directorio especificado |
| UPLOAD_ERR_EXTENSION | 8 | Una extensión de PHP ha detenido la subida |

### 6. Subida múltiple de archivos

Una subida múltiple de archivos en **PHP** en la práctica se hace normalmente con ayuda de **JavaScript**. Una buena librería para subir archivos es [DropzoneJS](http://www.dropzonejs.com). 

En el **formulario HTML** lo único que cambia es que se añade un atributo _multiple_ y que en el atributo _name_ del input _file_ se añade corchetes para indicar que es un array:

```
<input type="file" name="imagenes[]" multiple="multiple" />
```

En cuanto al backend, omitiendo validaciones esta puede ser una forma de hacerlo:

```
$directorioSubida = "uploads/";
$max_file_size = "51200";
if(isset($_POST["submit"]) && isset($_FILES['imagenes'])){
    $nombres = $_FILES['imagenes']['name'];
    $temporales = $_FILES['imagenes']['tmp_name'];
    $tipos = $_FILES['imagenes']['type'];
    $errores = $_FILES['imagenes']['error'];
    // Iteramos sobre los arrays creados
    for ($i = 0; $i < count($temporales); $i++){
        if(move_uploaded_file($temporales[$i], $directorioSubida.$nombres[$i])){
            echo "Se ha subido {$nombres[$i]} correctamente <br>";
        } else {
            echo "Ha habido algún error al subir algún archivo";
        }
    }
}
```