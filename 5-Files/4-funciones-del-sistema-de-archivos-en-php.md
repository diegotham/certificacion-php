Las funciones del sistema de archivos que existen en la extensión filesystem de PHP se han agrupado en cuatro grupos básicos: directorios, modificación, información, comprobación y enlaces. No se incluyen funciones de las secciones anteriores para el manejo de archivos.

**Indice de contenido**

1.  Funciones de directorios
2.  Funciones de modificación de archivos
3.  Funciones de información de archivos
4.  Funciones de comprobación de archivos
5.  Funciones de enlaces

### 1\. Funciones de directorios

*  basename
```
string basename (string $path [, string $suffix ])
```

Devuelve el último componente de una ruta o directorio. El parámetro _$suffix_ permite excluir parte del componente a devolver.

```
$directorio = "este/es/un/directorio/cualquiera.csv";
echo basename($directorio, '.csv'); // Devuelve cualquiera
```

*  dirname
```
_string_ **dirname (**_string_ **$path****)**
```

Devuelve la ruta de un directorio padre.

```
$directorio = "este/es/un/directorio/cualquiera.csv";
echo dirname($directorio); // este/es/un/directorio
```

*  pathinfo
```
mixed pathinfo (string $path [, int $options = PATHINFO_DIRNAME | PATHINFO_BASENAME | PATHINFO_EXTENSION | PATHINFO_FILENAME ])
```

Devuelve **información sobre la ruta de un archivo**. Devuelve _string_ si sólo se indica una opción de _$options_, o un _array_ si se solicitan todas.

```
$directorio = "este/es/un/directorio/cualquiera.csv";
var_dump(pathinfo($directorio));
/*
array (size=4)
  'dirname' => string 'este/es/un/directorio' (length=21)
  'basename' => string 'cualquiera.csv' (length=14)
  'extension' => string 'csv' (length=3)
  'filename' => string 'cualquiera' (length=10)
*/
var_dump(pathinfo($directorio, PATHINFO_DIRNAME));
/*
string 'este/es/un/directorio' (length=21)
*/
```

*  realpath
```
_string_ **realpath (**_string_ **$path)**
```

Devuelve el **nombre de la ruta absoluta canonizada**. Expande los enlaces simbólicos y remueve los caracteres '/./', '/../' y '/' extra. No funciona en rutas que no existen.

```
$directorio = '../slimproject/Slim/Helper/';
echo realpath($directorio); // /Users/usuario/Sites/slimproject/Slim/Helper
```

*  mkdir
```
bool mkdir (string $pathname [, int $mode = 0777 [, bool $recursive = false [, resource $context ]]] )
```

Crea el directorio especificado por _$pathname_. _$mode_ indica los permisos de acceso que tendrá el directorio. _$recursive_ permite la creación de directorios anidados, especificados desde _$pathname_.

```
$directorio = 'NuevaCarpeta/Otra/Yotra';
mkdir($directorio, 0777, true); // Ha creado desde el directorio actual 3 carpetas recursivas
```

*  rmdir
```
bool rmdir (string $dirname [, resource $context ])
```

Elimina el directorio _$dirname_. El directorio tiene que estar vacío, y los permisos relevantes deben permitirlo.  

```
$directorio = 'MiCarpeta';
rmdir($directorio); // Eliminada MiCarpeta
```

### 2. Funciones de modificación de archivos

*  copy
```
bool copy (strong $source, string $dest [, resource $context ])
```

Copia el archivo _$source_ al destino _$dest_. Ambas son rutas, por lo que deben incluir el directorio (si es distinto del actual) y el nombre del archivo. Para mover un fichero se usa _rename()_.

```
$destino = "MiCarpeta/micopiadearchivo.txt";
copy("miarchivo.txt", $destino);
```

*  unlink
```
bool unlink (string $filename [, resource $context ]) 
```

Elimina el archivo _$filename_. 

```
unlink("MiCarpeta/micopiadearchivo.txt");

```

*  rename
```
bool rename (string $oldname, string $newname [, resource $context ])
```

Renombra _$oldname_ a _$newname_, moviéndolo a otro directorio si es necesario. Si _$newname_ ya existe, lo sobreescribirá.

```
$archivo = "miarchivo.txt";
rename ($archivo, "esmiarchivo.txt");
```

*  move_uploaded_file
```
bool move_uploaded_file (string $filename, string $destination)
```

Mueve un archivo subido _$filename_ a una ubicación _$destination_. La función se asegura de que **el archivo subido es válido** (es válido si se ha subido mediante un **mecanismo de subida HTTP POST de PHP**), si es así, lo mueve al nombre de archivo dado por _$destination_.

