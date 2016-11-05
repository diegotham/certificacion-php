**Indice de contenido**

| | |
| -------- | -------- |
| 1\. Directivas php.ini | 9\. Funciones para el manejo de archivos |
| 2\. Variables y bases predefinidas | 10\. Funciones para bases de datos |
| 3\. Funciones de información de PHP | 11 Funciones para el formato de datos |
| 4\. Funciones para errores | 12\. Funciones para clases y objetos |
| 5\. Funciones para variables | 13\. Funciones y clases predefinidas |
| 6\. Funciones para funciones | 14\. Funciones SPL |
| 7\. Funciones para strings | 15. Otras funciones |
| 8\. Funciones para arrays | 16\. Headers HTTP |

## 1\. Directivas php.ini

| | | | |
| -------- | -------- |
| **Directiva** | **Valor por defecto** | **Cambiable** | **Acción** |
| track_errors | 0 | PHP_INI_ALL | Si está habilitado el último mensaje de error siempre estará presente en la variable **$php_errormsg**. |
| html_errors | 1 | PHP_INI_ALL | Activa/Desactiva las etiquetas HTML en los mensajes de error. Proporciona formato HTML a los errores PHP. |
| error_reporting | NULL | PHP_INI_ALL | Establece el nivel de notificaciones de errores. El parámetro puede ser un integer que representa un campo de bits o constantes con nombre. Valor predeterminado: E_ALL & ~E_NOTICE & ~E_STRICT & ~E_DEPRECATED. |
| display_errors | 1 | PHP_INI_ALL | Determina si los errores deben ser impresos en pantalla como parte de la salida o si deben ocultarse al usuario. |
| log_errors | 0 | PHP_INI_ALL | Indica si los mensajes de error del script deben registrarse en el registro del servidor o en el error_log. |
| error_log | NULL | PHP_INI_ALL | Nombre del archivo donde se registrarán los errores del script. El archivo debe ser modificable por el usuario y el servidor. Si se usa _syslog_, los errores se enviarán al registro del sistema. |
| default_charset | "" | PHP_INI_ALL | Valor predeterminado de codificación de caracteres para las funciones htmlentities, html_entity_decode, htmlspecialchars, iconv y mbstring. |
| default_mimetype | "text/html" | PHP_INI_ALL | Por defecto PHP imprime una codificación de caracteres con Content-Type. |
| register_argc_argv | "1" | PHP_INI_PERDIR | Indica a PHP si debe declarar las variables argc y argv. |
| include_path | ".;/path/to/php/pear" | PHP_INI_ALL | Especifica la lista de directorios donde las funciones _require_, _include_, _fopen()_, _file()_, _readfile()_ y _file_get_contents()_ buscarán archivos. Puede establecerse con la función _set_include_path()_ u obtenerse con _get_include_path()_. |
| allow_url_fopen | "1" | PHP_INI_SYSTEM | Habilita las envolturas fopen de tipo URL que permiten el acceso a objetos URL como archivos. |
| allow_url_include | "0" | PHP_INI_ALL | Esta opción permite el uso de envolturas fopen de tipo URL con las funciones include, include_once, require, require_once. |
| auto_prepend_file | NULL | PHP_INI_PERDIR | Especifica el nombre de archivo que será analizado automáticamente antes del archivo principal. |
| auto_append_file | NULL | PHP_INI_PERDIR | Especifica el nombre de archivo que será analizado automáticamente después del archivo principal. |
| engine | "1" | PHP_INI_ALL | Acitva o desactiva la ejecución de PHP. Sólo se puede utilizar en la versión de PHP como módulo de Apache. |
| short_open_tag | "1" | PHP_INI_PERDIR | Indica si se permite el uso de la forma abreviada de las etiquetas de apertura de PHP: <? ?>. |
| output_buffering | "0" | PHP_INI_PERDIR | Se habilita el búfer de salida para todos los archivos. Si se quiere limitar el tamaño del búfer se puede emplear un numero máximo de bytes en lugar de On. |
| output_handler | NULL | PHP_INI_PERDIR | Se pueden redireccionar todas las salidas producidas por los scripts a una función, por ejemplo, mb_output_handler(). |
| expose_php | "1" | php.ini only | Se expone la versión PHP instalada en el servidor mediante headers HTTP. |
| disable_functions | "" | php.ini only | Permite desactivar ciertas funciones por razones de seguridad. No se ve afectada por safe_mode. Sólo se ven afectadas las funciones internas, no las definidas por el usuario. |
| disable_classes | "" | php.ini only | Permite desactivar ciertas clases por razones de seguridad. Mismas características que disable_functions. |
| extension_dir | "path/to/php" | PHP_INI_SYSTEM | Directorio donde PHP buscará las extensiones que se pueden cargar dinámicamente. Tambén se puede emplear la función dl(), pero enable_dl ha de estar activada. |
| enable_dl | "1" | PHP_INI_SYSTEM | Funciona con el módulo de Apache. Activa o desactiva la carga dinámica de extensiones con la función _[dl()](http://php.net/manual/es/function.dl.php)_. |
| session.auto_start | "0" | PHP_INI_PERDIR | Especifica si el módulo de sesión inicia una sesión automáticamente cuando arranque una petición. |
| session.cache_expire | "180" | PHP_INI_ALL | Especifica el tiempo de vida en minutos para las páginas de sesión examinadas. No tiene efecto para el limitador nocache. La función _[session_cache_expire()](http://php.net/manual/es/function.session-cache-expire.php)_ devuelve y puede cambiar el valor por defecto. |
| session.cache_limiter | "nocache" | PHP_INI_ALL | Especifica el método de control de caché usado por páginas de sesión. Puede ser: nocache, private, private_no_expire o public. La función _[session_cache_limiter()](http://php.net/manual/es/function.session-cache-limiter.php)_ devuelve y puede cambiar el valor por defecto. |
| session.name | "PHPSESSID" | PHP_INI_ALL | Especifica el nombre de la sesión que se usa como nombre de cookie. |
| session.save_path | "" | PHP_INI_ALL | Define el directorio temporal para la información de las sesiones. La función [session_save_path()](http://php.net/manual/es/function.session-save-path.php) obtiene y establece la ruta de almacenamiento. |
| session.cookie_domain | "" | PHP_INI_ALL | Especifica el dominio a establecer en la cookie de sesión. |
| session.cookie_httponly | "" | PHP_INI_ALL | Marca la cookie como accesible sólo a través de HTTP. |
| session.cookie_path | "/" | PHP_INI_ALL | Especifica la ruta a establecer en la cookie de sesión. Puede emplearse la función [session_get_cookie_params()](http://php.net/manual/es/function.session-get-cookie-params.php) para obtener los parámetros de la cookie de sesión, y [session_set_cookie_params()](http://php.net/manual/es/function.session-set-cookie-params.php) para establecerlos. |
| session.cookie_secure | "" | PHP_INI_ALL | Especifica si las cookies sólo se enviarán sobre conexiones seguras (HTTPS). |
| session.cookie_lifetime | "0" | PHP_INI_ALL | Especifica el tiempo de vida en segundos de la cookie que se envía al navegador. Si es 0 indica "hasta que el navegador se cierre". |
| session.use_cookies | "1" | PHP_INI_ALL | Especifica si el módulo usará cookies para almacenar el id de sesión en la parte del cliente. |
| session.use_only_cookies | "1" | PHP_INI_ALL | Especifica si el módulo **sólo** usará cookies para almacenar el id de sesión en la parte del cliente. |
| session.use_strict_mode | "0" | PHP_INI_ALL | Especifica si el módulo usará el modo de id de sesión estricto. Si está habilitado, el módulo no aceptará IDs de sesiones no inicializados. Si se envía desde el navegador un ID de sesión no inicializado, se envía un nuevo ID de sesión al navegador. Previene session fixation. |
| session.use_trans_sid | "0" | PHP_INI_ALL | Activa o desactiva el manejo de sesiones transparentes. |
| session.referer_check | "" | PHP_INI_ALL | Contiene la subcadena para comprobar cada **HTTP referer**. Si la referencia fue enviada por el cliente y la subcadena no se encontró, el id de sesión embebido será marcado como no válido. Por defecto es una cadena vacía. |
| session.hash_function | "0" | PHP_INI_ALL | Permite especificar el algoritmo utilizado para generar los ID de sesión. 0 es MD5 (128 bits) y 1 es SHA-1 (160 bits). La lista completa de algoritmos puede verse con [hash_algos()](http://php.net/manual/es/function.hash-algos.php). |
| session.entropy_file | "" | PHP_INI_ALL | Da una ruta a un recurso externo (archivo) que será usado como una fuente de entropía adicional en el proceso de creación del id de sesión. |
| session.entropy_length | "0" | PHP_INI_ALL | Especifica el número de bytes que serán leídos desde el archivo especificado arriba. Por defecto es 0 (deshabilitado). |
| session.hash_bits_per_character | "4" | PHP_INI_ALL | Permite definir cuántos bits son almacenados en cada carácter cuando se convierte la información hash binaria a algo legible. Valores posibles: 4 (0-9, a-f), 5 (0-9, a-v) y 6 (0-9, a-z, A-Z, "-", ","). |
| session.gc_maxlifetime | "1440" | PHP_INI_ALL | Especifica el número de segundos transcurridos después de que la información sea vista como basura y potencialmente limpiada. La recolección de basura puede suceder durante el inicio de sesiones, dependiendo de session.gc_probability y session.gc_divisor. |
| session.gc_probability | "1" | PHP_INI_ALL | Se usa junto a session.gc_divisor para manejar la probabilidad de inicio de la rutina de gc. |
| session.gc_divisor | "100" | PHP_INI_ALL | Junto con session.gc_probability define la probabilidad de que el proceso de gc se inicie en cada inicialización de sesión. La probabilidad se calcula: gc_probability/gc_divisor.  |
| open_basedir | NULL | PHP_INI_ALL | Límite de los archivos a los que PHP puede acceder partiendo del árbol de directorios especificado. Si PHP trata de acceder a un archivo con include o require que no está en este directorio, lo rechazará. |
| max_execution_time | "30" | PHP_INI_ALL | Tiempo máximo en segundos que PHP esperará para que un script finalice su ejecución antes de forzar su cierre.  |
| file_uploads | "1" | PHP_INI_SYSTEM | Permite o no la subida de archivos mediante HTTP. |
| upload_max_filesize | "2M" | PHP_INI_PERDIR | Tamaño máximo de archivo que se puede subir. |
| post_max_size | "8M" | PHP_INI_PERDIR | Define el tamaño máximo de los datos de POST permitidos. Esta valor ha de ser mayor que upload_max_filesize, y memory_limit debe ser mayor que post_max_size. |
| max_file_uploads | 20 | PHP_INI_SYSTEM | Numero máximo de archivos a subir de forma simultánea. |
| max_input_vars | 1000 | PHP_INI_PERDIR | Cuantas variables de entrada pueden ser aceptadas. Límite aplicado a los arrays superglobales $_GET, $_POST, $_COOKIE de forma separada. |
| max_input_time | "-1" | PHP_INI_PERDIR | Tiempo máximo en segundos que se permite a un script analizar datos de entrada como POST y GET. |
| upload_tmp_dir | NULL | PHP_INI_SYSTEM | Directorio temporal usado para almacenar archivos durante el proceso de subida. |
| memory_limit | "128M" | PHP_INI_ALL | Máximo de memoria en bytes que un script puede consumir. |
| zend.enable_gc | "1" | PHP_INI_ALL | Habilita o deshabilita el colector de referencia circular. Tiene el mismo efecto que las funciones [gc_enable](http://php.net/manual/es/function.gc-enable.php) y [gc_disable](http://php.net/manual/es/function.gc-disable.php). |
| opcache.enable | "1" | PHP_INI_ALL | Habilita la caché de opcode. A través de ini_set() sólo se puede deshabilitar, no habilitar. |
| opcache.use_cwd | "1" | PHP_INI_SYSTEM | Si está habilitada, OPcache añade el directorio de trabajo actual a la clave del script, eliminando las posibles colisiones entre archivos con el mismo nombre. |
| opcache.load_comments | "1" | PHP_INI_ALL | Permite cargar los comentarios de la documentación en la caché opcode. |
| opcache.save_comments | "1" | PHP_INI_SYSTEM | Permite guardar los comentarios de la documentación en la caché opcode. |
| opcache.blacklist_filename | "" | PHP_INI_SYSTEM | Ubicación de un archivo blaclist, que contiene los nombres de los archivos que no deberían ser cacheados por opcache. |
| mbstring.language | neutral | PHP_INI_ALL | Ajuste de lenguaje nacional predeterminado NLS usado en mbstring. |
| mbstring.detect_order | NULL | PHP_INI_ALL | Define el orden de detección de códigos de caracteres predeterminado. Se puede establecer y obtener con [mb_detect_order()](http://php.net/manual/es/function.mb-detect-order.php). |
| mbstring.substitute_character | NULL | PHP_INI_ALL | Define el carácter de sustitución para caracteres inválidos. |
| mbstring.func_overload | "0" | PHP_INI_SYSTEM | Sustituye el comportamiento de las funciones de strings monobyte por sus homólogas multibyte. |
| mbstring.encoding_translation | "0" | PHP_INI_PERDIR | Activa el filtro transparente de codificación de caracteres para las peticiones HTTP, lo que hace que PHP detecte y convierta la codificación de entrada a la codificación del sistema. |
| mbstring.strict_detection | "0" | PHP_INI_ALL | Activa la detección de la codificación del script. |

## 2\. Variables y bases predefinidas

**Superglobals**: $GLOBALS, $_SERVER, $_GET, $_POST, $_FILES, $_COOKIE, $_SESSION, $_REQUEST, $_ENV.

### Keys de $_SERVER

**PHP_SELF**. El nombre del archivo del script actual, relativo al document root.

**SERVER_ADDR**. La dirección IP del servidor bajo la cual se ejecuta el script actual.

**SERVER_NAME**. El nombre del servidor bajo el cual se ejecuta el script actual.

**SERVER_SOFTWARE**. Identificación del servidor en los headers cuando responden a los requests.

**REQUEST_METHOD**. Método request utilizado para acceder a la página: GET, HEAD, POST, PUT.

**REQUEST_TIME**. Un timestamp del comienzo del request.

**REQUEST_TIME_FLOAT**. Un timestamp del comienzo del request con precisión de microsegundos.

**QUERY_STRING**. El query string, si existe, con el que se ha accecido.

**DOCUMENT_ROOT**. El directorio document root bajo el cual el script actual se ejecuta, tal y como es definido en el archivo de configuración del servidor.

**HTTP_ACCEPT**. Contiene el header Accept del request actual, si existe.

**HTTP_ACCEPT_CHARSET**. Contiene el header Accept-Charset del request actual si existe (utf-8).

**HTTP_ACCEPT_ENCODING**. Contiene el header Accept-Encoding del request actual, si existe (gzip).

**HTTP_ACCEPT_LANGUAGE**. Contiene el header Accept-Language del request actual, si existe. (en).

**HTTP_CONNECTION**. Contiene el header Connection del request actual, si existe (Keep-Alive).

**HTTP_HOST**. Contiene el header Host del request actual, si existe.

**HTTP_REFERER**. La URL de la página anterior del cliente, si existe.

**HTTP_USER_AGENT**. Contiene el header User-Agent del request actual, si existe. (Mozilla/e.5 [en] (Z11; U; Linuz 2.2.9).

**HTTPS**. Se establece como valor no vacío si el script fué solicitado mediante el protocolo HTTPS.

**REMOTE_ADDR**. La dirección IP desde la cual el usuario está viendo la página actual.

**REMOTE_HOST**. El hostname desde el cual el usuario está viendo la página actual (basado en REMOTE_ADDR).

**REMOTE_PORT**. Puerto desde el cual el usuario se comunica con el servidor.

**REMOTE_USER**. El usuario autentificado.

**SCRIPT_FILENAME**. El directorio absoluto del script actual. Devuelve lo mismo que __FILE__.

**SCRIPT_NAME**. El directorio actual del script.

**SERVER_ADMIN**. El valor dado a la directiva SERVER_ADMIN (en Apache) en el archivo de configuración del servidor web.

**SERVER_PORT**. El puerto del servidor utilizado por el servidor web para la comunicación. Por defecto es 80, con SSL se cambiará a lo definido.

**REQUEST_URI**. El URI para acceder a la página actual (/index.html).

**PHP_AUTH_DIGEST**. Cuando se hace Digest authentication esta variable se establece al header Authorization enviado por el cliente.

**PHP_AUTH_USER**. Cuando se hace HTTP Authentication esta variable se establece al nombre del usuario.

**PHP_AUTH_PW**. Cuando se hace HTTP Authentication esta variable se establece a la contraseña del usuario.

**AUTH_TYPE**. Cuando se hace HTTP Authentication esta variable se establece al tipo de authentication.

**PATH_INFO**. Contiene el directorio si el query string.

**Otras variables predefinidas**: $php_errormsg, $http_response_header, $argc, $argv.

### Estructuras de control

if, else, elseif, while, do-while, for, foreach, break, continue, switch, declare, return, include/include_once, require/require_once, goto

### Constructores del lenguaje

die, exit, echo, print, empty, eval, isset, unset, list

### Constantes predefinidas

PHP_VERSION, PHP_EOL, PHP_INT_MAX, DEFAULT_INCLUDE_PATH, PHP_EXTENSION_DIR, PHP_MANDIR, PHP_CONFIG_FILE_PATH, TRUE, FALSE, NULL

E_ERROR (1), E_WARNING (2), E_NOTICE (8), E_CORE_ERROR (16), E_CORE_WARNING (32), E_COMPILE_ERROR (64), E_COMPILE_WARNING (128), E_USER_ERROR (256), E_USER_WARNING (512), E_USER_NOTICE (1024), E_STRICT (2048), E_RECOVERABLE_ERROR (4096), E_DEPRECATED (8192), E_ALL (32767)

### Constantes mágicas

\_\_LINE\_\_, \_\_FILE\_\_, \_\_DIR\_\_, \_\_FUNCTION\_\_, \_\_CLASS\_\_, \_\_TRAIT\_\_, \_\_METHOD\_\_, \_\_NAMESPACE\_\_.

## 3\. Funciones de Información de PHP

string **set_include_path** (string $new_include_path)

string **get_include_path** (void)

array **get_included_files** (void) // Alias: get_required_files()

array **get_loaded_extensions** ([bool $zend_extensions = false])

array **get_extension_funcs** (string $module_name)

array **get_defined_constants** ([bool $categorize = false])

array **get_resources** ([string $type])

string **ini_set** (string $varname, string $newvalue)

string **ini_get** (string $varname)

array **ini_get_all** ([string $extension [, bool $details = true ]])

void **ini_restore** (string $varname)

string **php_ini_loaded_file** (void)

bool **phpinfo** ([int $what = INFO_ALL])

string **phpversion** ([string $extension])

mixed **version_compare** (string $version1, string $version2 [, string $operator ])

int **memory_get_usage** ([bool $real_usage = false])

int **memory_get_peak_usage** ([bool $real_usage = false])

bool **set_time_limit** (int $seconds)

void **gc_enable** (void)

bool **gc_enabled** (void)

void **gc_disable** (void)

int **gc_collect_cycles** (void)

array **opcache_get_configuration** (void)

array **opcache_get_status** ([boolean $get_scripts = TRUE])

## 4\. Funciones para errores

bool **trigger_error** (string $error_msg [, int $error_type = E_USER_NOTICE ]) // Alias: user_error()

array **error_get_last** (void)

void **error_clear_last** (void)

bool **error_log** (string $message [, int $message_type = 0 [, string $destination [, string $extra_headers ]]])

int **error_reporting** ([int $level])

mixed **set_error_handler** (callable $error_handler [, int $error_types = E_ALL | E_STRICT ])

bool **handler** (int $errno, string $errstr [, string $errfile [, int $errline [, array $errcontext ]]])

callable **set_exception_handler** (callable $exception_handler)

bool **restore_error_handler** (void)

bool **restore_exception_handler** (void)

## 5\. Funciones para variables

bool **empty** (mixed $var)

bool **isset** (mixed $var [, mixed $... ])

void **unset** (mixed $var [, mixed $... ])

string **gettype** (mixed $var)

bool **settype** (mixed &$var, string $type)

array **get_defined_vars** (void)

mixed **print_r** (mixed $expression [, bool $return = false ])

void **var_dump** (mixed $expression [, mixed $... ])

mixed **var_export** (mixed $expression [, bool $return = false ])

int **intval** (mixed $var [, int $base = 10 ])

float **floatval** (mixed $var)

bool **boolval** (mixed $var)

string **strval** (mixed $var)

bool **is_array** (mixed $var)

bool **is_float** (mixed $var)

bool **is_int** (mixed $var)

bool **is_numeric** (mixed $var)

bool **is_object** (mixed $var)

bool **is_string** (mixed $var)

bool **is_bool** (mixed $var)

string **serialize** (mixed $value)

string **unserialize** (string $str)

## 6\. Funciones para funciones

mixed **call_user_func** (callable $callback [, mixed $parameter [, mixed $... ]] )

mixed **forward_static_call** (callable $function [, mixed $parameter [, mixed $... ]] )

mixed **call_user_func_array** (callable $callback, array $param_arr)

mixed **forward_static_call_array** (callable $function, array $parameters)

mixed **func_get_arg** (int $arg_num)

array **func_get_args** (void)

int **func_num_args** (void)

bool **function_exists** (string $function_name)

array **get_defined_functions** (void) // ['internal'] y ['user']

void **register_shutdown_function** (callable $callback [, mixed $parameter [, mixed $... ]] )

## 7\. Funciones para strings

int **strcmp** (string $str1, string $str2)

int **strcasecmp** (string $str1, string $str2)

int **strncmp** (string $str1, string $str2, int $length)

int **strncasecmp** (string $str1, string $str2, int $length)

int **strnatcmp** (string $str1, string $str2)

int **strnatcasecmp** (string $str1, string $str2)

int **substr_compare** (string $main_str, string $str, int $offset [, int $length [, bool $case_insensitivity = false ]])

int **similar_text** (string $first, string $second [, float &$percent ])

int **levenshtein** (string $str1, string $str2)

string **soundex** (string $str)

string **metaphone** (string $str [, int $phonemes = 0 ])

string **substr** (string $str, int $start [, int $length ])

string **mb_substr** (string $str, int $start [, int $length = NULL [, string $encoding = mb_internal_encoding() ]])

string **strtok** (string $str, string $token) // string strtok (string $token)

array **explode** (string $delimeter, string $string [, int $limit ])

string **implode** (string $glue, array $pieces) // implode (array $pieces) // Alias: join()

array **str_split** (string $string [, int $split_length = 1 ])

void **parse_str** (string $str [, array &$arr ])

string **trim** (string $str [, string $character_mask = " \t\n\r\0\x0B" ])

string **ltrim** (string $str [, string $character_mask ])

string **rtrim** (string $str [, string $character_mask ]) // Alias: chop()

mixed **count_chars** (string $string [, int $mode = 0 ])

mixed **str_word_count** (string $string [, int $format = 0 [, string $charlist ]])

int **strlen** (string $string)

int **substr_count** (string $haystack, string $needle [, int $offset = 0 [, int $length ]])

string **strstr** (string $haystack, mixed $needle [, bool $before_needle = false ]) // Alias: strchr()

string **stristr** (string $haystack, mixed $needle [, bool $before_needle = false ])

string **strrchr** (string $haystack, mixed $needle)

mixed **strpos** (string $haystack, mixed $needle [, int $offset = 0 ])

mixed **stripos** (string $haystack, string $needle [, int $offset = 0 ])

int **strrpos** (string $haystack, string $needle [, int $offset = 0 ])

int **strripos** (string $haystack, string $needle [, int $offset = 0 ])

mixed **str_replace** (mixed $search, mixed $replace, mixed $subject [, int &$count ])

mixed **str_ireplace** (mixed $search, mixed $replace, mixed $subject [, int &$count ])

mixed **substr_replace** (mixed $string, mixed $replacement, mixed $start [, mixed $length ])

string **lcfirst** (string $str)

string **ucfirst** (string $str)

string **ucwords** (string $str [, string $delimeters = " \t\r\n\f\v" ])

string **strtolower** (string $string)

string **strtoupper** (string $string)

string **str_pad** (string $input, int $pad_length [, string $pad_string = " " [, int $pad_type = STR_PAD_RIGHT ]])

string **str_repeat** (string $input, int $multiplier)

string **nl2br** (string $string [, bool $is_xhtml = true ])

string **wordwrap** (string $str [, int $width = 75 [, string $break = "\n" [, bool $cut = false ]]] )

string **strip_tags** (string $str [, string $allowable_tags ])

string **sprintf** (string $format [, mixed $args [, mixed $... ]])

int **printf** (string $format [, mixed $args [, mixed $... ]])

string **vsprintf** (string $format, array $args)

int **vprintf** (string $format, array $args)

int **fprintf** (resource $handle, string $format [, mixed $args [, mixed $... ]])

mixed **sscanf** (string $str, string $format [, mixed &$... ])

mixed **preg_replace** (mixed $pattern, mixed $replacement, mixed $subject [, int $limit = -1 [, int &$count ]] )

mixed **preg_filter** (mixed $pattern, mixed $replacement, mixed $subject [, int $limit = -1 [, int &$count ]])

array **preg_grep** (string $pattern, array $input [, int $flags = 0 ])

int **preg_match** (string $pattern, string $subject [, array &$matches [, int $flags = 0 [, int $offset = 0 ]]])

int **preg_match_all** (string $pattern, string $subject [, array &$matches [, int $flags = PREG_PATTERN_ORDER [, int $offset = 0 ]]])

mixed **preg_replace_callback** (mixed $pattern, callable $callback, mixed $subject [, int $limit = -1 [, int &$count ]])

array **preg_split** (string $pattern, string $subject [, int $limit = -1 [, int $flags = 0 ]])

string **chr** (int $ascii)

int **ord** (string $string)

void **echo** (string $arg1 [, string $... ])

string **addslashes** (string $str)

string **stripslashes** (string $str)

string **quotemeta** (string $str)

string **htmlentities** (string $string [, int $flags = ENT_COMPAT | ENT_HTML401 [, string $encoding = ini_get("default_charset") [, bool $double_encode = true ]]] )

string **htmlspecialchars** (string $string [, int $flags = ENT_COMPAT | ENT_HTML401 [, string $encoding = ini_get("default_charset") [, bool $double_encode = true ]]] )

string **html_entity_decode** (string $string [, int $flags = ENT_COMPAT | ENT_HTML401 [, string $encoding = ini_get("default_charset") ]] )

string **htmlspecialchars_decode** (string $string [, int $flags = ENT_COMPAT | ENT_HTML401 ] )

string **crypt** (string $str [, string $salt ])

string **md5** (string $str [, bool $raw_output = false ])

string **md5_file** (string $filename [, bool $raw_output = false ])

string **sha1** (string $str [, bool $raw_output = false ])

string **sha1_file** (string $filename [, bool $raw_output = false ])

string **number_format** (float $number [, int $decimals = 0 ])

string **money_format** (string $format, float $number)

string **str_shuffle** (string $str)

string **strtr** (string $str, string $from, string $to)

string **setlocale** (int $category, string $locale [, string $... ])

array **localeconv** (void)

## 8\. Funciones para arrays

bool **array_walk** (array &$array, callable $callback [, mixed $userdata  = null ])

bool **array_key_exists** (mixed $key, array $array) // Alias: key_exists()

bool **in_array** (mixed $needle, array $haystack [, bool $strict = FALSE ])

array **array_keys** (array $array [, mixed $search_value [, bool $strict = false ]])

array **array_values** (array $array)

array **array_diff** (array $array1, array $array2 [, array $... ])

array **array_diff_assoc** (array $array1, array $array2 [, array $... ])

array **array_diff_key** (array $array1, array $array2 [, array $... ])

array **array_diff_uassoc** (array $array1, array $array2 [, array $... ], callable $key_compare_func)

array **array_diff_ukey** (array $array1, array $array2 [, array $... ], callable $key_compare_func)

array **array_merge** (array $array1 [, array $... ])

array **array_merge_recursive** (array $array1 [, array $... ])

array **array_combine** (array $keys, array $values)

bool **sort** (array &$array [, int $sort_flags = SORT_REGULAR ])

bool **rsort** (array &$array [, int $sort_flags = SORT_REGULAR ])

bool **asort** (array &$array [, int $sort_flags = SORT_REGULAR ])

bool **arsort** (array &$array [, int $sort_flags = SORT_REGULAR ])

bool **ksort** (array &$array [, int $sort_flags = SORT_REGULAR ])

bool **krsort** (array &$array [, int $sort_flags = SORT_REGULAR ])

bool **usort** (array &$array, callable $value_compare_func)

bool **uasort** (array &$array, callable $value_compare_func) 

bool **uksort** (array &$array, callable $key_compare_func) 

bool **natsort** (array &$array) 

bool **natcasesort** (array &$array)

bool **shuffle** (array &$array) 

bool **array_multisort** (array &$array1 [, mixed $array1_sort_order = SORT_ASC [, mixed $array1_sort_flags = SORT_REGULAR [, mixed $... ]]]) 

array **array_fill** (int $start_index, int $num, mixed $value) 

array **array_fill_keys** (array $keys, mixed $value) 

array **range** (mixed $start, mixed $end [, number $step = 1]) 

array **array_slice** (array $array, int $offset [, int $length = null [, bool $preserve_keys = false ]]) 

array **array_splice** (array &$input, int $offset [, int $length [, mixed $replacement = array() ]]) 

array **array_chunk** (array $array, int $size [, bool $preserver_keys = false ]) 

mixed **array_shift** (array &$array) 

int **array_unshift** (array &$array, mixed $value1 [, mixed $... ]) 

mixed **array_pop** (array &$array) 

int **array_push** (array &$array, mixed $value1 [, mixed $... ]) 

array **array_flip** (array $array) 

array **array_column** (array $array, mixed $column_key [, mixed $index_key = null ]) 

array **array_count_values** (array $array) 

array **array_filter** (array $array [, callable $callback ) 

array **array_intersect** (array $array1, array $array2 [, array $... ]) 

array **array_map** (callable $callback, array $array1 [, array $... ]) 

array **array_pad** (array $array, int $size, mixed $value) 

mixed **array_rand** (array $array [, int $nym = 1 ]) 

mixed **array_reduce** (array $array, callable $callback [, mixed $initial = NULL ]) 

mixed **array_reverse** (array $array [, bool $preserve_keys = false ]) 

mixed **array_search** (mixed $needle, array $haystack [, bool $strict = false ]) 

array **compact** (mixed $varname1 [, mixed $... ]) 

int **extract** (array &$array [, int $flags = EXTR_OVERWRITE [, string $prefix = NULL ]]) 

array **list** (mixed $var1 [, mixed $... ]) 

array **each** (array &$array) 

int **count** (mixed $array_or_countable [, int $mode = COUNT_NORMAL ]) // Alias: sizeof() 

mixed **reset** (array &$array) 

mixed **current** (array &$array) // Alias: pos() 

mixed **next** (array &$array) 

mixed **prev** (array &$array) 

mixed **end** (array &$array)

## 9\. Funciones para el manejo de archivos

resource **fopen** (string $filename, string $mode [, bool $use_include_path = false [, resource $context ]])

string **fread** (resource $handle, int $length)

bool **fclose** (resource $handle)

int **fwrite** (resource $handle, string $string [, int $length ]) // Alias: fputs()

bool **feof** (resource $handle)

int **fseek** (resource $handle, int $offset [, int $whence = SEEK_SET ])

int **ftell** (resource $handle)

array **stat** (string $filename)

resource **finfo_open** ([int $options = FILEINFO_NONE [, string $magic_file = NULL ]] )

string **finfo_file** (resource $finfo, string $file_name = NULL [, int $options = FILEINFO_NONE [, resource $context = NULL ]] )

bool **finfo_close** (resource $finfo)

bool **chdir** (string $directory)

bool **chroot** (string $directory)

string **getcwd** (void)

array **scandir** (string $directory [, int $sorting_order = SCANDIR_SORT_ASCENDING [, resource $context ]] )

resource **opendir** (string $path [, $resource $context ])

string **readdir** ([resource $dir_handle])

void **closedir** ([resource $dir_handle])

void **rewinddir** ([resource $dir_handle])

string **fgets** (resource $handle [, int $length ])

mixed **fscanf** (resource $handle, string $format [, mixed &$... ])

string **fgetss** (resource $handle [, int $length [, string $allowable_tags ]] )

int **fpassthru** (resource $handle)

array **fgetcsv** (resource $handle [, int $length = 0 [, string $delimeter = "," [, string $enclosure = '"' [, string $escape = "\" ]]]])

string **fgetc** (resource $handle)

string **file_get_contents** (string $filename [, bool $use_include_path = false [, resource $context [, int $offset = -1 [, int $maxlen ]]]])

int **readfile** (string $filename [, bool $use_include_path = false [, resource $context ]])

array **file** (string $filename [, int $flags = 0 [, resource $context ]] )

array **parse_ini_file** (string $filename [, bool $process_sections = false [, int $scanner_mode = INI_SCANNER_NORMAL ]] )

int **file_put_contents** (string $filename, mixed $data [, int $flags = 0 [, resource $context ]] )

int **fputcsv** (resource $handle, array $fields [, string $delimeter = "," [, string $enclosure = '"' [, string $escape_char = "\" ]]])

string **basename** (string $path [, string $suffix ])

string **dirname** (string $path)

mixed **pathinfo** (string $path [, int $options = PATHINFO_DIRNAME | PATHINFO_BASENAME | PATHINFO_EXTENSION | PATHINFO_FILENAME ])

string **realpath** (string $path)

array **glob** (string $pattern [, int $flags = 0 ])

bool **mkdir** (string $pathname [, int $mode = 0777 [, bool $recursive = false [, resource $context ]]] )

bool **rmdir** (string $dirname [, resource $context ])

bool **copy** (string $source, string $dest [, resource $context ])

bool **unlink** (string $filename [, resource $context ])

bool **rename** (string $oldname, string $newname [, resource $context ])

bool **move_uploaded_file** (string $filename, string $destination)

bool **rewind** (resource $handle)

bool **flock** (resource $handle, int $operation [, int &$wouldblock ])

bool **ftruncate** (resource $handle, int $size)

string **tempnam** (string $dir, string $prefix)

resource **tmpfile** (void)

bool **touch** (string $filename [, int $time = time() [, int $atime ]])

bool **chown** (string $filename, mixed $user)

bool **chmod** (string $filename, mixed $mode)

bool **chgrp** (string $filename, mixed $group)

int **fileatime** (string $filename)

int **filectime** (string $filename)

int **filemtime** (string $filename)

int **fileowner** (string $filename)

int **fileperms** (string $filename)

int **filegroup** (string $filename)

int **filesize** (string $filename)

string **filetype** (string $filename)

bool **file_exists** (string $filename)

bool **is_file** (string $filename)

bool **is_dir** (string $filename)

bool **is_link** (string $filename)

bool **is_executable** (string $filename)

bool **is_readable** (string $filename)

bool **is_writable** (string $filename)

bool **is_uploaded_file** (string $filename)

bool **symlink** (string $target, string $link)

string **readlink** (string $path)

bool **link** (string $target, string $link)

int **linkinfo** (string $path)

array **lstat** (string $filename)

bool **lchown** (string $filename, mixed $user)

bool **lchgrp** (string $filename, mixed $group)

resource **stream_socket_client** (string $remote_socket [, int &$errno [, string &$errstr [, float $timeout = ini_get("default_socket_timeout") [, int $flags = STREAM_CLIENT_CONNECT [, resource $context ]]]]] )

string **stream_get_contents** (resource $handle [, int $maxlength = -1 [, int $offset = -1 ]] )

resource **stream_socket_server** (string $local_socket [, int &$errno [, string &$errstr [, int $flags = STREAM_SERVER_BIND | STREAM_SERVER_LISTEN [, resource $context ]]]] )

resource **stream_socket_accept** (resource $server_socket [, float $timeout = ini_get("default_socket_timeout") [, string &$peername ]])

string **stream_socket_get_name** (resource $handle, bool $want_peer)

int **stream_copy_to_stream** (resource $source, resource $dest [, int $maxlength = -1 [, int $offset = 0 ]])

resource **stream_context_create** ([ array $options [, array $params ]])

array **stream_context_get_options** (resource $stream_or_context)

array **stream_context_get_params** (resource $stream_or_context)

resource **stream_context_get_default** ([array $options])

bool **stream_context_set_option** (resource $stream_or_context, array $options)

bool **stream_context_set_params** (resource $stream_or_context, array $params)

resource **stream_context_set_default** (array $options)

array **stream_get_meta_data** (resource $stream)

resource **stream_filter_append** (resource $stream, string $filtername [, int $read_write [, mixed $params ]])

resource **stream_filter_prepend** (resource $stream, string $filtername [, int $read_write [, mixed $params ]])

## 10\. Funciones para bases de datos

public static **PDO::getAvailableDrivers** (void) // array pdo_drivers (void)

public **PDO::__construct** (string $dsn [, string $username [, string $password [, array $options ]]] )

public bool **PDO::setAttribute** (int $attribute, mixed $value)

PDO::ATTR_CASE: PDO::CASE_LOWER, PDO::CASE_NATURAL, PDO::CASE_UPPER

PDO::ATTR_ERRMODE: PDO::ERRMODE_SILENT, PDO::ERRMODE_WARNING, PDO::ERRMODE_EXCEPTION

PDO::ATTR_ORACLE_NULLS: PDO::NULL_NATURAL, PDO::NULL_EMPTY_STRING, PDO::NULL_TO_STRING

PDO::ATTR_STRINGIFY_FETCHES

PDO::ATTR_AUTOCOMMIT

PDO::ATTR_TIMEOUT

PDO::ATTR_EMULATE_PREPARES

PDO::MYSQL_ATTR_USE_BUFFERED_QUERY

PDO::ATTR_DEFAULT_FETCH_MODE

public bool **PDOStatement::setFetchMode** (int $mode)

public string **PDOStatement::errorCode** (void)

public array **PDOStatement::errorInfo** (void)

public PDOStatement **PDO::prepare** (string $statement [, array $driver_options = array() ])

public bool **PDOStatement::execute** ([array $input_parameters ])

public bool **PDOStatement::bindParam** (mixed $parameter, mixed &$variable [, int $data_type = PDO::PARAM_STR [, int $length [, mixed $driver_options ]]])

public bool **PDOStatement::bindValue** (mixed $parameter, mixed $value [, int $data_type = PDO::PARAM_STR ])

public bool **PDOStatement::bindColumn** (mixed $column, mixed &$param [, int $type [, int $maxlen [, mixed $driverdata ]]] )

public mixed **PDOStatement::fetch** ([int $fetch_style [, int $cursor_orientation = PDO::FETCH_ORI_NEXT [, int $cursor_offset = 0 ]]])

**$fetch_style** => PDO::FETCH_ASSOC, PDO::FETCH_BOTH, PDO::FETCH_BOUND, PDO::FETCH_CLASS, PDO::FETCH_INTO, PDO::FETCH_LAZY, PDO::FETCH_NAMED, PDO::FETCH_NUM, PDO::FETCH_OBJ

public array **PDOStatement::fetchAll** ([int $fetch_style [, mixed $fetch_argument [, array $ctor_args = array() ]]])

**$fetch_argument** => PDO::FETCH_COLUMN, PDO::FETCH_CLASS, PDO::FETCH_FUNC

PDO::FETCH COLUMN puede ir acompañado de PDO::FETCH_UNIQUE o PDO::FETCH_GROUP

public mixed **PDOStatement::fetchColumn** ([int $column_number = 0])

public mixed **PDOStatement::fetchObject** ([string $class_name = "stdClass" [, array $ctor_args ]])

public int **PDOStatement::rowCount** (void)

public int **PDOStatement::columnCount** (void)

public PDOStatement **PDO::query** (string $statement)

public int **PDO::exec** (string $statement)

public string **PDO::lastInsertId** ([string $name = NULL ])

public bool **PDO::beginTransaction** (void)

public bool **PDO::commit** (void)

public bool **PDO::rollback** (void)

## 11\. Funciones para el formato de datos

### Simple XML

SimpleXMLElement **simplexml_load_file** (string $filename [, string $class_name = "SimpleXMLElement" [, int $options = 0 [, string $ns = "" [, bool $is_prefix = false ]]]])

SimpleXMLElement **simplexml_load_string** (string $data [, string $class_name = "SimpleXMLElement" [, int $options = 0 [, string $ns = "" [, bool $is_prefix = false ]]]] )

SimpleXMLElement **simplexml_import_dom** (DOMNode $node [, string $class_name = "SimpleXMLElement" ])

final public **SimpleXMLElement::__construct** (string $data [, int $options = 0 [, bool $data_is_url = false [, string $ns = "" [, bool $is_prefix = false ]]]] )

public void **SimpleXMLElement::addAttribute** (string $name [, string $value [, string $namespace ]] )

public SimpleXMLElement **SimpleXMLElement::addChild** (string $name [, string $value [, string $namespace ]] )

public SimpleXMLElement **SimpleXMLElement::attributes** ([string $ns = NULL [, bool $is_prefix = false ]] )

public SimpleXMLElement **SimpleXMLElement::children** ([string $ns [, bool $is_prefix = false ]] )

public int **SimpleXMLElement::count** (void)

public string **SimpleXMLElement::getName** (void)

public array **SimpleXMLElement::getNamespaces** ([bool $recursive = false ])

public array **SimpleXMLElement::getDocNamespaces** ([bool $recursive = false [, bool $from_root = true ]])

public bool **SimpleXMLElement::registerXPathNamespace** (string $prefix, string $ns )

public array **SimpleXMLElement::xpath** (string $xpath)

public string **SimpleXMLElement::__toString** (void)

public mixed **SimpleXMLElement::asXML** ([string $filename]) // Alias saveXML()

### XML Parser

resource **xml_parser_create** ([string $encoding])

resource **xml_parser_create_ns** ([string $encoding [, string $separator = ":" ]])

bool **xml_set_element_handler** (resource $parser, callable $start_element_handler, callable $end_element_handler )

bool **xml_set_character_data_handler** (resource $parser, callable $handler)

int **xml_parse** (resource $parser, string $data [, bool $is_final = false ])

string **xml_error_string** (int $code)

int **xml_get_error_code** (resource $parser)

int **xml_get_current_line_number** (resource $parser)

bool **xml_parser_free** (resource $parser)

### Document Object Model

public **DOMDocument::__construct** ([string $version [, string $encoding ]])

DOMElement **dom_import_simplexml** (SimpleXMLElement $node)

public mixed **DOMDocument::load** (string $filename [, int $options = 0 ]) // loadHTML, loadHTMLFile, loadXML

public bool **DOMDocument::validate** (void)

public bool **DOMDocument::schemaValidate** (string $filename [, int $flags ])

public bool **DOMDOcument::schemaValidateSource** (string $source [, int $flags ])

public void **DOMElement::setIdAttribute** (string $name, bool $isId)

public DOMElement **DOMDocument::getElementById** (string $elementId)

public DOMNodeList **DOMDocument::getElementsByTagName** (string $name)

public DOMElement **DOMDocument::createElement** (string $name [, string $value ])

public DOMNode **DOMNode::appendChild** (DOMNode $newnode)

DOMNode **DOMNamedNodeMap::item** (int $index)

DOMNode **DOMNamedNodeMap::getNamedItem** (string $name)

public DOMAttr **DOMElement::setAttribute** (string $name, string $value)

public DOMAttr **DOMElement::setAttributeNode** (DOMAttr $attr)

public int **DOMDocument::save** (string $filename [, int $options ]) // saveHTML, saveHTMLFile, saveXML

public DOMNode **DOMNode::cloneNode** ([bool $deep])

public string **DOMNode::getNodePath** (void)

public bool **DOMNode::hasAttributes** (void)

public bool **DOMNode::hasChildNodes** (void)

public DOMNode **DOMNode::insertBefore** (DOMNode $newnode [, DOMNode $refnode ])

public DOMNode **DOMNode::removeChild** (DOMNode $oldnode)

public **DOMXPath::__construct** (DOMDocument $doc)

public DOMNodeList **DOMXpath::query** (string $expression [, DOMNode $contextnode [, bool $registerNodeNS = true ]])

### Fechas y DateTime

int **time** (void)

string **date** (string $format [, int $timestamp = time() ])

mixed **microtime** ([bool $get_as_float = false ])

int **strtotime** (string $time [, int $now = time() ])

string **strftime** (string $format [, int $timestamp = time() ])

bool **date_default_timezone_set** (string $timezone_identifier)

string **date_default_timezone_get** (void)

public **DateTime::__construct** ([string $time = "now" [, DateTimeZone $timezone = NULL ]])

public static DateTime **DateTime::createFromFormat** (string $format, string $time [, DateTimeZone $timezone ])

public string **DateTime::format** (string $format)

public int **DateTime::getTimestamp** (void)

public DateTimeZone **DateTime::getTimezone** (void)

public DateInterval **DateTime::diff** (DateTimeInterface $datetime2 [, bool $absolute = false ])

public DateTime **DateTime::modify** (string $modify)

public DateTime **DateTime::setDate** (int $year, int $month, int $day)

public DateTime **DateTime::setTime** (int $hour, int $minute [, int $seconde = 0 ])

public DateTime **DateTime::setTimestamp** (int $unixtimestamp)

public DateTime **DateTime::setTimezone** (DateTimeZone $timezone)

public DateTime **DateTime::add** (DateInterval $interval)

public DateTime **DateTime::sub** (DateInterval $interval)

public **DateInterval::__construct** (string $interval_spec) // P: Y M D W H M S

public **DatePeriod::__construct** (DateTimeInterface $start, DateInterval $interval, int $recurrences [, int $options ])

public **DateTimeZone::__construct** (string $timezone)

## 12\. Funciones para clases y objetos

void **__autoload** (string $class)

bool **class_alias** (string $original, string $alias [, bool $autoload = TRUE ])

bool **class_exists** (string $class_name [, bool $autoload = true ])

string **get_called_class** (void)

array **get_class_methods** (mixed $class_name)

array **get_class_vars** (string $class_name)

string **get_class** ([object $object = NULL])

array **get_declared_classes** (void)

array **get_declared_interfaces** (void)

array **get_declared_traits** (void)

array **get_object_vars** (object $object)

string **get_parent_class** ([mixed $object])

bool **interface_exists** (string $interface_name [, bool $autoload = true ])

bool **is_a** (object $object, string $class_name [, bool $allow_string = FALSE ])

bool **is_subclass_of** (mixed $object, string $class_name [, bool $allow_string = TRUE ])

bool **method_exists** (mixed $object, string $method_name)

bool **property_exists** (mixed $class, string $property)

bool **trait_exists** (string $traitname [, bool $autoload ])

### Exception

**Exceptions**: BadFunctionCallException, BadMethodCallException, DomainException, InvalidArgumentException, LengthException, LogicException, OutOfBoundsException, OutOfRangeException, OverflowException, RangeException, RuntimeException, UnderflowException, UnexpectedValueException.

public **Exception::__construct** ([ string $message = "" [, int $code = 0 [, Exception $previous = NULL ]]] )

final public string **Exception::getMessage** (void)

final public **Exception Exception::getPrevious** (void)

final public mixed **Exception::getCode** (void)

final public string **Exception::getFile** (void)

final public int **Exception::getLine** (void)

final public array **Exception::getTrace** (void)

final public string **Exception::getTraceAsString** (void)

public string **Exception::__toString** (void)

final private void **Exception::__clone** (void)

### Reflection

**Reflections**: ReflectionClass, ReflectionZendExtension, ReflectionExtension, ReflectionFunction, ReflectionFunctionAbstract, ReflectionMethod, ReflectionObject, ReflectionParameter, ReflectionProperty, Reflector, ReflectionException.

public **ReflectionClass::__construct** (mixed $argument)

public object **ReflectionClass::newInstance** (mixed $args [, mixed $... ])

public array **ReflectionClass::getConstants** (void)

public array **ReflectionClass::getInterfaces** (void)

public array **ReflectionClass::getMethods** ([int $filter])

public array **ReflectionClass::getProperties** ([int $filter])

public array **ReflectionClass::getTraits** (void)

public ReflectionMethod **ReflectionClass::getMethod** (string $name)

public bool **ReflectionClass::hasMethod** (string $name)

public ReflectionProperty **ReflectionClass::getProperty** (string $name)

public bool **ReflectionClass::hasProperty** (string $name)

public bool **ReflectionClass::isAbstract** (void)

public bool **ReflectionClass::isFinal** (void)

public bool **ReflectionClass::isTrait** (void)

### Magic Methods

\_\_get, \_\_set, \_\_isset, \_\_unset, \_\_toString, \_\_sleep, \_\_wakeup, \_\_call, \_\_callStatic, \_\_clone, \_\_invoke

## 14\. Interfaces y clases predefinidas

### Traversable

Interfaz para detectar si una clase puede ser recorrida mediante foreach. Esta interfaz abstracta base no puede ser implementada sola. En su lugar debe ser implementada con IteratorAggregate o con Iterator. No tiene métodos.

### Iterator

Interfaz para iteradores externos u objetos que pueden ser iterados internamente por sí mismos.
```
Iterator extends Traversable {
...abstract methods: current, key, next, rewind, valid...
}
```

### IteratorAggregate

Para crear una interfaz externa Iterator.
```
IteratorAggregate extends Traversable {
...abstract public Traversable getIterator (void)
}

```

### ArrayAccess

Interfaz para proporcionar acceso a objetos como arrays.
```
ArrayAccess {
...abstract methods: offsetExists, offsetGet, offsetSet, offsetUnset...
}
```

### Serializable

Interfaz para personalizar la serialización.
```
Serializable {
...abstract methods: serializable, unserializable...
}
```

### Closure

Clase empleada para representar funciones anónimas.
```
Closure {
...__construct, bind, bindTo, call...
}
```

### Generator

Clase que define los objetos Generator, devueltos desde generadores. Los objetos Generator no pueden instanciarse con **_new_**. 
```
Generator implements Iterator {
...public methods: current, key, next, rewind, send, throw, valid, __wakeup...
}

```

## 13\. SPL

array **spl_classes** (void)

array **class_implements** (mixed $class [, bool $autoload = true ])

array **class_parents** (mixed $class [, bool $autoload = true ])

array **class_uses** (mixed $class [, bool $autoload = true ])

int **iterator_apply** (Traversable $iterator, callable $function [, array $args ])

int **iterator_count** (Traversable $iterator)

array **iterator_to_array** (Traversable $iterator [, bool $use_keys = true ])

void **spl_autoload** (string $class_name [, string $file_extensions = spl_autoload_extensions() ])

bool **spl_autoload_register** ([ callable $autoload_function [, bool $throw = true [, bool $prepend = false ]]] )

string **spl_autoload_extensions** ([string $file_extensions])

array **spl_autoload_functions** (void)

### Countable

Las clases implementando Countable pueden ser utilizadas con la función count().
```
Countable {
abstract public int count (void)
}
```

### OuterIterator

Las clases que implementan OuterIterator pueden usarse para iterar sobre iteradores.
```
OuterIterator extends Iterator {
public Iterator getInnerIterator (void)
...
}
```

### RecursiveIterator

Las clases que implementan RecursiveIterator pueden ser usadas para iterar sobre iteradores de forma recursiva.
```
RecursiveIterator extends Iterator {
public RecursiveIterator getChildren (void)
public bool hasChildren (void)
...
}
```

### SeekableIterator
```
SeekableIterator extends Iterator {
abstract public void seek (int $position)
​...
}
```

### ArrayIterator

Este iterador permite eliminar y modificar keys y values cuando se recorren arrays y objetos.
```
ArrayIterator implements ArrayAccess, SeekableIterator, Countable, Serializable {
​...
public __construct ([mixed $array = array() [, int $flags = 0 ]])
...}

```

### ArrayObject

Esta clase permite a los objetos funcionar como arrays.
```
ArrayObject implements IteratorAggregate, ArrayAccess, Serializable, Countable {
...
public __construct ([ mixed $input = [] [, int $flags = 0 [, string $iterator_class = "ArrayIterator" ]]] )
...
}
```

### RecursiveIteratorIterator

Se utiliza para recorrer iteradores recursivos.
```
RecursiveIteratorIterator implements OuterIterator {
...
public __construct (Traversable $iterator [, int $mode = RecursiveIteratorIterator::LEAVES_ONLY [, int $flags = 0 ]] )
...
}
```

### RecursiveArrayIterator

Este iterador permite destruir y modificar keys y values mientras se iteran arrays y objetos de la misma manera que con ArrayIterator.
```
RecursiveArrayIterator extends ArrayIterator implements RecursiveIterator {
...
public ArrayIterator::__construct ([ mixed $array = array() [, int $flags = 0 ]] )
...
}
```

### DirectoryIterator

La clase DirectoryIterator proporciona una sencilla interface para ver el contenido de los directorios del sistema de ficheros.
```
DirectoryIterator extends SplFileInfo implements SeekableIterator {
...
public __construct (string $path)
...
}
```

### FilterIterator

Este iterador abstracto filtra valores no deseados. Para implementar filtros personalizados debe ser ampliada la clase FilterIterator. El método obligatorio para extenderla es _FilterIterator::accept()_.
```
abstract FilterIterator extends IteratorIterator implements OuterIterator {
...
public abstract bool accept (void)
public __construct (Iterator $iterator)
...
} 
```

### AppendIterator

Iterador que recorre varios iteradores uno tras otro.
```
AppendIterator extendsIteratorIterator implements OuterIterator {
...
public void append (Iterator $iterator)
...
}
```

### LimitIterator

Permite recorrer un limitado subconjunto de elemento de un Iterador.
```
LimitIterator extends IteratorIterator implements OuterIterator {
public __construct (Iterator $iterator [, int $offset = 0 [, int $count = -1 ]])
...
}
```

### NoRewindIterator

Este iterador no puede ser rebobinado (no llama a rewind).
```
NoRewindIterator extends IteratorIterator {
...
public __construct (Iterator $iterator)
...
}
```

### SplFileInfo

La clase SplFileInfo ofrece una interface orientada a objetos para la información de un archivo individual.
```
SplFileInfo {
public __construct (string $file_name)
...
}
```

### SplFileObject

SplFileObject proporciona una interface orientada a objetos para manejar archivos.
```
SplFileObject extends SplFileInfo implements RecursiveIterator, SeekableIterator {
...
public SplFileInfo::__construct (string $file_name)
...
}
```

### SimpleXMLIterator

Proporciona una iteración recursiva sobre todos los nodos de un objeto **SimpleXMLElement**.
```
SimpleXMLIterator extends SimpleXMLElement implements RecursiveIterator, Countable {
public methods: ...current, getChildren, hasChildren, key, next, rewind, valid...
...
}

```

## 15\. Otras funciones

### Diversas

bool **mail** (string $to, string $subject, string $message [, string $additional_headers [, string $additional_parameters ]] )

bool **ctype_alnum** (string $text)

bool **ctype_alpha** (string $text)

bool **ctype_digit** (string $text)

mixed **get_browser** ([string $user_agent [, bool $return_array = false ]])

string **uniqid** ([string $prefix = "" [, bool $more_entropy = false ]])

### JSON

string **json_encode** (mixed $value [, int $options = 0 [, int $depth = 512 ]])

mixed **json_decode** (string $json [, bool $assoc = false [, int $depth = 512 [, int $options = 0 ]]])

int **json_last_error** (void)

string **json_last_error_msg** (void)

### Funciones Matemáticas

int **rand** (int $min, int $max)

int **mt_rand** (int $min, int $max)

foat **round** (float $val [, int $precision = 0 [, int $mode = PHP_ROUND_HALF_UP ]] )

float **ceil** (float $value)

mixed **floor** (float $value)

number **pow** (number $base, number $exp)

float **sqrt** (float $arg)

### Ejecución de programas

string **exec** (string $command [, array &$output [, int &$return_var ]])

string **shell_exec** (string $cmd)

string **system** (string $command [, int &$return_var ])

void **passthru** (string $command [, int &$return_var ])

string **escapeshellarg** (string $arg)

string **escapeshellcmd** (string $command)

### Filtrado

array **filter_list** (void)

mixed **filter_var** (mixed $variable [, int $filter = FILTER_DEFAULT [, mixed $options ]])

mixed **filter_var_array** (array $data [, mixed $definition [, bool $add_empty = true ]])

mixed **filter_input** (int $type, string $variable_name [, int $filter = FILTER_DEFAULT [, mixed $options ]] )

mixed **filter_input_array** (int $type [, mixed $definition [, bool $add_empty = true ]] )

bool **filter_has_var** (int $type, string $variable_name)

### Encriptación de contraseñas

string **hash** (string $algo, string $data [, bool $raw_output = false ])

string **password_hash** (string $password, integer $algo [, array $options ])

boolean **password_verify** (string $password, string $hash)

boolean **password_needs_rehash** (string $hash, integer $algo [, array $options ])

### Funciones de redes

void **header** (string $string [, bool $replace = true [, int $http_response_code ]])

void **header_remove** ([string $name])

bool **header_register_callback** (callable $callback)

array **headers_list** (void)

bool **headers_sent** ([string &$file [, int &$line ]])

int **http_response_code** ([int $response_code])

bool **setcookie** (string $name [, string $value = "" [, int $expire = 0 [, string $path = "" [, string $domain = "" [, bool $secure = false [, bool $httponly = false ]]]]]] )

bool **setrawcookie** (string $name [, string $value [, int $expire = 0 [, string $path [, string $domain [, bool $secure = false [, bool $httponly = false ]]]]]] )

### Funciones de sesiones

bool **session_start** (void)

void **session_unset** (void)

bool **session_destroy** (void)

string **session_name** ([string $name])

string **session_save_path** ([string $path])

void **session_set_cookie_params** (int $lifetime [, string $path [, string $domain [, bool $secure = false [, bool $httponly = false ]]]])

string **session_id** ([string $id])

bool **session_regenerate_id** ([bool $delete_old_session = false ])

int **session_cache_expire** ([string $new_cache_expire])

string **session_cache_limiter** ([string $cache_limiter])

void **session_write_close** (void) // Alias: session_register_shutdown(), session_commit()

bool **session_set_save_handler** (callable $open, callable $close, callable $read, callable $write, callable $destroy, callable $gc [, callable $create_sid ])

### Funciones de control de salida

bool **ob_start** ([callable $output_callback = NULL [, int $chunk_size = 0 [, int $flags = PHP_OUTPUT_HANDLER_STDFLAGS ]]])

string **ob_get_contents** (void)

bool **ob_end_flush** (void)

string **ob_get_flush** (void)

bool **ob_end_clean** (void)

string **og_get_clean** (void)

void **ob_flush** (void)

void **ob_clean** (void)

### Funciones de URLs

array **get_headers** (string $url [, int $format = 0 ])

array **get_meta_tags** (string $filename [, bool $use_include_path = false ])

string **http_build_query** (mixed $query_data [, string $numeric_prefix [, string $arg_separator [, int $enc_type = PHP_QUERY_RFC1738 ]]])

mixed **parse_url** (string $url [, int $component = -1 ])

string **rawurlencode** (string $str)

string **rawurldecode** (string $str)

string **urlencode** (string $str)

string **urldecode** (string $str)

## 16\. Headers HTTP

### Requests

**Host**. $_SERVER['HTTP_HOST']

**User-Agent**. $_SERVER['HTTP_USER_AGENT']

**Accept-Language**. $_SERVER['HTTP_ACCEPT_LANGUAGE']

**Accept-Encoding**. $_SERVER['HTTP_ACCEPT_ENCODING']

**If-Modified-Since**. $_SERVER['HTTP_IF_MODIFIED_SINCE']

**Cookie**. $_COOKIE

**Referer**. $_SERVER['HTTP_REFERER']

**Authorization**. $_SERVER['PHP_AUTH_USER'], $_SERVER['PHP_AUTH_PW']

### Responses

**Cache-Control**. public, max-age, no-cache.

**Content-Type**. text/html; charset=UTF-8, image/gif, application/pdf.

**Content-Disposition**. attachment; filename="descargar.zip". Debe ir con Content-Type: application/zip.

**Content-Length**. 42341.

**Etag**. "pub34234;gz". Reques del navegador: If-None-Match: "pub34234;gz".

**Last-Modified**: Mon, 09 Nov 2015 11:50:11 GMT.

**Location**. http://www.google.com.

**Set-Cookie**. name=X; path=/; domain=google.com; expires= Sun, 21-Nov-2'15 14:44:22 GMT.

**WWW-Authenticate**. Basic realm= "Restricted Area".

**Content-Encoding**. gzip.