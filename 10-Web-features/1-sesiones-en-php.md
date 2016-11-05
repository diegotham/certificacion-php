**$_SESSION** es un _**array**_ especial utilizado para guardar información a través de los _requests_ que un usuario hace durante su visita a un sitio web o aplicación. La información guardada en una sesión puede llamarse en cualquier momento mientras la sesión esté abierta.

A cada usuario que accede a la aplicación e inicia sesión se le asigna un **_session ID_ único**, y es lo que le permite identificar la sesión y que esté disponible para ese usuario en concreto. La forma más segura de manejar sesiones es almacenando en el cliente sólo esta session ID, y cualquier información de la sesión guardarla en el lado del servidor.

**Indice de contenido**

1.  Iniciar una sesión
2.  Cerrar una sesión
3.  Seguridad en las sesiones

### 1. Iniciar una sesión

Antes de **guardar cualquier información en una sesión** es necesario **iniciar el manejo se la sesión** (_session handling_). Esto se hace al principio del código PHP, y debe hacerse antes de que que cualquier texto, HTML o JavaScript se envíe al navegador. Para comenzar la sesión tan sólo hay que utilizar la función _session_start()_, vamos a iniciarla en un archivo llamado _primero.php_:

```
// Comiendo de la sesión
session_start();
// Guardar datos de sesión
$_SESSION["usuario"] = "Peter";

echo "Sesión iniciada y establecido nombre de usuario!" . "<br>";
?>
<a href="segundo.php">Segundo!</a>
```

La función _sessión_start()_ inicia la sesión entre el **usuario** y el **servidor**, y permite a los valores guardados en **$_SESSION** ser accesibles después.

En un segundo archivo, llamado _segundo.php_, llamamos de nuevo a _session_start()_, que lo que hace es mantener la sesión, y entonces podemos devolver los valores que habíamos guardado en el primer archivo:

```
// Continuamos la sesión
session_start();
// Devolver los valores de sesión
echo "Nombre de usuario: " . $_SESSION["usuario"];
```

Este ejemplo es muy sencillo, simplemente permite entender el concepto de sesión. En el primer archivo hemos asignado el _value_ "Peter" la _key_ "usuario" del array $_SESSION. En el segundo archivo hemos devuelto ese dato utilizando el _key_. $_SESSION permite guardar y devolver información entre los requests de la sesión activa de un usuario.

### 2. Cerrar una sesión

Tan importante es iniciar una sesión como cerrarla. Incluso cuando realmente una sesión es sólo una forma temporal de almacenar datos, es importante **limpiarla después para asegurar la máxima seguridad** especialmente cuando se trata con información personal. 

Para **eliminar un valor de sesión** simplemente se utiliza _unset()_:

```
session_start();
// Eliminar el nombre de usuario:
unset($_SESSION["usuario"]);
```

Para **desvincular todos los valores de sesión de vez** se puede emplear _session_unset()_:

```
session_start();
// Eliminar todas las sesiones:
session_unset();
```

Ambas acciones anteriores sólo afectan a los valores guardados en la sesión, no a la propia sesión. Todavía se pueden guardar nuevos valores en $_SESSION. Si se quiere **dejar de utilizar por completo la sesión**, por ejemplo cuando el usuario hace **log out**, se utiliza _session_destroy()_:

```
session_start();
// Terminar la sesión:
session_destroy();
```

Es muy recomendable que **cuando ya no se necesite la sesión se destruya** con _session_destroy()_, en vez de **desvincular el valor de sus valores** con _session_unset()_. Si sólo se desvinculan los valores la sesión permanecerá activa, lo que deja la puerta abierta a intrusos.

### 3. Seguridad en las sesiones

A pesar de su simplicidad, hay en determinadas ocasiones que el uso de sesiones puede salir mal. En esta sección se muestran algunas **técnicas sencillas para evitar riesgos con las sesiones**: 

*   **Sessión Time-Outs**. Establecer un tiempo de vida a las sesiones es algo importante para lidiar con las sesiones de usuarios en aplicaciones. Si un usuario inicia sesión en un lugar y se olvida de cerrarla, otros pueden continuar con la misma, por eso es mejor establecer un tiempo máximo de sesión:

   
```
    session_start();
    // Establecer tiempo de vida de la sesión en segundos
    $inactividad = 600;
    // Comprobar si $_SESSION["timeout"] está establecida
    if(isset($_SESSION["timeout"])){
        // Calcular el tiempo de vida de la sesión (TTL = Time To Live)
        $sessionTTL = time() - $_SESSION["timeout"];
        if($sessionTTL > $inactividad){
            session_destroy();
            header("Location: /logout.php");
        }
    }
    // El siguiente key se crea cuando se inicia sesión
    $_SESSION["timeout"] = time();
   
```

El código asegura que si no hay ninguna actividad en 10 minutos, cualquier request en adelante redigirirá a la página de logout.

*   **Regenerar el Session ID**. La función _session_regenerate_id()_ crea un nuevo ID único para representar la sesión actual del usuario. Esto se debe realizar cuando se realizan acciones importantes como logearse o modificar los datos del usuario.  Darle a las sesiones un nuevo ID reduce la probabilidad de [ataques session hijacking](http://diego.com.es/seguridad-de-sesiones-en-php#PrevenirAtaqueSessionHijacking):

   
```
    session_start();
    if($_POST["usuario"] = "admin" && $_POST["password"] == sha1($password)){
        $_SESSION["autorizado"] = true;
        session_regenerate_id();
    }
   
```

*   **Destruir la sesión**. Como ya se ha mencionado antes, es importante utilizar _session_destroy()_ cuando ya no se vaya a hacer uso de la sesión. 
*   **Utilizar almacenamiento permanente**. Utilizar una base de datos para almacenar los datos que se cree que serán persistentes. Dejarlos en la sesión por demasiado tiempo abre de nuevo puertas a ataques. Depende del desarrollador decidir que datos serán almacenados o se mantendrán en $_SESSION.

Estas son técnicas sencillas se seguridad, para saber más puedes leer el artículo sobre la [seguridad en las sesiones](http://diego.com.es/seguridad-de-sesiones-en-php).