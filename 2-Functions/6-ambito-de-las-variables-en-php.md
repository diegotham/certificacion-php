Las **variables que se declaran fuera de funciones** o clases se encuentran en el **ámbito** _**global **_(_global **scope**_), disponibles en cualquier parte del script.

Las **funciones** son **bloques independientes**, las variables que se definen dentro de una función tienen **ámbito local** (_**local scope**_) y no afectan a otras en el **script global**, de la misma forma que las **variables globales** no están disponibles dentro de las funciones:

    function saludar()
    {
        $saludo = "Hola";
        print $saludo;
    }

    $saludo = "Buenos dias";
    saludar(); // Devuelve: Hola
    print $saludo; // Devuelve: Buenos días

La ejecución del script empieza en _$saludo = "Buenos días"_, y después llama a la función _saludar()_. Esta función establece _$saludo_ a _"Hola"_ y lo imprime, y después devuelve el control al script principal donde se imprime _$saludo_. Puede comprobarse que sus valores son diferentes y no se afecta el uno al otro. 

Es posible emplear una **variable global dentro de una función** con la palabra **global** o con el _array_ **$GLOBALS**:

    // --- Ejemplo con la palabra global
    $x = 2;
    $y = 20;

    function suma()
    {
        global $x, $y;
        return $x + $y;
    }

    echo suma(); // Devuelve: 22
    // --- Ejemplo con $GLOBALS
    $x = 4;
    $y = 21;

    function otraSuma()
    {
        return $GLOBALS['x'] + $GLOBALS['y'];
    }

    echo otraSuma(); // Devuelve 25

Cuando se hace global a una variable, es lo mismo que referenciar el nombre de la misma al elemento GLOBALS en cuestión:

    $x = 10;
    function funcionUno(){
        global $x;
        $x++;
        return $x;
    }
    function funcionDos(){
        $x =& $GLOBALS['x'];
        $x++;
        return $x;
    }
    funcionUno();
    funcionDos();
    echo $x; // Devuelve: 12

La diferencia entre utilizar la palabra global y el array $GLOBALS se produce cuando se hacen referencias dentro de las funciones:

    $x = "Soy X";
    $y = "Soy Y";
    function funcionUno()
    {
        global $x, $y;
        $y =& $x;
    }
    function funcionDos(){
        global $x;
        $GLOBALS['y'] =& $x;
    }
    funcionUno();
    echo $y; // Devuelve: Soy Y
    funcionDos();
    echo $y; // Devuelve: Soy X

En _funcionUno()_ se han importado _$x_ e _$y_ con la palabra global y se ha referenciado _$y_ a _$x_, pero esto ha ocurrido sólo dentro de la función. En _funcionDos()_ se ha empleado $GLOBALS con _$y_ y su valor ha permanecido referenciado después de la función.

Categoría Publicado GUARDAR CAMBIOS Volver al listado Cuidado con este botón, no hay vuelta atrás: