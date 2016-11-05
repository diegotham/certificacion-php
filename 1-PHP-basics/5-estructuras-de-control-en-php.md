Un script PHP está contruido a partir de **sentencias**. Una sentencia puede ser una asignación, una llamada a una función, un loop, una sentencia condicional o una sentencia vacía. Las sentencias normalmente finalizan con un punto y coma.

Las **estructuras de control** son sentencias que permiten controlar cómo el código fluye en nuestro script basándose en ciertos factores. Por ejemplo, cuando queremos realizar una acción sólo si cierta variable está definida, o cuando queremos mostrar un **array** de datos a través de un **loop**.

Las estructuras de control son mayoritariamente **condicionales** (if, switch, etc) o **loops** (for, foreach, etc).

**Indice de contenido**

| | |
| -------- | -------- |
| 1. if | 9. continue |
| 2. else | 10. switch |
| 3. elseif/else if | 11. declare |
| 4. while | 12. return |
| 5. do-while | 13. include/include_once |
| 6. for | 14. require/require_once  |
| 7. foreach | 15. goto |
| 8. break | 16. Sintaxis alternativas |

### 1. if

La estructura de control **if** permite la ejecución condicional de fragmentos de código.

```
<?php
if ($x > $y) {
    echo "$x es mayor que $y";
}
```

Las sentencias if se pueden incluir unas dentro de otras indefinidamente.

### 2. else

Sirve para ejecutar una sentencia cuando otra no se cumple. _**else**_ extiende una sentencia _**if**_, y se ejecuta cuando ésta es **false**. Siguiendo el ejemplo anterior:

```
<?php
if ($x > $y) {
    echo "$x es mayor que $y";
} else {
    echo "$y es mayor que $x";
}
```

### 3. elseif/else if

Es una combinación entre _**if**_ y _**else**_. Se ejecuta cuando if es **false**, pero sólo si la expresión condicional del _**elseif**_ es **true**.

```
<?php
if ($x > $y) {
    echo "$x es mayor que $y";
} elseif ($x == $y) {
    echo "$x es igual que $y";
} else {
    echo "$y es mayor que $x";
}
```

Puede haber varios _**elseif**_ dentro de una sentencia _**if**_ (aunque en ese caso suele ser más recomendable usar _**switch**_). No hay diferencias de comportamiento entre _**elseif**_ y _**else if**_. 

_**elseif**_ sólo se ejecuta si el _**if**_ precedente o cualquier _**elseif**_ anterior son evaluadas como **false**.

### 4. while 

Es el tipo más sencillo de **loop** en **PHP**. Se ejecutan las sentencias dentro del _**while**_ siempre y cuando se evalúen como **true**. El valor de la expresión se comprueba cada vez al inicio del **loop**, y la ejecución no se detendrá hasta que finalice la **iteración** (cada vez que PHP ejecuta las sentencias en un loop es una iteración). Si la expresión _**while**_ se evalúa como **false**, las sentencias no se ejecutarán ni siquiera una vez.

También es posible agrupar varias instrucciones _**while**_ dentro de una.

```
<?php
$i = 1;
while($i <= 10){
    echo $i;
    $i++;
}
```

### 5. do-while

Muy similares a los loops _**while**_, simplemente aquí la expresión para el loop se verifica al final en lugar de al principio, esto garantiza que el código se ejecute por lo menos la primera vez. 

```
<?php
$i = 0;
do {
    echo $i;
} while ($i > 0);
```

Este loop se ejecutaría sólo una vez, ya que después no cumple la condición.

### 6. for

Los loops _**for**_ son los más complejos en PHP. 

```
<?php
for ($i = 1; $i <= 10; $i++) {
    echo $i;
} // Devuelve 123456789
```

*   Las expresiones o conjunto de expresiones van separadas por punto y coma **;** y sólo hay 3.
*   La primera expresión, **$i = 1**, se ejecuta una vez incondicionalmente al comienzo del bucle. 
*   La segunda expresión, **$i <= 10**, es una **condición**, si es true, se ejecutará la tercera expresión.
*   La tercera expresión, **$i++**, es la acción a realizar si se cumple la segunda expresión.

Cada una de las expresiones pueden estar **vacías** o contener **múltiples expresiones**, lo que resulta útil en ciertas ocasiones. Si la expresión 2 está vacía, el loop será definido como **true**. 

