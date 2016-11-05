Los _**arrays**_ nos permiten **almacenar datos** de forma que después sea más fácil añadirlos, eliminarlos, modificarlos o mostrarlos. La **iteración de arrays** consiste en **recorrer arrays**, es decir, aplicar una acción a cada elemento de un array uno por uno, ya sea a su _key_, a su _value_ o a ambos. Esta acción puede ser **mostrar en un listado el array**, **extraer o modificar ciertos elementos** bajo alguna condición, **aplicar funciones** a los elementos del array, etc.

**Índice de contenido**

1. Formas de recorrer arrays

| | |
| -------- | -------- |
| **Funciones para recorrer arrays** | |
| 2\. array_walk | 5\. array_keys |
| 3\. array_key_exists | 6\. array_values |
| 4\. in_array | |

### 1. Formas de recorrer arrays

Los tres métodos siguientes nos permiten **recorrer arrays** para poder manipular las **claves** y los **valores** de un array:

*  Utilizando la estructura de control for

```
<?php
$array = array('perro', 'gato', 'avestruz');
$array_num = count($array);
for ($i = 0; $i < $array_num; ++$i){
    print $array[$i];
}
```

Este método **no debe emplearse** por varios motivos:

*   Te limita a **arrays numéricos**.
*   Los _keys_ pueden estar desordenados.
*   Puede faltar algún _key_, por lo que genera un error del tipo _Undefined offset_.
*  Utilizando la estructura de control foreach

```
<?php
foreach ($array as $val) {
    print $val;
}
```

Este método es mucho más rápido y fácil de utilizar. En este ejemplo $val es el **valor** de cada _key_ que pasa por la iteración, por lo que las _keys_ quedan ignoradas. 

Para extraer también las _keys_:

```
<?php
foreach($array as $key => $val) {
    print "$key = $val <br>";
}
```

En este caso también podemos utilizar las _keys_, especialmente útil cuando son **arrays asociativos**.

*  Utilizando la estructura de control while

