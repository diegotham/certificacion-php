**Indice de contenido**

1.  Introducción
2.  ¿Qué es UTF-8?
3.  Equivalencias de mbstring con funciones de strings
4.  Configuración de mbstring

### 1. Introducción

Un **bit** es la unidad básica de almacenamiento de información, y un **byte** es una secuencia de ocho bits. El valor máximo que puede tomar un byte es 256.

Si partimos del idioma inglés, cada carácter (a, b, c...) está representado en el ordenador en un **byte**. El número total de caracteres en inglés no es mayor a 256, así que cada carácter puede ser representado usando diferentes secuencias de 8 bits. Por ejemplo en el [código de caracteres ASCII](http://www.ascii-code.com/), la letra D mayúscula tiene asignado el número binario 01000100, que escrito en número decimal es el 68\. UTF-8 hereda la misma base de codificación y tiene los mismos bits asignados a los caracteres más básicos.

En un idioma diferente al inglés, como el chino mandarín, un sólo byte para representar un carácter se queda corto, y aquí es necesario que se empleen **multibytes**.

Las funciones básicas de strings como _strcmp()_ o _strlen()_ funcionan **incorrectamente** para idiomas con caracteres especiales, como el chino mandarín o el español. Es por ello que **para trabajar con cadenas de caracteres multibyte**, se necesitan funciones especiales, en **PHP** estas funciones son posibles con la [extensión incorporada mbstring](http://php.net/manual/en/book.mbstring.php).

### 2. ¿Qué es UTF-8?

**UTF** significa **Unicode Transformation Format** y es un **sistema de codificación** cuyo objetivo es representar cada carácter de cada lenguaje en un conjunto de caracteres. Existen diferentes versiones de UTF, las más conocidas son:

| | |
| -------- | -------- |
| **Formato de codificación** | **Descripción** |
| UTF-8 | Sistema de codificación de 8 bits, compatible con ASCII, amplitud variable |
| UTF-16 | Sistema de codificación de 16 bits, amplitud variable |
| UTF-32 | Sistema de codificación de 32 bits, amplitud fija |

Cuando se trabaja con **caracteres multibyte** se suele trabajar con **UTF-8**, que además de ser **compatible con ASCII**, codifica caracteres en **múltiples bytes** con **amplitud variable**, es decir, que si puede emplear un byte para un carácter empleará un byte. El **UTF-32** por ejemplo emplea 4 bytes para cualquier carácter.

### 3. Equivalencias de mbstring con funciones de strings

Para **funciones básicas de manipulación de strings**, como _strlen()_ o _substr()_, existen versiones compatibles con **caracteres multibyte**. Si no se usan estas versiones surgen resultados inesperados y errores de codificación.

Tabla de equivalencias de funciones de strings y mbstring:

| | | |
| -------- | -------- |
| **Monobyte** | **Multibyte** | **Descripción** |
| strlen() | mb_strlen | Devuelve la longitud del string |
| strpos() | mb_strpos() | Encuentra la posición de la primera ocurrencia de un string en otro |
| substr() | mb_substr() | Devuelve parte de un string |
| strtolower() | mb_strtolower() | Devuelve un string en minúsculas |
| strtoupper() | mb_strtoupper() | Devuelve un string en mayúsculas |
| substr_count() | mb_substr_count() | Cuenta el número de ocurrencias de un substring |
| split() | mb_split() | Divide un string en un array mediante una expresión regular |
| mail() | mb_send_mail() | Envía un email |

Las funciones preg_* vienen con compatibilidad con UTF-8 por defecto.

Existen más **funciones compatibles**, las puedes ver en esta [lista de funciones para strings multibyte](http://php.net/manual/es/ref.mbstring.php).

**Algunos ejemplos**:

```
// strlen y mb_strlen
$cadena = "Ese camión es marrón";
echo strlen($cadena) . PHP_EOL; // Devuelve 22
echo mb_strlen($cadena). PHP_EOL; // Devuelve 20

// strpos y mb_strpos
$cadena = "Araña marrón";
echo strpos($cadena, "a", 3). PHP_EOL; // Devuelve 5
echo mb_strpos($cadena, "a", 3). PHP_EOL; // Devuelve 4

// strtolower y mb_strtolower
$cadena = "MAYÚSCULAS";
echo strtolower($cadena); // Devuelve: mayÚsculas
echo mb_strtolower($cadena); // Devuelve: mayúsculas
```

En cada una de las funciones **mb_** es posible añadir un argumento para indicar el **esquema de codificación** que se quiere utilizar (como UTF-8). También es posible configurar globalmente el esquema que se quiere utilizar en todas las **funciones multibyte** desde el _php.ini_ con _mbstring.internal_encoding_. Generalmente es mejor esta segunda opción.

### 4. Configuración de mbstring

Para poder utilizar las **funciones mbstring** tiene que existir la extensión en la instalación de PHP. Puede comprobarse mediante la función _phpinfo()_ o mirando si está el archivo _php_mbstring.dll_ en la carpeta de extensiones.

También tiene que estar activada en en archivo _php.ini_. Desde este mismo archivo se pueden personalizar algunas configuraciones importantes para nuestras aplicaciones:

| | |
| -------- | -------- |
| **Nombre** | **Valor por defecto** | **Opciones de cambio*** | **Descripción** |
| mbstring.language | neutral | PHP_INI_ALL | Ajuste de lenguaje nacional predeterminado NLS usado en mbstring. |
| mbstring.detect_order | NULL | PHP_INI_ALL | Define el orden de detección de códigos de caracteres predeterminado. Se puede establecer y obtener con [mb_detect_order()](http://php.net/manual/es/function.mb-detect-order.php). |
| mbstring.substitute_character | NULL | PHP_INI_ALL | Define el carácter de sustitución para caracteres inválidos. |
| mbstring.func_overload | "0" | PHP_INI_SYSTEM | Sustituye el comportamiento de las funciones de strings monobyte por sus homólogas multibyte. |
| mbstring.encoding_translation | "0" | PHP_INI_PERDIR | Activa el filtro transparente de codificación de caracteres para las peticiones HTTP, lo que hace que PHP detecte y convierta la codificación de entrada a la codificación del sistema. |
| mbstring.strict_detection | "0" | PHP_INI_ALL | Activa la detección de la codificación del script. |