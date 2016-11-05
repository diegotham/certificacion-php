**Indice de contenido**

| | |
| -------- | -------- |
| 1\. fgets | 7. fgetc |
| 2\. fread | 8. file_get_contents |
| 3\. fscanf | 9. readfile |
| 4\. fgetss | 10. file |
| 5. fpassthru | 11. parse_ini_file |
| 6. fgetcsv | 12. Lectura desde una posición exacta |

### 1. fgets
```
string fgets (resource $handle [, int $length ])
```

Obtiene una línea desde el **puntero de un archivo**. La **lectura** termina cuando se llegue a _$length_, se llegue a una nueva línea o se alcance el final del archivo.

```
$fp = fopen("miarchivo.txt", "r");
while (!feof($fp)){
    $linea = fgets($fp);
    echo $linea;
}
fclose($fp);
```

### 2. fread
```
string fread (resource $handle, int $length)
```

Lectura de un fichero en **modo binario**. Se puede incluir un parámetro opcional _$length_ para limitar lo que devuelva.

Esta función tiene una función diferente a _fgets()_, ya que lee **archivos binarios** (archivos que no están creados para ser legibles por humanos). Ya que las líneas no son algo muy común en este tipo de archivos, se suele especificar el tamaño _$length_ en bytes a devolver:

```
$fp = fopen ("miarchivo.txt", "rb");
$datos = fread($fp, 4096);
```

Es frecuente emplear la función _filesize()_ en lugar de un número concreto para especificar que se quiere devolver el archivo entero:

```
$fp = fopen("miarchivo.txt", "rb");
$datos = fread($fp, filesize("miarchivo.txt"));
fclose($fp);
```

### 3. fscanf
```
mixed fscanf (resource $handle, string $format [, mixed &$... ])
```

Interpreta la entrada de un archivo _$handle_ en función de un formato _$format_. El formato es el mismo que se emplea en la función _sprintf()_. Cada llamada a _fscanf()_ lee una línea del archivo.

```
$fp = fopen("miarchivo.txt", "r");
while ($animalinfo = fscanf($fp, "%s\t%s")){
    list($animal, $nombre) = $animalinfo;
    echo "El " . $animalinfo[0] . " se llama {$animalinfo[1]} <br>";
}
fclose($fp);
```

### 4. fgetss
```
string fgetss (resource $handle [, int $length [, string $allowable_tags ]] )
```

Es igual que _fgets()_ pero elimina cualquier **null**, **etiquetas HTML y PHP** del texto. Se puede especificar un parámetro opcional _$allowable_tags_ para especificar etiquetas que se permiten.

```
$cadena = <<<HEY
<html>
<h1>Perfil de <?php echo "Nombre de usuario";?></h1>
</html>
HEY;

$file = "miarchivo.txt";
file_put_contents($file, $cadena);

$gestor = fopen($file, "r");

while (!feof($gestor)){
    $buffer = fgetss($gestor, 4096);
    echo $buffer;
}
fclose($gestor);
// Devuelve: Perfil de
```

### 5. fpassthru
```
int fpassthru (resource $handle)
```

Lee toda la información restante desde un puntero hasta el final de un archivo (EOF) y escribe el resultado **en el buffer de salida**.

Se puede utilizar _rewind()_ primero para **reiniciar el puntero al principio del archivo** si ya se ha escrito en él. 

Si simplemente se quiere **verter los contenidos en el búfer de salida sin modificarlo antes** o **buscar un índice concreto**, es mejor usar _readfile()_ y así se evita llamar a _fopen()_.

Devuelve el **número de caracteres** leídos desde _$handle_ y pasados hacia la salida.

Esta función no hace falta guardarla en una variable ya que imprime directamente el resultado:

```
$fp = fopen("miarchivo.txt","r");
// Lee la primera línea, que es Esta es la línea 1
fgets($fp);
// Envía el resto del archívo al output buffer
echo fpassthru($fp);
/* Devuelve el resto de líneas:
Esta es la línea 2
Esta es la línea 3
Esta es la línea 4
Y el número de caracteres: 59, porque hemos hecho echo
*/
fclose($fp);
```

Otro ejemplo **devolviendo el index de una página web**:

```
$fp = fopen("http://www.ejemplo.com", "r");
fpassthru($fp);
```

### 6. fgetcsv
```
array fgetcsv (resource $handle [, int $length = 0 [, string $delimeter = "," [, string $enclosure = '"' [, string $escape = "\" ]]]] )
```

Busca **campos en formato csv** en el recurso _$handle_ y devuelve un _array_ conteniéndolos. Se pueden especificar, opcionalmente, la longitud de línea a devolver _$length_, el delimitador de campo _$delimeter_, el carácter circundante de cada campo _$enclosure_, y el carácter de escape _$escape_.

```
$fila = 1;
if(($fp = fopen("archivo.csv", "r")) !== FALSE) {
    while (($datos = fgetcsv($fp)) !== FALSE) {
        $numero = count($datos);
        echo "Animal de la fila $fila:\n";
        $fila++;
        for ($i = 0; $i < $numero; $i++){
            echo $datos[$i] . "\t";
        }
        echo "\nFrase: el tipo de animal es {$datos[0]} y se llama {$datos[1]} <br>";
    }
    fclose($fp);
}
```

