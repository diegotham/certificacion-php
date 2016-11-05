**Indice de contenido**


| | |
| --- | --- |
| 1.  Comparación de strings con operadores ||
| 2. strcmp | 8. substr_compare |
| 3. strcasecmp | 9. similar_text |
| 4. strncmp | 10. levenshtein |
| 5. strncasecmp | 11. soundex |
| 6. strnatcmp | 12. metaphone |
| 7. strnatcasecmp ||

### 1. Comparación de strings con operadores

A la hora de **comparar variables**, resulta sencillo cuando se trata de _**booleanos**_ o de _**integers**_, pero cuando se trata de **_strings_** o partes de _strings_ se puede complicar un poco. 

La forma más común de comparar dos strings es con el **operador ==**. Si los dos strings son iguales, el condicional devuelve true:

```
if('hola' == 'hola'){
    echo "El string coincide";
} else {
    echo "El string no coincide";
}
```

Este tipo de comparación es sensible a mayúsculas y minúsculas, por lo que _'hola' == 'Hola'_ devolvería **false**.

Si todavía se quiere ser más estricto, se pueden emplear tres signos de igual, **===**. De esta forma el tipo de **valor** del que se trata tiene que ser igual:

```
if( 4 === '4'){
    echo "El string coincide";
} else {
    echo "El string no coincide";
}
```

En este caso la condición resulta **false**, ya que son dos tipos distintos, un _integer_ y un _string_. Deberían ser '4' === '4' o 4 === 4.

### 2\. strcmp

int strcmp (string $str1, string $str2)

Es la función más básica para comparar dos strings a nivel binario. Tiene en cuenta mayúsculas y minúsculas. Devuelve < 0 si el primer valor dado es menor que el segundo, > 0 si es al revés, y 0 si son iguales:

```
$var1 = "hola";
$var2 = "hola";
if (strcmp($var1, $var2) === 0){
    echo "Los strings coinciden";
}
```

