**Indice de contenido**

| 1. Introducción | 7. Manejo de errores |
| 2. Orden de búsqueda del archivo de configuración | 8. Activar extensiones |
| 3. Archivos user.ini | 9. Configurar variables específicas de extesiones |
| 4. Dónde y cómo cambiar la configuración PHP | 10. Ajustes de seguridad |
| 5. Configuración del parser | 11. Ajustes de rendimiento |
| 6. Configurar el directorio de búsqueda de PHP | 12. La función ini_set() |

### 1. Introducción

**PHP** generalmente funciona bien sin necesidad de hacer cambios en su **configuración**, pero los creadores de PHP hicieron posible poder manipular el comportamiento del lenguaje para las necesidades de cada desarrollador. El archivo de configuración _**php.ini**_ permite alterar el comportamiento de PHP relacionado con **directorios**, **sesiones**, **parámetros de las bases de datos** y **extensiones**. 

El archivo se divide en secciones, cada una con variables relacionadas con esa sección:

```
[MiSección]
variable1="valor1"
variable2="valor2"
```

Como en las variables de php, son sensibles a mayúsculas y no pueden contener espacios. Los valores pueden ser **numéricos**, **strings** o **booleanos**. Los booleanos pueden ser: **true**, **on** o **yes** y **false**, **off**, **no** o **none**. 

El punto y coma al principio de cada línea indica que es un comentario, siendo así fácil poder activar y desactivar características. 

El archivo de configuración _**php.ini**_ se lee cuando arranca **PHP**. Si funciona como **módulo de servidor** (Ej: Apache), sólo se leerá cuando se inicia el servidor, por lo que será necesario reiniciarlo para que se produzcan los cambios. En las versiones **CGI** y **CLI** se lee en cada ejecución.

### 2. Orden de búsqueda del archivo de configuración

El **orden de búsqueda** del archivo _php.ini_ es el siguiente:

1.  Ubicación del **módulo SAPI**
    Apache 2 - directiva **PHPIniDir**
    CGI y CLI - línea de comandos: **-c**
    NSAPI - parámetro **php_ini**
    THTTPD - variable de entorno **PHP_INI_PATH**
2.  Variable de entorno **PHPRC**
3.  Se puede establecer la **ubicación del archivo php.ini para direferentes versiones de PHP**. Se comprueban en orden:
    - [HKEY_LOCAL_MACHINE\SOFTWARE\PHP\x.y.z],
    - [HKEY_LOCAL_MACHINE\SOFTWARE\PHP\x.y] y
    - [HKEY_LOCAL_MACHINE\SOFTWARE\PHP\x], donde 'x', 'y', y 'z' significan versión mayor, menor y de edición de PHP.
4.  [HKEY_LOCAL_MACHINE\SOFTWARE\PHP], valor de IniFilePath (Windows).
5.  Directorio actual de trabajo (salvo CLI).
6.  Directorio del **servidor web (para módulos SAPI)**, o el directorio de PHP (salvo Windows).
7.  Directorio de Windows.

Si existe un archivo **php-SAPI.ini** (Ej: _php-cli.ini_ o _php-apache.ini_) se usaría éste en lugar de php.ini. Se puede determinar el nombre de la SAPI usando _**php_sapi_name()**_.

### 3. Archivos user.ini

Los archivos de configuración _user.ini_ son archivos _php.ini_ a nivel de directorios. Estos archivos los procesa la **SAPI CGI/FastCGI**. Con **Apache** es posible usar el _.htaccess_ para conseguir el mismo efecto.

Además del archivo _php.ini_ principal, **PHP** explora cada directorio en busca de archivos _php.ini_, comenzando por el directorio del archivo PHP solicitado, y continuando hasta el directorio raíz de documentos (como está establecido en $_SERVER['DOCUMENT_ROOT']). 

En los archivos .user.ini sólo se reconocerán las configuraciones INI que tengan los modos **PHP_INI_PERDIR** y **PHP_INI_USER**.

La directivas INI _user_ini.filename_ y _user_ini.cache_ttl_ controlan el uso de los ficheros INI de usuarios:

*   **_user_ini.filename_** establece el nombre del archivo que PHP buscará en cada directorio.
*   **_user_ini.cache_ttl_ **controla con qué frecuencia se releen los ficheros INI de usuario. (valor por defecto 300 segundos, 5 minutos).

### 4. Dónde y como cambiar la configuración en PHP

