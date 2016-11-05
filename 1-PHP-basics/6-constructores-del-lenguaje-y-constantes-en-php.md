**Indice de contenido**

| | | |
| -------- | -------- |
| 1. Introduccion | 6. echo() VS print() | 11. list() |
| 2. die() | 7. empty() | 12. Constantes |
| 3. exit() | 8. eval() | 13. Constantes predefinidas |
| 4. echo() | 9. isset() | 14. Constantes mágicas |
| 5. print() | 10. unset() |

También se consideran estructuras de control: [return()](http://diego.com.es/estructuras-de-control-en-php#return), [include/include_once()](http://diego.com.es/estructuras-de-control-en-php#include-include_once), [require/require_once()](http://diego.com.es/estructuras-de-control-en-php#require-require_once).

### 1. Introduccion

Los **constructores del lenguaje** se usan y comportan casi idénticamente como las funciones predefinidas. La diferencia estriba en cómo el **motor PHP** los interpreta. La conclusión es que los **constructores** son ligeramente **más rápidos** y **no necesitan paréntesis**. Forman parte de la [lista de palabras reservadas de PHP](http://php.net/manual/es/reserved.keywords.php).

*   No pueden utilizarse como **constantes**, nombres de **clases**, nombres de **funciones** o nombres de **métodos**.
*   Se pueden utilizar como nombres de **variables**, pero no es recomendable.
*   No pueden ser llamadas usando [funciones variables](http://php.net/manual/es/functions.variable-functions.php).

### 2. die()

_**die()**_ equivale a _**exit()**_.

### 3. exit()

Termina el script actual y puede añadir un mensaje también. 

Al ser una **construcción del lenguaje**, se puede llamar sin paréntesis a no ser que se le pase un **status** (mensaje de salida):

Las [funciones shutdown](http://php.net/manual/es/function.register-shutdown-function.php) y los [objectos destruct](http://php.net/manual/es/language.oop5.decon.php#language.oop5.decon.destructor) serán ejecutados igualmente:

```
<?php
class Clase {
    public function __destruct()
    {
        echo 'Destructor: ' . __METHOD__ . '()' . '<br>';
    }
}
function shutdown()
{
    echo 'Shutdown: ' . __FUNCTION__ . '()' . '<br>';
}

$clase = new Clase();
register_shutdown_function('shutdown');

exit(); // Mostrará los dos echo, el del __destruct y el del shutdown
echo 'Mensaje que no se muestra';
```

### 4. echo()

Muestra **una o más** cadenas.

_**echo**_ no se comporta como una función (no siempre puede usarse en el contexto de una función). Si se quiere pasar más de un parámetro, **no** deben estar entre paréntesis.

No devuelve ningún valor.

Existe una **sintaxis abreviada** para _echo_: **<?=** $nombre **?>** que equivale a: **<?php echo** $nombre **?>**.

```
<?php

echo "echo permite
saltos de línea";

// \n separa líneas
echo "Esto es una línea\n
Esto otra;

echo "Se pueden escapar caracteres \"asi\"";

// Se pueden usar variables o arrays dentro de echo:
$color = 'verde';
$numero = 5;
$array = ['animal'=>'perro'];

echo "Color: $color";
echo "El animal es {$array['animal']}";

// Si se usan comillas simples, se muestra el nombre de la variable
echo 'Color es $color'; // Devuelve Color es $color

// Pasar varios parámetros
echo 'Esta ', 'cadena ', 'tiene ', 'varios ', 'parámetros.' ;
echo 'Esta ' . 'cadena ' . 'está ' . 'hecha ' . 'con ' . 'concatenación.';

// Sintaxis de "here document" para mostrar múltiples líneas con interpolación de $variable
echo <<<END
Hola que tal
END;
// END debe aparecer en una línea con sólo un punto y coma, SIN ESPACIOS

// Al no comportarse como una función, esto no es válido:
($variable) ? echo 'true' : echo 'false'; // Syntax error, unexpected echo
// print si que funciona como función, por lo que esto es válido:
($variable) ? print 'true' : print 'false';
```

### 5. print()

Muestra **una** cadena. Devuelve siempre 1.

Funciona casi idénticamente a echo:

```
<?php

print("Hola!");

print "print() también funciona sin paréntesis.";

print "Saltos de linea
también se muestran;

// \n separa líneas
print "Esto es una línea\n
Esto otra";

print "Se pueden también escapar caracteres \"así\"";

// Uso de variables
$color = 'verde';
print "El color es $color";

// Uso de arrays
$array = ['animal'=> 'perro'];
print "Esto es un {$array['animal']}";

// Comillas simples imprime valores literales
print 'color es $color'; // Devuelve color es $color

print <<<END
Hola que tal
END;
```

### 6\. echo() VS print()

*   _echo()_ no tiene valor de return, _print()_ tiene un valor de return de 1, por lo que puede usarse en expresiones.
*   _echo()_ acepta múltiples parámetros, mientras que _print()_ sólo acepta uno.
*   _echo()_ es mínimamente más rápido que _print()_.

### 7. empty()

Determina si una variable está **vacía**, es decir, si **no existe** o su valor es igual a **false**. Si la variable no existe no se genera ninguna advertencia. 

```
<?php
$var = '';
if(empty($var)) { echo "Variable vacía <br>"; }

// Es lo mismo que lo siguiente:

if(!isset($var) || $var == false) { echo "Variable vacía <br>"; }
```

Devuelve **false** si _$var_ existe y tiene un valor vacío distinto de 0, sino devuelve **true**. Expresiones consideradas vacías:

*   "" (cadena vacía)
*   0 (0 como integer)
*   0.0 (0 como float)
*   "0" (0 como string)
*   null
*   false
*   array() (array vacío)
*   $var (variable declarada, pero sin valor)

Hay que tener en cuenta el comportamiento que tiene cuando se pasan **índices de string**:

```
$string = 'algo';
var_dump(empty($string['cualquier_key'])); // Devuelve true
var_dump(empty($string[0])); // Devuelve false
var_dump(empty($string['0'])); // Devuelve false
var_dump(empty($string[0.5])); // Devuelve false
var_dump(empty($string['0.5'])); // Devuelve true
var_dump(empty($string['0 Mostel'])); // Devuelve true
```

*   Cuando se utiliza _empty()_ en **propiedades inaccesibles** de objetos, se llamará al [método mágico ___isset()_](http://php.net/manual/es/language.oop5.overloading.php#object.isset) si se declara.

### 8. eval()

Evalúa una cadena como código php.

No se aconseja su uso ya que permite la **ejecución de código PHP**. Si se usa con **información proporcionada por un usuario** hay que cercionarse de validar ésta antes.

El código no ha de llevar etiquetas de apertura y cierre, aunque si que se puede cerrar la que está abierta por defecto y abrir una nueva: 

```
<?php
eval('echo "Estoy en el modo PHP";?>Esto es modo HTML<?php echo "De nuevo modo PHP";');
```

Una sentencia _**return**_ finalizará la evaluación del código.

El código se ejecutará en el **ámbito del código** que llama a eval(). Cualquier **variable definida o cambiada** en _eval()_ permanecerá visible después de que finalice.

_eval()_ devuelve **null** a no ser que se llame a _return_, que entonces se devuelve el valor pasado a _return_.

Si hay **error de análisis en el código** evaluado, _eval()_ devuelve **false**, y la ejecución del código posterior seguirá su curso. **No** se pueden **capturar errores de análisis de eval()** con [set_error_handler()](http://php.net/manual/es/function.set-error-handler.php). En caso de **error fatal** se saldrá del script por completo.

_"Si eval() es la respuesta, casi seguro que estás haciendo la pregunta equivocada"_, **Rasmus Lerdorf**, creador de **PHP**.

### 9. isset()

Determina si una **variable** está **definida** y **no** es **null**.

Si una **variable** se ha removido con _**unset()**_, esta ya no estará definida, _**isset()**_ devolverá **null**.

Si se pasan **varios parámetros a isset()**, devolverá **true** sólo si todos los parámetros están definidos.

```
<?php
$var = '';
if(isset($var)) {
    echo "La variable está definida"; // Devuelve el echo
}

$x = "un valor";
$y = "otro valor";

var_dump(isset($x)); // true
var_dump(isset($y)); // true

unset($x);

var_dump(isset($x)); // false
var_dump(isset($x, $y)); // false

$var = null;
var_dump(isset($var)); // false

// También con arrays:
$array = array (
'perro' => 1,
'gato' => null,
'insecto' => array(
        'primero' => 'mosca'
));

var_dump(isset($array['perro'])); // true
var_dump(isset($array['gato'])); // false
var_dump(isset($array['insecto']['primero'])); // true
```

*   _**isset()**_ sólo trabaja con variables, para comprobar constantes: [defined()](http://php.net/manual/es/function.defined.php).
*   Cuando se utiliza _isset()_ en **propiedades inaccesibles** de objetos, se llamará al **método de sobrecarga** ___isset()_ si se declara.

Cuando se pasan **índices de string** a _isset()_:

```
<?php
$string = 'algo';
var_dump(isset($string['cualquier_key'])); // Devuelve false
var_dump(isset($string[0])); // Devuelve true
var_dump(isset($string['0'])); // Devuelve true
var_dump(isset($string[0.5])); // Devuelve true
var_dump(isset($string['0.5'])); // Devuelve false
var_dump(isset($string['0 Mostel'])); // Devuelve false
```

### 10. unset()

Destruye una variable especificada. 

_**unset()**_ depende del **ámbito** donde se ejecute. Si se utiliza dentro de una función, sólo se eliminará la **variable local**. La **variable global** se mantendrá igual. 

```
<?php
function destruir_var(){
    global $var;
    unset($var);
}
$var = 'hola';
destruir_var();
echo $var; // La variable mostrará hola
```

Para destruir una variable global desde dentro de una función:

```
<?php
function destruir(){
    unset($GLOBALS['var']);
}

$var = 'hola';
destruir();
```

Si a una **variable pasada por referencia** se le aplica _unset()_ dentro de una función, sólo la **variable local** es destruída. La **variable en el entorno de la llamada** mantendrá el mismo valor:

```
<?php
function foo(&$bar)
{
    unset($bar);
    $bar = 'hola';
}

$bar = 'adios';
echo "$bar <br>"; // Devuelve adios

foo($bar);
echo "$bar <br>"; // Devuelve adios
```

Si a una **variable estática** se le emplea _unset()_ dentro de una función, _unset()_ destruye la variable sólo en el contexto del resto de la función. Si se le llama de nuevo tendrá el valor anterior a _unset()_:

```
function foo()
{
    static $bar;
    $bar++;
    echo "Antes de unset: $bar, ";
    unset($bar);
    $bar = 23;
    echo "Después de unset: $bar <br>";
}
foo();
foo();
foo();
/*
Antes de unset: 1, Después de unset: 23
Antes de unset: 2, Después de unset: 23
Antes de unset: 3, Después de unset: 23
*/
```

**Ejemplos de unset()**:

```
<?php
// Destruir una variable
unset($var);
//Destruir un elemento de array, borra key y value
unset($bar['animal']);
// Destruir varias variables
unset($var1, $var2, $var3);
```

El forzado a (unset) convierte a null la variable en un momento determinado. Después su valor es igual al anterior:

```
<?php
$animal = 'perro';

var_dump((unset) $animal); // null
var_dump($animal); // perro
```

*   No es posible destruir **$this**
*   Si se utiliza sobre **propiedades inaccesibles de objetos**, si está declarado __unset() será llamado

### 11. list()

Asigna variables desde un array.

```
<?php
$info = array('cafe', 'marron', ' cafeina');
// Enumerar todas
list($bebida, $color, $energía) = $info;
echo "El $bebida es $color y tiene $energía <br>";
// Enumerar algunas
list($bebida, , $energía) = $info;
echo "El $bebida tiene $energía <br>";

// list() anidados, funcionan con arrays múltiples:
list($x, list($b, $c)) = array(1, array(2, 3));

var_dump($a, $b, $c);

// list() con keys de arrays:
$info = array('cafe', 'marron', 'cafeina');
list($a[0], $a[1], $a[2]) = $info;

var_dump($a); /* Devuelve los valores de derecha a izquierda:
array(3){
[2] => "cafeina"
[1] => "marron"
[0] => "cafe" */
```

**Importante**: list() sólo funciona con arrays numéricos y asume que los índices numéricos empiezan en 0. Aspecto clave para entender el [funcionamiento entre **list()** y **each()** en la iteración a través de arrays](http://diego.com.es/formas-de-recorrer-arrays-en-php#UtilizandoLaEstructuraDeControlWhile).

### 12. Constantes

Las constantes son **nombres** (identificadores) que expresan un **valor** y que no varían durante la ejecución del script. Son sensibles a mayúsculas y minúsculas, pero por convención se usan **siempre en mayúsculas**.

*   Tienen que empezar por letra o barra baja, y puede ir seguido de cualquier número, letra o barra baja.
*   Conviene no utilizar doble barra baja al principio en la definición de variables para evitar posibles colisiones futuras con [constantes mágicas](http://php.net/manual/es/reserved.constants.php).
*   Deben **definirse** antes de usarse y **no** se puede **cambiar** su valor después (devuelve un Notice Constant already defined).

```
<?php
define("SALUDO", "Hola, ¿Qué tal?");
echo SALUDO;
```

### 13. Constantes predefinidas

Existen alrededor de 50 [constantes predefinidas](http://php.net/manual/es/language.constants.predefined.php) **en el núcleo de PHP**, de las cuales se enumeran algunas de las más importantes:

| PHP_VERSION | Versión actual de PHP |
| PHP_EOL | Símbolo de fín de línea para la plataforma en uso |
| PHP_INT_MAX | Número entero más grande admitido en la compilación actual. Suele ser _int(2147483647)_ |
| DEFAULT_INCLUDE_PATH | Directorio por defecto para include |
| PHP_EXTENSION_DIR | Directorio de las extensiones PHP |
| PHP_MANDIR | Directorio donde están instalados los manuales |
| PHP_CONFIG_FILE_PATH | Directorio donde se encuentra el archivo de configuración _php.ini_  |
| TRUE | Verdadero |
| FALSE | Falso |
| NULL | Sin valor |
| **Constantes de error:** |
| E_ERROR (1) | Errores fatales en tiempo de ejecución, no se pueden recuperar y la ejecución del script se interrumpe |
| E_WARNING (2) | Advertencias en tiempo de ejecución. La ejecución del script no se interrumpe |
| E_NOTICE (8) | Avisos en tiempo de ejecución. El script encontró algo que podría señalar como error. |
| E_CORE_ERROR (16) | Errores fatales que ocurren durante el arranque inicial de PHP |
| E_CORE_WARNING (32) | Advertencias que ocurren durante el arranque inicial de PHP |
| E_COMPILE_ERROR (64) | Errores fatales en tiempo de compilación |
| E_COMPILE_WARNING (128) | Advertencias en tiempo de compilación |
| E_USER_ERROR (256) | Mensaje de error generado por el usuario. Es como E_ERROR pero generado con la función [trigger_error()](http://php.net/manual/es/function.trigger-error.php) |
| E_USER_WARNING (512) | Mensaje de advertencia generado por el usuario con trigger_error() |
| E_USER_NOTICE (1024) | Mensaje de aviso generado por el usuario con trigger_error() |
| E_STRICT (2048) | PHP sugiere cambios en el código que aseguran la interoperabilidad y compatibilidad con versiones posteriores |
| E_RECOVERABLE_ERROR (4096) | Error fatal capturable. Ocurrió un error probablemente peligroso, pero no dejó al motor de PHP en un estado inestable. Si no se captura el error mediante un gestor definido por el usuario (véase [set_error_handler()](http://php.net/manual/es/function.set-error-handler.php)), la aplicación se abortará como si fuera un E_ERROR. |
| E_DEPRECATED (8192) | Avisos en tiempo de ejecución sobre código que no funcionará en futuras versiones |
| E_ALL (32767) | Todos los errores y advertencias soportados |

### 14. Constantes mágicas

Existen 8 **constantes mágicas** predefinidas de PHP en cualquier script, que cambian en función de donde son usadas (aunque también existen otras creadas por extensiones que han de ser cargadas o compiladas).

| **Nombre** | **Descripción** |
| `__LINE__` | Número de línea actual del archivo |
| `__FILE__` | **Ruta** completa y nombre del archivo actual |
| `__DIR__` | **Directorio** del archivo. Equivale a dirname(`__FILE__`). El directorio no lleva la barra final (/) a no ser que sea el directorio root |
| `__FUNCTION__` | Nombre de la función. Incluye namespace si tiene. |
| `__CLASS__` | Nombre de la **clase**. Incluye el **namespace**. También funciona con **traits**, pero devuelve el nombre de la clase que usa el trait. |
| `__TRAIT__` | Nombre del **trait**. Incluye el **namespace** |
| `__METHOD__` | Nombre del **método** de la clase. Incluye el nombre de la clase y namespace si tiene. |
| `__NAMESPACE__` | Nombre del **namespace** actual |