```
<?php
// Todos los siguientes ejemplos son válidos y devuelven lo mismo, 123456789
// EJEMPLO 1
for($i = 1; $i <= 10; $i++) {
    echo $i;
}

// EJEMPLO 2
for ($i = 1; ; $i++){
    if($i > 10) {
        break;
    }
    echo $i;
}

// EJEMPLO 3
$i = 1;
for( ; ; ){
    if($i > 10){
        break;
    }
    echo $i;
    $i++;
}

// EJEMPLO 4
for ($i = 1, $j = 0; $i <= 10; $j += $i, print $i, $i++);
```

Dependiendo de donde se coloque el operador **incremento** o **decremento**, dará un resultado distinto:

```
for ($i = 1; $i <= 10; print $i++); // 12345678910
for ($i = 1; $i <= 10; print ++$i); // 234567891011
```

Se pueden hacer **iteraciones por medio de arrays**:

```
$gente = array(
    array('nombre' => 'Carlos', 'salt' => 123),
    array('nombre' => 'Maria', 'salt' => 124));
// Numero de gente
$size = count($gente);
// Loop que da un salt de 4 dígitos aleatorio
for ($i=0; $i < $size; ++$i) {
    $gente[$i]['salt'] = mt_rand(0000, 9999);
}
var_dump($gente);
/*
* array (size=2)
  0 =>
    array (size=2)
      'nombre' => string 'Carlos' (length=6)
      'salt' => int 2029
  1 =>
    array (size=2)
      'nombre' => string 'Maria' (length=5)
      'salt' => int 9724
*/
```

### 7. foreach

_**foreach**_ permite una forma fácil de iterar sobre **arrays** u **objetos**.

Cuando _**foreach**_ inicia su ejecución, el puntero apunta directamente al primer elemento del **array**, por lo que no es necesario llamar a la función _reset()_ antes que un **loop** _foreach_. Es recomendable no cambiar el puntero dentro del loop.

Se puede iterar de las siguientes dos formas:

```
<?php
// Devuelve directamente el value de cada key, comenzando desde el primero
foreach ($array as $value) {
    // Lo que se desee mostrar
}
// Devuelve cada key con cada value, para poder trabajar con cualquiera de los dos
foreach ($array as $key => $value){
    // Lo que se desee mostrar
}
```

Se pueden modificar los elementos del array dentro del loop, anteponiendo **&** a _$value_ (asignándolo por referencia).

```
<?php
$array = array(1, 2, 3, 4);
foreach ($array as &$value){
    $value = $value * 2;
}
// cada valor del array vale ahora : 2, 4, 6, 8
unset($value);
```

La función _unset()_ elimina la referencia del último elemento, pero el array sigue valiendo 2, 4, 6, 8\. Es recomendable hacer _unset()_ porque la referencia del _$valor_ y el último elemento del array permanecen aún después del loop foreach.