### 7. fgetc
```
stirng fgetc (resource $handle)
```

Obtiene **sólo un carácter** desde un puntero de un archivo. Devuelve el _**string**_ con el único carácter. Devuelve false si es **EOF**.

```
if($fp = fopen('miarchivo.txt', 'r')) {
    while (false !== ($caracter = fgetc($fp))) {
        echo "$caracter\n";
    }
}
```

### 8\. file_get_contents
```
string file_get_contents (string $filename [, bool $use_include_path = false [, resource $context [, int $offset = -1 [, int $maxlen ]]]] )
```

Devuelve un archivo entero a una cadena, se puede especificar el comienzo desde _$offset_ hasta _$maxlen_. 

Si falla devuelve false, pero genera un E_WARNING si _$filename_ no se pudo encontrar, _$maxlen_ es menor que cero o si falla la búsqueda del _$offset_ especificado en el flujo.

```
// Devuelve el contenido de una web
$web = file_get_contents("http://www.ejemplo.com");
echo $web;
// El código anterior es igual que el siguiente:
$fh = fopen("http://www.ejemplo.com", "r");
fpassthru($fh);
// Devuelve contenido de un archivo en el directorio actual
$contenido = file_get_contents("miarchivo.txt");
echo $contenido;
// Busca dentro de include_path
$archivo = file_get_contents('./miarchivo.txt', FILE_USE_INCLUDE_PATH);
// Devuelve 20 caracteres desde el carácter 100
$articulo = file_get_contents('archivo.txt', NULL, NULL, 100, 20);
echo $articulo;
```

### 9\. readfile
```
int readfile (string $filename [, bool $use_include_path = false [, resource $context ]])
```

Es una versión más simplificada de _file_get_contents()_. Lee un archivo y lo escribe en el **búfer de salida**. La función devuelve el número de **bytes leídos del archivo**.

```
// Devuelve el contenido de un archivo
readfile("miarchivo.txt");
// Devuelve el contenido y el número de bytes, 79
$contenido = readfile("miarchivo.txt");
echo $contenido;
```

### 10\. file
```
array file (string $filename [, int $flags = 0 [, resource $context ]] )
```

Transfiere **un archivo a un array**. Función similar a _file_get_contents()_ pero devuelve los contenidos en un array, donde cada elemento es una línea del archivo. El parámetro opcional flags puede ser una de las siguientes constantes:

**FILE_USE_INCLUDE_PATH**. Busca el fichero en include_path.

**FILE_IGNORE_NEW_LINES**. No se añade una nueva línea al final de cada elemento del array.

**FILE_SKIP_EMPTY_LINES**. Salta las líneas vacías.

```
// Devuelve el contenido en un array:
$array = file("miarchivo.txt", FILE_IGNORE_NEW_LINES);
var_dump($array);
/*
 array (size=4)
  0 => string 'Esta es la línea 1' (length=19)
  1 => string 'Esta es la línea 2' (length=19)
  2 => string 'Esta es la línea 3' (length=19)
  3 => string 'Esta es la línea 4' (length=19)
*/
```

### 11\. parse_ini_file
```
array parse_ini_file ( string $filename [, bool $process_sections = false [, int $scanner_mode = INI_SCANNER_NORMAL ]] )
```

Analiza el archivo de configuración _$filename_ y devuelve sus configuraciones en un array asociativo. La estructura del fichero es la misma que la del archivo de configuración php.ini. Si _$process_sections_ es true, se obtiene un array multidimensional.

Hay dos _$scanner_mode_ : INI_SCANNER NORMAL (por defecto), y INI_SCANNER_RAW. Con este segundo los valores de las opciones no se analizan.

La función no guarda ninguna relación con el archivo _php.ini_, se suele usar para **leer los archivos de configuración de aplicaciones propias**. 

```
// Tenemos en el archivo miarchivo.txt:
/*
[nombres_animales]
perro = Peter
gato = Louis
conejo = Thug
 */
$array = parse_ini_file("miarchivo.txt");
var_dump($array);
/*
array (size=3)
  'perro' => string 'Peter' (length=5)
  'gato' => string 'Louis' (length=5)
  'conejo' => string 'Thug' (length=4)
 */
$array = parse_ini_file("miarchivo.txt", INI_SCANNER_RAW);
var_dump($array);
/*
array (size=1)
  'nombres_animales' =>
    array (size=3)
      'perro' => string 'Peter' (length=5)
      'gato' => string 'Louis' (length=5)
      'conejo' => string 'Thug' (length=4)
*/
```

### 12. Lectura desde una posición exacta

Podemos llegar exactamente a un punto del archivo con _**fseek()**_. Una vez situados en una posición, podemos emplear cualquiera de las funciones anteriores como _fgets()_ o _fscanf()_ para leer los datos:

```
$fp = fopen("miarchivo.txt", "r");
fseek($fp, 28);

while(!feof($fp)){
    $linea = fgets($fp);
    echo $linea;
}
fclose($fp);
/*
la línea 2
Esta es la línea 3
Esta es la línea 4
*/
```