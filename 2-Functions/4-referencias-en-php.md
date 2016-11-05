Existen cuatro operaciones básicas con referencias:

1.  Asignar valores por referencia
2.  Pasar variables por referencia
3.  Devolver variables por referencia
4.  Destruir referencias

### 1. Asignar valores por referencia

Las referencias en PHP permiten que **dos variables hagan referencia al mismo contenido**:

```
<?php
$x =& $y; // $x e $y apuntan al mismo contenido (contenedor zval)
```

$x no apunta a $y o viceversa, $x e $ apuntan al mismo lugar.

Si se **asigna, pasa o devuelve una variable no definida** por referencia, **la variable se crea**:

```
function miFuncion(&$var){}

miFuncion($variable); // $variable se ha creado, es null

$array = array();
miFuncion($array['millave']); // La key millave se ha creado en el array $array

$miClase = new StdClass;
miFuncion($miClase->miPropiedad); // La propiedad miPropiedad se ha creado en miClase
```

Si se asigna una referencia a una **variable declarada como global dentro de una función**, la referencia se verá sólo dentro de la función. Si se quiere que el resultado sea visible en el ámbito global también se utiliza la matriz **$GLOBALS**:

```
$var1 = "Esto es una variable de ejemplo";
$var2 = "";
function referencias_globales($usar_globals)
{
    global $var1, $var2;
    if(!$usar_globals){
        $var2 =& $var1; // visible sólo en la función
    } else {
        $GLOBALS["var2"] =& $var1; // visible en el ámbito global también
    }
}
referencias_globales(false);
echo "var2 establecida a " . $var2 . "<br>"; // $var2 es ""
referencias_globales(true);
echo "var2 establecida a " . $var2 . "<br>"; // $var2 es "Esto es una variable de ejemplo"

// Es lo mismo hacer global $var que $var =& $GLOBALS['var']
```

Si se asigna un valor a una variable con referencias en una sentencia foreach, también se modifican las referencias:

```
$var1 = 0;
$var2 =& $var1;
$animales = ['perro', 'gato', 'conejo'];
foreach ($animales as $var2) {
    // hacer algo
}
echo $var1; // Devuelve: conejo, por ser el último valor del foreach
```

Las **expresiones creadas con array()** también pueden comportarse como referencias prefijando **&** a los elementos:

```
$x = 1;
$y = ['2', '3'];
$array = array(&$x, &$y[0], &$y[1]);
echo ++$array[0] . "<br>"; // Devuelve 2: $x + 1
echo ++$array[1] . "<br>"; // Devuelve 3: $y[0] + 1
echo ++$array[2] . "<br>"; // Devuelve 4: $y[1] + 1
echo $x . "<br>"; // Devuelve 2
var_dump($y);
/*
* Devuelve
array (size=2)
  0 => &int 3
  1 => &int 4
*/
```

**Los elementos de _array_ pasados por referencia son peligrosos**. Si se hace una asignación normal con una referencia en el lado derecho (_$array2 = $array_ en el ejemplo de debajo) no convierte el lado izquierdo en una referencia, pero **las referencias dentro de arrays se conservan** en estas asignaciones normales:

```
$x = 1;
$y =& $x;
$z = $y;
$z = 5; // $z no es una referencia, no cambia $x o $y

$array = array(1, 2);
$x =& $array[0]; // $x y $array[0] son el mismo conjunto de referencias
$array2 = $array; // NO se asigna por referencia
echo ++$array2[0] . "<br>"; // Devuelve 2
echo $array[0] . "<br>"; // Devuelve 2
echo ++$array2[1] . "<br>"; // Devuelve 3
echo $array[1]; // Devuelve 2
/* El contenido de $array[0] se cambia con el contenido de $array2[0]
incluso cuando no se han referenciado
*/
```

El **comportamiento de las referencias de arrays** está definido en una base elemento por elemento. El **comportamiento de las referencias de elementos individuales** está desasociado del estado de la referencia del array contenedor.

### 2. Pasar variables por referencia

Si se **pasan variables por referencia a una función** ésta puede modificar el valor de la variable:

```
function incrementar(&$var){
    $var++;
}
$numero = 10;
incrementar($numero);
echo $numero; // $número ahora vale 11
```

Otro ejemplo de pasar por referencia:

```
// En esta primera función no pasamos por referencia
function elevarAlCuadrado($numero)
{
    return $numero * $numero;
}
$nuevoValor = elevarAlCuadrado($valor);

// En esta segunda función pasamos por referencia:
function otraElevarAlCuadrado(&$numero)
{
    $numero = $numero * $numero;
}
otraElevarAlCuadrado($valor);
```

En el primer caso se pasa una copia de _$valor_, se eleva al cuadrado, y devuelve el resultado que es asignado a _$nuevoValor_. En el segundo caso _$valor_ se pasa por referencia, y se modifica directamente dentro de la función. Hay que tener en cuenta que **no se pueden pasar por referencia valores constantes**, no es posible escribir _otraElevarAlCuadrado(10)_; porque da un error fatal: _**Fatal Error: only variables can be passed by reference**_. 

Es posible pasar también **nuevas declaraciones de funciones** o **referencias devueltas desde funciones**:

```

function incrementar(&$var){
    $var++;
}

function &funcionValor()
{
    $x = 10;
    return $x;
}

incrementar(funcionValor());
```

Lo que se referencia es el return de la función _funcionValor()_. La función _incrementar()_ recibirá $x con valor de 10 y lo incrementará en 1.

### 3. Devolver variables por referencia

Para **devolver por referencia** es necesario especificarlo dos veces: en la definición de la función y cuando es llamada.

Para especificar que una función devuelva una referencia, se pone el **ampersand &** antes del nombre de la función, y para especificar a que variable quieres referenciar el resultado de la función, se usa la asignación por referencia normal:

```
function &elevarAlCuadrado($numero){
    $resultado = $numero * $numero;
    return $resultado;
}

$valor =& elevarAlCuadrado(3); 
```

**Devolver por referencia** se puede emplear también con métodos que devuelven propiedades de clases:

```
class Clase {
    public $valor = 20;

    public function &obtenerValor(){
        return $this->valor;
    }
}

$objeto = new Clase;
$miValor =& $objeto->obtenerValor();
// $miValor es una referencia a $objeto->valor, 20

$objeto->valor = 40;
echo $miValor; // Devuelve el nuevo valor de $objeto->valor, 40
```

Esta forma de referenciar también se puede utilizar con **elementos de arrays**. Con las referencias se puede cambiar el elemento que es devuelto, y cambiará el elemento en el array original. Sin referencias sólo se obtiene una copia del elemento, y cualquier cambio no afectará al elemento original.

Para usar una **referencia retornada**:

```
function &colector(){
    static $coleccion = array();
    return $coleccion;
}

$coleccion =& colector();
$coleccion[] = 'hola';
```

Podemos emplear esta referencia para asignar nuevos valores al array desde la función:

```
function &colector(){
    static $coleccion = array();
    return $coleccion;
}

$animales =& colector();

array_push(colector(), 'perro', 'gato', 'conejo');

var_dump($animales); // Devuelve el array con los elementos perro, gato y conejo
```

### 4. Destruir referencias

Cuando se destruye una referencia no se rompe el contenido de la variable, sólo se rompe el vínculo entre el nombre de la variable y el contenido de la variable:

```
$x = 1;
$y =& $x;
unset($x); // No destruye $y, sólo $x
```