```
if(isset($_POST['submit'])){
    $file = $_FILES['file_upload'];
    $directorio_temporal = $file['tmp_name'];
    $nombre_archivo = basename($file['name']);
    $directorio_objetivo = "/uploads/". $nombre_archivo;

    if(move_uploaded_file($directorio_temporal, $directorio_objetivo)) {
        echo "Archivo subido correctamente";
    }
}
```

*  rewind
```
bool rewind (resource $handle)
```

Establece el puntero de un archivo del recurso _$handle_ al principio. Si el archivo se ha abierto en **modo adición** como **a** o **a+**, cualquier información que se escriba en el archivo irá siempre al final, no importa la posición del puntero.

```
// Contenido de archivo.txt: Hola, ¿Qué tal?
$gestor = fopen('archivo.txt', 'r+');
// Añadimos Hey! , al principio
fwrite($gestor, "Wey!, ");
// Volvemos al inicio
rewind($gestor);
// Añadimos Hey!, al principio
fwrite($gestor, "Hey!, ");
// Si no hacemos este rewind de nuevo, al llamar a fgets, comenzará
// a leer desde el final del anterior fwrite()
rewind($gestor);

echo fgets($gestor); // Devuelve Hey!, ¿Qué tal?

fclose($gestor);
```

*  flock
```
bool flock (resource $handle, int $operation [, int &$wouldblock ])
```

Permite **bloquear un archivo** del resource _$handle_. Bloquear un archivo evita que pueda ser accedido a la vez desde diferentes scripts. El **bloqueo** sólo actúa en el **proceso PHP actual**, otros procesos pueden modificar o eliminar un archivo bloqueado si se tienen los permisos necesarios.

El parámetro _$operation_ permite especificar el tipo de bloqueo:

**LOCK_SH**. Bloqueo compartido (lectura).

**LOCK_EX**. Bloqueo exclusivo (escritura).

**LOCK_UN**. Libera un bloqueo, compartido o exclusivo. 

También se puede añadir LOCK_NB a una de las operaciones anteriores si no se quiere que flock() espere hasta que obtiene el bloqueo.

Si parámetro opcional _$wouldblock_ es 1 el bloqueo hará que otros procesos de la función queden en espera.

```
$archivo = "archivo.txt";
$fp = fopen($archivo, 'r+');

if(flock($fp, LOCK_EX)){ // Inicia el bloqueo exclusivo
    ftruncate($fp, 0);
    fwrite($fp, "Texto nuevo del archivo\n");
    fflush($fp); // Vuelca la salida antes de liberar el bloqueo
    flock($fp, LOCK_UN);// Libera el bloqueo
} else {
    echo "No se pudo bloquear";
}
fclose($fp);
```

*  ftruncate
```
bool ftruncate (resource $handle, int $size)
```

Trunca un archivo en _$handle_ a la longitud _$size_. 

```
// Contenido de archivo.txt: Hola que tal
$archivo = "archivo.txt";
$fp = fopen($archivo, 'r+');
ftruncate($fp, rand(1, filesize($archivo)));
echo fread($fp, filesize($archivo)); // Contenido final: Hola que
fclose($fp);

```

*  tempnam
```
_string_ **tempnam (**_string_ **$dir,** _string_ **$prefix)**
```

Crea un archivo con un nombre de archivo único, cuyo **permiso de acceso se establece a 0600** en el directorio _$dir_. Si el directorio no existe o no es escribible, _tempnam()_ puede generar un archivo en el directorio temporal del sistema, y devolver la ruta completa del archivo, incluyendo su nombre. _$prefix_ establece el prefijo del nombre del archivo.

```
$archivo = tempnam("MiCarpeta", "hola");

$fp = fopen($archivo, "w");
fwrite($fp, "Texto que irá al archivo nuevo temporal");
fclose($fp);

// Realizar alguna acción

// Borrar el archivo:
unlink($archivo);
```

*  tmpfile
```
resource tmpfile (void)
```

Crea un **archivo temporal de nombre único** en modo lectura-escritura (w+) y **devuelve un gestor de archivo**. El archivo se elimina cuando se cierra (con _**fclose()**_, cuando no quedan referencias al gestor de ficheros devuelto o cuando el script finaliza).

```
$temporal = tmpfile();
fwrite($temporal, "Texto en el archivo temporal");
fseek($temporal, 0); // También podría usarse rewind()
echo fread($temporal, 100);
fclose($temporal);
```

*  touch
```
bool touch (string $filename [, int $time = time() [, int $atime ]] )
```

Establece momentos de acceso y modificación del archivo _$filename_. Si el archivo no existe, se crea. _$time_ es el momento de modificación, si no se proporciona, se emplea la hora actual del sistema. _$atime_ es el momento de acceso, si no existe, se toma el valor de _$time_. Si ninguno de los dos existen, se toma el valor actual del sistema.

```
$momento = time() - 1800;

if(touch('archivo.txt', $momento)){
    echo "Archivo afectado correctamente";
} else {
    echo "Error";
}
```

*  chown
```
bool chown (string $filename, mixed $user)
```

Cambia el propietario de un archivo a _$user_. Sólo puede hacerlo un **superusuario**.

```
$archivo = "archivo.txt"; // Ruta al archivo
$usuario = "root";
// Establecemos usuario
chown($archivo, $usuario);

$stats = stat($archivo);
echo $stats[4];
```

*  chmod
```
bool chmod (string $filename, mixed $mode)
```

Cambia el modo de un archivo a _$mode_.

```
// Es necesario prefijar el modo con un 0
chmod("MiCarpeta/archivo.txt", 755); // Mal
chmod("MiCarpeta/archivo.txt", "u+rwx, go+rx"); // Mal
chmod("MiCarpeta/archivo.txt", 0755); // CORRECTO
```

*  chgrp
```
bool chgrp (string $filename, mixed $group)
```

Cambia el grupo de un archivo a _$group_. Sólo el superusuario puede cambiarlo a cualquier grupo, los demás usuarios lo pueden cambiar por grupos del que ellos sean miembro.

```
chmod("MiCarpeta/archivo.txt", 8); // Cambiado de grupo
```

### 3\. Funciones de información de archivos

*  fileatime
```
int fileatime (string $filename)
```

Obtiene el momento del **último acceso a un archivo**. 

```
$archivo = "archivo.txt";

if(file_exists($archivo)){
    echo "última modificación a $archivo: ". date("D d Y H:i:s", fileatime($archivo));
} // Devuelve: última modificación a archivo.txt: Thu 08 2015 11:12:26
```

*  filectime
```
int filectime (string $filename)
```

Obtiene el **momento del último cambio del i-nodo de un archivo**. 

```
$archivo = "archivo.txt";

if(file_exists($archivo)){
    echo "último acceso a $archivo: ". date("D d Y H:i:s", filectime($archivo));
} // Devuelve: último acceso a archivo.txt: Thu 08 2015 11:12:24
```

*  filemtime
```
int filemtime (string $filename)
```

Obtiene el **momento de la última modificación de un archivo**. 

```
$archivo = "archivo.txt";

if(file_exists($archivo)){
    echo "última modificación a $archivo: ". date("D d Y H:i:s", filemtime($archivo));
} // Devuelve: última modificación a archivo.txt: Thu 08 2015 11:42:24
```

*  fileowner
```
int fileowner (string $filename)
```

Obtiene el **propietario** de un archivo.

```
$archivo = "archivo.txt";
echo fileowner($archivo);
```

*  fileperms
```
int fileperms (string $filename)
```

Obtiene los **permisos de un archivo**.

```
$archivo = "archivo.txt";
/*
 Si sólo se usa fileperms(), devuelve un número que incluye los permisos
e información del archivo dado. Para devolver exactamente los permisos en formato
octal como en chmod(), se utiliza la siguiente expresión:
 */
echo substr(sprintf('%o', fileperms($archivo)), -4);
```

*  filegroup
```
int filegroup (string $filename)
```

Obtiene el **grupo de un archivo**.

```
$archivo = "archivo.txt";
echo filegroup($archivo);
```

*  filesize
```
int filesize (string $filename)
```

Obtiene el **tamaño** de un archivo. Devuelve el tamaño en **bytes**. 

```
$archivo = "archivo.txt";
echo filesize($archivo);
```

*  filetype
```
_string_ **filetype (**_string_ **$filename)**
```

Obtiene el **tipo de archivo**. Los valores que puede devolver son: fifo, char, dir, block, link, file, socket y unknown.

```
$archivo = "archivo.txt";
echo filetype($archivo); // file
```

### 4\. Funciones de comprobación de archivos

*  file_exists
```
bool file_exists (string $filename)
```

Comprueba si existe el **archivo** o **directorio** _$filename_.

```
$archivo = "archivo.txt";

if(file_exists($archivo)){
    echo "El archivo $archivo existe";
} else {
    echo "El archivo $archivo no existe";
}
```

*  is_file
```
bool is_file (string $filename)
```

Indica si el archivo _$filename_ es del tipo _**file**_ (un archivo normal).

```
$archivo = "archivo.txt";

if(is_file($archivo)){
    echo "El archivo $archivo existe y es del tipo file";
} else {
    echo "El archivo $archivo no existe o no es del tipo file";
}
```

*  is_dir
```
bool is_dir (string $filename)
```

Indica si _$filename_ es un directorio.

```
$directorio = "MiCarpeta";

if(is_dir($directorio)){
    echo "El directorio $directorio existe";
} else {
    echo "El directorio $directorio no existe";
}
```

*  is_link
```
bool is_link (string $filename)
```

Indica si _$filename_ es un enlace simbólico. 

```
$enlace = "archy";

if(is_link($enlace)){
    echo (readlink($enlace));
} else {
    symlink('archivo.txt', $enlace);
}
```

*  is_executable
```
bool is_executable (string $filename)
```

Indica si el archivo _$filename_ es ejecutable. 

```
$archivo = "miarchivo.sh";

if(is_executable($archivo)){
    echo "$archivo es ejecutable";
} else {
    echo "$archivo no es ejecutable";
}
```

*  is_readable
```
bool is_readable (string $filename)
```

Indica si el archivo _$filename_ existe y es legible. 

```
$archivo = "archivo.txt";

if(is_readable($archivo)){
    echo "$archivo es legible";
} else {
    echo "$archivo no es legible";
}
```

*  is_writable
```
bool is_writable (string $filename)
```

Devuelve **true** si _$filename_ existe y es escribible. Puede ser también un directorio. 

```
$archivo = "archivo.txt";

if(is_writable($archivo)){
    echo "$archivo es escribible";
} else {
    echo "$archivo no es escribible";
}
```

*  is_uploaded_file
```
bool is_uploaded_file (string $filename)
```

Devuelve **true** si el archivo _$filename_ fue subido mediante HTTP POST. Es útil en términos de seguridad para asegurarse de que el archivo con el que se trabaja es el subido con ese método. Para que funcione correctamente necesita el argumento con el nombre que se le ha puesto en el servidor: _**$_FILES['archivo']['tmp_name']**_, no funciona con el nombre del usuario: _**$_FILES['archivo']['name']**_.

```
$file = $_FILES['archivo']['tmp_name'];

if(is_uploaded_file($file)){
    echo "El archivo se ha subido mediante HTTP POST";
} else {
    echo "El archivo no se ha subido mediante HTTP POST";
}
```

### 5\. Funciones de enlaces

*  symlink
```
bool symlink (string $target, string $link)
```

Crea un **enlace simbólico** a _$target_ con el nombre _$link_. 

```
$archivo = "archivo.txt";
$link = "archy";
symlink($archivo, $link);

echo readlink($link); // archivo.txt
```

*  readlink
```
_string_ **readlink (**_string_ **$path)**
```

Devuelve el objetivo de un **enlace simbólico**. Se puede ver en el ejemplo anterior.

*  link
```
bool link ( string $target, string $link)
```

Crea un **enlace hard** a _$target_. 

```
$archivo = "archivo.txt";
$link = "arc";
var_dump(link($archivo, $link)); // boolean true
```

*  linkinfo
```
int linkinfo (string $path)
```

Obtiene información acerca de un **enlace**. Sirve para verificar si un enlace **existe** realmente.

```
$archivo = "archivo.txt";
$link = "archy";
echo linkinfo($link);//Devuelve el campo st_dev de C en Unix:
// 16777218
```

*  lstat
```
array lstat (string $filename)
```

Proporciona información acerca de un **archivo** o **enlace simbólico**. Función idéntica a _stat()_, excepto que si el parámetro _$filename_ es un enlace simbólico, devuelve el **estado** del mismo **enlace simbólico**. En cambio, _stat()_ devolverá el estado del archivo aunque se le proporcione el enlace simbólico.

```
$archivo = "archivo.txt";
$link = "archy";
var_dump(lstat($link));
```

*  lchown
```
bool lchown (string $filename, mixed $user)
```

Cambia el **propietario** de un **enlace simbólico**. 

```
$archivo = "archivo.txt";
$link = "archy";
symlink($archivo, $link);

lchown($link, 8);
```

*  lchgrp
```
bool lchgrp (string $filename, mixed $group)
```

Cambia el **grupo** de un **enlace simbólico**. Sólo el **superusuario** puede cambiarlo a cualquier grupo, los demás usuarios lo pueden cambiar por grupos del que ellos sean miembro.

```
$archivo = "archivo.txt";
$link = "archy";
symlink($archivo, $link);

lchgrp($link, 8);
```