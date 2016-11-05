Las **clases**, **propiedades** y **métodos** se definen mediante los **modificadores** _public_, _protected,_ _private_, _final_ o _abstract_ seguido de una declaración de variable normal:

1.  Public: la **propiedad o método** podrá usarse en cualquier parte del script
2.  Private: la **propiedad o método** sólo podrá usarse en la clase a la que pertenece
3.  Protected: la **propiedad o método** se podrá usar por la clase a la que pertenece y por sus descendientes.
4.  Final: la **clase o método** no puede ser sobreescrito en clases descendientes.
5.  Abstract: la **clase o método** no puede ser usado directamente, ha de ser heredado primero para usarse.

Primero es necesario saber qué es la **herencia de clases**. Una clase puede heredar los métodos y propiedades de otra clase usando la palabra _**extends**_. No es posible extender a múltiples clases, sólo se puede heredar de una clase. Los métodos y propiedades heredados pueden ser sobreescritos declarándolos de nuevo con el mismo nombre que tienen en la clase padre:

```
class Perro
{
    public $nombre = "Rudolf";
    public function ladrar(){
        print "Guau!";
    }
}
class Bulldog extends Perro {
    public function ladrar(){
        print "Woof!";
    }
}
$cachorro = new Bulldog(); // Instancia de la clase hija
$cachorro->nombre = "Jeffrey"; // Heredamos la propiedad padre $nombre y le asignamos Jeffrey
echo $cachorro->nombre; // Devuelve Jeffrey
$cachorro->ladrar(); // Devuelve Woof! ya que ha sobreescrito la función padre ladrar().
```

Si una clase extiende a otra, la clase padre debe declararse antes de hacerlo la clase hija (no da error, pero es una regla general).

Ahora que ya tenemos una idea de cómo funciona la herencia de clases, vamos a ver cada uno de los **modificadores**.

### 1. Public

Las **propiedades y métodos public** son accesibles desde cualquier parte del script, siendo este modificador el más fácil de usar. En PHP 4 las variables se declaraban con "_var_" y eran public, pero esa forma de denominarlas está obsoleta y puede generar algunos warnings.

```
class Perro
{
    public $nombre;
    public function ladrar(){
        print "Guau!";
    }
}
class Bulldog extends Perro {
    public function ladrar(){
        print "Woof!";
    }
}
$cachorro = new Bulldog();
$cachorro->nombre = "BunBuns";
print $cachorro->nombre; // Devuelve: BunBuns
```

**Por defecto todas las propiedades y funciones son public**. No es necesario especificar _**public**_, pero es mejor hacerlo para que sea más legible. Con los métodos puedes no utilizar ninguna palabra y escribir directamente function. Con las propiedades en cambio necesitas una palabra por lo menos, sino da un **error de sintaxis, **ya que sino no hay manera de saber qué propiedades tiene una clase. Nunca hay que usar _var_, siempre un modificador.

### 2. Private

El problema de las **propiedades y métodos public** es que se permite llamar a los métodos y establecer las propiedades desde cualquier lado del script. En el ejemplo anterior, si en lugar de crear un objeto **Bulldog**, creamos un objeto **Perro** e intentamos establecer nombre, nos dará un fatal error: **Cannot access private property**.

```
class Perro
{
    private $nombre;
    public function ladrar(){
        print "Guau!";
    }
}
$cachorro = new Perro();
$cachorro->nombre = "BunBuns"; // Fatal error
```

No se podrá ni mostrar ni modificar el nombre. Para hacerlo se utilizan _**getters**_ y _**setters**_, **métodos _public_** para poder acceder a estas propiedades y métodos _**private**_ desde otras partes del script:

```
class Perro
{
    private $nombre;
    public function ladrar(){
        print "Guau!";
    }
    public function setNombre($nombre){
        $this->nombre = $nombre;
    }
    public function getNombre(){
        return $this->nombre;
    }
}
$cachorro = new Perro();
$cachorro->setNombre("Chicken");
echo $cachorro->getNombre(); // Devuelve: Chicken
```

Sin embargo, **si desde un objeto que es instancia de una clase heredada se intenta modificar el valor private**, PHP lo que hace es **crear una varable pública y una privada**. Vamos a partir del ejemplo que hemos puesto en _public_, y cambiar la propiedad a _private_:

```
class Perro
{
    private $nombre;
    private function ladrar(){
        print "Guau!";
    }
}
class Bulldog extends Perro {}
$cachorro = new Bulldog();
$cachorro->nombre = "BunBuns";
var_dump($cachorro);
/*
Devuelve:
object(Bulldog)[1]
  private 'nombre' (Perro) => null
  public 'nombre' => string 'BunBuns' (length=7)
*/
// Los métodos en cambio no se pueden usar:
$cachorro->ladrar(); // Fatal error: Call to private method
```

Ahora hay dos variables, una privada que no se puede modificar y es null porque no se le ha asignado ningún valor, y otra public que crea PHP. Esto es algo confuso, por lo que lo mejor es **evitar esta situación**. Los métodos y propiedades privados sólo deben ser accedidos por la clase en la que se encuentran, las clases hijas no pueden acceder a ellos. Para ello se utiliza el **modificador protected**.

### 3. Protected

Las propiedades y métodos marcados como **protected** son accesibles a través de la clase donde se crean y sus descendientes:

```
class Perro
{
    protected $nombre;
    protected function ladrar(){
        print "Guau!";
    }
}
class Bulldog extends Perro {
    public function ladrarBulldog(){
        // Podemos acceder a ladrar() de la clase Perro
        return $this->ladrar();
    }
    public function setNombre($nombre){
        $this->nombre = $nombre;
    }
    public function getNombre(){
        print $this->nombre;
    }
}
```

Diferentes ejemplos de acceso a propiedades _protected_:

```
$cachorro = new Bulldog();
// Podemos acceder a la función ladrar() desde ladrarBulldog():
$cachorro->ladrarBulldog(); // Devuelve Guau!
// No podemos acceder a ladrar() desde el objeto cachorro:
$cachorro->ladrar(); // Fatal error: Call to protected method Perro::ladrar()
// Tampoco podemos asignarle un nombre, pues $nombre también es protected
$cachorro->nombre = "Hunky"; // Fatal Error: Cannot access protected property
// Si podemos asignarle un valor con el método setNombre():
$cachorro->setNombre("Hunky");
// Y mostrarlo con getNombre():
echo $cachorro->getNombre(); // Devuelve: Hunky
```

### 4. Final

La palabra _**final**_ se utiliza para declarar que **un método o clase no puede ser sobreescrito por una clase hija**:

```
class Perro
{
    public $nombre;
    final public function ladrar(){
        print "Guau!";
    }
}
class Bulldog extends Perro {
    // Dará error:
    public function ladrar()
    {
        print "Woof!";
    }
}
$cachorro = new Bulldog();
$cachorro->ladrar(); // Cannot override final method
```

Lo mismo ocurre si se intenta **heredar una clase final**:

```
final class Perro
{
    public $nombre;
}
class Bulldog extends Perro {
}
// Fatal error. Class Bulldog may not inherit from final class
```

### 5. Abstract

El **modificador _abstract_** indica que una clase o método no puede instanciarse y **sólo puede heredarse**, transladando su funcionamiento **obligatorio** a las clases hijas. Una explicación más concreta puede encontrarse en este artículo sobre las [clases abstractas en PHP](http://diego.com.es/clases-abstractas-en-php).