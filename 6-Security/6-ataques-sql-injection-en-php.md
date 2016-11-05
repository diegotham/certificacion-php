Un **ataque SQL Injection** se produce cuando el atacante intenta **inyectar código SQL malicioso** en la base de datos de la víctima, y **fuerza a la base de datos** a ejecutar esa sentencia. Las sentencias pueden hacer cosas como **destruir las tablas de la base datos** o **extraer información** privada como **contraseñas**.

**Indice de contenido**

1.  Como funciona un ataque SQL Injection
2.  Ejemplos de ataques SQL Injection
3.  Como prevenir ataques SQL Injection en PHP

### 1. Como funciona un ataque SQL Injection

Los **ataques SQL Injection** normalmente comienzan con el atacante introduciendo su código malicioso en un campo de **formulario específico en una aplicación**.

Pongamos el caso de un **formulario que envía por email la contraseña al usuario**: el usuario introduce su email en un campo y la aplicación hace una búsqueda en la base de datos en busca de esa dirección. Si la aplicación no encuentra la dirección de email, no envía un email con la nueva contraseña. Sin embargo, si la aplicación encuentra esa dirección de email en la base de datos, enviará un email a esa dirección de email con una nueva contraseña, o con la información necesaria para restaurarla.

En lugar de introducir una dirección de email válida, lo que haría el atacante sería **inyectar una sentencia SQL** que extraiga información o destruya o cambie algunos datos.

La sentencia SQL que tiene la aplicación sería algo así (el atacante la desconoce):

```
SELECT data FROM table WHERE email = '$email';
```

Antes de que el atacante pueda empezar a dañar la aplicación, debe averiguar como ésta maneja un _**input**_ incorrecto primero, por ejemplo añadiendo una **comilla simple '** al final del email a introducir:

```
ejemplo@ejemplo.com'
```

A partir de aquí pueden ocurrir **dos posibilidades**:

*   La aplicación primero **sanitiza el _input_** removiendo la comilla simple al final, porque considera que direcciones de email con comillas pueden ser potencialmente maliciosas (aunque según los estándares [IETF](https://en.wikipedia.org/wiki/Internet_Standard) las direcciones de email pueden contener comillas). Una vez sanitizado el email, procede a su búsqueda en la base de datos.
*   La aplicación **no sanitiza el _input_**, y directamente ejecuta la sentencia SQL con el email con la comilla. Esto es lo que el atacante espera que ocurra. La sentencia quedará:

```
SELECT data FROM table WHERE email = 'ejemplo@ejemplo.com'';
```

Lo que ocurre si se ejecuta así es que **abortará la sentencia con un error de sintaxis**. Dependiendo de cómo la aplicación maneje los errores, puede mostrar el error o no, pero generalmente aparecerá un mensaje de _Database error_ o _Internal Error_. Lo importante es que el atacante no recibe un mensaje diciéndole que la dirección no existe en el sistema, por lo que la aplicación no sanitiza el input y es posible manipular su base de datos.

### 2. Ejemplos de ataques SQL Injection

Una vez que el atacante sabe que la **base de datos es vulnerable**, puede realizar el ataque. Por ejemplo puede **editar un email de una cuenta de usuario**:

```
Y';
UPDATE table
    SET email = 'atacante@ejemplo.com'
    WHERE email = 'usuario@ejemplo.com';
```

Después de la Y hay una **comilla** y un **punto y coma**, lo que permite al atacante **cerrar la sentencia** y ejecutar otra. Lo anterior resultaría en el siguiente código:

```
SELECT data
    FROM table
        WHERE Emailinput = 'Y';
    UPDATE table
        SET email = 'atacante@ejemplo.com'
        WHERE email = 'usuario@ejemplo.com';
```

Para realizar el ataque el atacante ha tenido que saber primero el **nombre de la tabla y la estructura**.

Una de las **técnicas para conseguir más datos de los que devolvería una sentencia SQL** de la aplicación es empleando **1=1**, que es siempre **true**. La forma es la siguiente:

```
SELECT * FROM users WHERE id = 10 or 1=1
```

Esto devolvería toda la tabla de usuarios, ya que **WHERE 1=1** es true. 

### 3. Como prevenir ataques SQL Injection en PHP

*   **Usar prepared statements y parameterized queries**. Esto son sentencias SQL preparadas que se envían a la base de datos de forma separada a cualquier parámetro. De esta forma es imposible para un atacante **inyectar SQL malicioso**. Es la forma más recomendable y segura de evitar este tipo de ataques. Se puede hacer de dos formas:
    1\. Con **PDO**:
   
```
    $stmt = $pdo->prepare('SELECT * FROM usuarios WHERE nombre = :nombre');
    $stmt->execute(array('nombre' => $nombre));
    foreach ($stmt as $row) {
        // Hacer algo con $row
    }
   
```

2\. Con **MySQLi**:
   
```
    $stmt = $dbConnection->prepare('SELECT * FROM usuarios WHERE nombre = ?');
    $stmt->bind_param('s', $nombre);

    $stmt->execute();

    $result = $stmt->get_result();
    while ($row = $result->fetch_assoc()){
        // Hacer algo con $row
    }
   
```

Si se utiliza PostgreSQL existen opciones como _pg_prepare()_ y _pg_execute()_ si se quiere hacer mediante esta segunda opción. PDO es una opción universal que vale para todas las bases de datos.

Cuando se usa una conexión PDO a MySQL los **prepared statements de verdad** puede que no se utilicen por defecto. Para usarlos siempre, hay que **desactivar la emulación de prepared statements**. La conexión se ha de hacer así:

```
$db = new PDO('mysql:dbname=test;host=localhost;charset=utf8', 'usuario', 'contraseña');
$db->setAttribute(PDO::ATTR_EMULATE_PREPARES, false);
$db->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
```

El _**error mode**_ no es estrictamente necesario, pero es aconsejable ponerlo. De esta forma el script no parará con un **Fatal Error** cuando algo sale mal, y da la posibilidad al desarrollador de capturar (_**catch**_) cualquier error lanzado (_**throw**_) como **PDOExceptcion**.

Configurar **ATTR_EMULATE_PREPARES** a false puede ser importante ya que le dice a **PDO** que deshabilite los **prepared statements emulados** y sólo utilice los de verdad (por ejemplo en caso de que el driver no pueda preparar satisfactoriamente la sentencia).

De esta forma la sentencia y los valores no son analizados por PHP antes de ser enviados al **servidor de bases de datos**, sino que son analizados y compilados por éste, eliminando cualquier posibilidad de inyectar SQL malicioso. 
Los valores de los parámetros se combinan con una sentencia ya compilada, no son un string SQL. La inyección SQL funciona de forma que se inyecta el SQL malicioso en el string antes de ser enviado al servidor de bases de datos, por lo que enviando de forma separada la sentencia de los parámetros se reduce mucho el riesgo. 

*   **Escapar caracteres especiales**. La función [_mysqli_real_escape_string_](http://php.net/manual/en/mysqli.real-escape-string.php), o _mysqli::escape_string_ y _mysqli::real_escape_string_ en su versión OOP, coge el string que va a ser pasado a la sentencia y lo devuelve con los **posibles ataques SQL injection eliminados**. Ejemplo usando su versión OOP:
   
```
$mysqli = new mysqli("localhost", "usuario", "contraseña", "database");

$usuario = "' OR 1'";
$usuario = $mysqli->escape_string($usuario);

$query1 = "SELECT * FROM user WHERE name = '$usuario'";
echo "SQL injection escapado: <br />" . $query1 . "<br />";

$usuario2 = "'; DELETE FROM customers WHERE 1 or username = '";
$usuario2 = $mysqli->real_escape_string($usuario2);

$query2 = "SELECT * FROM user WHERE name = '$usuario2'";
echo "SQL injection escapado: <br/>" . $query2; 
```

El resultado lo devuelve con las **comillas simples ' escapadas**.

*   **Función addslashes()**. Esta es una solución que **ya no se utiliza** pues es bastante vulnerable. Lo que hace la función [_addslashes()_](http://php.net/manual/es/function.addslashes.php) es **escapar el string mediante barras**. Los strings escapados son ', ", \ y el byte **null**. Si por ejemplo insertas una barra \ en medio de un carácter múltiple en concreto, la barra pierde su valor ya que es parte de ese carácter múltiple, pudiéndose insertar una comilla después. [Aquí](http://shiflett.org/blog/2006/jan/addslashes-versus-mysql-real-escape-string) hay una explicación más detallada.

La mejor opción es usar _**prepared statements**_ y _**parameterized queries**_. De todas formas siempre es mejor combinar este tipo de precauciones con otras como [data validation](http://diego.com.es/ataques-xss-cross-site-scripting-en-php#DataValidation), indicando expresamente el tipo de variable que se espera (_integer_, por ejemplo).