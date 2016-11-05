**Indice de contenido**

1.  Introducción
2.  Como funcionan los ataques CSRF
3.  Como evitar ataques CSRF

### 1. Introducción

Los **ataques Cross-Site Request Forgeries**, más comúnmente llamados **ataques CSRF**, se producen cuando el atacante provoca que el usuario ejecute una acción de forma no intencionada en una aplicación en la que había iniciado sesión.

Por ejemplo cuando, estando el usuario logeado en su sitio favorito, procede a **hacer click en un enlace que parece inofensivo**. En el fondo, su información de perfil está siendo actualizada con la dirección email del atacante. El atacante puede ahora usar la opción de recuperar la contraseña en el sitio web para cambiar la contraseña de la cuenta mediante email.

Cualquier acción que pueda realizar un usuario cuando está logeado en un sitio web la puede realizar también el atacante, ya sea **actualizar su perfil**, añadir **objetos a la cesta de la compra**, postear **mensajes en un foro** o cualquier otra cosa.

### 2. Como funcionan los ataques CSRF

Para ver cómo funcionan es mejor verlos en acción. Vamos a simular un sistema de usuarios con su **página de acceso** (_login.php_), un **script que procese el inicio y cierre de sesión** (_process.php_), y una página que simula un ataque (_veneno.html_).

El código para _login.php_:

```
<?php
session_start();
?>
<html>
<body>
<?php
if (isset($_SESSION["user"])) {
    echo "<p>Bienvenido de vuelta, " . $_SESSION["user"] . "!<br>";
    echo '<a href="process.php?action=logout">Logout</a></p>';
}
else {
    ?>
    <form action="process.php?action=login" method="post">
        <p>El nombre de usuario es: admin</p>
        <input type="text" name="user" size="20">
        <p>La contraseña es: test</p>
        <input type="password" name="pass" size="20">
        <input type="submit" value="Login">
    </form>
    <?php
}
?>
</body>
</html>
```

El script de _login.php_ primero inicia los datos se sesión con _sessión_start()_, después comprueba si el usuario ya está logeado, y si no es así, muestra un **formulario de login**.

El archivo de proceso _process.php_ es el siguiente:

```
<?php
session_start();
switch($_GET["action"]) {
    case "login":
        if ($_SERVER["REQUEST_METHOD"] == "POST") {
            $user = (isset($_POST["user"])) &&
            ctype_alnum($_POST["user"]) ? $_POST["user"] : null;
            $pass = (isset($_POST["pass"])) ? $_POST["pass"] : null;
            $salt = '$2a$07$my.salt.mUy.Secr3t0';

        if (isset($user, $pass) && (crypt($user . $pass, $salt) ==
                crypt("admintest", $salt))) {
                $_SESSION["user"] = $_POST["user"];
            }
        }
        break;
    case "logout":
        $_SESSION = array();
        session_destroy();
        break;
}
header("Location: login.php");
?>
```

El archivo _process.php_ de nuevo inicia los datos de sesión, y después comprueba a ver si existe alguna acción con la que trabajar. En el caso de _**login**_, se realiza una pequeña **validación de _input_** con el operador ternario de PHP junto con las funciones _ctype_alnum()_ y _crypt()_. En el caso de _**logout**_, se destruye la sesión. Finalmente se redirige al usuario a _login.php_.

El **archivo malicioso** en cuestión es _veneno.html_:

```
<html>
<body>
<p>Esta página es veneno!!</p>
<img src="process.php?action=logout" style="display:none;">
</body>
</html>
```

Si visitas _login.php_, te logeas, y después visitas _veneno.html_, automáticamente se cerrará tu sesión aunque no hayas hecho click en el botón de _**logout**_. El navegador envía un _**request**_ al servidor para acceder al script _process.php_, esperando que sea realmente una imagen. El script en _process.php_ no tiene forma de diferenciar entre un _**request**_ válido iniciado por el usuario que hace click en el link de _**logout**_ o procedente de un **archivo malicioso**.

El archivo _veneno.html_ se puede alojar en un servidor totalmente diferente al que estás logeado, y funcionaría igual, ya que la página del atacante está haciendo un **request en tu nombre** usando la **sesión que tienes abierta**. No importa ni siquiera si el sitio web en el que estás logeado está en una red privada, el request se enviará desde tu dirección IP como si hubieras hecho el request tú mismo, haciendo que sea difícil rastrear la fuente maligna.

Si permites que los usuarios puedan **enlazar imágenes como imagen de perfil**, sin **escapar** apropiadamente y **sanitizar** los datos del usuario, podría realizarse incluso desde tu propio sitio web.

Como es lógico, cerrar la sesión de un usuario no es lo que se suele hacer para hacer daño. Se puede utilizar un **iframe escondido** (en lugar de una imagen) con un **formulario que automáticamente envía cuando se carga la página**, lo que haría que fuera posible cualquiera de los ataques mencionados al principio.

### 3. Como evitar ataques CSRF

Para asegurarse que una acción está realmente siendo llevada a cabo por el usuario en lugar de un tercero, hay que asociarlo con algún tipo de **identificador único** que puede ser verificado después, llamado _**token**_. Para prevenir el ataque, podemos modificar _login.php_ así:

```
$_SESSION["token"] = md5(uniqid(mt_rand(), true));
echo '<a href="process.php?action=logout&csrf=' . $_SESSION["token"] . '">Logout</a></p>';
```

Y para **verificar el identificador**, podemos modificar _process.php_ como sigue:

```
case "logout":
    if (isset($_GET["csrf"]) && $_GET["csrf"] == $_SESSION["token"]) {
        $_SESSION = array();
        session_destroy();
    }
    break;
```

Con estas modificaciones, _veneno.html_ ya no puede cerrar la sesión porque se le ha añadido una nueva tarea al atacante: tener que **adivinar el token aleatorio**.

Para **proteger formularios**, se suele incluir el **identificador dentro de un campo escondido**, siendo enviado con el resto de los datos del formulario:

```
<input type="hidden" name="csrf" value="<?php echo $_SESSION["token"]; ?>">
```