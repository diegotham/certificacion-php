La mayoría de las veces para **mostrar un string en PHP** se utiliza echo(), que devuelve el string mostrándolo en HTML. Con **echo** incluso se pueden **concatenar varios strings juntos** (siempre sin el uso de paréntesis). 

Una opción para **mostrar múltiples opciones en una caja de selección** con _**echo**_ es la siguiente:

```
$opciones = array(
    'opcion1' => 'titulo1',
    'opcion2' => 'titulo2',
    'opcion3' => 'titulo3'
);

echo '<select>';

foreach ($opciones as $key => $value)
{
    echo 'option value="'.$key.'">'.$value.'</option>';
}

echo '</select>';
```

El código anterior resulta **legible** y fácil de construir, pero si por ejemplo se tiene que mostrar un **array de imágenes** con sus correspondientes **etiquetas**, la cosa se complica:

```
$imagenes = array(
    'imagen1' => array('class'=> 'style', 'alt'=>'imagen1', 'title'=> 'imagen1','src'=>'http://www.example.com/imagen1.jpg'),
    'imagen2' => array('class'=> 'style', 'alt'=>'imagen2', 'title'=> 'imagen2','src'=>'http://www.example.com/imagen2.jpg'),
    'imagen3' => array('class'=> 'style', 'alt'=>'imagen3', 'title'=> 'imagen3','src'=>'http://www.example.com/imagen3.jpg')
);

echo '<select>';

foreach ($imagenes as $key => $value)
{
    echo '<img class="'.$value["class"].'" alt="'.$value["alt"].'" title="'.$value["title"].'" src="'.$value["src"].'" />';
}

echo '</select>';
```

Ahora no es tan fácil de leer con tanto abrir y cerrar comillas simples y dobles.

La solución la proporcionan las **funciones incorporadas de formateo de strings**.

| | |
| -------- | -------- |
| 1. sprintf | 5. fprintf |
| 2. printf | 6. sscanf |
| 3. vsprintf | 7. fscanf |
| 4. vprintf | |

### ​1\. sprintf

```
string sprintf (string $format [, mixed $args [, mixed $... ]] )
```

Devuelve un string formateado según _$format_. 

El **string de formateo** está compuesto de cero o más **caracteres** (excluyendo %), que se copian al resultado, junto con **especificaciones de conversión** que permiten concretar la salida de los argumentos. Esto se aplica para **sprintf()** y **printf()**.

Cada especificación de conversión se constituye por un **signo de porcentaje %** seguido de un elemento (los 5 primeros puntos son opcionales):