Este método utiliza también las funciones _list()_ y _[each()](http://php.net/manual/es/function.each.php)_:

```
<?php
reset($array);
while(list($var, $val) = each($array)) {
    print "$var is $val <br>";
}
```

La función _**list()**_ parte de un array para convertirlo en variables individuales.

_each()_ devuelve un array del elemento en el que está situado el **cursor del array** que queremos iterar (el cursor del array hace referencia al elemento del array que está siendo evaluado). El _array_ devuelto está formado por 4 keys: [0], [1], [key], [value]. **0** y **key** contienen el nombre de la key del elemento actual, y **1** y **value** contienen su valor. Supongamos un _array_ de sólo un elemento:

```
<?php
$array = array('animal'=>'perro');
var_dump(each($array)); /*
Devuelve un array de 4 elementos:
'1' => perro,
'value' => perro,
'0' => animal,
'key' => animal
*/
```

_each()_, además de leer el elemento actual, pasa el cursor al siguiente elemento del array. Todos los arrays tienen un cursor que puedes ir moviendo según las necesidades. Para garantizar que el array comienza con el cursor en el principio, se puede utilizar _reset($array)_ (esto no es necesario con _foreach()_ ya que siempre empieza desde el principio). Mientras se vaya asignando cada variable de _list()_ a cada elemento del _array_, el cursor irá pasando de elemento en elemento hasta que finalice el _array_. **_list()_ sólo coge los elementos de array numérico**, es por ello que del _array_ que devuelve _each()_, coge el 0 y el 1, y no el _key_ y el _value_.

A partir de PHP 5.5 es posible **utilizar _list()_ con _foreach()_**:

```
<?php
$users = array(
    array('John', 'West'),
    array('Peter', 'Parker'),
    array('Ann', 'Jolie')
);
// Esto:
foreach ($users as $user){
    list($nombre, $apellido) = $user;
    echo "Usuario: $nombre $apellido <br>";
}
// Devuelve lo mismo que esto:
foreach ($users as list($nombre, $apellido))
{
    echo "Usuario: $nombre $apellido <br>";
}
```

#### ¿Qué método utilizar?

Normalmente se utiliza _**foreach**_ porque es más legible y está más optimizado, pero _**while**_ también se utiliza con frecuencia. Pueden utilizarse los dos, la principal diferencia es que _foreach_ siempre empieza desde el principio.

El método que no se recomienda es el **método con for**, por los motivos que ya se han explicado.

### Funciones para recorrer arrays

Funciones que son utilizadas con frecuencia en la **iteración de arrays**.

### 2\. array_walk
```
bool array_walk (array &$array, callable $callback [, mixed $userdata = null ])
```

Aplica una función definida por el usuario dada por _$callback_ a cada elemento del array dado por _$array_.

A _array_walk()_ no le afecta la posición del puntero, recorrerá el array entero sin tenerlo en cuenta. 

El _callback_ asume dos parámetros _$callback ($value, $key)_. El primero es el _**value**_ del elemento del array siendo recorrido y el segundo su _**key**_. Sólo se pueden **cambiar** los valores del array, no se puede alterar su estructura: ni añadir, ni eliminar ni reordenar elementos.

El parámetro opcional _$userdata_ se puede pasar como tercer parámetro de la función dada por _$callback_.

Devuelve **true** en caso de éxito o **false** en caso de error. Si _$callback_ requiere más parámetros de los proporcionados se genera un **E_WARNING** cada vez que _array_walk_ llame a callback.

```
$animales = array(
    "perro" => "Pooh",
    "gato" => "Smiles",
    "avestruz" => "Spunky",
    "conejo" => "Sugar"
);
function cambiarNombre(&$nombre, $animal){
    // Añade Nombre : en cada valor del array
    $nombre = "Nombre: " . $nombre;
}
function mostrarMascotas($nombre, $animal){
    echo strtoupper($animal) . " -> $nombre" . "<br>";
}
// Muestra el listado del array:
array_walk($animales, 'mostrarMascotas');
// Modifica cada value para añadir Nombre: al principio
array_walk($animales, 'cambiarNombre');
// Ahora el listado mostrado incluye Nombre:
array_walk($animales, 'mostrarMascotas');
```

### 3\. array_key_exists
```
bool array_key_exists (mixed $key, array $array)
```

Comprueba que el _key_ existe en el array. Devuelve **true** si existe o **false** si da error.

```
$animales = array ("perro" => "Bruce", "gato" => "Schmeichel");
if(array_key_exists('perro', $animales)){
    echo "Tengo un perro";
}
```

_array_key_exists()_ retorna true también si un key tiene un valor **null**. _isset()_ no lo hace.

También devuelve **true** si el key es una propiedad definida dentro de un objeto dado como array.

### 4\. in_array
```
bool in_array (mixed $needle, array $haystack [, bool $strict = FALSE ])
```

Comprueba si un valor existe en un array. Busca _$needle_ en el array _$haystack_. 

Si _$strict_ es true, la función también comprobará los tipos de _$needle_ en _$haystack_.

Es sensible a mayúsculas y minúsculas.

```
$animales = array ("perro", "gato", "Oso");
// Muestra tengo un perro:
if(in_array("perro", $animales)){
    echo "Tengo un perro";
}
// No muestra tengo un oso, porque es un Oso el que tengo:
if(in_array("oso", $animales)){
    echo "Tengo un oso";
}
// Ejemplo con modo estricto
$numeros = array(5, "10", 20, "40");
// Muestra Tengo un 10 como string
if(in_array('10', $numeros, true)){
    echo "Tengo un 10 como string";
}
// NO lo muestra porque el 10 de $numeros es un string
if(in_array(10, $numeros, true)){
    echo "Tengo un 10 como integer";
}
```

### 5\. array_keys
```
array array_keys (array $array [, mixed $search_value [, bool $strict = false ]] )
```

Devuelve un array con todas las **claves de un array** o un **subconjunto de claves de un array**. 

Si se especifica _$search_value_ sólo se devuelven las keys para ese valor.

_$strict_ determina si ha de usarse una comparación estricta === o no.

```
$animales = array (
    "perro" => "Stoichkov",
    "gato" => "Sunny",
    "conejo" => "Tarzan"
);
print_r(array_keys($animales));
/*
 Devuelve:
Array
(
    [0] => perro
    [1] => gato
    [2] => conejo
)
 */
print_r(array_keys($animales, "Sunny"));
/*
 Devuelve
Array
(
    [0] => gato
)
 */
```

### 6\. array_values
```
array array_values (array $array)
```

Devuelve todos los **valores de un array** y los indexa numéricamente.

```
$animales = array (
    "perro" => "Stoichkov",
    "gato" => "Sunny",
    "conejo" => "Tarzan"
);
print_r(array_values($animales));
/*
 Devuelve:
Array
(
    [0] => Stoichkov
    [1] => Sunny
    [2] => Tarzan
)
 */
```