Algunos ajustes pueden realizarse directamente en el script PHP con [ini_set()](http://php.net/manual/es/function.ini-set.php), otros es necesario hacerlos en el _php.ini_ o _httpd.conf_.

Por ejemplo el ajuste [output_buffering](http://php.net/manual/es/outcontrol.configuration.php#ini.output-buffering) es **PHP_INI_PERDIR**, por lo que **no** puede establecerse usando _init_set_. En cambio [display_errors](http://php.net/manual/es/errorfunc.configuration.php#ini.display-errors) es **PHP_INI_ALL** y se puede establecer desde cualquier lugar, incluyendo init_set().

**Modos de PHP_INI_***:

| **Modo** | **Significado** |
| PHP_INI_USER | La variable se puede establecer desde **scripts de usuario** o en archivos **.user.ini** |
| PHP_INI_PERDIR | La variable se puede establecer en **php.ini**, **.htaccess**, **httpd.conf** o **user.ini** |
| PHP_INI_SYSTEM | La variable sólo se puede establecer en **php.ini** o **httpd.conf** |
| PHP_INI_ALL | La variable se puede establecer en cualquier lugar |

#### **Ejecutar PHP como un módulo Apache**

Cuando se usa PHP como un módulo de Apache, se puede cambiar la configuración desde _httpd.conf_ y ._htaccess_. Se necesitan los privilegios **AllowOverride Options** o **AllowOverride All** para poder hacerlo.

Desde los archivos de **Apache** sólo se pueden cambiar las configuraciones bajo los modos PHP_INI_ALL, PHP_INI_PERDIR y PHP_INI_SYSTEM (puedes ver el listado [aquí](http://php.net/manual/es/ini.list.php)).

*   **php_value** _nombre valor_. Establece un valor de la variable especificada. Sólo PHP_INI_ALL y PHP_INI_PERDIR. (Para borrar un valor se usa _none_).
*   **php_flag** _nombre _on|off. Establece una variable de tipo boolean. Sólo PHP_INI_ALL y PHP_INI_PERDIR.
*   **php_admin_value** _nombre valor_. Establece un valor de la variable especificada. No se puede en archivos ._htaccess_. Ninguno de estos valores podrá ser sobreescrito en _.htaccess_ o por _ini_set()_. (Para borrar un valor usa _none_).
*   **php_admin_flag** _nombre_ on|off. Establece una variable de tipo boolean. Igual que el anterior, no puede usarse en _.htaccess_ ni ser sobreescrito por éste ni _ini_set()_.

#### **Ejemplo de configuración en Apache**

```
<IFModule mod_php5.c>
    php_value include_path ".:/usr/local/lib/php"
    php_admin_flag engine on
</IFModule>
<IFModule mod_php4.c>
    php_value include_path ".:/usr/local/lib/php"
    php_admin_flag engine on
</IFModule>
```

Hay que tener en cuenta que las constantes en PHP no existen fuera de PHP, como **E_ALL** o **E_NOTICE**. 

### 5. Configuración del parser

Una de las configuraciones más importantes es la del **intérprete del lenguaje**. La primera opción es la variable que controla el **motor de PHP**, que ha de ser On o Off. Si se cambia a Off el código PHP no será analizado por el servidor. 

```
engine = On
```

La variable _**short_open_tag**_ controla si el parser debería reconocer las etiquetas **<?...?>**, además de **<?php...?>**. Si esta etiqueta genera conflictos con otros lenguajes, es mejor que esté en Off. 

```
short_open_tag = Off
```

Los datos de **session**, **cookie** o **HTTP headers** en un script PHP deben enviarse antes que sea enviada cualquier otra salida. Si esto no es posible, se pueden habilitar las **llamadas output buffering**, con la variable _output_buffering_.

Con el **output buffering on**, PHP guarda la **salida del script** en una **memoria especial buffer** y la envía sólo cuando se le dice de hacerlo. Esto permite enviar HTTP headers y datos de cookies a mitad o incluso al final del script (aunque esto empeora el rendimiento).

```
output_buffering = Off
```

También puedes pasar a la variable un valor numérico con el tamaño del buffer:

```
output_buffering = 2048
```

Cuando se inicia PHP, se añade un mensaje indicando su versión al **header** _Web server_. Para evitarlo, se deshabilita la variable **expose_php**:

```
expose_php = On
```

### 6. Configurar el directorio de búsqueda de PHP

Se puede configurar el directorio de búsqueda en PHP con la variable _include_path_, que acepta una lista de directorios. PHP buscará automáticamente en estos directorios cuando se encuentre referencias a archivos que no tienen un directorio señalado.

Si tienes funciones o clases de librerías que usas con frecuencia, puedes listar su localización aquí para simplificar las búsquedas de archivos:

```
include_path = ".:/usr/local/php56/lib/php..."
```

Para indicar varias localizaciones, en **Windows** se hace con punto y coma, en **UNIX** con dos puntos.

Existen dos variables para especificar archivos que PHP anexa directamente al principio o al final de cualquier documento PHP: _auto_prepend_file_ y _auto_append_file_. Se pueden usar para añadir páginas header o footer. Lo malo es que los archivos serán anexados en todos los documentos PHP.

Los archivos a anexar pueden ser HTML o PHP. Código PHP embebido debe ir con las etiquetas **<?php...?>**.

```
auto_prepend_file = /home/web/includes/layout/header.php
auto_append_file = /home/web/includes/layout/footer.php
```

### 7. Manejo de errores

Los errores se dividen en cuatro categorías: **parsing errors**, **notices**, **warnings** y **fatal errors**. 

Normalmente cuando PHP se encuentra con un error **parsing**, **warning** o **fatal** el script muestra el error, y en caso de que sea fatal, se interrumpe en ese punto. Se puede alterar el comportamiento de **mostrar los mensajes de error** con la variable _error_reporting_, que acepta códigos de error y sólo muestra aquellos que concuerdan con esos códigos:

```
error_reporting = E_ALL
```

Para **desactivar el mostrar errores** (recomendable en un entorno de producción), se establece la variable _display_errors_ a **false**, y en cambio escribe los errores en un **archivo log** (habilitando la variable _log_errors)_. Esto es bueno para la seguridad también, porque no se muestra información del sistema que podría usarse para dañar la aplicación. 

El **archivo log** se establece con la variable **error_log**, donde puede indicarse el nombre de un archivo o el valor especial _syslog_. 

```
display_errors = Off
log_errors = On
error_log = "error.log"
```

### 8. Activar extensiones

En los sistemas UNIX las extensiones se construyen en la compilación. En Windows, los archivos DLL se incluyen con las distribuciones PHP. 

La variable _extension_dir_ contiene el nombre del directorio donde PHP debe mirar para las [extensiones](http://diego.com.es/extensiones-en-php):

```
extension_dir = "/usr/local/php56/php/extensions/..."
```

Para activar una extensión simplemente hay que remover el punto y coma y reiniciar el servidor.

Si la extensión no está listada, puedes añadirla:

```
extension=php_extension.dll
```

### 9. Configurar variables específicas de extensiones

Las **variables específicas de extensiones** se guardan en secciones de _php.ini_. Por ejemplo, todas las variables relacionadas con la extensión MySQL estarán en la sección [MySQL].

Por ejemplo si se usa la **variable mail() de PHP** hay tres variables que hay que configurar:

```
SMTP = miservidor.localnet.com
sendmail_from = yo@localhost.com
sendmail_path = /usr/sbin/sendmail 
```

Si quieres **integrar aplicaciones Java**, con la **extensión Java**:

```
java.class.path = .\php_java.jar
java.home = c:\jdk
java.library = c:\jdk\jre\bin\hotspot\jvm.dll
java.library.path = .\
```

La variable _session.save_path_ especifica el directorio temporal para la información de las sesiones. Puedes también controlar cuanto tiempo una **cookie de sesión** permanece válida, en segundos, con _session.cookie_lifetime_:

```
session.save_path = /var/www
session.cookie_lifetime = 1800
```

### 10. Ajustes de seguridad

Antes de PHP 5.4 existían la directivas _safe_mode_, que limitaban al usuario las cosas que podía hacer a través de PHP, como restringir directorios, pero fueron eliminadas en dicha versión.

Se puede **restringir la manipulación de archivos** con la variable _open_basedir_, que establece el directorio nombrado como el **directorio root** para operaciones con archivos. Si este valor está establecido, archivos fuera de este directorio y subsidiarios serán inaccesibles para PHP:

```
open_basedir = /home/web
```

La variable _max_execution_time_ establece el numero máximo de segundos que PHP esperará para que un script finalice su ejecución, antes de forzar su cierre:

```
max_execution_time = 30
```

#### **Configuración de subida de archivos y variables de formularios**

Se puede incrementar la seguridad desactivando la subida de archivos con la variable _file_uploads_ o limitando el tamaño de archivo máximo con _upload_max_filesize_:

```
file_uploads = On
upload_max_filesize = 2M
```

En cuanto a los **formularios**, la variable _post_max_size_ controla la **cantidad máxima de datos** que PHP aceptará en un formulario de vez con el método POST:

```
post_max_size = 8M
```

Otra variable es _max_input_time_, que limita en segundos el **tiempo de recibir datos** a través de POST, GET y PUT:

```
max_input_time = 90
```

### 11. Ajustes de rendimiento

Hay algunas variables que pueden **mejorar el rendimiento del intérprete PHP**. La variable _memory_limit_ especifica la memoria máxima que un simple script puede utilizar:

```
memory_limit = 8M //(Este valor deberá ser mayor que post_max_size)
```

Otra **opción para mejorar el rendimiento es desactivar las variables $argc y $argv**, que guardan el número de argumentos pasados a una aplicación desde la línea de comandos así como sus valores:

```
register_argc_argv = false
```

### 12. La función ini_set()

PHP lee toda la configuración al iniciarse desde el archivo _php.ini_, pero también permite sobreescribirla con la función _**ini_set()**_, que acepta dos argumentos, el nombre de la variable a configurar y su nuevo valor. El siguiente ejemplo incrementa el tiempo de ejecución:

```
<?php

ini_set('max_execution_time', 900);

// código
```

La configuración afecta sólo al script en donde está configurado. Cuando el script termina, el valor de la variable vuelve a su valor inicial.