**Funciones básicas para la escritura de archivos en PHP**

| | |
| -------- | -------- |
| 1\. fwrite | 3. fputcsv |
| 2. file_put_contents | |

### 1\. fwrite
```
int fwrite (resource $handle, string $string, [, int $length ])
```

Escribe el contenido de _$string_ en el recurso _$handle_. Si se facilita una longitud _$length_ se escribirá hasta ésta o hasta que acabe _$string_.

Devuelve el **número de bytes** escritos.

```
$fp = fopen("miarchivo.txt", "w");

fwrite($fp, "Hola que tal");
fwrite($fp, ", ¿Cómo estás? \n");

fclose($fp);
// Escribirá en miarchivo.txt : Hola que tal, ¿Cómo estás?
```

En el ejemplo anterior hemos escrito en el archivo de forma que hemos sobreescrito lo anterior (con el **modo w**). Si ahora queremos añadir nuevo contenido al archivo, podemos abrir un recurso con el **modo a de _append_**:

```
if ($fp = fopen("miarchivo.txt", "a")){
    fwrite($fp, "Yo muy bien, ¿Y tú?");
}
fclose($fp);
```

La función _fputs()_ es un alias de _fwrite()_.

### 2\. file_put_contents
```
int file_put_contents (string $filename, mixed $data [, int $flags = 0 [, resource $context ]] )
```

Escribe información _$data_ en un archivo _$filename_. Viene a ser equivalente a hacer _fopen()_, _fwrite()_ y _fclose()_ a la vez. Si el archivo _$filename_ no existe, se crea, a no ser que la bandera FILE_APPEND esté establecida. Las banderas pueden ser:

**FILE_USE_INCLUDE_PATH**. Busca _$filename_ en el directorio _include_path_.

**FILE_APPEND**. Si el archivo _$filename_ ya existe, añade la información en el mismo en lugar de sobreescribirlo.

**LOCK_EX**. Adquiere **acceso exclusivo** mientras se ejecuta la escritura. Es como llamar a [_flock()_](http://php.net/manual/es/function.flock.php) entre la llamada a _**fopen()**_ y _**fwrite()**_. 

La función devuelve el **número de bytes** escritos en el archivo _$filename_.

```
// Uso junto con file_get_contents y añadiendo al ya existente:
$archivo = "miarchivo.txt";
$actual = file_get_contents($archivo);
$actual .= "Esto añade más texto al archivo\n";
file_put_contents($archivo, $actual);
// Podemos utilizar también la bandera FILE_APPEND para añadir al final sin eliminar:
$archivo = "miarchivo.txt";
$mascontenido = "¡Todavía más contenido!\n";
// También vamos a emplear la bandera LOCK_EX para evitar cualquier modificación mientras:
file_put_contents($archivo, $mascontenido, FILE_APPEND | LOCK_EX);
```

### 3\. fputcsv
```
int fputcsv (resource_ $handle, array $fields [, string $delimeter = "," [, string $enclosure = '"' [ , string $escape_char = "\" ]]] )
```

Escribe en **formato csv** en un archivo abierto _$handle_ desde un array _$fields_.

Se puede especificar, opcionalmente, el delimitador de campos _$delimeter_, un carácter circundante de cada campo _$enclosure_ y un parámetro de escape _$escape_char_.

Devuelve la **longitud del string escrito** en cado de éxito o **false** en caso de error. 

```
$animales = array (
    array ('tortuga', 'Nicole', '5'),
    array ('perro', 'Chukie', '10'),
    array ('gato', 'Whiskie', 8)
);
$fp = fopen('archivo.csv', 'w');
foreach ($animales as $row){
    fputcsv($fp, $row);
}
fclose($fp);
```