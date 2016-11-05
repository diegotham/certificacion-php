**Indice de contenido**

| **Variación de strings** | **Modificación de caracteres** | **Alteración de strings** |
| 1. str_replace | 4. lcfirst | 9. str_pad |
| 2. str_ireplace | 5. ucfirst | 10. str_repeat |
| 3. substr_replace | 6. ucwords | 11. nl2br |
| 7. strtolower | 12. wordwrap | 8. strtoupper |
| 13. strip_tags |

### Variación de strings

Funciones que reemplazan strings en otros.

### 1\. str_replace
```
mixed str_replace (mixed $search, mixed $replace, mixed $subject [, int &$count ])
```

Devuelve un string o un array con todas las apariciones de _$search_ en _$subject _reemplazadas con el valor dado de _$replace_.

Si _$search_ y _$replace_ son arrays, se toma un valor de cada array en orden y se utilizan para reemplazarlos en _$subject_. Si _$replace_ tiene menos valores que _$search_, un string vacío se utiliza para el resto de valores de reemplazo. Si _$search_ es un array y _$replace_ es un string, el string de reemplazo se usa para cada valor de _$search_.

Si _$subject_ es un array, la búsqueda y el reemplazo se realizan con cada entrada de _$subject_, y se devolverá un array.

_$count_ indica el número de reemplazos que se han realizado. 

**Ejemplos**

```
// Crea un string de un div con la clase table:
$divtag = str_replace("%class%", "table", "<div class='%class%'>"); // Devuelve: <div class='table'>

// Elimina las vocales de una frase:
$vocales = array("a", "e", "i", "o", "u");
$sinvocales = str_replace($vocales, "", "Hola, ¿Que tal?"); // Devuelve: Hl, ¿Q tl?

// Prueba con count, número de vocales de una frase:
$vocales = array("a", "e", "i", "o", "u", "á", "é", "í", "ó", "ú");
$frase = "En ese paisaje se ve un río y una montaña, ningún árbol";
$numerodevocales = str_replace($vocales, "", $frase, $count);
echo $count; // Devuelve: 20
echo $numerodevocales; // Devuelve: En s psj s v n r y n mntñ, nngn rbl
```

**Ejemplos con orden de reemplazo**

```
$str     = "Line 1\nLine 2\rLine 3\r\nLine 4\n";
$order   = array("\r\n", "\n", "\r");
$replace = '<br>';
// Primero convertirá todos los \r\n, después los \n y después los \r
echo $newstr = str_replace($order, $replace, $str);

// Otro ejemplo
$search  = array('A', 'B', 'C', 'D', 'E');
$replace = array('B', 'C', 'D', 'E', 'F');
$subject = 'A';
echo str_replace($search, $replace, $subject); // Devuelve: F
```

**Otros ejemplos con orden de reemplazo**

```
$numeros = array(4, 3);
$cifras = array (2, 5);
$texto = 'Hay 34 mesas y más de 134 vasos';
$salida = str_replace($numeros, $cifras, $texto);
echo $salida; // Hay 52 mesas y más de 152 vasos

// Si cambiamos el orden:
$numeros = array(3, 4);
$salida = str_replace($numeros, $cifras, $texto);
echo $salida; // Hay 25 mesas y más de 125 vasos
```

### 2\. str_ireplace
```
mixed str_ireplace (mixed $search, mixed $replace, mixed $subject [, int &$count] )
```

Esta función hace lo mismo que _str_replace()_ pero es insensible a mayúsculas y minúsculas.

```
$search = "que tal";
$replace = "COMO ESTAS";
$string = "HOLA QUE TAL";
$resultado = str_ireplace($search, $replace, $string);
echo $resultado; // Devuelve: HOLA COMO ESTAS
```

### 3\. substr_replace
```
mixed substr_replace (mixed $string, mixed $replacement, mixed $start [, mixed $length ])
```

Reemplaza un texto dentro de un string. La función inserta _$replacement_ desde la posición _$start_ hasta _$length_ (opcional) en _$string_.

