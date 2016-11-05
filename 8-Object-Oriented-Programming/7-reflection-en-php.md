**Reflection** (o _reflexión_) es cuando un **objeto** es capaz de **examinarse a sí mismo de forma retrospectiva** para **informarte** de sus métodos, propiedades o clases **durante la ejecución del script**. Es una funcionalidad importante y usada con frecuencia en el **desarrollo de aplicaciones**.

Reflection es frecuentemente utilizado para **hacer debugging**. Si se utilizan funciones como _get_class()_ o _get_class_methods()_ cuando se trabaja con un objeto, se está utilizando la **reflexión**. También se emplea para **crear documentación**. Reflection puede generar documentación automáticamente, inspeccionando cada método, constructor o clase para determinar que es lo que entra y sale de cada uno.

La **reflexión** es posible en objetos, clases, métodos, propiedades, funciones, parámetros, excepciones y extensiones. Cada uno tiene una clase particular de la forma: ReflectionObject, ReflectionClass, ReflectionMethod, ReflectionProperty, ReflecionFunction, ReflectionParameter, ReflectionException y ReflectionExtension.

**Indice de contenido**

1.  Ejemplo de uso de Reflection
2.  Obtener información con Reflection
3.  Manipular información con Reflection
4.  Uso indirecto de Reflection
5.  Cuando usar Reflection

### 1. Ejemplo de uso de Reflection

Suponemos un simple blog, con las clases **Post** y **Autor**:

```
// Post.php
require_once 'Autor.php';
class Post {
    function publicarNuevoArticulo(){
        $autor = new Autor('Diego');
        $autor->nuevoArticulo('2451');
        $autor->publicar();
    }
}
// Autor.php
class Autor {
    private $nombre;
    public $articuloID;
    function __construct($nombre){
        $this->nombre = $nombre;
    }
    public function nuevoArticulo($articuloID){
        $this->articuloID = $articuloID;
    }
    public function publicar(){
        // Código para publicar un artículo
        return true;
    }
}
```

En la función _publicarNuevoArticulo()_ la variable _$autor_ es del tipo clase **Autor**. No sería necesario emplear **reflection**. Pero si introducimos una **nueva clase Manager**:

```
// Manager.php
require_once 'Autor.php';
require_once 'Post.php';
class Manager {
    function hacerTarea(DateTime $date){
        if((new DateTime())->getTimestamp() > $date->getTimestamp()){
            $autor = new Autor('Diego');
            $post = new Post();
            $post->publicarNuevoArticulo($autor);
        }
    }
}
// Modificaremos también Post con lo siguiente:
// Post.php
class Post {
    function publicarNuevoArticulo($autor){
        $autor->nuevoArticulo('2451');
        $autor->publicar();
    }
}
```

Ahora Post no tiene relación con la clase Autor. No incluye su archivo, no inicializa su clase y desconoce que exista. Se podría pasar un objeto de cualquier tipo a _publicarNuevoArticulo()_ y el código funcionaría.

![Diagrama explicación de Reflection en PHP](/uploads/articulos/diagramapostautor.png)

**Post** sólo tiene una relación directa con **Manager**. **Manager** lo crea, y por tanto **Post** depende de **Manager**. **Post** ya no tiene ninguna relación con la clase **Autor**, y **Autor** sólo tiene relación con **Manager**.

Durante la ejecución, **Post** utiliza un objeto **Autor**, de ahí que en el diagrama se vea un interrogante junto con "utiliza". Durante la ejecución, **PHP** simplemente inspecciona el objeto recibido y verifica que implementa los métodos _nuevoArticulo()_ y _publicar()_.

### 2. Obtener información con Reflection

Con **PHP** y **Reflection** podemos mostrar información de un objeto. Pongamos un _**var_dump**_ en _publicarNuevoArticulo()_ e iniciemos el proceso:

```
// Modificamos Post en Post.php añadiendo el var_dump
class Post {
    function publicarNuevoArticulo($autor){
        $autor->nuevoArticulo('2451');
        $autor->publicar();
        var_dump(new ReflectionClass($autor));
    }
}
// Iniciamos el proceso
$autor = new Autor("Diego");
$post = new Post();
$post->publicarNuevoArticulo($autor);
/*
Devuelve:
object(ReflectionClass)[3]
  public 'name' => string 'Autor' (length=5)
*/
```

