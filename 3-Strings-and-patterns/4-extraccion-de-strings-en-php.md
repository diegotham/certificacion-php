**Indice de contenido**

1.  Acceder y modificar strings por caracteres
2.  Funciones incorporadas para la extracción de strings

| | | |
| -------- | -------- |
| **Extracción parcial** | **Extracción con array** | **Extracción de elementos** |
| 3. substr | 6. explode | 11. trim |
| 4. mb_substr | 7. implode | 12. ltrim |
| 5. strtok | 8. join | 13. rtrim |
|9. str_split | 14. chop | 10. parse_str |

### 1. Acceder y modificar strings por caracteres

Se puede acceder y modificar los **caracteres de un string** especificando el índice de base cero del carácter deseado, como si fuera un _array_: 

```
$cadena = "animal";
echo $cadena[1]; // Devuelve: n
// Pueden emplearse llaves también:
echo $cadena{3}; // Devuelve: m
```

**Un string es como un array de caracteres**. Internamente **los strings son arrays de bytes**, por lo que acceder o modificar un string mediante corchetes o llaves no es seguro con **caracteres multibyte**.

Si el **valor del índice** es negativo o mayor que el propio string, se emite un error E_NOTICE. Sólo se puede devolver el primer carácter señalado.

```
$cadena = "Blog sobre programación";

$quinto = $cadena[5]; // Devuelve: s
$cuarto = $cadena[4]; // Devuelve: espacio en blanco

$ultimo = $cadena[strlen($cadena)-1]; // Devuelve: n
// No utilizarlo con caracteres multibyte:
$penultimo = $cadena[strlen($cadena)-2]; // Devuelve: �

// Modificar el último carácter de un string
$cadena = "Feliz";
$cadena[strlen($cadena)-1] = "x";
echo $cadena; // Devuelve Felix
```

Los **índices de string** tienen que ser de tipo _integer_ o integer en forma de _string_:

```
$cadena = "animal";

var_dump($cadena['1']); // Devuelve n
var_dump($cadena['1.0']); // Devuelve un Warning Illegal string offset
var_dump($cadena['x1']); // Devuelve un Warning Illegal string offset
var_dump($cadena['1x']); // Devuelve un Notice: a non well formed numeric value encountered
```

### 2. Funciones incorporadas para la extracción de strings

Existen diversas funciones incorporadas en **PHP** para extraer porciones de strings de otros strings, extraer strings de strings para formar arrays, extraer los espacios de un string, etc. Se dividen en tres secciones principales: extracción parcial de strings, extracciones relacionadas con arrays y extracciones de elementos.

### Extracción parcial

Funciones que extraen partes de un string.

### 3\. substr

```
string substr (string $str, int $start [, int $length])
```

Devuelve parte de un string definido por los parámetros _$start_ y _$length_. El parámetro _$start_ indica la posición del carácter desde la cual se comenzará la extracción, por ejemplo en 'perro', el carácter en 0 es p, en 1 es e, etc. Si _$start_ es **negativo** se comienza desde el final del string.

Si la **longitud del string ** es menor que _$start_, la función devuelve **false**.

```
$str = substr("perro", -1); // Devuelve o
$str = substr("perro", -4); // Devuelve erro
$str = substr("calabacín", 3, 2); // Devuelve ab
$str = substr("pupitre", -4, 3); // Devuelve itr
$str = substr("gato", 5, 2); // Devuelve false
```

Si se especifica _$length_, el string devuelto contendrá tantos caracteres como el número indicado. Si _$length_ es mayor que el numero de caracteres existente en el string desde donde extraer, se extraerá sólo el máximo existente.

Si _$length_ es **negativo**, se omite ese mismo número de caracteres del final del string devuelto. 

Si no se especifica _$length_, el string devuelto será desde _$start_ hasta el final.

Otros ejemplos de uso:

```
$str = substr("diego", 0, -1); // Devuelve: dieg
$str = substr("cascada", 5, -2); // Devuelve: string vacío
$str = substr("cascada", 5, -3); // Devuelve false
// No acepta caracteres multibyte:
$str = substr("almacén", -2, 2); // Devuelve: �n
```

