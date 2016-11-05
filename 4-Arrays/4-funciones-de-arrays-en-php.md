Según la **documentación de PHP**, existen un total de 79 funciones de arrays. En este apartado se recogen algunas de las más importantes divididas por secciones (no se incluyen las ya utilizadas para la iteración de arrays).

**Indice de contenido**

| | |
| -------- | -------- |
| 1. Comparar arrays | 4. Rellenar arrays |
| 2. Unir arrays | 5. Dividir arrays |
| 3. Ordenar arrays | 6. Modificar elementos en arrays |

### 1. Comparar arrays

Para **comparar arrays** existen 5 funciones principales de la familia **array_diff_*** :

* array_diff
```
array array_diff (array $array1, array $array2 [, array $... ])
```

Compara  _$array1_ con uno o más arrays y devuelve los **valores** de _$array1_ que no estén presentes en **cualquiera** de los demás arrays.

```
$animales = array (
    "gato" => "Sunny",
    "perro" => "Stoichkov",
    "chimpancé" => "Muggles",
    "chinchilla" => "Herminia",
    "Charles",
    "oso" => "Tarzan"
);
$animales2 = array (
    "perro" => "Stoichkov",
    "gato" => "Sunny",
    "conejo" => "Tarzan",
    "Muggles"
);
$diferencia = array_diff($animales, $animales2);
var_dump($diferencia);
/*
Devuelve:
array (size=2)
  'chinchilla' => string 'Herminia' (length=8)
  0 => string 'Charles' (length=7)
*/
```

Se observa que lo que compara son los **valores**.

* array_diff_assoc
```
array array_diff_assoc (array $array1, array $array2 [, array $... ])
```

Compara _**$array1**_ con $_**array2**_ y devuelve la diferencia, pero esta vez comparando **values** y _**keys**_. Se aceptan también más arrays opcionales.

```
$animales = array (
    "gato" => "Sunny",
    "perro" => "Stoichkov",
    "chimpancé" => "Muggles",
    "chinchilla" => "Herminia",
    "Charles",
    "Muggles",
    "oso" => "Tarzan"
);
$animales2 = array (
    "perro" => "Stoichkov",
    "gato" => "Sunny",
    "conejo" => "Tarzan",
    "Muggles"
);
$diferencia = array_diff_assoc($animales, $animales2);
var_dump($diferencia);
/*
array (size=5)
  'chimpancé' => string 'Muggles' (length=7)
  'chinchilla' => string 'Herminia' (length=8)
  0 => string 'Charles' (length=7)
  1 => string 'Muggles' (length=7)
  'oso' => string 'Tarzan' (length=6)
*/
```

"Muggles" también lo añade porque tienen diferente key. En el primer array tiene key 1 y en el segundo key 0.

* array_diff_key
```
array array_diff_key (array $array1, array $array2 [, array $... ])
```

Compara las _**keys**_ de _$array1_ con las keys de _$array2_ y devuelve la diferencia. Es como _array_diff()_ pero sólo comparando los _**keys**_ en lugar de los _values_.

```
$animales = array (
    "gato" => "Sunny",
    "perro" => "Stoichkov",
    "chimpancé" => "Muggles",
    "chinchilla" => "Herminia",
    "Charles",
    "Muggles",
    "oso" => "Tarzan"
);
$animales2 = array (
    "perro" => "Stoichkov",
    "gato" => "Sunny",
    "conejo" => "Tarzan",
    "Muggles"
);
$diferencia = array_diff_key($animales, $animales2);
var_dump($diferencia);
/*
array (size=4)
  'chimpancé' => string 'Muggles' (length=7)
  'chinchilla' => string 'Herminia' (length=8)
  1 => string 'Muggles' (length=7)
  'oso' => string 'Tarzan' (length=6)
*/
```

* array_diff_uassoc
```
array array_diff_uassoc (array $array1, array $array2 [, array $... ], callable $key_compare_func)
```

Compara _$array1_ con _$array2_ y devuelve la diferencia. Difiere de _array_diff_assoc()_ en que se añade una **función callable** para la **comparación de los elementos**.

La **función de comparación** tiene que devolver un entero menor, igual o mayor que cero si el primer argumento es menor, igual o mayor que el segundo.

Devuelve todos los elementos de _$array1_ que no están en ninguno de los demás arrays.

