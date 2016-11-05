Cuando se permite la **subida de archivos a usuarios en una aplicación web**, se abre la puerta a nuevas **posibilidades de hackeo**, pero suele ser un requisito en las aplicaciones de hoy en día. Subir archivos está dispobible tanto en redes sociales como en cualquier aplicación con perfiles de usuario, blogs, foros, sitios de banca, etc. Los usuarios pueden subir imágenes, vídeos y muchos otros tipos de archivos más. Cuanta más libertad se deja al usuario más **aumentan los riesgos**. Aun así, **un gran número de sitios web no disponen de las medidas de seguridad adecuadas**.

**Indice de contenido**

| | |
| -------- | -------- |
| 1. Formulario simple sin validación | 5. Comprobar el header de las imágenes |
| 2. Validación Mime Type | 6. Proteger la carpeta de subidas con .htaccess |
| 3. Bloquear extensiones de archivos peligrosas | 7. Validación por el lado del cliente |
| 4. Extensiones dobles de archivos | 8. Medidas de prevención de ataques |

### 1. Formulario simple sin validación

Un **formulario sencillo de subida de archivos** normalmente consiste en un **formulario HTML** y un **script PHP**. 

```
<form enctype="multipart/form-data" action="upload.php" method="POST">
    <input type="hidden" name="MAX_FILE_SIZE" value="100000" />
    Elige el archivo a subir: <br>
    <input name="uploadedfile" type="file" /><br />
    <input type="submit" value="Subir archivo" />
</form>
<?php
$target_path  =  "uploads/";
$target_path  =  $target_path  .  basename($_FILES['uploadedfile']['name']);
if (move_uploaded_file($_FILES['uploadedfile']['tmp_name'], $target_path)) {
    echo "El archivo " . basename($_FILES['uploadedfile']['name']) . " se ha subido con éxito";
} else {
    echo "Hubo un error subiendo el archivo, por favor inténtalo de nuevo!";
}
?>
```

Cuando **PHP recibe el POST _request_** con el tipo de codificación **multiplart/form-data**, crea un archivo temporal con un nombre aleatorio en un directorio temporal. PHP también genera el array global $_FILES con información sobre el archivo subido:

*   **$_FILES['uploadedfile']['name']**. Nombre original del archivo en el ordenador del usuario
*   **$_FILES['uploadedfile']['type']**. El MIME type del archivo
*   **$_FILES['uploadedfile']['size']**. El tamaño del archivo en bytes
*   **$_FILES['uploadedfile']['tmp_name']**. El archivo temporal guardado de archivo subido

La función _**move_uploaded_file()**_ moverá el archivo temporal a una carpeta, en este caso a _/uploads_. En este ejemplo no existe ninguna restricción acerca de los archivos permitidos para subir, por lo que un atacante puede subir archivos PHP o .NET con código malicioso.

### 2. Validación MIME Type

Un error común que cometen los **desarrolladores web** cuando intentan **proteger las subidas** es comprobar sólo el **MIME Type devuelto por PHP**. Cuando un archivo se sube al servidor, PHP establecerá el elemento del array **$_FILES['uploadedfile']['type']** en función del MIME Type proporcionado por el **navegador web que el cliente está usando**. Sin embargo, la validación no puede depender sólo en este valor. Un usuario malicioso puede subir archivos fácilmente usando un script o aplicación automática que permite el envío de **HTTP POST requests**, que le permiten enviar un **MIME Type falso**.

### 3. Bloquear extensiones de archivo peligrosas

En ocasiones el desarrollador crea una **lista negra de extensiones de archivo peligrosas**, y el acceso es denegado si la extensión del archivo subido está en esa lista negra.

Una **desventaja de usar listas negras de extensiones de archivos** es que es casi imposible tener una **lista completa** que incluya **todas las posibles extensiones que un atacante puede usar**. Si el código funciona en un **entorno de hosting**, normalmente éstos permiten una gran cantidad de lenguajes: Perl, Python, Ruby, etc, y la lista puede ser interminable.

Un atacante podría subir un archivo del tipo _.htaccess_ que podría contener una línea de código como la siguiente:

```
AddType application/x-httpd-php.jpg
```

La línea anterior de dice al **servidor de Apache** que ejecute las imágenes _**.jpg**_ como si fueran **scripts PHP**, por lo que ahora el atacante puede subir un archivo _**.jpg**_ que contiene **código PHP**. Este código podría ejecutar la función _phpinfo()_ que mostraría una gran cantidad de información de la instalación PHP y el servidor.

### 4. Extensiones dobles en archivos

El caso es similar al anterior, aunque en lugar de **comprobar la extensión del archivo**, al desarrollador extrae la extensión buscando el carácter '.' en el archivo, y extrayendo el string después del punto.

El método usado para superar esta barrera es algo más complicado, pero todavía relativamente fácil. **Apache maneja los archivos de forma que pueden tener más de una extensión**, y el orden de las extensiones normalmente es irrelevante. Por ejemplo si un archivo _hola.html.es_ indica que es del tipo _text/html_ y su lenguaje es el español, entonces el archivo _hola.es.html_ contendrá la misma información. Si más de una extensión es dada e indica el mismo tipo de meta información, entonces el de la derecha se usará, excepto para **lenguajes** y **codificación de contenidos**. Por ejemplo, si _**.gif**_ indica el MIME type _image/gif_ y **.html** indica que es _text/html_, entonces el archivo _hola.gif.html_ se asociará con el MIME Type text/html.

