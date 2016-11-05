**Índice de contenido**

1.  Introducción
2.  Variables predefinidas
3.  Ámbito de las variables
4.  Variables variables
5.  Variables desde fuentes externas

### 1. Introducción

Las **variables en PHP** se representan mediante el símbolo de **dolar $**, y **son sensibles a mayúsculas y minúsculas**.

**Reglas para nombres de variables**:

*   Tienen que empezar con una **letra** o **barra baja _,** seguido de cualquier número, letra o barra baja.
*   **$this** es una variable especial que no puede ser asignada.

Las variables por defecto se asignan por **valor**, por lo que cuando se asigna una **expresión** a una **variable**, el valor de la expresión se copia en la variable. Una expresión es cualquier cosa que tenga un valor:

    $x = 3; //  3 es una expresión
    function numero(){
        return 3;
    }
    // Las funciones son expresiones con el valor de sus valores devueltos
    $x = prueba(); // La expresión prueba() es 3

Si se asigna el valor de una variable a otra, los cambios que se hagan en una de las dos no afectarán a la otra:

    $x = 3;
    $y = $x;
    echo $y; // Devuelve 3
    $y = 4;
    echo $y; // Devuelve 4, y $x sigue valiendo 3

No ocurre lo mismo si se asignan **valores por referencia**, donde los cambios que afecten a una variable afectarán a la otra. Para ello se antepone el signo **ampersand &** al comienzo de la variable cuyo valor se quiere asignar. 

    <?php
    $uno = 'Coche';
    $dos = &$uno;

    $uno = 'Moto';

    echo $uno; // Devuelve Moto
    echo $dos; // Devuelve Moto

Cuando se intentan emplear **variables no definidas**, PHP crea _**notices**_ o _**warnings**_, aunque devuelve un valor dependiendo del tipo que se está solicitando:

    <?php
    //--- Uso de variable no definida y no referenciada
    var_dump($variable_indefinida); // Devuelve null
    // Notice: Undefined variable

    //--- Uso booleano
    echo($booleano_indefinido ? "true\n" : "false\n"); // Devuelve false
    // Notice: Undefined variable

    //--- Uso de una cadena
    $cadena_indefinida .= 'abc';
    var_dump($cadena_indefinida); // Devuelve 'string(3)' "abc"
    // Notice: Undefined variable

    //--- Uso de un entero
    $int_indefinido += 25; // 0 + 25 => 25
    var_dump($int_indefinido); // Devuelve 'int(25)'
    // Notice: Undefined variable

    //--- Uso de float
    $flotante_indefinido += 1.25;
    var_dump($flotante_indefinido); // Devuelve 'float(1.25)'
    // Notice: Undefined variable

    //--- Uso de array
    $array_indefinida[3] = "def";
    var_dump($array_indefinida); // Devuelve array(1) {  [3]=>  string(3) "def" }
    // No genera ningún mensaje de error

    //--- Uso de objetos; crea un nuevo objeto stdClass
    $objeto_indefinido->foo = 'bar';
    var_dump($objeto_indefinido); // Devuelve object(stdClass)#1 (1) {  ["foo"]=>  string(3) "bar" }
    //  Warning: Creating default object from empty value
    ?>

### 2. Variables predefinidas

La mayoría de variables predefinidas son **superglobals**, que son variables internas disponibles en cualquier lugar del script PHP: 