El _$string_ puede ser un array de strings, en cuyo caso se hará una sustitución en cada string.  Los valores _replacement_, _start_ y _length_ pueden proporcionarse también como arrays, en cuyo caso cada valor será correspondiente a cada valor de entrada.

Si _$start_ es **positivo** el reemplazo comenzará desde el principio contando con el valor de _$start_. Si es **negativo**, se contará desde el final del string el valor de _$start_.

Si _$length_ es positivo, representa la longitud de la porción de _$string_ que se va a reemplazar. Si es negativo, representa el número de caracteres desde el final del string en el cual se deja de sustituir. 

```
$texto = 'En ese parque hay un perro verde';

// Reemplazo de toda la frase:
echo substr_replace($texto, 'Una cosa', 0) . "<br>"; // Una cosa
echo substr_replace($texto, 'Otra cosa', 0, strlen($texto)). "<br>"; // Otra cosa
// Inserta "Frase:" al comienzo de $texto:
echo substr_replace($texto, 'Frase: ', 0, 0). "<br>"; // Frase: En ese parque hay un perro verde
// Cambio perro verde por perro azul:
$posicion = strpos($texto, "verde");
echo substr_replace($texto, 'azul', $posicion) . "<br>"; // En ese parque hay un perro azul
// Otra forma de ver un perro azul:
echo substr_replace($texto, 'azul', -5) . "<br>"; // En ese parque hay un perro azul
// Cambio de perro por oso:
$posicion = strpos($texto, "perro");
echo substr_replace($texto, "oso", $posicion, -6) . "<br>"; // En ese parque hay un oso verde
echo substr_replace($texto, "oso", $posicion) . "<br>"; // En ese parque hay un oso
```

Ejemplo de reemplazo en un array:

```
// Añadir un determinante en cada elemento del array:
$animales = array("perro", "gato", "conejo");
$animales2 = substr_replace($animales, 'un ', 0, 0);
var_dump($animales2);
/* Muestra:
 array (size=3)
  0 => string 'un perro' (length=8)
  1 => string 'un gato' (length=7)
  2 => string 'un conejo' (length=9)
 */
```

Más ejemplos sobre arrays:

```
$input = array('A: XXX', 'B: XXX', 'C: XXX');
// EJEMPLO 1
$nuevo_array = substr_replace($input, 'YYY', 3, 3);
var_dump($nuevo_array);// Devuelve: A: YYY; B: YYY; C: YYY

// EJEMPLO 2 (mismo $input)
$replace = array('AAA', 'BBB', 'CCC');
$nuevo_array = substr_replace($input, $replace, 3, 3);
var_dump($nuevo_array);// Devuelve: A: AAA; B: BBB; C: CCC

// EJEMPLO 3 (mismo $input y $replace)
$length = array(1, 2, 3);
$nuevo_array = substr_replace($input, $replace, 3, $length);
var_dump($nuevo_array); // Devuelve: A: AAAXX; B: BBBX; C: CCC
```

### Modificación de caracteres

Funciones que modifican ciertos caracteres de un string.

### 4\. lcfirst
```
string lcfirst (string $str)
```

Devuelve un string con el primer carácter de _$str_ en minúscula. 

```
$string = "Hola que tal";
echo lcfirst($string); // Devuelve: hola que tal
```

### 5\. ucfirst
```
string ucfirst (string $str)
```

Devuelve un _string_ con el primer carácter de _$str_ en mayúscula.

```
$string = "hola que tal";
echo ucfirst($string); // Devuelve: Hola que tal
```

### 6\. ucwords
```
string ucwords (string $str [, string $delimeters = " \t\r\n\f\v" ])
```

Convierte a mayúsculas el primer carácter de cada palabra de una cadena _$str_ (caracteres alfanuméricos). Los _$delimeters_ señalan la separación de cada palabra, por defecto se entiende que cada palabra estará separada por cualquiera de los siguientes: **\t\r\n\f\v** y **espacio**. 

```
$string = "hola que tal";
echo ucwords($string); // Devuelve: Hola Que Tal
```

### 7\. strtolower
```
string strtolower (string $string)
```

Convierte una cadena a minúsculas. Todos los caracteres.

```
$string = "HOla QUE tal COMO EStas";
echo strtolower($string); // Devuelve: hola que tal como estas
```

### 8\. strtoupper
```
string strtoupper (string $string)
```

Convierte una cadena a mayúsculas. Todos los caracteres.

```
$string = "HOla QUE tal COMO EStas";
echo strtoupper($string); // Devuelve: HOLA QUE TAL COMO ESTAS
```

### Alteración de strings

Funciones que reemplazan o alteran strings de diferentes formas.

### 9\. str_pad
```
string str_pad (string $input, int $pad_length [, string $pad_string = " " [, int $pad_type = STR_PAD_RIGHT ]])
```

Rellena un string _$input_ hasta una longitud determinada _$pad_length_ con otro string _$pad_string_.

Esta función devuelve el _string_ _$input_ rellenado por la izquierda (_$pad_type_ = **STR_PAD_LEFT**), por ambos lados (_$pad_type_ = **STR_PAD_BOTH**) o por la derecha que es por defecto. 

El _$pad_length_ es la longitud del string más el relleno, si es negativo o inferior a _$string_, no se realiza ningún relleno. 

El _$pad_string_ se puede truncar si el número de caracteres de relleno no se pueden dividir de forma uniforme por la longitud del _pad_string_.

```
$input = "Cadena";
echo str_pad($input, 10, "s"); // Devuelve Cadenassss
echo str_pad($input, 14, "=", STR_PAD_BOTH); // Devuelve ====Cadena====
echo str_pad($input, 11, "ku"); // Devuelve Cadenakukuk
echo str_pad($input, 5, "la", STR_PAD_LEFT); // Devuelve Cadena
```

### 10\. str_repeat
```
string str_repeat (string $input, int $multiplier)
```

Repite un string. Devuelve el string repetido _$multipler_ veces. Si éste es 0, se devuelve el _$input_ vacío. 

```
$saludo = "hola";
echo str_repeat($saludo, 3); // Devuelve: holaholahola
```

### 11\. nl2br
```
string nl2br (string $string [, bool $is_xhtml = true ])
```

Inserta saltos de línea en un string. Devuelve _$string_ con '<br />' o '<br>' insertado antes de cada nueva línea (partiendo de \r\n, \n\r y \r).

_$is_xhtml_ es un valor opcional para indicar si utilizar saltos de línea compatibles con XHTML o no. 

```
$texto = "Esto\r\nes un texto\n\r con varias\n líneas";
echo nl2br($texto);
```

### 12\. wordwrap
```
string wordwrap (string $str [, int $width = 75 [, string $break = "\n" [, bool $cut = false ]]] )
```

Permite dividir un string _$str_ en porciones de caracteres de tamaño _$width_. El valor opcional de _$break_ es la forma en que se rompe el texto. Si _$cut_ es **true**, el _string_ se ajustará al valor de $width. Si una palabra es más larga que el ancho dado, será dividida, si es **false** la función no la divide:

```
$texto = "Esto es un texto normal";
echo wordwrap($texto, 3, "<br>");
/* Devuelve:
Esto
es
un
texto
normal
 */
$texto = "Esto es un texto normal";
echo wordwrap($texto, 3, "<br>", true);
/* Devuelve:
Est
o
es
un
tex
to
nor
mal
 */
```

### 13\. strip_tags
```
string strip_tags (string $str [, string $allowable_tags ] )
```

Retira los bytes **null** y las etiquetas HTML y PHP de un string _$str_. 

El parámetro opcional _$allowable_tags_ permite especificar qué etiquetas no deben ser retiradas. 

```
<?php
$texto = '<h1>Esto es un saludo:</h1>
<p>Hola que tal</p>
<h3>Algo más de contenido aquí</h3>';
echo strip_tags($texto);
```