Una cadena es mayor que otra en función de los [códigos numéricos](https://es.wikipedia.org/wiki/ASCII) equivalentes de cada carácter, por ejemplo la letra D tiene el código 68 y d el código 100\. En la práctica sólo se suelen utilizar estas funciones utilizando estructuras de control condicionales respecto al valor 0, como en el ejemplo anterior y en los siguientes.

### 3\. strcasecmp

```
int strcasecmp (string $str1, string str2)
```

Idéntica a la anterior pero esta vez insensible a mayúsculas y minúsculas. Devuelve los mismos resultados:

```
$var1 = "Hola";
$var2 = "hola";
if (strcmp($var1, $var2) === 0){
    echo "Los strings coinciden";
}
```

### 4\. strncmp

```
int strncmp (string $str1, string $str2, int $length)
```

Comparación a nivel binario de los primeros **n** caracteres entre strings. Es igual que _strcmp()_, solo que se puede especificar un **límite de caracteres** de cada string a comparar. Es sensible a mayúsculas y minúsculas, y devuelve el mismo tipo de resultado:

```
$var1 = "holG34d";
$var2 = "holaquetal";
if (strncmp($var1, $var2, 3) === 0){
    echo "Los strings coinciden";
} // Coinciden, ya que hol === hol
```

### 5\. strncasecmp

```
int strncasecmp (string $str1, string $str2, int $length)
```

Idéntico a _strncmp()_ pero insensible a mayúsculas y minúsculas. Devuelve lo mismo que las anteriores:

```
$var1 = "hoLAQG34d";
$var2 = "holaQuetal";
if (strncasecmp($var1, $var2, 5) === 0){
    echo "Los strings coinciden";
} // Los strings también coinciden
```

### 6\. strnatcmp

```
int strnatcmp (string $str1, string $str2)
```

Compara dos strings utilizando un algoritmo de orden natural, de la forma en que un ser humano lo haría. Por ejemplo, de forma computerizada 10 va antes que 2, 22 va antes que 4... Es sensible a mayúsculas y minúsculas:

```
$arr1 = $arr2 = array("img12.png", "img10.png", "img2.png", "img1.png");
echo "Comparación estándar normal\n";
usort($arr1, "strcmp");
print_r($arr1);
/*
 * Devuelve:
 Array
(
    [0] => img1.png
    [1] => img10.png
    [2] => img12.png
    [3] => img2.png
)
 */
echo "\nComparación con orden natural\n";
usort($arr2, "strnatcmp");
print_r($arr2);
/*
 Devuelve:
Array
(
    [0] => img1.png
    [1] => img2.png
    [2] => img10.png
    [3] => img12.png
)
 */
```

### _7_. strnatcasecmp

```
int strnatcasecmp (string $str1, string $str2)
```

Versión igual a la anterior solo que ésta es insensible a mayúsculas y minúsculas.

### 8\. substr_compare

```
int substr_compare (string $mainstr, string $str, int $offset [, int $length [, bool $case_insensitivity = false]])
```

Esta función compara el string _$main_str_ con _$str_ desde la **posición** _$offset_. Se puede especificar la **cantidad de caracteres** con _$length_. 

_$offset_ marca el inicio para la comparación. Si es **negativo**, se comienza a contar desde el final del _string_.

Si _**case_insensitivity**_ es true, la comparación no tiene en cuenta mayúsculas y minúsculas.

Si _$main_str_ (desde _$offset_) es menor que **str** devuelve **< 0**, si es mayor **> 0**, y si son iguales devuelve 0.

```
echo substr_compare("calabaza", "baza", 4, 4); // Devuelve 0 (iguales)
echo substr_compare("calabaza", "baza", 5, 4); // Devuelve -1
echo substr_compare("calabaza", "baza", 7, 4); // Devuelve -1
echo substr_compare("calabaza", "baza", -4, 4); // Devuelve 0
echo substr_compare("calabaza", "baza", -6, 4); // Devuelve 10
```

### 9\. similar_text

```
int similar_text (string $first, string $second [, float &$percent])
```

Calcula la similitud entre dos strings según el algoritmo **ISBN 0-131-00413-1**. Devuelve el número de caracteres que coinciden. Si se precisa un porcentaje se obtiene una medida de la similitud en %:

```
$var1 = 'Mi lista de la compra de ayer fue: alcachofas, calabacines y manzanas';
$var2 = 'Mi lista de la compra de hoy es: calabacines, pimientos y peras';
similar_text($var1, $var2, $porcentaje);

echo $porcentaje; // Devuelve 68.1818
```

Puede resultar útil a la hora de aceptar contenidos de usuario al poner un condicional con un porcentaje X para evitar **duplicación de contenido**, por ejemplo, ponerlo en revisión o rechazarlo si supera el 90% de similitud.

### 10\. levenshtein

```
int levenshtein (string $str1, string $str2)
```

La **distancia Levenshtein** es el número mínimo de caracteres que se tienen que sustituir, insertar o borrar para transformar _**str1**_ en _**str2**_. 

En la versión más sencilla de la función se obtienen los dos _strings_ como parámetros y se calcula el número de operaciones de insertar, reemplazar y eliminar necesarias para transformar _**str1**_ en _**str2**_. 

La versión más precisa toma tres parámetros adicionales que definen el costo de las operaciones de insertar, reemplazar y eliminar:

```
int levenshtein (string $str1, string $str2, int $costins, int $costrep, int $cost_del])
```

La función devuelve la **distancia Levenshtein entre los dos _strings_** o -1 si uno de los strings es mayor que el límite de 255 caracteres. El siguiente es un ejemplo con la versión sencilla:

```
// palabra de entrada mal escrita
$miAnimal = 'perrl';
// array de palabras a verificar
$animales  = array('perro', 'gato', 'oso', 'liebre', 'ardilla', 'vaca', 'cerdo');
// aún no se ha encontrado la distancia más corta
$shortest = -1;
// bucle a través de las palabras para encontrar la más cercana
foreach ($animales as $animal) {
    // calcula la distancia entre la palabra de entrada
    // y la palabra actual
    $lev = levenshtein($miAnimal, $animal);
    // verifica por una coincidencia exacta
    if ($lev == 0) {
        // la palabra más cercana es esta (coincidencia exacta)
        $closest = $animal;
        $shortest = 0;
        // salir del bucle ya que se ha encontrado una coincidencia exacta
        break;
    }
    // si esta distancia es menor que la siguiente distancia
    // más corta o si una siguiente palabra más corta aun no se ha encontrado
    if ($lev <= $shortest || $shortest < 0) {
        // establece la coincidencia más cercana y la distancia más corta
        $closest  = $animal;
        $shortest = $lev;
    }
}
echo "Palabra de entrada: $miAnimal\n";
if ($shortest == 0) {
    echo "Palabra exacta encontrada: $closest\n";
} else {
    echo "¿Quisiste decir: $closest?\n";
}
// Devuelve: Palabra de entrada: perrl ¿Quisiste decir: perro?
```

### 11\. soundex

```
string soundex (string $str)
```

Calcula la **clave soundex** de un _string_. Las palabras pronunciadas de forma similar producen la misma clave soundex, y se usan por ejemplo para **simplificar búsquedas en bases de datos** cuando se conoce la pronunciación pero no como se escribe, o si se ha escrito mal un valor, encontrar el correcto en la base de datos. 

Devuelve un _string_ de 1 letra seguida de 3 números. La letra es la misma con la que empieza la palabra:

```
echo soundex("Mayones") . PHP_EOL;
echo soundex("Mayonaise") . PHP_EOL;
echo soundex("Maeiionis") . PHP_EOL;
echo soundex("Mayonis") . PHP_EOL;
echo soundex("Meyonis") . PHP_EOL;
echo soundex("Mayonaise") . PHP_EOL;
// Todos devuelven M520
```

### 12\. metaphone

```
string metaphone (string $str [, int $phonemes = 0 ])
```

Calcula la **clave metaphone** de un _string_. De forma similar a _soundex()_, crea la misma clave para palabras que suenan parecido. Es más precisa que _soundex()_ ya que conoce las **reglas básicas de la pronunciación del inglés**. Las claves generadas son de longitud variable.

El valor opcional de _$phonemes_ restringe la clave devuelta por metaphone en caracteres de longitud. Por defecto es 0 (sin restricción).

```
var_dump(metaphone("analyzer")); // string 'ANLSR' (length=5)
var_dump(metaphone("analyzing")); // string 'ANLSNK' (length=6)

// Utilizando phonemes
var_dump(metaphone("analyzer", 4)); // string 'ANLS' (length=4)
var_dump(metaphone("analyzing", 4)); // string 'ANLS' (length=4)
```