```
$animales = array (
    "gato" => "Sunny",
    "perro" => "Stoichkov",
    "chimpancé" => "Muggles",
    "chinchilla" => "Herminia",
    "Charles",
    "Muggles",
    "oso" => "Tarzan"
);
$animales2 = array (
    "perro" => "Stoichkov",
    "gato" => "Sunny",
    "conejo" => "Tarzan",
    "Muggles"
);
function funcionComparacion($x, $y)
{
    if ($x === $y){
        return 0;
    }
    return ($x > $y)? 1:-1;
}
$array = array_diff_uassoc($animales, $animales2, "funcionComparacion");
var_dump($array);
/*
array (size=5)
  'chimpancé' => string 'Muggles' (length=7)
  'chinchilla' => string 'Herminia' (length=8)
  0 => string 'Charles' (length=7)
  1 => string 'Muggles' (length=7)
  'oso' => string 'Tarzan' (length=6)
 */
```

* array_diff_ukey
```
array array_diff_ukey (array $array1, array $array2 [, array $... ], callable $key_compare_func )
```

Compara las _**keys**_ del _$array1_ con las _**keys**_ del _$array2_ y devuelve la diferencia. Difiere de _array_diff_key_ en que añade una función callable para la comparación de los **keys**.

La **función de comparación** tiene que devolver un entero menor, igual o mayor que cero si el primer argumento es menor, igual o mayor que el segundo.

Devuelve todos los elementos de _$array1_ que no están en ninguno de los demás arrays.

```
$animales = array (
    "gato" => "Sunny",
    "perro" => "Stoichkov",
    "chimpancé" => "Muggles",
    "chinchilla" => "Herminia",
    "Charles",
    "Muggles",
    "oso" => "Tarzan"
);
$animales2 = array (
    "perro" => "Stoichkov",
    "gato" => "Sunny",
    "conejo" => "Tarzan",
    "Muggles"
);
function funcionComparacion($key1, $key2)
{
    if ($key1 === $key2){
        return 0;
    }
    return ($key1 > $key2)? 1:-1;
}
$array = array_diff_ukey($animales, $animales2, "funcionComparacion");
var_dump($array);
/*
Devuelve
array (size=4)
  'chimpancé' => string 'Muggles' (length=7)
  'chinchilla' => string 'Herminia' (length=8)
  1 => string 'Muggles' (length=7)
  'oso' => string 'Tarzan' (length=6)
 */
```

### 2. Unir arrays

Para **unir dos arrays en PHP** se pueden emplear diferentes métodos:

* Operador suma

Con el **operador de suma +** se eliminan los elementos duplicados que aparecen en el array a la derecha del operador.

```
$animales = array("perro" => "Munchkin", "gato" => "Paradise", "Sailor", 1 => "Rudolf");
$animales2 = array("cocodrilo" => "Romeo", "perro" => "Peach", 1 => "Peter", "Sailor");

$union = $animales + $animales2;
var_dump($union);
/*
array (size=6)
  'perro' => string 'Munchkin' (length=8)
  'gato' => string 'Paradise' (length=8)
  0 => string 'Sailor' (length=6)
  1 => string 'Rudolf' (length=6)
  'cocodrilo' => string 'Romeo' (length=5)
  2 => string 'Sailor' (length=6)
*/
```

Si el _key_ ya está cogido no lo incluye en _$union_, independientemente del valor.

* array_merge
```
array array_merge (array $array1 [, array $... ])
```

Combina dos o más arrays, de modo que **los valores de uno se unen al final del anterior**. Devuelve el array resultante. Al contrario que con el operador suma, **si los arrays de entrada tienen las mismas keys de tipo string**, el último valor para esa key sobreescribirá al anterior. Pero **si son arrays numéricas**, el último valor no sobreescribirá al valor original, sino que se añadirá al final.

```
$animales = array("perro" => "Munchkin", "gato" => "Paradise", 1 => "Rudolf", "Sailor");
$animales2 = array("cocodrilo" => "Romeo", "perro" => "Peach", 1 => "Peter", "Sailor");

$union = array_merge($animales, $animales2);
var_dump($union);
/*
 Devuelve:
array (size=7)
  'perro' => string 'Peach' (length=5)
  'gato' => string 'Paradise' (length=8)
  0 => string 'Sailor' (length=6)
  1 => string 'Rudolf' (length=6)
  'cocodrilo' => string 'Romeo' (length=5)
  2 => string 'Peter' (length=5)
  3 => string 'Sailor' (length=6)
*/
```

