Una **Sentencia Preparada**, o **Prepared Statement**, es una característica de algunos **sistemas de bases de datos** que permite ejecutar la misma (o similar) **sentencia SQL** provocando una mejora en la seguridad y en el rendimiento de la aplicación.

**Indice de contenido**

1.  Cómo funcionan las Sentencias Preparadas
2.  Ventajas de las Sentencias Preparadas
3.  Sentencias Preparadas en MySQLi
4.  Sentencias Preparadas en PDO

### 1. Cómo funcionan las sentencias preparadas

*   **Prepare**. Primero una **plantilla de la sentencia SQL** se crea y se envía a la base de datos. Algunos valores se dejan sin especificar, llamados **parámetros** y representados por un **interrogante** "**?**":

```
INSERT INTO Clientes VALUES (?, ?, ?, ?);
```

*   Después, la base de datos analiza, compila y realiza la optimización de la consulta sobre la sentencia SQL, y **guarda el resultado sin ejecutarlo**.
*   **Execute**. Por último, **la aplicación enlaza valores con los parámetros**, y la base de datos ejecuta la sentencia. La aplicación puede entonces ejecutar la sentencia tantas veces como quiera con valores diferentes.

### 2. Ventajas de las Sentencias Preparadas

*   Las **Sentencias Preparadas** reducen en **tiempo de análisis** ya que la preparación de la consulta de realiza sólo una vez (aunque la sentencia se ejecute las veces necesarias).
*   Los **parámetros** enlazados minimizan el ancho de banda consumida del servidor ya que sólo necesitas enviar los parámetros cada vez, no la consulta entera.
*   Las **Sentencias Preparadas** son muy últiles frente a [Inyecciones SQL](http://diego.com.es/ataques-sql-injection-en-php), ya que los valores de los parámetros, que son transmitidos después usando un protocolo diferente, no necesitan ser escapados. Si la plantilla de la sentencia original no es derivada de un **input externo**, los **ataques SQL Injection** no pueden ocurrir.

### 3. Sentencias Preparadas en MySQLi

Vamos a ver un **ejemplo de Sentencias Preparadas en PHP con MySQLi**.

```
$server = "localhost";
$user = "usuario";
$password = "password";
$dbname = "ejemplo";
// Conectar
$db = new mysqli($server, $user, $password, $dbname);
// Comprobar conexión
if($db->connect_error){
    die("La conexión ha fallado, error número " . $db->connect_errno . ": " . $db->connect_error);
}

```

Ya tenemos la conexión a la base de datos, ahora podemos utilizarla en los archivos donde tengamos que hacer sentencias.

```
// Preparar
$stmt = $db->prepare("INSERT INTO Clientes (nombre, ciudad, contacto) VALUES (?, ?, ?)");
$stmt->bind_param('ssi', $nombre, $ciudad, $contacto);
// Establecer parámetros y ejecutar
$nombre = "Donald Trump";
$ciudad = "Madrid";
$contacto = 4124124;
$stmt->execute();
$nombre = "Hillary Clinton";
$ciudad = "Barcelona";
$contacto = 4665767;
$stmt->execute();
// Mensaje de éxito en la inserción
echo "Se han creado las entradas exitosamente";
// Cerrar conexiones
$stmt->close();
$db->close();
```

Incluimos un interrogante donde queremos sustituir un _**integer**_, un _**string**_, un _**double**_ o un _**blob**_ en la función _[prepare()](http://php.net/manual/es/mysqli.prepare.php)_. Después usamos la función _[bind_param()](http://php.net/manual/es/mysqli-stmt.bind-param.php)_:

```
$stmt->bind_param('ssi', $nombre, $ciudad, $contacto);
```

La función enlaza los **parámentros** con la **consulta SQL** y le dice a la **base de datos** que parámetros son. El argumento "ssi" especifica el **tipo de dato** que se espera que sea el parámetro. Pueden ser de cuatro tipos:

| | |
| -------- | -------- |
| **Letra** | **Tipo de dato** |
| i | Integer |
| d | double |
| s | string |
| b | blob |

Se debe especificar uno por cada parámetro.

La función _[mysqli_stmt::bind_result()](http://php.net/manual/es/mysqli-stmt.bind-result.php)_ permite enlazar las columnas con variables para su uso posterior. No hace falta especificar el tipo de dato.

```
// Iniciamos sentencia preparada
if ($stmt = $mysqli->prepare("SELECT nombre, ciudad FROM Clientes")) {
    $stmt->execute();
    // Vinculamos variables a columnas
    $stmt->bind_result($nombre, $ciudad);
    // Obtenemos los valores
    while ($stmt->fetch()) {
        printf("%s %s\n", $nombre, $ciudad);
    }
    // Cerramos la sentencia preparada
    $stmt->close();
}
```

### 4. Sentencias Preparadas en PDO

Vamos a hacer el mismo ejemplo anterior pero con **PDO**.

```
$server = "localhost";
$user = "usuario";
$password = "password";
$dbname = "ejemplo";
try {
// Conectar
$db = new PDO("mysql:host=$server;dbname=$dbname", $user, $password);
// Establecer el nivel de errores a EXCEPTION
$db->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
} catch (PDOException $e){
    echo "Error: " . $e->getMessage();
}
```

Una vez establecida la conexión, podemos usarla donde la necesitemos:

```
// Preparar
$stmt = $db->prepare("INSERT INTO Clientes (nombre, ciudad, contacto) VALUES (:nombre, :ciudad, :contacto)");
$stmt->bindParam(':nombre', $nombre);
$stmt->bindParam(':ciudad', $ciudad);
$stmt->bindParam(':contacto', $contacto);
// Establecer parámetros y ejecutar
$nombre = "Donald Trump";
$ciudad = "Madrid";
$contacto = 4124124;
$stmt->execute();
$nombre = "Hillary Clinton";
$ciudad = "Barcelona";
$contacto = 4665767;
$stmt->execute();
// Mensaje de éxito en la inserción
echo "Se han creado las entradas exitosamente";
// Cerrar conexiones
$db = null;
```

Los parámetros también pueden enlazarse sin utilizar _bindParam()_, directamente con un array en _execute()_:

```
// Preparar
$stmt = $db->prepare("INSERT INTO Clientes (nombre, ciudad, contacto) VALUES (:nombre, :ciudad, :contacto)");
// Establecer parámetros y ejecutar
$nombre = "Donald Trump";
$ciudad = "Madrid";
$contacto = 4124124;
$stmt->execute(array(':nombre' => $nombre, ':ciudad' => $ciudad, ':contacto' => $contacto));
// Mensaje de éxito en la inserción
echo "Se han creado las entradas exitosamente";
// Cerrar conexiones
$db = null;
```

Hemos asignado los parámetros con nombre de variable, pero también se puede hacer con números:

```
$stmt = $db->prepare("INSERT INTO Clientes (nombre, ciudad, contacto) VALUES (?, ?, ?)");
$stmt->bindParam(1, $nombre);
$stmt->bindParam(2, $ciudad);
$stmt->bindParam(3, $contacto);
```

En caso de pasar los parámetros a través de un array con _execute()_, se haría de la misma forma que antes:

```
// Preparar
$stmt = $db->prepare("INSERT INTO Clientes (nombre, ciudad, contacto) VALUES (?, ?, ?)");
// Establecer parámetros y ejecutar
$nombre = "Donald Trump";
$ciudad = "Madrid";
$contacto = 4124124;
$stmt->execute(array($nombre, $ciudad, $contacto));
// Mensaje de éxito en la inserción
echo "Se han creado las entradas exitosamente";
// Cerrar conexiones
$db = null;
```

Sólo hemos empleado las sentencias preparadas para insertar datos, pero también se emplean para consultas y con otros métodos de enlazado como [bindValue()](http://php.net/manual/es/pdostatement.bindvalue.php) o [bindColumn()](http://php.net/manual/es/pdostatement.bindcolumn.php).