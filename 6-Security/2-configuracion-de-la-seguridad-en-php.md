**Indice de contenido**

1.  Reporte de errores
2.  PHP como CGI binario
3.  PHP como módulo de Apache
4.  Seguridad del sistema de archivos

### 1. Reporte de errores en PHP

Los **errores** nos proporcionan información de **por qué ha fallado la ejecución de una aplicación**, **dónde** y **cómo ocurrió el error**. Esta información es fundamental para luego solucionar un problema. Sin embargo, puede haber más gente interesada en saber **por qué ocurrió un error**. Si se facilita esta información a los usuarios se puede dar información bastante valiosa de **cómo funciona la aplicación**. Esta es una de las formas con las que los atacantes pueden comenzar a investigar **vulnerabilidades en el código**. Es muy importante no mostrar nunca errores del código cuando la aplicación está en **entorno de producción**. En el **entorno de desarrollo**, en cambio, es frecuente y práctico mostrarlos para evitar tener que mirar el log continuamente para ver los errores.

En el entorno de producción, cuando la app está visible y accesible para los usuarios, los errores se han de guardar en un **archivo log**. Por defecto, **PHP inserta todos los errores en un _archivo log_ en el servidor**. Sin embargo, si estás en un servidor compartido, lo más probable es que no tengas acceso a ese archivo, por lo que será necesario que los escribas en un archivo propio. Existen algunas directivas en el **archivo de configuración php.ini** que son relevantes para el **manejo de errores en PHP**:

*   _**display_errors**_ controla si los errores PHP han de ser devueltos a la pantalla. En entorno de producción esto siempre debe estar OFF.
*   _**error_reporting**_ controla qué errores han de ser reportados. Conviene que siempre esté en E_ALL y que soluciones los errores que aparezcan.
*   _**log_errors**_ controla si los errores han de ser logeados en un archivo. Lo ideal es siempre ON.
*   _**error_log**_ es el directorio del archivo donde se registrarán los errores. Sólo aplicable si _log_errors_ está activa.

La mejor **configuración** para las cuatro directivas anteriores es la siguiente:

| | | |
| -------- | -------- |
| **Directiva** | **Entorno de producción** | **Desarrollo** |
| _display_errors_ | Off | On |
| _error_reporting_ | E_ALL | E_ALL |
| _log_errors_ | On | On |
| _error_log_ | varies | varies |

#### **Configurar las directivas**

Existen diferentes maneras de configurar las directivas anteriores para conseguir la forma más eficiente y segura de **manejar los errores**:

1.  Con el **archivo de configuración _php.ini_**. Si eres el administrador del servidor, la principal forma de hacerlo es mediante el **archivo _php.ini_**. 
2.  Si usas **Apache**, desde _**httpd.conf**_ o el **archivo _.htaccess_** se pueden configurar directivas de Apache para el directorio y subdirectorios donde se encuentra dicho archivo. Algunos alojamientos no permiten este método, pero si se puede, el **módulo PHP de Apache** tiene una directiva llamada _php_flag_ que permite **configurar directivas PHP**, de la forma:

   
```
    php_flag nombre_directiva valor_directiva
   
```

    No se pueden usar **constantes de PHP** en este archivo, como **E_ALL**, por lo que se tienen que usar sus valores numéricos. El valor de E_ALL es 32767, aunque puede variar con nuevas versiones. Aquí está el [listado de errores](http://php.net/manual/en/errorfunc.constants.php). 
3.  Una tercera forma de hacerlo es mediante la función [ini_set()](http://php.net/ini_set). La función tiene dos argumentos: el nombre de la directiva que se quiere cambiar y su nuevo valor. Aquí sí se pueden usar las constantes. Existe una función también llamada [error_reporting()](http://php.net/manual/en/function.error-reporting.php) que se puede utilizar también para la directiva _error_reporting_. Gran parte de las directivas del php.ini no se pueden configurar desde _ini_set()_, sólo las que son PHP_INI_ALL o PHP_INI_USER.

### 2. PHP como CGI binario

Usar PHP como un [CGI](http://diego.com.es/concepto-y-funcionamiento-de-cgi) binario es una alternativa para configuraciones en las que no es posible integrar PHP como un módulo dentro del software del servidor (como **Apache**), o cuando se usa PHP con diferentes **wrappers CGI** para crear entornos _**chroot**_ y _**setuid**_ seguros para scripts. Este tipo de instalación normalmente requiere que se instale un **ejecutable PHP binario** en el **directorio _cgi-bin_** del servidor. El [CERT](http://www.cert.org/historical/advisories/CA-1996-11.cfm) recomienda no poner ningún intérprete dentro de la carpeta cgi-bin. Pero incluso si el PHP binario se usa como intérprete, PHP está diseñado para prevenir los ataques que se pueden recibir con este tipo de instalación:

*   **Accediendo a los archivos del sistema**: _http://mi.servidor/cgi-bin/php?/etc/passwd_. Cuando se consulta información desde una URL después del signo de interrogación (?), se pasa como **argumento** de la línea de comandos al intérprete mediante la interfaz del CGI. Normalmente los intérpretes abren y ejecutan el archivo especificado de forma que es el primer argumento en la línea de comandos. **Cuando se invoca como CGI binario, PHP rechaza interpretar los argumentos de la línea de comandos**.
*   **Accediendo a cualquier documento web en el servidor**: _http://my.host/cgi-bin/php/secret/doc.html_. La información de la ruta de la URL después del nombre del PHP binario, _/secret/doc.html_, es usada para especificar el nombre del archivo que ha de ser abierto e interpretado por el programa CGI.

    Normalmente algunas directrices de configuración de los servidores web se usan para redirigir _**requests**_ de documentos como _http://my.host/secret/script.php_ al intérprete de PHP. Con esta configuración el servidor web primero comprueba los permisos de acceso en el directorio _/secret_, y después de eso crea el _**request**_ redirigido _http://my.host/cgi-bin/php/secret/script.php_. Desafortunadamente, si el request se da originariamente de esta forma, el servidor no comprueba los accesos a _/secret/script.php_, sino sólo para el archivo _/cgi-bin/php_. Así cualquier usuario con acceso a /cgi-bin/php puede acceder a cualquier documento protegido del servidor. En PHP, las directivas de configuración en tiempo de ejecución _**cgi.force_redirect**_, _**doc_root**_ y _**user_dir**_ pueden usarse para prevenir este ataque, si los documentos del servidor tienen directorios con restricciones de acceso.

Existen 4 casos posibles que se explican en la [documentación de PHP](http://php.net/manual/es/security.cgi-bin.default.php):

1.  **Sólo se sirven archivos públicos**. Si el servidor no tiene ningún contenido restringido por **contraseña** o control de acceso basado en la **dirección IP**, no es necesario aplicar ninguna configuración. Si el servidor no permite hacer redirecciones, o el servidor no tiene una forma de decirle al PHP binario que el request es un **request redigirido de forma segura**, puedes especificar la opción _**--enable-force-cgi-redirect**_ en la configuración del script. De todas formas también es necesario asegurarse de que los scripts PHP no dependen de una u otra forma de llamar al script, ni mediante _http://my.host/cgi-bin/php/dir/script.php_ ni mediante redirección _http://my.host/dir/script.php_.
2.  **Usar _cgi.force_redirect_**. La directiva de configuración _**cgi.force_redirect**_ previene a cualquier usuario de llamar a PHP directamente con una URL como _http://my.host/cgi-bin/php/secretdir/script.php_. En cambio, PHP lo analizará de este modo si ha sido a través de una regla de redirección del servidor. Anteriormente a la versión PHP 4.2, PHP utilizaba la opción de tiempo de compilación _**--enable-force-cgi-redirect**_ para esto.

    Normalmente la redirección en la configuración de Apache se hace con estas directivas:
    `Action php-script /cgi-bin/php
    AddHandler php-script .php`

    Esta opción sólo se ha probado con el **servidor web de Apache**, y depende de Apache el configurar la **variable de entorno** CGI **REDIRECT_STATUS** para requests redirigidos. Si tu servidor web no soporta ninguna forma de saber si el **request** es **directo** o **redirigido**, no puedes usar esta opción y debes usar una de las otras formas de iniciar la versión de CGI explicada aquí.
3.  **Configurar doc_root o user_dir**. Incluir contenido activo (_**scripts**_) en los directorios de documentos del servidor web es considerada una práctica insegura. Esto es así porque si por algún **error de configuración** los scripts no se ejecutan sino que se muestran en formato HTML se puede mostrar contenido privado como contraseñas. Muchos administradores de sistemas prefieren configurar otras estructuras de directorios que son accesibles sólo a través de PHP CGI, y por lo tanto siempre interpretados y nunca mostrados.

    Si el método para asegurarse de que los requests no son redirigidos no está disponible, es necesario configurar un _**doc_root**_ que es diferente del **web document root**. Se puede establecer la directiva doc_root en el **archivo de configuración php.ini**, o establecer la variable de entorno **PHP_DOCUMENT_ROOT**. Si se establece, la versión CGI de PHP siempre abrirá los archivos de _**doc_root**_, por lo que te aseguras de que ningún script se ejecuta fuera de este directorio (excepto _**user_dir**_).

    La otra opción disponible es _**user_dir**_. Cuando éste no está establecido, lo único que controla los archivos abiertos es _**doc_root**_. Abrir una URL como _http://my.host/~user/doc.php_ no significa que se abre un archivo bajo el directorio users, sino que se abre un archivo llamado _~user/doc.php_ bajo el directorio _**doc_root**_. Si _**user_dir**_ está configurado para, por ejemplo, _public_php_, un request del tipo _http://my.host/~user/doc.php_ abrirá un archivo llamado _doc.php_ bajo el directorio _public_php_ en el **directorio del usuario**. Si el directorio del usuario es _/home/user_, el archivo ejecutado será _/home/user/public_php/doc.php_.
4.  Una **opción muy segura es poner el analizador PHP binario en algún sitio fuera de los archivos de la web**. En **_/usr/local/bin_** por ejemplo. La única pega es que ahora tendrás que poner una línea como esta:
    `#!/usr/local/bin/php`
    como primera línea de cualquier archivo que contenga [etiquetas PHP](http://diego.com.es/sintaxis-basica-de-php#EtiquetasPHP). También tendrás que hacer que el archivo sea ejecutable. Esto es exactamente igual a como se haría con cualquier otro script CGI escrito en Perl o Sh o cualquier otro lenguaje de script común que usa el comando #! para ejecutarse a sí mismo.
    Para que PHP pueda manejar **PATH_INFO** y **PATH_TRANSLATED** correctamente con esta configuración, el analizador PHP debería ser compilado con la opción de configuración _**--enable-discard-path**_.

### 3. PHP como módulo de Apache

Cuando **PHP se usa como módulo de Apache** hereda los permisos del usuario de Apache (normalmente aquellos permisos del usuario "nobody"). Esto tiene algunas consecuencias en el tema de la **seguridad** y la **autorización**. Por ejemplo, si usas PHP para acceder a una base de datos, a no ser que esa base de datos tenga control de acceso incorporado, tendrás que hacer la base de datos accesible al usuario "nobody". Esto significa que un script malicioso podría acceder y modificar la base de datos, incluso sin nombre de usuario ni contraseña. Es posible que una araña web se tope con la página de administración de la base de datos y elimine todas las bases de datos. Puedes protegerte de esto con la **autorización Apache**, o puedes diseñar tu propio modelo de acceso usando **LDAP**, archivos _.htaccess_, etc, e incluir ese código como parte de tus scripts PHP.

A menudo, una vez que la seguridad es establecida hasta el punto en el que el usuario PHP (en este caso el usuario Apache) tiene poco riesgo de ataque, se descubre que PHP no puede escribir archivos en los directorios de usuario, o no puede acceder o cambiar bases de datos. Un error frecuente en estas situaciones es permitir **permisos de administrador (root) de Apache** o aumentar las posibilidades del usuario de Apache de alguna otra forma. 

**Aumentar los permisos de los usuarios de Apache** a los de **administrador** es peligroso y puede comprometer el sistema entero, por lo que hacer _**sudo**_, _**chroot**_ u otras configuraciones como **usuario root** debe estar limitado a muy pocos usuarios.

Hay algunas soluciones más simples, usando _**open_basedir**_ se puede controlar y restringir qué directorios tienen permiso para ser usados para PHP. También es posible configurar áreas de sólo apache, para restringir el acceso de toda actividad web a archivos que no son de usuarios o del sistema.

Además de que [PHP funcione con Apache](https://www.feistyduck.com/library/apache-security/online/apachesc-CHP-3.html) en el servidor donde está instalado el módulo, una **versión de línea de comandos de PHP** se compila y copia al directorio _**/usr/local/apache/php/bin/php**_. La versión de comando de línea es útil si quieres usar PHP para scripting general, no relacionado con servidores web.

La siguiente **configuración hace que Apache cargue PHP cuando se inicia** y permite a Apache identificar que páginas contienen código PHP:

```
# Carga el módulo PHP (el módulo está en el
# subdirectorio modules/ en Apache 2)
LoadModule php5_module libexec/libphp5.so
# Activa el módulo (no necesario con Apache 2)
AddModule mod_php5.c

# Asocia extensiones de archivos con PHP
AddHandler application/x-httpd-php .php
AddHandler application/x-httpd-php .php3
AddHandler application/x-httpd-php .inc
AddHandler application/x-httpd-php .module
```

Se puede actualizar la directiva **DirectoryIndex** en caso de que sea necesario: 

```
DirectoryIndex index.html index.php
```

Es necesaria una copia del archivo _**php.ini**_ en _/usr/local/apache/php/lib/_. Un error frecuente de instalación es que el archivo de configuración está en un lugar equivocado, por lo que no tiene efectos sobre el **motor PHP**. Para asegurarse de que el **archivo de configuración** está activo, se puede crear un archivo con una llamada a _**phpinfo()**_ y comparar los resultados con las configuraciones establecidas en el archivo _**php.ini**_.

### 4. Seguridad del sistema de archivos

PHP está **integrado en la seguridad de los servidores** en lo que respecta a permisos de archivos y directorios, por lo que se puede controlar qué archivos pueden leerse. Hay que tener cuidado y controlar los permisos de los usuarios que tienen acceso a la modificación y lectura de archivos.

Se pueden escribir **scripts PHP que pueden leer archivos del sistema** como /etc/passwd, modificar conexiones de red, imprimir de forma masiva, etc, por lo que hay que asegurar bien los **permisos de archivos** y la **forma de acceder a los archivos** en general. 

Considerando un script en el que un usuario quiere borrar un archivo de su directorio home (es necesario que el usuario Apache pueda borrar archivos de los directorios home de los usuarios):

```
$username = $_POST['nombre_de_usuario'];
$userfile = $_POST['archivo_de_usuario'];

$directorio = "/home/$username";

unlink("$directorio/$userfile");

echo "Se ha borrado el archivo";
```

Ya que el **nombre de usuario** y el **nombre del archivo** se envían desde un **formulario**, éstos pueden ser un nombre de archivo o usuario que pertenecen a otra persona, y se podría borar a pesar de que supuestamente no está permitido. Por ello se debería usar algún tipo de autentificación. Suponemos ahora que las variables enviadas son:

```
$username = $_POST['nombre_de_usuario']; // "../etc"
$userfile = $_POST['archivo_de_usuario']; // "passwd"

$directorio = "/home/$username"; // "/home/../etc"

unlink("$directorio/$userfile"); // "/home/../etc/passwd"

echo "Se ha borrado el archivo";
```

Existen dos medidas imprescindibles para prevenir este tipo de casos:

*   Limitar los permisos a los usuarios web
*   Revisar las variables que se envían

Una versión mejorada de la anterior puede ser la siguiente:

```
$username = $_SERVER['REMOTE_USER']; // Mecanismo de autentificación
$userfile = basename($_POST['archivo_de_usuario']);

$directorio = "/home/$username";
$directorio_archivo = "$directorio/$userfile";

// Se registra el éxito o fracaso en un archivo log
if(file_exists($directorio_archivo) && unlink($directorio_archivo)){
    $log = "Se ha eliminado el archivo $directorio_archivo";
} else {
    $log = "No se ha podido eliminar el archivo $directorio_archivo";
}

$fp = fopen("/home/log/borradoArchivos.log", "a");
fwrite($fp, $log);
fclose($fp);

echo htmlentities($log, ENT_QUOTES);
```

Pero incluso el código anterior es vulnerable. Si el sistema de autentificación permite a los usuarios crear sus propios inicios de sesión, y uno elige la entrada "../etc", el sistema está expuesto de nuevo. Por ello, se puede realizar la siguiente comprobación:

```
$username = $_SERVER['REMOTE_USER']; // Mecanismo de autentificación
$userfile = basename($_POST['archivo_de_usuario']);

$directorio = "/home/$username";
$directorio_archivo = "$directorio/$userfile";

if(!ctype_alnum($username) || !preg_match('/^(?:[a-z0-9_-]|\.(?!\.))+$/iD', $directorio_archivo)){
    die("Nombre de usuario o archivo incorrectos");
}
```

Dependiendo del sistema operativo existen unos archivos u otros por los que preocuparse especialmente. Lo mejor es llevar una **política de prohibirlo todo salvo lo que expresamente se permite**.

#### **Bytes nulos**

PHP utiliza las **funciones de C** para operaciones relacionadas con el **sistema de archivos**, por lo que se podrían manejar bytes nulos de forma inesperada. **Un byte nulo denota el fin de un string en C**, por lo que las cadenas que los contengan no se considerarán por completo, sólo hasta que ocurre un byte nulo. Ejemplo que muestra el problema:

```
$file = $_GET['file']; // "../../etc/passwd\0"

if(file_exists('/home/wwwrun/'.$file.'.php')){
    // Será true si existe el archivo /home/wwwrun/../../etc/passwd
    include '/home/wwwrun/'.$file.'.php';
    // Se incluirá el archivo /etc/passwd
}

```

Por lo que cualquier string que se utilice en una operación de sistema de archivos se ha de validar correctamente. Esta es una versión mejorada:

```
$file = $_GET['file'];
switch ($file){
    case 'main':
    case 'foo':
    case 'bar':
        include '/home/wwwrun/include/'.$file.'.php';
        break;
    default:
        include '/homre/wwwrun/include/main.php';
}
```