Por lo tanto, un archivo llamado _archivo.php.123_ será **interpretado como PHP** y podrá ejecutarse. Esto sólo funciona si la última extensión _.123_ no está especificada en la **lista de mime-types conocidos por el servidor**. Los desarrolladores a veces no son conscientes de esa característica de Apache, y puede ser muy peligrosa. Un atacante puede subir un archivo _hey.php.123_ y evitar el control de subida. El script comprobará que _.123_ no está en la lista negra.

Dicho esto, es imposible predecir la cantidad de extensiones que podría subir un atacante.

Una mejor forma de **protegerse frente a la subida de archivos** es **con una lista blanca**. En este caso el desarrollador define una lista de extensiones conocidas y no permite extensiones que no estén especificadas en la lista.

Sin embargo esta opción también es vulnerable. Existen dos **formas de configurar Apache para ejecutar código PHP**: con la directiva **AddHandler** o con la directiva **AddType**. **Si se usa la directiva AddHandler**, todos los archivos que contienen la extensión .php (incluídas tambíen las dobles como .php.jpg) se ejecutarán como script PHP. Si tu configuración de Apache contiene la siguiente línea, es posible que tu script sea vulnerable:

```
AddHandler php5-script.php
```

Un atacante puede subir un archivo llamado _filename.php.jpg_, saltarse la protección y ejecutar el código.

### 5. Comprobar el header de las imágenes

Cuando sólo se permite la subida de imágenes, los desarrolladores suelen comprobar el _header_ de la imagen mediante la función _getimagesize()_, que devuelve el tamaño de la imagen. Si la **validación de la imagen** es incorrecta (el _header_ no es correcto), la función devuelve **false**. Si un atacante intenta subir un comando PHP embebido en un archivo _**.jpg**_, la función devolverá **false**, y no podrá subir el archivo. Sin embargo esta medida se puede saltar fácilmente: si una imagen se abre en un **editor de imágenes** como Gimp, se puede editar el **comentario de la imagen** donde se puede insertar el **código PHP**.

La imagen mantendrá el _header_, y por lo tanto pasa el control de _getimagesize()_. 

### 6. Proteger la carpeta de subidas con .htaccess

Otra forma popular de **asegurar la subida de archivos** es **proteger la carpeta donde los archivos se suben** mediante _.htaccess_. La idea es restringir la ejecución de archivos desde esta carpeta. Si se usa este tipo de escenario, se inserta una línea similar a ésta:

```
AddHandler cgi-script .php .php3 .php4 .phtml .pl .py .jsp .asp .htm .shtml .sh .cgi Options –ExecCGI
```

La línea anterior es otro tipo de lista negra, que no es realmente muy segura. En el propio manual de PHP, en la sección _move_uploaded_file_, hay un warning que dice que si el archivo de destino ya existe, será sobreescrito.

Ya que los archivos subidos podrán y serán sobreescritos con los ya existentes, un atacante puede fácilmente reemplazar el archivo _.htaccess_ con una versión modificada. Esto le permitirá ejecutar scripts específicos que pueden comprometer la seguridad del servidor.

### 7. Validación por el lado del cliente

Otra medida común de **seguridad usada en la subida de archivos es por el lado del cliente**. Esta medida suele ser más común en lenguajes como **ASP.NET**, ya que ofrece facilidades en los **controles de validación**.

Este tipo de controles de validación permiten al usuario hacer **comprobaciones de expresiones regulares en el archivo que se sube**, para comprobar que la extensión del archivo que se sube está especificado en la lista de **extensiones permitidas**.

Igualmente, es franqueable, se puede escribir un script en el lado del cliente que hará la validación en lugar del script que proporciona la aplicación web. Si no usa un **navegador web**, el atacante puede usar una aplicación que permite enviar HTTP POST request para subir el archivo. 

### 8. Medidas de prevención de ataques

La siguiente lista de medidas ayuda a prevenir ataques en la subida de archivos:

*   Define un archivo _.htaccess_ que sólo permita el acceso a archivos con **extensiones específicas**.
*   No pongas el _.htaccess_ en la misma carpeta donde se suben los archivos, se debe situar en el **directorio padre**.
*   Un _.htaccess_ típico que sólo permite los archivos **gif**, **png**, **jpg**, **jpeg** y **png** debería incluir el siguiente código, que **previene también de ataques por doble extensión de archivos**:

```
deny from all

    <Files ~ “^w+.(gif|jpe?g|png)$”>

    order deny,allow

    allow from all

    </Files>
```

*   Si es posible, sube los archivos en un **directorio fuera del server root**.
*   Evita la posibilidad de **sobreescritura de archivos** (para evitar el ataque que sobreescribe el _.htaccess_).
*   Crea una lista de **mime-types aceptados**.
*   Genera un **nombre de archivo aleatorio** y añade la extensión previamente generada.
*   No confíes sólo en una validación por el lado del cliente, porque no es suficiente. Lo ideal es **implementar una seguridad tanto por el lado del cliente como por el lado del servidor**.