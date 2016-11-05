Un _**array**_ es un **mapa ordenado** que asocia **valores** con **claves** (_values_ con _keys_). Esta forma de organizar los datos permite manejarlos en forma de listas, tablas asociativas, colecciones, diccionarios o lo que sea necesario. El **objetivo** es poder acceder, administrar y modificar los datos empleando su **sintaxis básica**, estructuras de control y **funciones especializadas**.

**Indice de contenido**

1.  Sintaxis
2.  Acceso y modificación de elementos de un array
3.  Arrays multidimensionales
4.  Referencias en arrays
5.  Comparación de arrays

### 1. Sintaxis

Un _array_ puede crearse de dos formas: 

*   Mediante el constructor del lenguaje _**array()**_:

```
$miArray = array(
    'key' => 'value',
    'key2' => 'value2',
    'key3' => 'value3'
    // ...
);
```

*   Mediante **corchetes []**:

```
$miArray = [
    'key' => 'value',
    'key2' => 'value2',
    'key3' => 'key3'
    //...
];
```

La coma después del último elemento del array es opcional. 

La _**key**_ puede ser un _integer_ o un _string_. El _**value**_ puede ser de cualquier tipo.

Consideraciones respecto a las **keys** de un _array_:

*   Un _string_ que contenga un integer válido se amoldará a tipo _integer_. Ejemplo: "8" se guardará como _integer_ 8 (no ocurre lo mismo con "08").
*   Un _float_ también se amoldará a un _integer_, la parte fraccionaria se elimina. Ejemplo: 8,7 se guardará como 8.
*   Los _booleanos_ se amoldan a _integers_ también, true se almacena como 1 y false como 0.
*   Un _null_ se amoldará a un _string_ vacío, "".
*   Los _arrays_ y _objetos_ no pueden utilizarse como keys.
*   Si varios elementos usan el mismo _key_, se devolverá el último, los demás serán sobreescritos.

```
$animales = [
    1 => "Perro", // 1
    "1" => "Gato", // 1, sobreescribe al anterior
    1.6 => "Avestruz", // 1, sobreescribe al anterior
    true => "Rinoceronte" // 1, sobreescribe al anterior
];
// El único valor del array será [1] => "Rinoceronte"
```

Especificar la _**key**_ es opcional, si no se hace se añade automáticamente como _key_ un **integer incremental**:

```
$miArray = [
    "a",
    "b",
    5 => "c",
    "d"
];
// El key integer será último key numérico + 1
array (size=4)
  0 => string 'a' (length=1)
  1 => string 'b' (length=1)
  5 => string 'c' (length=1)
  6 => string 'd' (length=1)

```

### 2. Acceso y modificación de elementos de un array

Se puede **acceder a los elementos de un array** con la sintaxis de **llaves** _array{key}_ o de **corchetes** _array[key]_:

```
$planetas = [
    1 => "Marte",
    "Otro" => "Júpiter",
    "4" => "Saturno",
    "Otromas" => "Plutón"
];

echo $planetas[1] . PHP_EOL; // Devuelve Marte
echo $planetas{"Otro"} . PHP_EOL; // Devuelve Júpiter

```

También es posible **hacer referencia al array resultado de una función**:

```
function misAnimales()
{
    return array("perro", "gato", "avestruz");
};

$miPerro = misAnimales()[0];
echo $miPerro; // Devuelve perro
```

Si se intenta **acceder a una key de un array que no se ha definido** ocurrirá lo mismo que al **intentar acceder a una variable sin definir**: el resultado será **null** y se emitirá un **mensaje de error E_NOTICE**.

#### **Crear elementos en un array**

Para **crear los elementos de un array** se utilizan también los corchetes de la misma forma. Si no se incluye nada en los corchetes la _key_ será un **integer incremental automático**, igual que cuando se crean arrays y no se indica el _key_:

```
$animales = [
    "perro" => "Bobby",
    "gato" => "Puskas",
    "avestruz" => "Charles"
];
// Crear elementos de un array:
$animales["rinoceronte"] = "Brutus"; // Se crea el key rinoceronte con el nombre Brutus
$animales[] = "Bob"; // Se crea directamente el nombre de la mascota, con key numérica 0
```

#### **Modificar elementos en un array**

Para **modificar elementos de un array** se hace lo mismo, salvo que en los corchetes se incluye el elemento que se quiere modificar, sea numérico o asociativo:

```
$animales["perro"] = "Casanova"; // Se modifica el nombre del perro a Casanova
$animales[0] = "Browneyes"; // Se modifica el valor Bob del key 0 por Browneyes
```

Si el **array** al que se quiere añadir o modificar un elemento **no existe**, se crea, por lo que ésta también es una forma de crear un array, pero no es aconsejable. Siempre es mejor iniciar variables mediante asignación directa.

#### **Eliminar elementos en un array**

Para **eliminar elementos de un array** se emplea _unset()_:

```
unset($animales["perro"]) // Elimina el elemento de key perro
unset($animales); // Elimina el array entero
```

Con _unset()_ el array no se reindexa. Los **keys numéricos** en el _array_ guardan el último key que ha estado presente en el array, aunque se haya eliminado con _unset()_ y luego se cree de nuevo. A los nuevos valores que se inserten sin key, se les asignará el número de ese último key anterior al unset:

```
// Crear un array simple.
$miArray = array(1, 2, 3, 4, 5);
print_r($miArray); // Devuelve: Array ( [0] => 1 [1] => 2 [2] => 3 [3] => 4 [4] => 5 )
// Ahora elimina cada elemento, pero deja el mismo array intacto:
foreach ($miArray as $i => $value) {
    unset($miArray[$i]);
}
print_r($miArray); // Devuelve: Array ( )
// Agregar un elemento (nótese que la nueva clave es 5, en lugar de 0).
$miArray[] = 6;
print_r($miArray); // Devuelve: Array ( [5] => 6 )
// Para evitar esto se puede emplear array_values
// La función array_values modifica este comportamiento y resetea los keys:
$miArray = array_values($miArray);
$miArray[] = 7;
print_r($miArray); // Devuelve: Array ( [0] => 6 [1] => 7 )

```

### 3. Arrays multidimensionales

Los **arrays multidimensionales** son arrays que contienen uno o más arrays. Sirven para guardar valores con más de un key. **PHP interpreta arrays multidimensionales de cualquier nivel de profundidad**, pero más de tres niveles es considerado poco práctico y complejo de entender.

Para **acceder a un valor de un array de segundo nivel**, se necesita emplear dos veces los corchetes, para **acceder a un valor de un array de tercer nivel**, tres corchetes, y así sucesivamente. 

Ejemplo con **dos niveles**:

```
// El array guarda datos de Animal, Nombre y Edad
$animales = array
(
    array("perro","Peanut",7),
    array("gato","Prince",13),
    array("avestruz","Schmoopie",4),
);
echo "El animal es un " . $animales[0][0] . " tiene " . $animales[0][2] . " años y se llama " . $animales[0][1];
// El animal es un perro tiene 7 años y se llama Peanut
```

Ejemplo con **tres niveles**:

```
$animales = array
(
    array("perro", array("Sparky")),
    array("gato", "Soldier", 13),
    array("avestruz", "Spunky", 4),
);
echo "El animal es un " . $animales[0][0] . " y se llama " . $animales[0][1][0];
// El animal es un perro y se llama Sparky
```

### 4. Referencias en arrays

Es posible **pasar los valores de un array por referencia**, de esta forma se pueden **modificar todos los valores de un array de vez** mediante un loop:

```
$animales = array('perro', 'gato', 'liebre', 'jabalí');

foreach ($animales as &$animal)
{
    $animal = mb_strtoupper($animal);
}
unset($animal);
var_dump($animales);
```

Se hace _unset($animal)_ para asegurar que las escrituras subsiguientes a _$animal_ no modifiquen el último elemento del array, ya que hemos empleado foreach.

También se pueden **copiar arrays por referencia**, para que cualquier modificación en una, afecte a otra:

```
$animales1 = array('perro', 'gato', 'liebre', 'jabalí');

$animales2 = $animales1;

$animales2[] = 'león'; // Sólo cambia animales2, que se le añade el león
// $animales1 tiene la misma lista inicial

$animales3 =& $animales1;
$animales3[] = 'tigre'; // Ahora $animales1 y $animales3 tienen los mismos animales
// Los 4 iniciales + tigre
```

### 5. Comparación de arrays

La **comparación de arrays**, como en los strings, se puede realizar con los símbolos de igual (==) o idéntico (===).

```
$a = array('a'=>1, 'b'=>2, 'c'=>3); // Array de referencia
$b = array('a'=>1, 'b'=>2, 'c'=>3); // Igual e idéntica
$c = array('a'=>1, 'b'=>2); // Un elemento menos
$d = array('a'=>1, 'b'=>100, 'c'=>3); // Un elemento tiene un valor diferente
$e = array('a'=>1, 'c'=>3, 'b'=>2);  // Mismos key=>value pero en diferente orden
echo '$a == $b es ', var_dump($a == $b); // $a == $b es boolean true
echo '$a === $b es ', var_dump($a === $b); // $a === $b es boolean true
echo '$a == $c es ', var_dump($a ==$c); // $a == $c es boolean false
echo '$a === $c es ', var_dump($a === $c); // $a === $c es boolean false
echo '$a == $d es ', var_dump($a ==$d); // $a == $d es boolean false
echo '$a === $d es ', var_dump($a === $d); // $a === $d es boolean false
echo '$a == $e es', var_dump($a ==$e); // $a == $e es boolean true
echo '$a === $e es', var_dump($a === $e); // $a === $e es boolean false
```