Para que se pueda extraer un string con **caracteres multibyte** se puede usar la función [_mb_substr()_](http://php.net/manual/es/function.mb-substr.php).

### 4\. mb_substr
```
string mb_substr (string $str, int $start [, int $length = NULL [, string $encoding = mb_internal_encoding() ]])
```

Obtiene parte de una cadena de caracteres. Es igual que _substr()_ pero se puede especificar la **codificación de caracteres**. Si se omite, se usará el valor de la **codificación de caracteres interna**.

```
$frase = "Hay castañas y arañas en ese camión marrón";
$str = ucfirst(mb_substr($frase, 4, null, 'UTF-8'));
echo $str; // Devuelve: castañas y arañas en ese camión marrón
```

### 5\. strtok
```
strtok strtok (string $str, string $token) y strtok (string $token)
```

Esta función divide un string _$str_ en strings más pequeños, cada uno delimitado por cualquier carácter de token.  

La primera llamada a _strtok()_ utiliza el string entero y devuelve el primer token. Las llamadas subsecuentes sólo necesitan el token a utilizar, pues hace un seguimiento del string. Para volver a empezar se llama de nuevo a _strtok()_ con el string inicial.

```
$cadena = "Esta cadena\nva a ser\tdividida en tokens";
// Se divide en espacios, tabulaciones y cambios de línea
$token = strtok($cadena, " \n\t"); // Primer token
echo "<br>";
while($token !== false) {
// En los tokens subsecuentes no se include el string $cadena
    echo "Palabra: " . $token . "<br>";
    $token = strtok(" \n\t");
}
// Devuelve:
/*
Palabra: Esta
Palabra: cadena
Palabra: va
Palabra: a
Palabra: ser
Palabra: dividida
Palabra: en
Palabra: tokens
*/
```

### Extracción con array

Las siguientes funciones extraen strings de arrays, extraen arrays para ponerlos en un string, convierten strings en arrays, etc. En general son funciones que tienen algún tipo de transformación entre arrays y strings y viceversa.

### 6\. explode
```
array explode (string $delimeter, string $string [, int $limit])
```

Divide un string _$string_ y lo convierte en un array. Cada uno de los strings formados por la división _$delimeter_ son elementos del array que se crea.

El límite _$limit_ indica el número máximo de elementos que devolverá, y el último elemento contendrá el resto del string. Si limit es **negativo**, se devolverán todos los elementos a excepción de los _-$limit_ últimos. Si _$limit_ es 0 se tratará como 1.

Devuelve el _array_ de _strings_ creados por la división _$delimeter_. Si _$delimeter_ está vacío, _explode()_ devuelve **false**. 

Si no se encuentra el delimitador en el string, se devuelte un array con un único elemento, el cual será el mismo string.

```
$pastel = "trozo1, trozo2 con mermelada, trozo3 con naranja, trozo4";
$trozos = explode(", ", $pastel);
echo $trozos[1]; // Devuelve trozo2 con mermelada
```

Ejemplo con el parámetro _$limit_:

```
$string = 'a, b, c, d, e, f, g';
$array = explode(", ", $string, 3);
// $array sólo estará compuesto de 3 elementos:
echo $array[0] . PHP_EOL; // Devuelve a
echo $array[1] . PHP_EOL; // Devuelve b
echo $array[2] . PHP_EOL; // Devuelve c, d, e, f, g
// El array estará compuesto de todos los elementos menos 3:
$array2 = explode(", ", $string, -3);
foreach ($array2 as $arr){
    echo $arr . "<br>";
}
```

### 7\. implode
```
string implode (array $pieces)
```

Une los elementos de un array _$pieces_ en un string con el pegamento _$glue_ que se indique.

Devuelve un string con todos los elementos del array en el mismo orden, separados por el pegamento.

Si se usa un **array vacío** devuelve un **string vacío**.

```
$animales = array('perro', 'gato', 'cerdo', 'vaca', 'gallina', 'escorpión', 'mono');
echo $conjunto_animales = implode(", ", $animales);
// Devuelve: perro, gato, cerdo, vaca, gallina, escorpión, mono
```

### 8\. join
```
**join** **()**
```

Alias de _**implode()**_.

### 9\. str_split
```
array str_split (string $string [, int $split_length = 1])
```

Convierte un string _$string_ en un array.

_$split_length_ es opcional. Si se especifica, el _array_ devuelto se separará en fragmentos que tendrán una longitud igual a su valor. Si no se especifica, el array será sólo de un elemento, cuyo valor será el de _$string_. Si es menor que uno, devolverá **false**.

Si trata con **strings con caracteres multibyte**, realizará la **división en bytes** en lugar de en caracteres, lo que suele dar errores:

```
$string = "Hola, ¿Cómo estás?";
$array = str_split($string, 3);
print_r($array);
/* Array (
[0] => Hol
[1] => a,
[2] => ¿C
[3] => óm
[4] => o e
[5] => st�
[6] => �s? ) */
```

Se puede emplear una función propia como la siguiente para **caracteres multibyte**:

```
function str_split_unicode($str, $l = 0) {
    if ($l > 0) {
        $ret = array();
        $len = mb_strlen($str, "UTF-8");
        for ($i = 0; $i < $len; $i += $l) {
            $ret[] = mb_substr($str, $i, $l, "UTF-8");
        }
        return $ret;
    }
    return preg_split("//u", $str, -1, PREG_SPLIT_NO_EMPTY);
}
// Esta vez el output de $array se mostrará como queremos:
$string = "Hola, ¿Cómo estás?";
$array = str_split_unicode($string, 3);
print_r($array);
```

### 10\. parse_str
```
void parse_str (string $str [, array &$arr ])
```

Esta función convierte un string en variables o elementos de un array, partiendo del QUERY_STRING proporcionado, que puede obtenerse mediante el elemento **$_SERVER['QUERY_STRING']**.

Si se proporciona un array también (es opcional), todos los valores se pasarán como elementos de ese mismo array.

```
$str = "id=322&cesta[]=traje+corbata&cesta[]=zapatos";
parse_str($str);
echo $id . PHP_EOL;  // 322\. ID de usuario
echo $cesta[0]. PHP_EOL; // Traje y corbata para la cesta
echo $cesta[1]. PHP_EOL; // Zapatos para la cesta

parse_str($str, $usuario);
echo $usuario['id']. PHP_EOL;  // value
echo $usuario['cesta'][0]. PHP_EOL; // Traje y corbata para la cesta
echo $usuario['cesta'][1]. PHP_EOL; // Zapatos para la cesta
```

### Extracción de elementos

Las siguientes funciones eliminan partes concretas de un string.

### 11\. trim
```
string trim (string $str [, string $character_mask = " \t\n\r\0\x0B" ])
```

Elimina espacios en blanco u otros caracteres **al principio y al final de la cadena**. Si no se especifica el segundo parámetro, se tienen en cuenta todos los siguientes: ** \t\n\r\0\x0B** (espacio, tabulación, salto de línea, retorno de carro, el byte null y tabulación vertical). Si se especifica sólo se retirarán los indicados.

```
$cadena1 = "\tEste texto es de prueba\n";
$cadena2 = " Este también";

$trimmed1 = trim($cadena1);
$trimmed2 = trim($cadena2);

echo $trimmed1 . PHP_EOL;
echo $trimmed2;
```

### 12\. ltrim
```
string ltrim (string $str [, string $character_mask])
```

Función igual que la anterior, pero sólo retira los espacios y otros caracteres del **principio del string**.

```
$cadena1 = "\tEste texto es de prueba\t";
$trimmed = ltrim($cadena1, "\t");
// En el resultado sólo se ha retirado \t del principio, no del final:
var_dump($trimmed);
```

### 13\. rtrim
```
string rtrim (string $str [, string $character_mask ])
```

Función igual que trim, pero sólo retira los espacios y otros caracteres del **final del string**.

```
$cadena1 = "\tEste texto es de prueba\t";
$trimmed = rtrim($cadena1, "\t");
// En el resultado sólo se ha retirado \t del final, no del principio:
var_dump($trimmed);
```

### 14\. chop
```
**chop ()**
```

Alias de _rtrim()_.