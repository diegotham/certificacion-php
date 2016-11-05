Si se llama a una variable que tiene paréntesis anexos a ella, **PHP** buscará una función con el **valor** que tiene dicha variable, e intentará ejecutarla. Se utilizan entre otras cosas para implementar **callbacks**.

Las funciones variables no funcionan con **constructores del lenguaje** como _echo_, _print_, _unset()_, _isset()_, _empty()_, _include_, _require_, etc. Para usar estos constructores como funciones variables se puede hacer con **funciones de envoltura**. 

*   **Ejemplo de función variable:**

```
function saludar(){
    echo "Hola que tal" . "<br>";
}

function despedirse($despedida = ''){
    echo "Nos vemos otro día, $despedida" . "<br>";
}

// Función de envoltura
function decirEsto($esto){
    echo $esto;
}

$miFuncion = 'saludar';
$miFuncion(); // Llama a saludar(). Devuelve: Hola que tal

$miFuncion = 'despedirse';
$miFuncion('hasta luego'); // Llama a despedirse(). Devuelve: Nos vemos otro día, hasta luego

$miFuncion = 'decirEsto';
$miFuncion('Esto es lo que digo');// Llama a decirEsto(). Devuelve: Esto es lo que digo
```

*   **Ejemplo con métodos de clases:**

```
class Comunicacion {

    public function saludar(){
        $variable = 'saludo';
        $this->$variable();
    }

    public function saludo()
    {
        echo "¡Hola! ¿Qué tal?";
    }
}

$comunicacion = new Comunicacion();
$miFuncion = "saludar";
$comunicacion->$miFuncion(); // Llama a $comunicacion->saludar(), y ésta
// llama a $comunicacion->saludo()
```

*   **Ejemplo con métodos estáticos**. La llamada al **método** es prioritaria al operador de la **propiedad static**:

```
class Comunicacion {

    static $saludo = "Variable saludo";

    public static function Saludo()
    {
        echo "Método saludo";
    }
}

echo Comunicacion::$saludo; // Devuelve: Variable saludo

$saludo = "Saludo";
Comunicacion::$saludo(); // Devuelve: Método saludo
```

*   **Llamar a cualquier callable de una variable**:

```
class Comunicacion {

    static function saludar()
    {
        echo "Hola" . "<br>";
    }

    function despedir()
    {
        echo "Adios" . "<br>";
    }
}

$miFuncion = array ("Comunicacion", "saludar");
$miFuncion(); // Devuelve: Hola

$otraFuncion = array(new Comunicacion, "despedir");
$otraFuncion(); // Devuelve: Adios
```