* array_merge_recursive
```
array ​array_merge_recursive (array $array1 [, array $... ])
```

Es igual que _**array_merge()**_, salvo que cuando los arrays de entrada tienen las **mismas keys de tipo string**, los valores de estas keys se unen en un array de forma recursiva, de forma que si uno de los valores es un array, la función unirá también ésta con la correspondiente entrada de otro array. Si los arrays tienen la **misma key numérica**, el valor posterior no sobreescribirá el valor original, sino que se añadirá al final.

```
$animales = array("perro" => "Munchkin", "gato" => "Paradise", 1 => "Rudolf", "Sailor");
$animales2 = array("cocodrilo" => "Romeo", "perro" => "Peach", 1 => "Peter", "Sailor");

$union = array_merge_recursive($animales, $animales2);
var_dump($union);
/*
array (size=7)
  'perro' =>
    array (size=2)
      0 => string 'Munchkin' (length=8)
      1 => string 'Peach' (length=5)
  'gato' => string 'Paradise' (length=8)
  0 => string 'Rudolf' (length=6)
  1 => string 'Sailor' (length=6)
  'cocodrilo' => string 'Romeo' (length=5)
  2 => string 'Peter' (length=5)
  3 => string 'Sailor' (length=6)
 */
```

* array_combine
```
array array_combine (array $keys, array $values )
```

Crea un array utilizando un array para sus _keys_ y otro array para sus _values_. Coge los _**values**_ de los arrays de origen, independientemente de que sean numéricos o asociativos.

```
$animales = array('perro', 'gato', 'leon');
$nombres = array('Jerry', 'Lord', 'Jesus');
$array = array_combine($animales, $nombres);
var_dump($array);
/*
array (size=3)
  'perro' => string 'Jerry' (length=5)
  'gato' => string 'Lord' (length=4)
  'leon' => string 'Jesus' (length=5)
*/
```

### 3. Ordenar arrays

Las **funciones de ordenación de arrays** actúan directamente en el mismo array, en vez de crear uno nuevo ordenado. Algunas de las funciones se basan en las _**keys**_ del array, otras en sus _**values**_. El **orden de clasificación** que llevan es: alfabético, de menor a mayor, de mayor a menor, numérico, natural, aleatorio o definido por el usuario.

Cuando se mantiene una **asociación entre _key_ y _values_ después de la clasificación**, las _**keys**_ se reestablecen numéricamente.

**Tabla de funciones para ordenar arrays**:

| | | | |
| -------- | -------- |
| **Nombre de la función** | **Ordena por** | **Mantiene asociación con las key** | **Orden de clasificación** |
| **sort()** | valor | no | Menor a mayor |
| rsort() | valor | no | Mayor a menor |
| **asort()** | valor | sí | Menor a mayor |
| arsort() | valor | sí | Mayor a menor |
| **ksort()** | key | sí | Menor a mayor |
| krsort() | key | sí | Mayor a menor |
| **usort()** | valor | no | Definido por el usuario |
| uasort() | valor | sí | Definido por el usuario |
| uksort() | key | sí | Definido por el usuario |
| **natsort()** | valor | sí | Natural |
| natcasesort() | valor | sí | Natural (case insensitive) |
| **shuffle()** | valor | no | Aleatorio |
| **array_multisort()** | valor | sí (asociativa) | no (numérica). Primer array u opciones de clasificación |

* sort
```
bool sort (array &$array [, int $sort_flags = SORT_REGULAR ])
```

Ordena un array de menor a mayor. El segundo parámetro es opcional y sirve para modificar la forma de orden:

| | |
| -------- | -------- |
| **Modo de ordenación** | **Descripción** |
| **SORT_REGULAR** | Compara elementos **normalmente** |
| **SORT_NUMERIC** | Compara elementos de **forma numérica** |
| **SORT_STRING** | Compara elementos como **cadenas** |
| **SORT_NATURAL** | Compara elementos como cadenas usando el orden natural como _**natsort()**_ |
| **SORT_LOCALE_STRING** | Compara elementos como cadenas basándose en la **configuración regional** en uso |
| **SORT_FLAG_CASE** | Se puede combinar con SORT_STRING o SORT_NATURAL para ordenar **cadenas** de forma **insensible a mayúsculas y minúsculas** |

Devuelve **true** en caso de éxito y **false** en caso de error. La función asigna **nuevas _keys_** a los elementos del array, por lo que borra las anteriores.

```
$animales = array(
    "perro" => "Munchkin",
    "gato" => "paradise",
    "cocodrilo" => "Romeo",
    "avestruz" => "jordan"
);
sort($animales);
var_dump($animales);
/*
array (size=4)
  0 => string 'Munchkin' (length=8)
  1 => string 'Romeo' (length=5)
  2 => string 'jordan' (length=6)
  3 => string 'paradise' (length=8)
*/
```

Ahora vamos a ordenar de forma natural e insensible a mayúsculas y minúsculas:

```
sort($animales, SORT_NATURAL | SORT_FLAG_CASE);
var_dump($animales);
/*
 Devuelve:
array (size=4)
  0 => string 'jordan' (length=6)
  1 => string 'Munchkin' (length=8)
  2 => string 'paradise' (length=8)
  3 => string 'Romeo' (length=5)
*/
```

* asort
```
bool asort (array &$array [, int $sort_flags = SORT_REGULAR ])
```

Ordena los _values_ de un array manteniendo los _**keys**_ con los que están asociados. 

Devuelve **true** en caso de éxito o **false** en caso de error.

```
$animales = array(
    "perro" => "Munchkin",
    "gato" => "Paradise",
    "cocodrilo" => "Romeo",
    "avestruz" => "Jordan"
);
asort($animales);
var_dump($animales);
/*
 array (size=4)
  'avestruz' => string 'Jordan' (length=6)
  'perro' => string 'Munchkin' (length=8)
  'gato' => string 'Paradise' (length=8)
  'cocodrilo' => string 'Romeo' (length=5)
 */
```

* ksort
```
bool ksort (array &$array [, int $sort_flags = SORT_REGULAR ] )
```

Ordena un _array_ por sus _**keys**_. Devuelve **true** en caso de éxito o **false** en caso de error. 

```
$animales = array(
    "perro" => "Munchkin",
    "gato" => "Paradise",
    "cocodrilo" => "Romeo",
    "avestruz" => "Jordan"
);
ksort($animales);
var_dump($animales);
/*
array (size=4)
  'avestruz' => string 'Jordan' (length=6)
  'cocodrilo' => string 'Romeo' (length=5)
  'gato' => string 'Paradise' (length=8)
  'perro' => string 'Munchkin' (length=8)
 */
```

* usort
```
bool usort (array &$array, callable $value_compare_func )
```

Ordena un array en función de sus valores mediante una **función de comparación** definida por el usuario. Asigna **nuevas _keys_** a los elementos del array, por lo que borra los anteriores.

Es similar a las funciones _array_diff_uassoc_ y _array_diff_ukey_ en el sentido de que acepta una **función callable** para comparar los valores. 

Ejemplo de usort utilizando un **array multidimensional**:

```
$animales = array(
    0 => array("mascota" => "perro"),
    1 => array("mascota" => "gato"),
    2 => array("mascota" => "liebre"),
    3 => array("mascota" => "cerdo"),
);
function funcionComparacion($x, $y)
{
  return strcmp($x["mascota"], $y["mascota"]);
}
usort($animales, "funcionComparacion");
while (list($clave, $valor) = each($animales)) {
  echo "\$mascota[$clave]: " . $valor["mascota"] . "\n";
} // $mascota[0]: cerdo $mascota[1]: gato $mascota[2]: liebre $mascota[3]: perro
```

Otro ejemplo de _usort()_ con una **función estática dentro de una clase**, cada instancia de la clase será un **elemento del array**:

```
class Fauna {
  public $nombre;
  function __construct($nombre)
  {
    $this->nombre = $nombre;
  }
  /* Ésta es la función de comparación estática: */
  static function funcionComparacion($x, $y)
  {
    $al = strtolower($x->nombre);
    $bl = strtolower($y->nombre);
    if ($al == $bl) {
      return 0;
    }
    return ($al > $bl) ? +1 : -1;
  }
}
$animales[] = new Fauna("Perro");
$animales[] = new Fauna("Gato");
$animales[] = new Fauna("Conejo");
$animales[] = new Fauna("Elefante");

usort($animales, array("Fauna", "funcionComparacion"));

foreach ($animales as $animal) {
  echo $animal->nombre . "\n";
} // Devuelve: Conejo Elefante Gato Perro
```

* natsort
```
bool natsort (array &$array)
```

Ordena un array usando un algoritmo de orden natural, simulando a como lo haría un humano. Mantiene las asociaciones _**key**_ => _**value**_. Devuelve **true** en caso de éxito o **false** en caso de error. Tiene el mismo comportamiento que la función _strnatcmp()_ de strings. 

```
$arr1 = $arr2 = array("img22.png", "img8.png", "img2.png", "img10");
asort($arr1);
print_r($arr1);
/*
Array
(
    [3] => img10
    [2] => img2.png
    [0] => img22.png
    [1] => img8.png
)
 */
natsort($arr2);
print_r($arr2);
/*
 Devuelve:
Array
(
    [2] => img2.png
    [1] => img8.png
    [3] => img10
    [0] => img22.png
)
*/
```

* shuffle
```
bool shuffle (array &$array)
```

Crea un orden aleatorio de los elementos de un array. Asigna nuevos _keys_ a los elementos.

```
$numeros = range(1,5);
shuffle($numeros);
print_r($numeros);
/*
Devuelve:
Array
(
    [0] => 2
    [1] => 1
    [2] => 3
    [3] => 5
    [4] => 4
)
*/
```

* array_multisort
```
bool array_multisort (array &$array1 [, mixed $array1_sort_order = SORT_ASC [, mixed $array1_sort_flags = SORT_REGULAR [, mixed $... ]]] )
```

Ordena **varios _arrays_ a la vez**, o un **array multidimensional** con diferentes niveles.

Las **_keys_ asociativas** se mantienen mientras que las **numéricas** se reindexan.

El parámetro opcional _$array1_sort_order_ indica si el orden será ascendente SORT_ASC o descendente SORT_DESC. _$array1_sort_flags_ permite indicar el modo de ordenación: SORT_NUMERIC, SORT_STRING... Con las mismas opciones que con la función _sort()_. 

Igual que las funciones anteriores, el **array se pasa por referencia a la función**, y ésta devuelve **true** en caso de éxito y **false** en caso de error. 

Los _arrays_ **deben tener el mismo número de elementos**. Se ordena el primer array, y los demás arrays se ordenarán en función del orden que se ha establecido a los valores del primero:

```
$animales = array("oso", "perro", "camaleón");
$animales2 = array ("gato", "liebre", "serpiente");

array_multisort($animales, $animales2);

var_dump($animales);
/*
 Orden alfabético
 array (size=3)
  0 => string 'camaleón' (length=9)
  1 => string 'oso' (length=3)
  2 => string 'perro' (length=5)
 */
var_dump($animales2);
/*
 Orden en función del orden que se ha establecido a $animales
 array (size=3)
  0 => string 'serpiente' (length=9)
  1 => string 'gato' (length=4)
  2 => string 'liebre' (length=6)
 */
```

Los arrays se ordenan como si se tratara de un Excel, **cada array es una columna**. Todos los arrays se ordenan en función del primero, y si dos o más elementos del array son idénticos, se ordenan en función del segundo array dado, y así con los demás arrays. Esto se ve más claro con un array en el que coinciden valores, en ese caso se ordenarán en función de las directrices del segundo array:

```
$miArray = array(
    array("10", 11, 100, 100, 100, 100, "a"),
    array(   1,  2, "2",   3,   4,   8,   1)
);
array_multisort($miArray[0], SORT_ASC, SORT_STRING,
    $miArray[1], SORT_NUMERIC, SORT_DESC);
var_dump($miArray);
/*
 El primer subarray se ordena de forma ascendente comparando elementos como strings:
 array (size=2)
  0 =>
    array (size=7)
      0 => string '10' (length=2)
      1 => int 100
      2 => int 100
      3 => int 100
      4 => int 100
      5 => int 11
      6 => string 'a' (length=1)
El segundo subarray se ordena con el orden del primero, pero si coinciden valores,
se ordenan de forma numérica y con orden descendente. En este caso coincide 100,
y ordena de forma numérica 8 > 4 > 3 > "2":
  1 =>
    array (size=7)
      0 => int 1
      1 => int 8
      2 => int 4
      3 => int 3
      4 => string '2' (length=1)
      5 => int 2
      6 => int 1
 */
```

### 4. Rellenar arrays

Las siguientes funciones se utilizan para rellenar arrays:

* array_fill
```
array array_fill (int $start_index, int $num, mixed $value)
```

Llena un array _$num_ veces (debe ser mayor que cero) con el valor _$value_ desde el key _$start_index_. Si _$start_index_ es negativo, el primer valor tendrá como key ese valor negativo y los demás comenzarán desde cero.

```
$animal = array_fill(3, 2, 'perro');
var_dump($animal);
/*
array (size=2)
  3 => string 'perro' (length=5)
  4 => string 'perro' (length=5)
*/
$animal = array_fill(-7, 3, 'gato');
var_dump($animal);
/*
array (size=3)
  -7 => string 'gato' (length=4)
  0 => string 'gato' (length=4)
  1 => string 'gato' (length=4)
*/
```

* range
```
array range (mixed $start, mixed $end [, number $step = 1 ])
```

Crea un array que contiene un rango de elementos. _$start_ es el valor con el que empieza, _$end_ el valor en el que acaba, y _$step_ es un número opcional que permite modificar el incremento de elemento a elemento.

En el caso de usar letras está limitada a emplear sólo un carácter, si se emplea más de uno, sólo tiene en cuenta el primero.

```
$numeros = range(0, 10, 2);
var_dump($numeros);
/*
array (size=6)
  0 => int 0
  1 => int 2
  2 => int 4
  3 => int 6
  4 => int 8
  5 => int 10
 */
$letras = range('a', 'd');
var_dump($letras);
/*
array (size=4)
  0 => string 'a' (length=1)
  1 => string 'b' (length=1)
  2 => string 'c' (length=1)
  3 => string 'd' (length=1)
 */
```

### 5. Dividir arrays

Existen diferentes **funciones que sirven para dividir un array en partes**:

* array_slice
```
array array_slice (array $array, int $offset [, int $length = null [, bool $preserve_keys = false ]])
```

Extrae una parte de un array, que comienza en _$offset_ (si es negativo comienza por el final) y con una longitud de _$length_.

Si _$length_ es **positivo**, la secuencia tendrá tantos elementos como indique el valor. Si es mayor que el propio _array_, se devolverán los elementos disponibles en el array. Si es **negativo**, la secuencia terminará en tantos elementos comenzando por el final del array. Si se omite, contendrá todos los elementos del array desde _$offset_.

El parámetro opcional _$preserve_keys_ reiniciará los **índices numéricos** de forma predeterminada. Se pueden preservar cambiándolo a **true**. 

```
$entrada = array("a", "b", "c", "d", "e");

$salida = array_slice($entrada, 2);      // "c", "d", y "e"
$salida = array_slice($entrada, -2, 1);  // "d"
$salida = array_slice($entrada, 0, 3);   // "a", "b", y "c"

// Observa las diferencias en las claves de los arrays:
var_dump($salida = array_slice($entrada, 2, -1)); // Devuelve [0]=>c, [1]=>d
var_dump($salida = array_slice($entrada, 2, -1, true)); // Devuelve [2]=>c, [3]=>d
```

* array_splice
```
array array_splice (array &$input, int $offset [, int $length [, mixed $replacement = array() ]] )
```

Elimina una porción del array y la reemplaza por algo. Comienza la eliminación desde _$offset_ (si es negativo, comienza por el final).

Si se omite _$length_, se elimina todo desde _$offset_ hasta el final del array. Si es **positivo**, se eliminan tantos elementos como indique su longitud. Si es **negativo**, el final de la porción eliminada será de tantos elementos como indique la longitud desde el final del array. Si es cero no se elimina ningún elemento. 

Si se especifica el array _$replacement_, los elementos eliminados se reemplazarán por los elementos de este array. 

Para eliminar todo desde _$offset_ hasta el final de array cuando se ha especificado $replacement, se puede utilizar count(_$input_) para _$length_.