*   [$GLOBALS](http://php.net/manual/es/reserved.variables.globals.php) - **array** con todas las variables disponibles en el ámbito global. Los nombres de las variables son los _**keys**_ del array.
*   [$_SERVER](http://php.net/manual/es/reserved.variables.server.php) - **array** que contiene información del entorno del servidor y de ejecución (headers, rutas, ubicaciones de script...). Son creadas por el servidor web. Contiene información importante sobre **request headers HTTP**.
*   [$_GET](http://php.net/manual/es/reserved.variables.get.php) - **array asociativo** de variables enviadas al script a través de **parámetros URL**.
*   [$_POST](http://php.net/manual/es/reserved.variables.post.php) - **array asociativo** de variables enviadas al script a través del **método POST de HTTP** cuando se emplea _application/x-www-form-urlencoded_ o _multipart/form-data_ como **Content-Type de HTTP** en el _request_.
*   [$_FILES](http://php.net/manual/es/reserved.variables.files.php) - **array asociativo** de elementos enviados al script a través del **método POST**.
*   [$_COOKIE](http://php.net/manual/es/reserved.variables.cookies.php) - **array asociativo** de variables enviadas al script a través de **Cookies HTTP**.
*   [$_SESSION](http://php.net/manual/es/reserved.variables.session.php) - **array asociativo** de variables de sesión disponibles en el script.
*   [$_REQUEST](http://php.net/manual/es/reserved.variables.request.php) - **array asociativo** que por defecto contiene $_GET, $_POST y $_COOKIE.
*   [$_ENV](http://php.net/manual/es/reserved.variables.environment.php) - **array asociativo** de variables enviadas al script a través del método del entorno.

Existen otras variables predefinidas no denominadas superglobals:

*   [$php_errormsg](http://php.net/manual/es/reserved.variables.phperrormsg.php) - **variable** que contiene el texto del último mensaje de **error** generado por PHP ([track_errors](http://php.net/manual/es/errorfunc.configuration.php#ini.track-errors) tiene que estar activado).
*   [$http_response_header](http://php.net/manual/es/reserved.variables.httpresponseheader.php) - array que contiene las cabeceras de respuesta HTTP enviadas por el servidor, similar a la función [get_headers()](http://php.net/manual/es/function.get-headers.php).
*   [$argc](http://php.net/manual/es/reserved.variables.argc.php) - **número de argumentos** enviados al script a través de la línea de comandos. Su valor mínimo es siempre 1, que es el nombre del script (tiene que estar activado [register_argc_argv](http://php.net/manual/es/ini.core.php#ini.register-argc-argv)).
*   [$argv](http://php.net/manual/es/reserved.variables.argv.php) - **array** de argumentos pasados a un script cuando se ejecuta a través de la línea de comandos.

### 3. Ámbito de las variables

El **ámbito** de una variable es el contexto en el que se ha definido. Se pueden crear variables en ámbito global o local. El ámbito abarca los ficheros de **include** y **require**.

Una variable creada en **ámbito global**:

    <?php
    $x = "Hola";
    include 'archivo.php';

La variable $x estará disponible en _archivo.php_. 

Las variables definidas dentro de una función tienen **ámbito local**, y su uso está limitado a esa función:

    <?php
    $x = 10;

    function test(){
        echo $x;
    }

    test(); // No devuelve ningún valor, ya que $x no está definida dentro de la función

Para poder utilizar una **variable global** dentro de una función se tiene que añadir la palabra **global**:

    <?php

    $x = "Peter ";
    $y = "West";

    function fullName()
    {
        global $x, $y;
        $x .= $y;
    }

    fullName();
    echo $x; // Devuelve Peter West

También es posible acceder a variables globales a través de **$GLOBALS**. En el ejemplo anterior, en lugar de añadir **global**, se utilizaría $GLOBALS['x'] .= $GLOBALS['y']:

    <?php
    ...
    function fullName()
    {
        $GLOBALS['x'] .= $GLOBALS['y'];
    }
    ...

#### **Las variables static**

Las **variables estáticas** existen sólo en el ámbito local de la función, pero no pierden su valor cuando se termina la ejecución del programa. Ejemplo:

    <?php

    function test()
    {
    $x=0;
    echo $x;
    $x++;
    }

En la función _test()_ _$x_ va a devolver siempre 0, ya que cuando finaliza la función, la variable _$x_ desaparece y no se muestra el incremento marcado por _$x++_. Para que esto no ocurra y en la variable _$x_ se mantenga el valor de su última modificación, se añade la palabra **static**:

    <?php

    function test()
    {
        static $x = 0;
        echo $x;
        $x++;
    }

Ahora cada vez que se llame a la función _test()_, el valor de _$x_ incrementará 1.

Consideraciones de las **variables estáticas**:

*   Son útiles para manejar **funciones recursivas** (una función recursiva es la que se llama a si misma).
*   No pueden ser el resultado de **expresiones**. Por ejemplo: _static $x = 1+2_ es incorrecto.

### 4. Variables variables

Las **variables variables** son nombres de variables que se pueden definir y usar de forma dinámica.

    <?php
    $x = 'var';

    $$x = 'Hola!';

    echo $var; // Mostrará 'Hola!'

En este ejemplo hemos nombrado una nueva variable como _$var_ con el string que había en _$x_. Vamos a complicar un poco más el ejemplo:

    <php

    $primero = 'segundo';
    $segundo = 'primero';

    echo $$$$primero; // Mostrará 'primero' porque:
    // $$$segundo
    // $$primero
    // $segundo = 'primero'

También se puede acceder a una variable incluyendo su nombre con dos corchetes:

    <?php
    echo "${$primero}"; // Mostrará 'primero'

El tema se complica todavía más con el uso de arrays, ya que se produce un problema de ambigüedad que es necesario aclarar al intérprete. Por ejemplo, **$$x[1]** deberemos escribirlo de cualquiera de las dos siguientes formas:

*   ${$x[1]}. Utiliza el valor del key 1 del array de $x como **nombre de la variable**.
*   ${$x}[1]. Utiliza el nombre de la variable $x para obtener el array con ese nombre, y después obtiene el** valor del key 1 ese array**.

### 5. Variables desde fuentes externas

Para **acceder a datos enviados desde formularios POST** existen dos métodos:

    <?php
    $_POST['variable'];
    $_REQUEST['variable'];

Si se usa un **formulario con GET**, las variables predefinidas deberán ser del tipo GET.

Para **acceder a datos enviados mediante query strings** (información tras el símbolo ? de una URL):

    <?php
    // example.com/test.php?variable=1
    $_GET['variable'];

*   Los puntos y espacios en nombres de variables se convierten a guiones bajos:  
    <input name="**x.y**" /> se transforma en **$_REQUEST["x_y"]**.

También se pueden utilizar **arrays para las variables de los formularios**:

    <?php
    if ($_POST) {
        echo '<pre>';
        echo htmlspecialchars(print_r($_POST, true));
        echo '</pre>';
    }
    ?>
    <form action="<?php echo $_SERVER['PHP_SELF']?>" method="post">
        Nombre:  <input type="text" name="personal[nombre]" /><br />
        Email:   <input type="text" name="personal[email]" /><br />
        Mascota: <br />
        <select multiple name="mascota[]">
            <option value="perro">Perro</option>
            <option value="gato">Gato</option>
            <option value="conejo">Conejo</option>
        </select><br />
        <input type="submit" value="Enviar" />
    </form>

#### **Cookies HTTP**

**PHP** soporta **cookies HTTP** tal y como están definidas en el [RFC 6265](http://www.faqs.org/rfcs/rfc6265.html). Las cookies permiten almacenar datos en el navegador para rastrear o identificar a usuarios que vuelven. 

Para crear cookies se utiliza [setcookie()](http://php.net/manual/es/function.setcookie.php). Las cookies son parte de los headers **HTTP**, por lo que se ha de llamar a dicha función antes de enviar cualquier cosa al navegador (HTML por ejemplo).

Los datos de las cookies están disponibles en los arrays [$_COOKIE](http://php.net/manual/es/reserved.variables.cookies.php) y [$_REQUEST](http://php.net/manual/es/reserved.variables.request.php).

Se pueden asignar múltiples valores a una cookie:

    <?php
    setcookie("MiCookie[foo]", 'Prueba 1', time()+3600);
    setcookie("MiCookie[var]", 'Prueba 2', time()+3600);

Esto resultan ser dos cookies, pero estarán representadas por un array en el script. Para definir sólo una cookie con varios valores se puede emplear [serialize()](http://php.net/manual/es/function.serialize.php) o [explode()](http://php.net/manual/es/function.explode.php).

Una cookie reemplazará a una anterior que tenga el mismo nombre en el navegador, a no ser que la ruta o el dominio sean diferentes. 

Los **puntos** en los nombres de **variables de entrada** se convierten en **guiones bajos**.

#### **Determinación de tipos de variables**

PHP determina los tipos de variables y los convierte. No siempre es fácil evaluar el tipo de variable del que se trata, para ello existen functiones para averiguar qué tipo son:

*   [gettype()](http://php.net/manual/es/function.gettype.php). Devuelve el tipo de variable del que se trata.
*   [is_array()](http://php.net/manual/es/function.is-array.php). Comprueba si la variable es un array.
*   [is_float()](http://php.net/manual/es/function.is-float.php). Comprueba si la variable es float.
*   [is_int()](http://php.net/manual/es/function.is-int.php). Comprueba si la variable es integer.
*   [is_object()](http://php.net/manual/es/function.is-object.php). Comprueba si la variable es un objeto.
*   [is_string()](http://php.net/manual/es/function.is-string.php). Comprueba si la variable es un string.
*   [is_bool()](http://php.net/manual/es/function.is-bool.php). Comprueba si la variable es booleano.