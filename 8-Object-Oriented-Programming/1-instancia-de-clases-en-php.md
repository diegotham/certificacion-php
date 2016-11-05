La **Programación Orientada a Objetos** (OOP, Object Oriented Programming) es un estilo de organizar el código que permite a los desarrolladores agrupar tareas similares en **clases**. Esto ayuda a que el código sea más fácil de mantener y a no repetirse (**DRY**, **Don't Repeat Yourself**).

Uno de los **grandes beneficios de OOP** es que si una parte de información cambia en el script, normalmente sólo hace falta cambiarla en un sitio para actualizar el código. Por ejemplo si se ha usado un **sistema de bases de datos como MySQL** utilizando [PDO (PHP Database Objects)](http://diego.com.es/tutorial-de-pdo) y se quiere cambiar a otro, los métodos a utilizar para las sentencias serán los mismos.

Una **clase** se puede ver como el **esquema de una casa** y un **objeto** como la **casa ya construída**. Una **clase** es el esquema de una casa, que define su estructura, sus medidas y características y las relaciones entre ellas. El **objeto** es la casa contruída con todos los datos y organización que la clase ha definido. Se pueden construir las casas (_objetos_) que se quieran con esa estructura (_clase_), y cada una tendrá sus propias familias y decoración.

Para definir una clase se utiliza la palabra reservada **class**, seguido del nombre de clase y dos llaves, entre las cuales se guardará toda la **información del esquema**. Esta información se guarda en _**propiedades**_ (así es como se les llama a las **variables** dentro de las clases), _**métodos**_ (**funciones** dentro de las clases) y _**constantes**_.

El nombre de la clase puede ser **cualquier etiqueta válida** siempre que no sea una **palabra reservada de PHP**. Un **nombre válido** comienza con una letra o guión bajo, seguido de una cantidad arbitraria de letras, números o guiones bajos. 

```
class NombreClase {
    // Declaración de una propiedad
    public $variable = 'Un valor cualquiera';
    // Declaración de un método
    public function mostrarVariable(){
        echo $this->variable;
    }
}
```

**$this** es una **pseudo-variable** que está disponible cuando una clase se instancia. La **clase instanciada es un objeto**, y **$this** hace referencia a ese objeto. Vamos a ver un ejemplo en el que se ve más claro a qué hace referencia $this:

```
class ClaseA {
    function funcionA()
    {
        if(isset($this)){
            echo '$this está definido, su clase es: ';
            // La función get_class devuelve el nombre de la clase de un objeto:
            echo get_class($this) . "<br>";
        } else {
            echo '$this no está definido <br>';
        }
    }
}
class ClaseB {
    function funcionB() {
        // Se llama estáticamente a la función A, pero ésta no es estática
        // Si E_STRICT está activado generará un aviso
        ClaseA::funcionA();
    }
} 
```

Realizaremos algunas acciones con las instancias de las clases A y B:

```
// Probamos a instanciar Clase A y a usar la función functionA
$a = new claseA(); // $a es un objeto
$a->funcionA(); // Devuelve: $this está definido, su clase es: ClaseA

// Llamada estática a funcionA
ClaseA::funcionA(); // Devuelve: $this no está definido
// $this no funciona, no hay objeto al que hacer referencia

// Instanciamos la clase B
$b = new ClaseB(); // $b es otro objeto
$b->funcionB(); // Devuelve: $this está definido, su clase es: ClaseB
// Pero emite un E_STRICT por llamar estáticamente ClaseA::funcionA()

// Llamada estática a funcionB
ClaseB::funcionB(); // Devuelve: $this no está definido
// De nuevo el mismo caso que con ClaseA::funcionA()
```

Para crear una instancia de una clase, se utiliza la palabra **_new_**. Un objeto será creado siempre a no ser que el objeto tenga un constructor definido y lance una excepción como error. Las clases han de definirse antes de instanciarse.

Se pueden utilizar _strings_ que contienen el nombre de la clase para definirla, instanciándola de forma dinámica:

```
$nombreDeClase = 'ClaseA';

$objeto = new ClaseA; // new ClaseA
$objeto = new $nombreDeClase; // new ClaseA
$objeto = new $nombreDeClase(); // new ClaseA
```

Dentro del contexto de una clase, se puede crear un nuevo objeto utilizando **new self** o **new parent**:

```
class Comunicacion {
    public $saludo = "Hola! <br>";
    // Con el constructor disparamos un echo cada vez que se instancia la clase
    function __construct(){
        echo $this->saludo;
    }
    // Si llamamos a este método, volvemos a instanciar la clase
    public function saludar(){
        $objeto = new self;
        return $objeto;
    }
}
$obj = new Comunicacion(); // Devuelve Hola!
$obj->saludar(); // Devuelve Hola!
```

Cuando se asigna una **instancia de clase ya creada a una nueva variable**, la nueva variable accederá a esa misma instancia (esto no significa que sean **referencias**). Vamos a ver un ejemplo de un objeto asignándole una nueva variable sin referencia y con referencia:

```
// Creamos un objeto
$objeto = new ClaseA();
// Asignamos la variable $asignada a ese objeto
$asignada = $objeto;
// Asignamos por referencia $referencia a ese objeto
$referencia =& $objeto;
// Desde el propio objeto, creamos la propiedad $var
$objeto->var = 'La propiedad $var de $asignada tendrá este valor';
// Asignamos null a $objeto, lo cual también afectará a $referencia
$objeto = null;
// Hacemos var_dump de las tres variables:
var_dump($objeto); // Devuelve : null
var_dump($referencia); // Devuelve : null
var_dump($asignada);
/*
Devuelve:
object(ClaseA)[1]
  public 'var' => string '$asignada tendrá este valor' (length=28)
*/
```

En ocasiones lo que queremos es hacer una copia de un objeto sin afectar al original. Una **copia** de un objeto ya creado se puede hacer con la palabra **clone**, que copia las propiedades y métodos del objeto en uno nuevo, y llama después a la función __clone() para la clase que está copiando. Se puede utilizar **__clone()** para ejecutar más acciones si se desea (algo así como un constructor para el objeto copiado):

```
class Perro {
    public $nombre;
}
$unPerro = new Perro;
$unPerro->nombre = "Werthers"; // $unPerro se llama Werthers
// Creamos una función para cambiar el nombre:
function cambiarNombre($perro, $nombre){
    $perro->nombre = $nombre;
}
cambiarNombre($unPerro, "Smirnoff"); // ahora $unPerro se llama Smirnoff
// Clonamos el objeto $unPerro:
$otroPerro = clone $unPerro;
// Cambiamos el nombre del perro clonado:
cambiarNombre($otroPerro, "Donald"); // $otroPerro se llama Donald
// $unPerro se sigue llamando Smirnoff
```

A partir de **PHP 5.3** se introdujeron **nuevas formas de crear instancias de objetos**. Con la palabra _**new static**_ es posible instanciar la clase desde la cual se llama a la instancia. Por ejemplo, si desde una **clase hija** se llama a un **método padre** que devuelve _**new static**_, la instancia será de la clase hija, no del padre. Veamos ésta y **otras formas de crear objetos**:

```
class ClaseA
{
    public $var = "hola";
    // Función estática que puede llamarse sin instanciar ClaseA:
    static public function obtenerNueva(){
        // Devuelve una instancia de la clase que llame a obtenerNueva()
        return new static;
    }
    public function setterVar($var){
        $this->var = $var;
    }
}
class ClaseHija extends ClaseA{}

```

Se presentan 3 casos para observar diferentes instancias de clases:

```
// +++ CASO 1 +++
// Creamos una instancia de la ClaseA
$objeto1 = new ClaseA();
// Creamos otra instancia de la ClaseA
$objeto2 = new $objeto1;
// El resultado son dos instancias distintas de la misma clase
var_dump($objeto1 !== $objeto2); // Devuelve true, no son idénticas
// +++ CASO 2 +++
$objeto3 = ClaseA::obtenerNueva();
// obtenerNueva() se ha llamado desde ClaseA, por lo que la instancia será de la ClaseA
var_dump($objeto3 instanceof ClaseA); // Devuelve true
// +++ CASO 3 +++
$objeto4 = ClaseHija::obtenerNueva();
// obtenerNueva() se ha llamado desde ClaseHija, por lo que la instancia será de la ClaseHija
var_dump($objeto4 instanceof ClaseHija); // Devuelve true
```

*El operador _instanceof_ también devuelve **true** con las interfaces y clases padre, por lo que _$objeto4_ también devolvería true con _$objeto4 instanceof ClaseA_, pero es un objeto de ClaseHija.

Las **propiedades y métodos de clases** viven en **espacios de nombres** diferentes, por lo que se puede tener una propiedad y un método con el mismo nombre en una clase:

```
class ClaseA
{
    public $prueba = "Esto es una propiedad";
    public function prueba() {
        return "Esto es un método";
    }
}
$a = new ClaseA;
echo $a->prueba . "<br>"; // Devuelve: Esto es una propiedad
echo $a->prueba(); // Devuelve: Esto es un método
```

Esto hace que llamar a [funciones anónimas](http://diego.com.es/funciones-anonimas-y-clausuras-en-php) que hayan sido **asignadas a una propiedad** no sea directamente posible, primero la propiedad ha de asignarse a una variable:

```
class ClaseA
{
    public $prueba;
    public function __construct(){
        $this->prueba = function() {
            return "HOLA!";
        };
    }
}
$a = new ClaseA;
$funcion = $a->prueba;
echo $funcion();

```

Es posible también **instanciar una clase dentro de la propiedad de otra**, como si fuera un array multidimensional:

```
class ClaseA {
    public $variable;
}
class ClaseB {
    public $prueba;

    public function unaFuncion()
    {
        print "Hola";
    }
}
$a = new ClaseA;
$a->variable = new ClaseB;
$a->variable->unaFuncion(); // Devuelve: Hola
```