La clase ReflectionClass tiene una propiedad _name_ con el tipo de _$autor_, Autor. Si queremos más información, podemos emplear diferentes métodos. Primero asignaremos la clase ReflectionClass a una variable y después emplearemos _getMethods()_:

```
class Post {
    function publicarNuevoArticulo($autor){
        $autor->nuevoArticulo('2451');
        $autor->publicar();
        $reflector = new ReflectionClass($autor);
        var_dump($reflector->getMethods());
    }
}
/*
Devuelve:
array (size=3)
  0 => &
    object(ReflectionMethod)[4]
      public 'name' => string '__construct' (length=11)
      public 'class' => string 'Autor' (length=5)
  1 => &
    object(ReflectionMethod)[5]
      public 'name' => string 'nuevoArticulo' (length=13)
      public 'class' => string 'Autor' (length=5)
  2 => &
    object(ReflectionMethod)[6]
      public 'name' => string 'publicar' (length=8)
      public 'class' => string 'Autor' (length=5)
*/
```

Otro método que se puede utilizar es _getProperties()_:

```
class Post {
    function publicarNuevoArticulo($autor){
        $autor->nuevoArticulo('2451');
        $autor->publicar();
        $reflector = new ReflectionClass($autor);
        var_dump($reflector->getProperties());
    }
}
/*
Devuelve:
array (size=2)
  0 => &
    object(ReflectionProperty)[4]
      public 'name' => string 'nombre' (length=6)
      public 'class' => string 'Autor' (length=5)
  1 => &
    object(ReflectionProperty)[5]
      public 'name' => string 'articuloID' (length=10)
      public 'class' => string 'Autor' (length=5)
*/
```

Desde un objeto **ReflectionClass** podemos obtener un objeto **ReflectionMethod** con _getMethod()_ y un objeto **ReflectionProperty** con _getProperty()_. Estos objetos extienden las posibilidades reflexivas:

```
class Post {
    function publicarNuevoArticulo($autor){
        $autor->nuevoArticulo('2451');
        $autor->publicar(); // Primera llamada a publicar()
        $reflector = new ReflectionClass($autor);
        $metodoPublicar = $reflector->getMethod('publicar');
        $metodoPublicar->invoke($autor); // Segunda llamada a publicar()
    }
}
```

Hemos utilizado _getMethod()_ para extraer sólo un método con el nombre "publicar". El resultado es un objeto **ReflectionMethod**. Desde este objeto hemos llamado al método _invoke()_, pasándole el objeto _$autor_, para ejecutar por segunda vez la función _publicar()_.

Este proceso ha resultado simple en este caso, porque ya teníamos un objeto **Autor** al que pasarle _invoke()_. Podríamos tener varios objetos **Autor** permitiéndonos elegir qué objeto usar. En otras circunstancias, podría no haber un objeto con el que trabajar, en ese caso necesitaríamos obtener uno con **ReflectionClass**.

Para ver que se ha llamado dos veces a _publicar()_, puedes modificar la función así:

```
// Autor.php
class Autor {
...
public function publicar(){
        echo ("Hola<br>");
        return true;
    }
}
```

### 3. Manipular información con Reflection

También es posible **modificar el código en ejecución**. Por ejemplo vamos a modificar una propiedad private que no tiene un _setter_.

Primero añadimos un método _getter_ en Autor que devuelve el _$nombre_:

```
class Autor {
    private $nombre;
    public $articuloID;
    function __construct($nombre){
        $this->nombre = $nombre;
    }
    public function nuevoArticulo($articuloID){
        $this->articuloID = $articuloID;
    }
    public function publicar(){
        return true;
    }
    public function obtenerNombre(){
        return $this->nombre;
    }
}
```

La propiedad _$nombre_ se establece en el momento en que se instancia la clase, y no tenemos ningún método público que nos permita cambiarlo, pero podemos modificarlo con Reflection.

Primero intentamos obtener el valor de $nombre:

```
class Post {
    function publicarNuevoArticulo($autor){
        var_dump($autor->obtenerNombre());
        $reflector = new ReflectionClass($autor);
        $nombreAutor = $reflector->getProperty('nombre');
        $nombreAutor->getValue($autor);
    }
}
/*
Devuelve:
Fatal error: Uncaught exception 'ReflectionException' with message
'Cannot access non-public member Autor::nombre'
*/
```

Pero como vemos nos devuelve un **error fatal**, ya que no podemos acceder a la propiedad privada. Para arreglar esto, tenemos que decirle al objeto **ReflectionProperty** que nos de **acceso a propiedades y métodos privados**:

```
class Post {
    function publicarNuevoArticulo($autor){
        var_dump($autor->obtenerNombre());
        $reflector = new ReflectionClass($autor);
        $nombreAutor = $reflector->getProperty('nombre');
        $nombreAutor->setAccessible(true);
        var_dump($nombreAutor->getValue($autor));
    }
}
/*
Devuelve:
string 'Diego' (length=5)
string 'Diego' (length=5)
*/
```

Con el método _setAccessible(true)_ hemos podido **acceder a una propiedad privada**. Lo devuelve dos veces porque primero hemos hecho **var_dump** con _obtenerNombre()_ y luego con _getValue()_. Ahora que ya podemos acceder, también podemos modificarla con _setValue()_:

```
class Post {
    function publicarNuevoArticulo($autor){
        var_dump($autor->obtenerNombre());
        $reflector = new ReflectionClass($autor);
        $nombreAutor = $reflector->getProperty('nombre');
        $nombreAutor->setAccessible(true);
        $nombreAutor->setValue($autor, 'Helena');
        var_dump($nombreAutor->getValue($autor));
    }
}
/*
Devuelve:
string 'Diego' (length=5)
string 'Helena' (length=6)
*/
```

### 4. Uso indirecto de reflection

Algunas funciones incorporadas de PHP usan indirectamente **reflection**, por ejemplo en la función _call_user_func()_.

La función _call_user_func()_ acepta un _array_: el primer elemento apunta a un **objeto**, y el segundo al nombre de un **método**. Se puede proporcionar un parámetro opcional, el cual se pasa al método llamado. Por ejemplo:

```
class Post {
    function publicarNuevoArticulo($autor){
        var_dump($autor->obtenerNombre());
        $reflector = new ReflectionClass($autor);
        $nombreAutor = $reflector->getProperty('nombre');
        $nombreAutor->setAccessible(true);
        $nombreAutor->setValue($autor, 'Helena');

        var_dump($nombreAutor->getValue($autor));

        var_dump(call_user_func(array($autor, 'obtenerNombre')));
    }
}
/*
Devuelve:
string 'Diego' (length=5)
string 'Helena' (length=6)
string 'Helena' (length=6)
*/
```

El código devuelve el valor correcto: Helena.

Otro ejemplo de **reflexión indirecta** es cuando se llama a un método de forma dinámica:

```
class Post {
    function publicarNuevoArticulo($autor){
        var_dump($autor->obtenerNombre());
        $reflector = new ReflectionClass($autor);
        $nombreAutor = $reflector->getProperty('nombre');
        $nombreAutor->setAccessible(true);
        $nombreAutor->setValue($autor, 'Helena');
        var_dump($nombreAutor->getValue($autor));
        $nombreMetodo = 'obtenerNombre';
        var_dump($autor->$nombreMetodo());
    }
}
/*
Devuelve:
string 'Diego' (length=5)
string 'Helena' (length=6)
string 'Helena' (length=6)
*/
```

Como vemos devuelve lo mismo que el ejemplo anterior. **PHP** simplemente reemplaza la variable con el _string_ que representa y llama al método. También funciona cuando se quiere crear un objeto usando variables como nombres de clases.

### 5. Cuando usar Reflection

Lista de **situaciones donde puede ser necesario usar Reflection**:

*   **Dynamic typing** (determinación de tipos dinámica).
*   **Aspect Oriented Programming** recibe llamadas a métodos y emplaza código alrededor de métodos.
*   **PHPUnit** utiliza bastante Reflection, al igual que otros mocking frameworks.
*   **Web frameworks** usan en general Reflection para diferentes aspectos: para inicar modelos, construir objetos para views, etc. **Laravel** por ejemplo usa mucho Reflecion para inyectar dependencias.
*   **Metaprogramming**, como los últimos ejemplos de reflexión indirecta.
*   **Code analysis frameworks**, para entender el código.

No conviene abusar de Reflection ya que puede resultar caro en recursos.