1.  Especificador de **signo** que fuerza a usar un **signo en un número** (+ ó -).
2.  Especificador de **relleno** que indica que **carácter** se utiliza para **rellenar el resultado** hasta el tamaño justo del string, con ceros o con espacios. El valor por defecto es rellenar con espacios, si se pone un cero, se rellenará con ceros. Un carácter de relleno alternativo se puede especificar prefijándolo con una comilla simple (').
3.  Especificador de **alineación** que indica si el resultado ha de alinearse a la izquierda o derecha. Por defecto es a la derecha, si incluye (-) se alineará a la izquierda.
4.  Especificación de un **número** opcional de **ancho mínimo de caracteres** como resultado de la conversión.
5.  Especificación de precisión en forma de **punto (.)** seguido de un **número** que indica cuántos dígitos decimales deben mostrarse para los números **_float_**. Si se utiliza con un string, actúa como punto de corte, estableciendo un **número máximo de caracteres el string**.
6.  Especificador de tipo (obligatorio) que indica el tipo con el que han de ser tratados los datos del argumento:

| | |
| -------- | -------- |
| **Tipo** | **Valor** |
| **%** | **% literal**, no requiere argumento |
| **b** | Argumento de tipo integer y presentado como **número binario** |
| **c** | Argumento de tipo integer y presentado como el **carácter** con ese **valor ASCII** |
| **d** | Argumento de tipo integer y presentado como un **número decimal** |
| **e** | Argumento tratado con **notación científica** (ej: 1.2e+2). El especificador de precisión indica el número de dígitos después del punto decimal |
| **E** | Como **%e** pero utiliza la letra mayúscula (ej: 1.2E+2) |
| **f** | Argumento tratado como valor de tipo float y presentado como un **float** (considerando la configuración regional) |
| **F** | Lo mismo que el anterior pero sin considerar la configuración regional |
| **g** | Igual que %e y %f |
| **G** | Igual que %E y %f |
| **o** | Argumento tratado como valor de tipo integer y presentado como un **número octal** |
| **s** | Argumento tratado y presentado como un **string** |
| **u** | Argumento tratado como valor de tipo integer y presentado como **número decimal sin signo** |
| **x** | Argumento tratado como valor de tipo integer y presentado como **número hexadecimal** con letras en minúscula |
| **X** | Lo mismo que x pero con las letras en mayúscula |

Las variables se fuerzan con el especificador a un tipo adecuado:

| | |
| -------- | -------- |
| **Tipo** | **Especificador** |
| **string** | s |
| **integer** | d, u, c, o, x, X, b |
| **double** | g, G, e, E, f, F |

**Ejemplo**:

```
$numero = 10;
$donde = "carretera";

$texto = 'Hay %d camiones en la %s';
echo sprintf($texto, $numero, $donde); // Devuelve: Hay 10 camiones en la carretera
```

Si cambiásemos el orden de los argumentos, no funcionaría como esperamos. Para ello podemos indicar con marcadores de posición el orden de los argumentos. Este marcador **n$** debe ir inmediatamente después del **signo de porcentaje %**:

```
$numero = 10;
$donde = "carretera";

$texto = 'Hay %2$d camiones en la %1$s';
echo sprintf($texto, $donde, $numero);
//Si no se incluyera el orden devolvería: Hay 0 camiones en la 10
```

Se pueden **repetir los marcadores de posición**:

```
$numero = 10;
$donde = "carretera";

$texto = 'Hay %2$d camiones en la %1$s, y también hay %2$d coches';
echo sprintf($texto, $donde, $numero);
```

Si se empiezan a **combinar especificadores** se pueden obtener diferentes resultados:

```
echo sprintf("%'.9d", 123) ."<br>"; // ......123
echo sprintf("%09d", 123) ."<br>"; // 000000123
echo sprintf("%'.19d", 123) ."<br>"; // ...............123
echo sprintf("%'p9d", 123) ."<br>"; // pppppp123
echo sprintf("%9d", 123) ."<br>"; // '      123'
echo sprintf("%'*9d", 123) ."<br>"; // ******123
```

En el ejemplo anterior tenemos la **comilla simple (')** para indicar que se rellene el string con los valores indicados. El **punto (.)** se ha empleado como relleno. Si son strings o floats el punto tiene el significado de indicar el número de caracteres o decimales a devolver, respectivamente:

```
echo sprintf("%.1f", 123.4433) ."<br>"; // 123.4
echo sprintf("%.1s", "hola") ."<br>"; // h
```

Usar una **combinación de especificadores con caracteres que requieren más de un byte** puede tener resultados inesperados.

Otros ejemplos de **combinaciones de números**. Siempre devuelven un _**string**_:

```
$a =  19905;
$b = -19905;
$c = 68; // ASCII 68 es 'D'
echo "Binario: " . sprintf("%%b = '%b'\n", $a) . "<br>"; // Binario:%b = '100110111000001'
echo "ASCII: " . sprintf("%%c = '%c'\n", $c) . "<br>"; // ASCII:%c = 'D'
echo "Integer: " . sprintf("%%d = '%d'\n", $a) . "<br>"; // Integer:%d = '19905'
echo "Notación científica: " . sprintf("%%e = '%e'\n", $a) . "<br>"; // Notación científica: %e = '1.990500e+4'
echo "Integer positivo: " . sprintf("%%u = '%u'\n", $a) . "<br>"; // Integer positivo: %u = '19905'
echo "Integer negativo: " . sprintf("%%u = '%u'\n", $b) . "<br>"; // Integer negativo: %u = '18446744073709531711'
echo "Float: " . sprintf("%%f = '%f'\n", $a) . "<br>"; // Float: %f = '19905.000000'
echo "Octal: " . sprintf("%%o = '%o'\n", $a) . "<br>"; // Octal: %o = '46701'
echo "Cadena: " . sprintf("%%s = '%s'\n", $a) . "<br>"; // Cadena: %s = '19905'
echo "Hexadecimal(minúsculas): " . sprintf("%%x = '%x'\n", $a) . "<br>"; // Hexadecimal(minúsculas): %x = '4dc1'
echo "Hexadecimal(mayúsculas): " . sprintf("%%X = '%X'\n", $a) . "<br>"; // Hexadecimal(mayúsculas): %X = '4DC1'
echo "Signo sobre entero positivo: " . sprintf("%%+d = '%+d'\n", $a) . "<br>"; // Signo sobre entero positivo: %+d = '+19905'
echo "Signo sobre entero negativo: " . sprintf("%%+d = '%+d'\n", $b); // Signo sobre entero negativo: %+d = '-19905'
```

Ejemplos con **_strings_** (obsérvese que se utiliza _printf()_, no _sprintf()_):

```
$a = 'moto';
$b = 'muchas motos';

printf("[%s]\n",      $a); // salida estándar de string
printf("[%10s]\n",    $a); // justificación a la derecha con espacios
printf("[%-10s]\n",   $a); // justificación a la izquierda con espacios
printf("[%010s]\n",   $a); // rellenado con ceros también funciona con strings
printf("[%'#10s]\n",  $a); // utiliza el carácter de relleno personalizado '#'
printf("[%.10s]\n", $b); // corte a los 10 caracteres
```

Ejemplo de **relleno de ceros para fechas**:

```
$año = 1999;
$mes = 2;
$día = 9;
echo $fecha_iso = sprintf("%02d-%02d-%04d", $día, $mes, $año);
```

Ejemplo de **relleno de ceros para dinero**:

```
$dinero1 = 88.72;
$dinero2 = 14.38;
$dinero = $dinero1 + $dinero2;
// echo $dinero producirá "103.1";
echo $formateado = sprintf("%.2f", $dinero);
// echo $formateado producirá "103.10"
```

</div>

### 2\. printf
```
int printf (string $format [, mixed $args [, mixed $... ]] )
```

Imprime una **cadena con formato**. La diferencia con _sprintf()_ es que _printf()_ muestra el resultado también, _sprintf()_ sólo lo devuelve.

```
$frase1 = "Esto es";
$frase2 = "programación en PHP";
$versión = "5.5";
printf('%1$s %2$s %3$.1f', $frase1, $frase2, $versión);
/* Nota:
Si se hace echo printf() devuelve primero el string y
después devuelve el número de caracteres devueltos
*/
echo printf('%1$s %2$s %3$.1f', $frase1, $frase2, $versión);
// Devuelve Esto es programación en PHP 5.532 // (32 caracteres)
```

### 3\. vsprintf
```
string vsprintf (string $format, array $args)
```

Devuelve una **cadena con formato**. Es igual que _sprintf()_ pero acepta un array de argumentos, en lugar de un número variable de argumentos. Lo que devuelve son los **valores**, por lo que los **keys** dan igual:

```
// Esto:
$array = array("Esto es", "programación en PHP", "5.5");
echo vsprintf('%1$s %2$s %3$.1f', $array);
// Devolverá lo mismo que:
$array = array("uno" => "Esto es", "dos" => "programación en PHP", "tres" => "5.5");
echo vsprintf('%1$s %2$s %3$01.1f', $array);
```

### 4\. vprintf
```
int vprintf (string $format, array $args)
```

Imprime una cadena con formato. La diferencia con _vsprintf()_ es que _vprintf_ muestra el resultado también, _vsprintf()_ sólo lo devuelve.

```
$frases = array("Esta es una historia", "de un integer:", "5",
    "y un float:",
    "0.7",
    "que vale:",
    "12",
    "euros,",
    "creada en fecha:"
);
$fecha = array("28", "9", "2015");
// vprintf sólo permite un array, por lo que hacemos array_merge
$texto = array_merge($frases, $fecha);
// Mostramos el texto:
vprintf("%s %s %d %s %.1f %s %.2f %s %s %02d-%02d-%04d", $texto);
// Esta es una historia de un integer: 5 y un float: 0.7 que vale: 12.00 euros, creada en fecha: 28-09-2015
```

### 5\. fprintf
```
int fprintf (resource $handle, string $format [, mixed $args [, mixed $... ]] )
```

Escribe un _**string**_ de acuerdo a _$format_ en el _resource_ especificado por _$handle_. 

```
if (!($archivo = fopen('hola.log', 'w')))
    return;
$dia = 28;
$mes = 9;
$año = 2015;
$texto = "Este es un texto de ejemplo a fecha de %02d/%02d/%4d";
fprintf($archivo, $texto, $dia, $mes, $año);
// Escribe en hola.log el texto: Este es un texto de ejemplo a fecha de 28/09/2015
```

### 6\. sscanf
```
mixed sscanf (string $str, string $format [, mixed &$... ])
```

Interpreta un string de entrada de acuerdo con un formato _$format_.

Cualquier **espacio en blanco** en el **string de formato** coincide con cualquier espacio en blanco en el **string de entrada**. Incluso un **tabulador \t** en el string de formato puede coincidir con un carácter simple de espacio en el string de entrada. 

El formato tiene las mismas características que en _sprintf()_ salvo algunas diferencias:

1.  La función no es consciente de la configuración regional.
2.  F, g, G y b no están soportados.
3.  D representa número decimal.
4.  i representa un integer con detección de base.
5.  n representa el número de caracteres procesados hasta el momento.

Se puede utilizar la función list para asignar cada formato especificado a cada variable, sino se obtiene un array:

```
// Aquí devolverá un array:
$coches = sscanf("2300", "%d");

// Fecha de manufactura
$fecha = "4 Marzo 2014";
// Aquí se crean tres variables:
list($day, $month, $year) = sscanf($fecha, "%d %s %d");
echo "Un total de $coches[0] coches se fabricaron en fecha: $day/$month/$year\n";
```

Si sólo se pasan dos parámetros a esta función, los valores se devolverán como un **array**, si se pasan más parámetros a la función, devolverá el número de valores asignados:

```
$usuario = "53\tPeter Morgan";
$var = sscanf($usuario, "%d\t%s %s", $id, $first, $last);
echo "<author id='$id'>
    <firstname>$first</firstname>
    <surname>$last</surname>
</author>\n";
```

### 7\. fscanf
```
mixed fscanf (resource $handle, string $format [, mixed &$... ])
```

Función similar a _sscanf()_ pero coge el input de un archivo _$handle_ y lo interpreta en función del formato _$format_.

Cada llamada a _fscanf()_ lee una línea del archivo.

En el siguiente ejemplo abre el archivo _usuarios.txt_, y por cada línea crea un array _$userinfo_, con el cual mediante _list()_ va asignando las variables: _$nombre_, _$profesion_ y _$countrycode_, separadas por tabulador _**\t**_:

```
$handle = fopen("usuarios.txt", "r");
while ($userinfo = fscanf($handle, "%s\t%s\t%s\n")) {
    list ($name, $profession, $countrycode) = $userinfo;
    //... hacer algo con los valores
}
fclose($handle);
```

Ahora el ejemplo del principio podemos mostrarlo mejor. Vamos a utilizar _sprintf()_:

```
$imagenes = array(
    'imagen1' => array('class'=> 'style', 'alt'=>'imagen1', 'title'=> 'imagen1','src'=>'http://www.example.com/imagen1.jpg'),
    'imagen2' => array('class'=> 'style', 'alt'=>'imagen2', 'title'=> 'imagen2','src'=>'http://www.example.com/imagen2.jpg'),
    'imagen3' => array('class'=> 'style', 'alt'=>'imagen3', 'title'=> 'imagen3','src'=>'http://www.example.com/imagen3.jpg')
);
foreach($imagenes as $key => $value)
{
    $image = sprintf('<img class="%s" alt="%s" title="%s" src="%s" />',
        $value["class"],
        $value["alt"],
        $value["title"],
        $value["src"]);
    echo $image;
}
```