```
$animales = array("perro", "gato", "avestruz", "oso", "toro");
array_splice($animales, 2);
var_dump($animales); // Devuelve perro, gato

$animales = array("perro", "gato", "avestruz", "oso", "toro");
array_splice($animales, 1, -1);
var_dump($animales); // Devuelve perro, toro

$animales = array("perro", "gato", "avestruz", "oso", "toro");
array_splice($animales, 1, count($animales), "rinoceronte");
var_dump($animales); // Devuelve perro, rinoceronte

$animales = array("perro", "gato", "avestruz", "oso", "toro");
array_splice($animales, -4, 2, "ardilla");
var_dump($animales); // Devuelve perro, ardilla, oso, toro

$animales = array("perro", "gato", "avestruz", "oso", "toro");
array_splice($animales, -4, 0, "koala");
var_dump($animales); // Devuelve perro, koala, gato, avestruz, oso, toro
```

* array_chunk
```
array array_chunk (array $array, int $size [, bool $preserve_keys = false ])
```

Divide un array en fragmentos de tamaño _$size_. El último fragmento puede contener menos elementos que size. 

Si _$preserve_keys_ es true se mantienen las _keys_, sino se reindexa numéricamente.

Devuelve un **array multidimensional indexado numéricamente**, empezando desde cero. 

```
$animales = array("perro", "gato", "avestruz", "oso", "toro");
$animales2 = array_chunk($animales, 2);
var_dump($animales2);
/*
array (size=3)
  0 =>
    array (size=2)
      0 => string 'perro' (length=5)
      1 => string 'gato' (length=4)
  1 =>
    array (size=2)
      0 => string 'avestruz' (length=8)
      1 => string 'oso' (length=3)
  2 =>
    array (size=1)
      0 => string 'toro' (length=4)
*/
```

### 6. Modificar elementos en arrays

Las siguientes son funciones que modifican los elementos de un array:

* array_shift
```
mixed array_shift (array &$array)
```

Quita el primer valor del _$array_ y lo devuelve. Los **arrays asociativos** no varían sus keys, mientras que los **numéricos** son reindexados y comienzan de cero.

Esta función después de usarse ejecuta _reset()_ en el array.

```
$animales = array("perro", "gato", "avestruz", "oso", "toro");

$unAnimal = array_shift($animales);
var_dump($animales); // Devuelve el array $animales pero sin "perro"
var_dump($unAnimal); // Devuelve el string "perro"
```

* array_unshift
```
int array_unshift (array &$array, mixed $value1 [, mixed $... ])
```

Añade los valores _$value_ al **inicio del _array_**. Igual que en _array_shift()_, en los **arrays asociativos** no varían sus keys, mientras que los **numéricos** se reindexan.

```
$animales = array("perro", "gato", "avestruz");

$masAnimales = array_unshift($animales, "delfin", "ballena");
var_dump($animales); // Devuelve el array $animales con delfín y ballena al principio
var_dump($masAnimales); // Devuelve el integer 5 (total de elementos del array)
```

* array_pop
```
mixed array_pop (array &$array)
```

Quita el último valor del array y lo devuelve.

Esta función después de usarse ejecuta _reset()_ en el array.

```
$animales = array("perro", "gato", "avestruz");

$unAnimal = array_pop($animales);
var_dump($animales); // Devuelve el array con perro, gato
var_dump($unAnimal); // Devuelve el string avestruz
```

* array_push
```
int array_push (array &$array, mixed $value1 [, mixed $... ])
```

Inserta uno o más elementos **al final de un _array_**. Viene a ser lo mismo que insertar un elemento a un array _$array[] = valor_, pero se pueden insertar varios de vez. Si se inserta sólo un valor, es mejor utilizar la forma tradicional. 

```
$animales = array("perro", "gato", "avestruz");

$masAnimales = array_push($animales, "delfin", "ballena");
var_dump($animales); // Devuelve el array $animales con delfín y ballena al final
var_dump($masAnimales); // Devuelve el integer 5 (total de elementos del array)
```

* array_flip
```
array array_flip (array $array)
```

Intercambia las _keys_ de un array con sus _values_.

Los values del array tienen que ser válidos (de tipo _integer_ o _string_), sino genera un **warning** y los elementos en cuestión no se incluirán en el resultado.

```
$animales = ["perro" => "Golden", "gato" => "Mooky"];
$array = array_flip($animales);
var_dump($array);
/*
array (size=2)
  'Golden' => string 'perro' (length=5)
  'Mooky' => string 'gato' (length=4)
*/
```