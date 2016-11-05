**Índice de contenido**

| | |
| -------- | -------- |
| 1. Precedencia de operadores | 7. Operadores de ejecución |
| 2. Operadores artiméticos | 8. Operadores de incremento/decremento |
| 3. Operadores de asignación | 9. Operadores lógicos |
| 4. Operadores bit a bit | 10. Operadores para strings |
| 5. Operadores de comparación | 11. Operadores para arrays |
| 6. Operadores de control de errores | 12. Operadores de tipo |

### 1. Precedencia de operadores

La **precedencia de los operadores en PHP** sigue casi las mismas reglas que en **matemáticas**, por ejemplo: multiplicación y división preceden a suma y resta, los **paréntesis** fuerzan una precedencia, etc.

Cuando tienen la misma **precedencia**, su **asociatividad** decide cómo ordenar los operadores. Por ejemplo el operador **suma +** tiene asociatividad izquierda, por lo que la operación 1 + 2 + 3 sumará primero 1 y 2, y después 3 => (1 + 2) + 3\. Por el contrario, el operador **=** tiene asociatividad derecha, por lo que $x = $y = $z se agrupa de forma $x = ($y = $z).

Los **operadores de misma precedencia** que **no** son **asociativos** no pueden usarse juntos, esto: 1 < 2 > 1 es **incorrecto**. 

En ocasiones es aconsejable usar paréntesis aunque no sea necesario para mejorar la legibilidad.

La precedencia de operadores y su asociatividad sólo determinan **cómo se agrupan las expresiones**. 

Lista de **operadores ordenados por precedencia**:

| | |
| -------- | -------- |
| **Asociatividad** | **Operadores** |
| No asociativo | clone, new |
| Izquierda | [ |
| Derecha | ** |
| Derecha | ++, --, ~, (int), (float), (string), (array), (object), (bool), @ |
| No asociativo | instanceof |
| Derecha | ! |
| Izquierda | *, /, % |
| Izquierda | +, -, . |
| Izquierda | <<, >> |
| No asociativo | <, <=, >, >= |
| No asociativo | ==, !=, ===, !==, <> |
| Izquierda | & |
| Izquierda | ^ |
| Izquierda | | |
| Izquierda | && |
| Izquierda | || |
| Izquierda | ?: |
| Derecha | =, +=, -=, *=, **=, /=, .=, %=, &=, /=, ^=, <<=, >>=, => |
| Izquierda | and |
| Izquierda | xor |
| Izquierda | or |
| Izquierda | , |

### 2. Operadores aritméticos

Los **operadores aritméticos en PHP** son los mismos que en las matemáticas:

| | |
| -------- | -------- |
| **Operadores artiméticos** | **Representación** |
| Suma | $x + $y |
| Resta | $x - $y |
| Multiplicación | $x * $y |
| División* | $x / $y |
| Módulo** | $x % $y |
| Exponenciación | $x ** $y |
| Negación | -$x |

***División** devuelve un _(int)_ si $x y $y son divisibles, o _(float)_ si no lo son.

**En **Módulo** se eliminarían primero las partes decimales transformándose en _(int)_ en caso de ser _(float)_ y luego se haría la operación. El signo (+ o -) del resultado dependerá del **dividendo**, por ejemplo: -5 % 3 mostraría -2. 

### 3. Operadores de asignación

Existen **operadores básicos** y **combinados**:

El **operador básico** de asignación es "**=**", que actúa como **definidor**, **no** como **igualador**. El valor de una expresión de asignación es el valor que se le ha asignado, esto es: "$x = 3" tiene un valor de 3. 

En el caso de **arrays**, se asigna un valor a una clave nombrada mediante el operador "**=>**".

Los **operadores combinados** permiten usar un valor en una expresión y establecer su nuevo valor como resultado de esa expresión:

```
<?php
$x = 3;
$x += 5;
// $x vale ahora 8

$y = "Hola ";
$y .= ", ¿Qué tal?";
// $y vale ahora "Hola, ¿Qué tal?"
```

Hay una **excepción** a la asignación por valor en PHP, y son los **objetos**, que se asignan por referencia. Los objetos se copian mediante la palabra _**clone**_.

#### **Asignación por referencia**

La **asignación por referencia** significa que las variables apuntan a los **mismos valores**, **sin** hacer ninguna **copia:**

```
<?php
$x = 3;
$y = &$x;

print "$x, $y"; // Mostrará 3, 3

$x = 5;

print "$x, $y"; // Mostrará 5, 5
```

Las **referencias** actúan como cuando se crea un **acceso directo** o **alias** de un archivo o carpeta en el ordenador. 

### 4. Operadores bit a bit

Los **operadores bit a bit** permiten la **evaluación y manipulación de bits específicos** dentro de un **integer**.

| | |
| -------- | -------- |
| **Ejemplo** | **Nombre** |
| $x & $y | And |
| $x | $y | Or |
| $x ^ $y | Xor |
| ~$x | Not |
| $x << $y | Shift left (desplazamiento izquierda) |
| $x >> $y | Shift right (desplazamiento derecha) |

### 5. Operadores de comparación

Los **operadores de comparación** permiten comparar dos valores. Estos valores dependen de los [tipos que tengan asignados](http://php.net/manual/es/language.types.type-juggling.php). Pueden verse las diferencias comparativas en la [tabla de comparación de tipos](http://php.net/manual/es/types.comparisons.php).

| | | |
| -------- | -------- |
| **Ejemplo** | **Nombre** | **Resultado** |
| $x == $y | Igual | true sean del mismo tipo o no |
| $x === $y | Idéntico | true sólo si son del mismo tipo |
| $x != $y | Distinto | true si son diferentes sean del mismo tipo o no |
| $x <> $y | Distinto | true si son diferentes sean del mismo tipo o no |
| $x !== $y | No idéntido | true sólo si no son iguales y tampoco del mismo tipo |
| $x < $y | Menor que | true si $x es menor que y |
| $x > $y | Mayor que | true si $x es mayor que $y |
| $x <= $y | Menor o igual que | true si $x es menor o igual que $y |
| $x >= $a | Mayor o igual que | true si $x es mayor o igual que $y |

Si se compara un número con un _string_ o la comparación es entre **_strings_ numéricos**, cada _string_ se convierte en número y la comparación se realiza **numéricamente** (esto también se incluye con el uso de _**switch**_). Cuando se hacen **comparaciones idénticas** como === esto no tiene sentido ya que también se comparan los tipos.

```
<?php
var_dump(0 == "x"); // 0 == 0 true
var_dump("1" == "01"); // 1 == 1 true
var_dump("10" == "1y1"); // 10 == 10 true
var_dump(100 == "1e2"); // 100 == 100 true

switch ("x") {
case 0:
    echo "0";
    break;
case "x": // Nunca llegará porque "x" ya se ha encontrado con el caso "0"
    echo "x";
    break;
}
```

Cuando un string se evalúa en un contexto numérico, el valor y el tipo dependen de la [conversión de string a números](http://php.net/manual/es/language.types.string.php#language.types.string.conversion).

**Comparaciones entre varios tipos**:

| | | |
| -------- | -------- |
| **$x** | **$y** | **Resultado** |
| _null_ o _string_ | _string_ | _null_ se convierte en "", comparación numérica o léxica |
| _bool_ o _null_ | cualquiera | Ambos serán _bool_, y **false** < **true** |
| _object_ | _object_ | Dos objetos serán **iguales** si tienen los mismos atributos y valores, siendo instancias de la misma clase. Dos objetos serán **idénticos** si además hacen referencia a la misma instancia de la misma clase.| 
| _string_, _resource_ o _number_ | _string_, _resource_ o _number_ | Se traducen a números y se comparan matemáticamente |
| _array_ | _array_ | Un _array_ con menos elementos es menor. Si una _key_ no coincide en ambos _arrays_, son **_arrays_ incomparables**. Se compara valor por valor. |
| _object_ | cualquiera | object siempre mayor |
| _array_ | cualquiera | array siempre mayor |

Debido a la forma en que son interpretados internamente los _floats_, su comparación puede dar resultados inesperados, aunque existen [formas de poder compararlos](http://php.net/manual/es/language.types.float.php#language.types.float.comparison).

#### **El operador ternario**

```
<?php
$x = "";
$accion = (empty($x)) ? 'valor1' : 'valor2'; // Devolverá (string) valor1

// Lo anterior será lo mismo que:
if(empty($x)) {
    $accion = 'valor1';
} else {
    $accion = 'valor2';
}
```

Supongamos la expresión $x ? $y : $z. Si $x es **true**, se evaluará $y. Si $x es **false**, se evaluará $z.

Una **forma más reducida del operador ternario** es mediante **?:**. Suponiendo $x ?: $y, devolverá $x si $x es true, y $y si $x es false.

### 6. Operadores de control de errores

Cuando se antepone el símbolo de arroba **@** ante cualquier **expresión**, cualquier **mensaje de error** que pueda generar esa expresión será ignorado.

Si [track_errors](http://php.net/manual/es/errorfunc.configuration.php#ini.track-errors) está activado, el **último error** se guardará en la variable _**$php_errormsg**_ y podrá emplearse.

```
<?php
$miArchivo = @file('archivo_que_no_existe') or
    die("No se ha podido abrir, el error ha sido '$php_errormsg'");

// Funciona también si por ejemplo se intenta acceder al key de un array que no existe:
$valor = @$array[$key]
```

@ **Sólo funciona con expresiones** (variables, llamadas a funciones o includes, constantes...) no funciona con definiciones de clases o funciones, condicionales como if o foreach...

### 7. Operadores de ejecución

El operador de ejecución `` hace que el contrenido entre las comillas invertidas se ejecute como **comando de consola**. Este operador es lo mismo que usar la functión [shell_exec()](http://php.net/manual/es/function.shell-exec.php).

```
<?php
$var = `ls -al`; // Comando linux para mostrar un listado de archivos completo del directorio actual
echo "<pre>$var</pre>";
```

*   Si [safe_mode](http://php.net/manual/es/ini.sect.safe-mode.php#ini.safe-mode) está activado o [shell_exec()](http://php.net/manual/es/function.shell-exec.php) está desactivado, las comillas no funcionarán.
*   Las comillas invertidas no significan nada cuando van dentro de comillas dobles.

### 8. Operadores de Incremento/decremento

Los **operadores de incremento** y **decremento** sólo afectan a **_números_** y **_strings_**, sin afectar a **_arrays_**, **_objects_** o **_resources_**. Decrementar un valor **NULL** no tiene efecto, pero si se incrementa se obtiene 1\. Incrementar o decrementar booleanos no tiene efecto.

| | |
| -------- | -------- |
| **Operador** | **Efecto** |
| ++$x | Incrementa $x en 1 y luego devuelve $x |
| $x++ | Retorna $x, y luego incrementa $x en 1 |
| --$x | Decrementa $x en 1, y luego retorna $x |
| $x-- | Retorna $x, y luego decrementa $x en 1 |

**Ejemplo**:

```
<?php
$x = 4;
echo "Esto es 4: " . $x++ . "</br>";
echo "Y esto es 5: " . $x . "</br>";

$x = 4;
echo "Esto es 5: " . ++$x . "</br>";
echo "Y esto es 5: " . $x . "</br>";

$x = 4;
echo "Esto es 4: " . $x-- . "</br>";
echo "Y esto es 3: " . $x . "</br>";
```

Al trabajar sobre caracteres, de Z pasa a AA. Por ejemplo:

```
<?php
$x = 'Z';
echo ++$x; // Devolverá AA
echo ++$x; // Devolverá AB

$x = 'A9';
echo ++$x; // Devolerá B0
echo ++$x; // Devolverá B1

$x = 'A09';
echo ++$x; // Devolverá A10
echo ++$x; // Devolverá A11
```

### 9. Operadores lógicos

| | |
| -------- | -------- |
| **Operador** | **Resultado** |
| $x and $y | true si $x y $y son true |
| $x or $y | true si uno de los dos es true (o los dos) |
| $x xor $y | true si **sólo uno** de los dos es true |
| !$x | true si $x **no** es true |
| $x && $y | true si $x y $y son true |
| $x || $y | true si uno de los dos es true (o los dos) |

* **&&** y **||** tienen precedencia sobre **and** y **or**.

**Notas importantes**:

```
<?php
$w = (false && foo()); // false
$x = (true  || foo()); // true
$y = (false and foo()); // false
$z = (true  or  foo()); // true

// Actúa como: ($m = (false || true))
$m = false || true;
// Actúa como: (($n = false) or true)
$n = false or true;

var_dump($m, $n);

// Actúa como: ($a = (true && false))
$a = true && false;
// Actúa como: (($b = true) and false)
$b = true and false;

var_dump($a, $b);
```

### 10. Operadores para strings

Existen dos operadores para strings:

*   **Operador de concatenación** '**.**'. Concatena los argumentos derecho e izquierdo.
*   **Operador de asignación sobre concatenación** '**.=**', añade el argumento del lado derecho al argumento del lado izquierdo.

```
<?php
$x = "Hola";
$y = $x . ", ¿Qué tal?"; // $y = "Hola, ¿Qué tal?"

$x = "Hola ";
$x .= ", ¿Qué tal?"; // $x = "Hola, ¿Qué tal?"
```

### 11. Operadores para arrays

| | |
| -------- | -------- |
| **Operador** | **Resultado** |
| $x + $y | Unión de $x e $y |
| $x == $y | true si $x e $y tienen las mismas parejas _key_ => _value_ |
| $x === $y | true si $x e $y tienen mismas parejas _key_ => _value_, mismo orden y mismos tipos |
| $x != $y | true si $x no es igual a $y |
| $x <> $y | true si $x no es igual a $y |
| $x !== $y | true si $x no es idéntica a $y |

El operador **+** devuelve el array derecho (**$y**) añadido al del izquierdo (**$x**). Si hay _keys_ que existen en ambas, se utilizarán las _keys_ del array izquierdo.

**Ejemplo de unión:**

```
$a = array("x" => "perro", "y" => "gato");
$b = array("x" => "pato", "y" => "liebre", "z" => "conejo");
// Unión de $a y $b
$c = $a + $b;
echo "Unión de \$a y \$b: \n";
var_dump($c);
/*
* array (size=3)
  'x' => string 'perro' (length=5)
  'y' => string 'gato' (length=4)
  'z' => string 'conejo' (length=6)
*/
// Unión de $b y $a
$c = $b + $a;
echo "Union de \$b y \$a: \n";
var_dump($c);
/*
* array (size=3)
  'x' => string 'pato' (length=4)
  'y' => string 'liebre' (length=6)
  'z' => string 'conejo' (length=6)
*/
```

**Ejemplo de comparación:**

```
$x = array("perro", "gato");
$y = array(1 => "gato", "0" => "perro");

var_dump($x == $y); // bool(true)
var_dump($x === $y); // bool(false)
```

### 12. Operadores de tipo

_**instanceof**_ es utilizado para saber si una **variable** es un **objeto instanciado** de una **clase**.

```
class MiClase
{
}

class NoEsMiClase
{
}
$x = new MiClase;

var_dump($x instanceof MiClase); // true
var_dump($y instanceof NoEsMiClase); // false
```

*   Devuelve true si la **instancia** es de una **clase padre**. _instanceof PadreClass_
*   Devuelve true con la **interface** de una **clase que la implementa**. _instanceof MyInterface_
*   Devuelve true si la variable es un _**string**_ con el nombre de la clase:
    $c = 'MiClase';
    _instanceof $c_
*   Si la variable que está siendo comprobada no es un objeto, no devuelve error, devuelve **false**.