[_foreach_](http://php.net/manual/es/control-structures.foreach.php) y _[list](http://php.net/manual/es/function.list.php)_ pueden devolver los mismos resultados de la siguiente forma:

```
<?php
$array = array("uno", "dos", "tres");
reset($array);
while (list($clave, $valor) = each($array)){
    echo "Clave: $clave; Valor: $valor </br>";
}

foreach ($array as $clave => $valor){
    echo "Clave: $clave; Valor: $valor </br>";
}
```

La función _list()_ coge un _array_ y lo convierte en **variables** individuales.

_each()_ coge un _array_ y devuelve el key y value actuales, es decir, donde está apuntando el cursor en el array. Es necesario hacer un _[reset()](http://php.net/manual/es/function.reset.php)_ del _array_ para asegurarse que el cursor comienza desde el principio (cosa que no es necesaria con el foreach).

Suele ser más óptimo y legible utilizar el _**foreach**_ para iterar, aunque ambos se utilizan más o menos con la misma frecuencia.

Con **arrays multidimensionales** se puede hacer un **doble foreach**:

```
<?php
$x = array();
$x[0][0] = "a";
$x[0][1] = "b";
$x[1][0] = "y";
$x[1][1] = "z";

foreach ($x as $primero){
    foreach ($primero as $segundo){
        echo "$segundo" . "</br>";
        }
    }

```

En el ejemplo anterior, si no se hace doble foreach con _$x_ y se hace sólo un foreach surgirá un _Notice: Array to string conversion_.

Desde PHP 5.5 se puede recorrer un **array de arrays** y utilizar el segundo array para asignar variables:

```
<?php
$array = array(
    array('azul', 'rojo'),
    array('verde', 'amarillo')
    );

foreach ($array as list($a, $b)) {
    echo "A: $a; B: $b" . "</br>";
}
// Devuelve: 
```

### 8. break

_**break**_ termina la ejecución de las siguientes estructuras: _**for**_, _**foreach**_, _**while**_, _**do-while**_ y _**switch**_.

Se puede añadir un **argumento numérico opcional** que indica de cuántas estructuras debe salir. El valor por defecto es 1:

```
// Ejemplo sin valor numérico
$array = array('uno', 'dos', 'parar', 'tres');
while(list(, $valor) = each($array)){
    if($valor == 'parar'){
        break;
    }
    echo "$valor</br>";
}
// Ejemplo con valor numérico
$i = 0;
while (++$i){
    switch($i){
        case 5:
            echo "He llegado a 5 </br>";
            break 1; // Aquí sólo saldría del switch
        case 10:
            echo "He llegado a 10 </br>";
            break 2; // Sale del switch y del while
        default:
            break;
    }
}
```

### 9. continue

Se utiliza **dentro de las estructuras iterativas** para **saltar** el resto de la iteración actual del loop y **continuar a la siguiente iteración** para su ejecución:

```
<?php
for ($i=0; $i < 10; $i++) {
    if($i % 2 == 0)
        continue;
    print "$i ";
} // Devuelve 1 3 5 7 9
```

El código anterior hace que cuando el número sea par, _**continue**_ se ejecute y no muestre el print de después, sino que vuelva a empezar con la siguiente iteración. _**print**_ sólo se ejecutará cuando _$i_ sea impar.

Al igual que con _**break**_, se puede añadir un número a _**continue**_ que indica el número de niveles de loops debe saltar:

```
<?php
$i = 0;
while ($i++ < 5){
    echo "Uno </br>";
    while (1) {
        echo "Dos </br>";
        while (1) {
            echo "Tres </br></br>";
            continue 3;
        }
        echo "Esto no aparece.";
    }
    echo "Esto tampoco aparece.";
}
```

En este ejemplo, cuando llega a _**continue 3**_, comienza de nuevo la iteración, y se comprueba otra vez la condición ($i++ < 5). Si se pone, por ejemplo, _**continue 2**_, salta al segundo while, cuya condición siempre se cumple, y se produce un **loop infinito** que imprime "Dos Tres" continuamente.

### 10. switch

_**switch**_ es como una serie de sentencias _**if**_. Es útil para comparar una misma **variable o expresión** con valores direrentes y ejecutar un código diferente a otro dependiendo de esos valores. 

```
<?php
switch ($i) {
    case "perro":
        echo "\$i es un perro";
        break;
    case "gato":
        echo "\$i es un gato";
        break;
    case "avestruz":
        echo "\$i es un avestruz";
        break;
}
```

Cuando una sentencia _**case**_ coincide con el valor de la sentencia _**switch**_, PHP ejecuta el código dentro del _case_. PHP sigue ejecutando las sentencias hasta el final o hasta que choca con un _**break**_, que entonces finaliza la iteración. Si se omite _break_, swith ejecutará todos los _cases_ restantes cuando encuentra uno que cumpla con la condición:

```
<?php
switch ($i) {
    case 0:
        echo "i es igual a 0";
    case 1:
        echo "i es igual a 1";
    case 2:
        echo "i es igual a 2";
}
```

En el ejemplo anterior, si se define a _$i_ como 0, se mostrarán todos los _**echo**_ restantes porque no hay ningún _**break**_. Si se define a _$i_ como 1, se mostrarán los _echo_ del _case_ 1 y _case_ 2\. Si se define a _$i_ como 2, sólo se mostrará el _case_ 2. 

Con _**switch**_ la condición sólo se evalúa una vez, y su valor es comparado con cada uno de los _case_, a diferencia de lo que ocurre con _**elseif**_, que la condión se va evaluando continuamente con el loop.

_**case**_ puede no ejecutar ningún código, pero hace que se activen los cases posteriores hasta que se encuentre con un break:

```
<?php
switch ($i) {
case 0:
case 1:
case 2:
    echo "i es menor que 3 no negativo";
    break;
case 3:
    echo "i es 3";
}
```

En caso de que no haya ningún _case_ válido, puede utilizarse _**default**_, para ejecutar algo cuando no se cumplen los case:

```
<?php
switch($i) {
    case 0:
        echo "i es igual a 0";
        break;
    case 1:
        echo "i es igual a 1";
        break;
    default:
        echo "i no es ni 0 ni 1";
}
```

*   En los cases sólo se permiten **tipos simples**: _int_, _float_ y _string_. Los **arrays** y **objetos** pueden utilizarse si se muestran como un tipo simple. 
*   Es posible escribir punto y coma ";" en lugar de dos puntos ":" después de un _case_. 

### 11. declare

_**declare**_ sirve para fijar directivas de ejecución para un bloque de código. 

```
declare (directive)
    statement
```

La sección directive permite configurar el comportamiento de declare. Sólo hay dos directivas: _**ticks**_ y _**encoding**_. 

*   No se pueden emplear variables ni constantes para las directivas.

Se pueden usar corchetes para fijar el statement o si no se aplican afecta a todo el archivo:

```
<?php
declare(ticks=1){
   // Código afectado
}

declare(ticks=1);
// Código afectado

```

En este segundo ejemplo si el archivo con el declare ha sido incluído, no afectará al archivo padre.

#### **Ticks**

La ejecución de un **script PHP** puede representarse como una ejecución de muchas sentencias. La mayoría de las sentencias provocan un **tick** (aunque no todas). Por ejemplo en el siguiente ejemplo habría 3 ticks:

```
<?php
$x = 1;
$y = 2;
$z = 3;
```

Con **declare(ticks=N)** y _[register_tick_function()](http://php.net/manual/es/function.register-tick-function.php)_ se puede ejecutar código entre los **ticks**. La función _register_tick_function_ especifica la función a llamar cuando un tick ocurre. declare establece cuantos ticks han de pasar hasta que un evento tick se ejecuta.

*   Con declare(ticks=1) y _register_tick_function('miFuncion')_; llamará a _miFuncion()_ después de cada tick. 
*   Con declare(ticks=3), _miFuncion()_ se ejecutará cada 3 ticks. 

```
<?php
function handler(){
    echo "Hola";
}

register_tick_function("handler");
$i = 0;
declare(ticks = 3) {
    while($i < 9)
        echo ++$i;
} // Devuelve 123Hola456Hola789Hola
```

#### **Encoding**

La codificación puede ser especificada para cada script con encoding:

```
<?php
declare(encoding='ISO-8859-1');
// Código a ejecutar
```

### 12. return

_**return**_ devuelve el control del programa al módulo que lo invoca. La ejecución vuelve a la siguiente declaración después del módulo que lo invoca. 

*   Si se usa en una **función**, return hace que la función termine, devolviendo los argumentos que le sigan como valor de la llamada a la función. 
*   Si se llama **globalmente**, finaliza la ejecución del script actual. 
*   Si el archivo actual fue incluido o requerido, el control regresa al archivo que llama al _**include**_ o _**require**_.
*   Si el archivo está incluído con _**include**_, los argumentos del return se devolverán como valor de la llamada _**include**_.
*   Si return se usa en el **archivo principal del script**, termina la ejecución del script.
*   También termina la ejecución de una sentencia _**eval()**_.

Es recomendable **no** usar paréntesis después de return. 

### 13. include/include_once

_**include**_ incluye y ejecuta un archivo. 

Los archivos se incluyen en base a la **ruta de acceso dada**, y si no se proporciona ninguna, se utiliza el [include_path](http://php.net/manual/es/ini.core.php#ini.include-path). Si el archivo tampoco se encuentra en el **include_path** se mirará en el **propio directorio** desde donde se hace la llamada, antes de devolver un **mensaje warning**. Es en el tipo de mensaje donde difiere con require, que devuelve un **error fatal**.  

*   Si se define una ruta absoluta (en Linux comenzando por /) o relativa al directorio actual (comenzando con . o ..) el **include_path** será ignorado.

Las variables del archivo del include estarán disponibles en el archivo desde el que se solicita:

```
<?php
// archivo1.php
$color = 'azul';
// archivo2.php
echo $color; // Devuelve un Notice: Undefined variable: color
include 'archivo1.php';
echo $color; // Devuelve azul
```

*   Si la inclusión se hace dentro de una **función**, el contenido del archivo es como si estuviera dentro de esa función, por tanto su contenido tendrá el mismo ámbito.
*   Cuando se incluye un archivo, el intérprete abandona el modo PHP e ingresa al modo HTML al comienzo del archivo incluído, y se reanuda de nuevo al final. Es por ello que cualquier código que tenga que ser interpretado como PHP debe incluir las etiquetas válidas de comienzo  y terminación (**<?php ?>**).

Si están activadas las **envolturas URL include**, se puede incluir un archivo a través de una URL (mediante HTTP u [otro protocolo](http://php.net/manual/es/wrappers.php)). Si el servidor objetivo interpreta el archivo como PHP, las variables se pueden pasar al archivo usando un string de petición como con HTTP GET. El resultado no es lo mismo que en local, pues el archivo se ejecuta en el servidor remoto y el resultado se incluye en el local.

```
<?php
include 'http://www.ejemplo.com/archivo.php?var=1';
$var = 1;
```

Por **seguridad** es mejor que el archivo remoto se procese en el servidor remoto y se reciba sólo la salida, con [_readfile()_](http://php.net/manual/es/function.readfile.php).

Es posible devolver valores desde los archivos include mediante **return**:

```
// archivo1.php con return
<?php
$var = 'PHP';
return $bar;
?>
// archivo2.php sin return
<?php
$var = 'PHP';
?>
//archivo3.php
<?php
$foo = include 'archivo1.php';
echo $foo; // devuelve PHP

$bar = include 'archivo2.php';
echo $bar; // Devuelve 1 porque el include ha sido exitoso, pero no tiene valor return. Si no hubiera funcionado devolvería false y un E_WARNING.
```

También se pueden incluir archivos PHP en variables con un buffering de salida:

```
<?php
$string = get_include_contents('archivo.php');

function get_include_contents($filename) {
    if(is_file($filename)) {
        ob_start();
        include $filename;
        return ob_get_clean();
    }
    return false;
}
```

Para incluir archivos automáticamente en scripts ver [auto_preprend_file](http://php.net/manual/es/ini.core.php#ini.auto-prepend-file) y [auto_append_file](http://php.net/manual/es/ini.core.php#ini.auto-append-file) de _php.ini_.

_**include_once**_ incluye el archivo especificado sólo una vez, si se incluye más veces tan sólo devuelve true. Es útil en casos donde el mismo fichero se podría incluir y evaluar más de una vez, para evitar así redefinir funciones o reasignar valores a variables.

### 14. require/require_once

_**require**_ hace lo mismo que _**include**_ pero en caso de fallar devuelve un **error fatal** de nivel **E_COMPILE_ERROR**, por lo que no puede continuar el script. _include_ sólo emite un **E_WARNING** que permite continuar el script.

_**require_once**_ es igual que _**require**_ pero PHP comprobará si el archivo ya ha sido incluído, y si es así no se incluirá otra vez. 

### 15. goto

_**goto**_ se utiliza para saltar a otra sección del script. La **etiqueta de destino** debe estar dentro del mismo fichero y ámbito. 

```
<?php
goto x;
echo 'Hola!';

x:
echo 'Adios!';  // sólo se imprimirá Adios!
```

También puede utilizarse en un **loop** en lugar de _**break**_:

```
<?php
for($i=0, $j=50; $i<100; $i++) {
    while($j--) {
        if($j==17) goto end;
        }
    }
echo "i = $i";
end:
echo 'j llegó a 17';
```

### 16. Sintaxis alternativas

PHP ofrece una **sintaxis alternativa** para _if_, _while_, _for_, _foreach_ y _switch_. Se cambia el corchete de apertura por dos puntos ":" y el corchete de cierre por _**endif**_, _**endwhile**_, _**endfor**_, _**endforeach**_, o _**endswitch**_. 

```
<?php if ($x == 3): ?>
X es 3
<?php endif; ?>
```

"X es 3" es un bloque HTML que se mostraría sólo si se cumple la condición.

Con **else** y **elseif**:

```
<?php
if($x == 3):
    echo "x igual a 3";
elseif ($x == 4):
    echo "x igual a 4";
else:
    echo "x no es ni 3 ni 4";
endif;
```