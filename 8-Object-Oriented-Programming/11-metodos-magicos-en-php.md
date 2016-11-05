Los **métodos mágicos** permiten realizar acciones en objetos cuando suceden determinados eventos que los activan. Estos métodos, denominados con doble barra baja ___metodo()_, determinan cómo reaccionará el objeto ante dichos eventos.

Los **métodos mágicos disponibles en PHP** son los siguientes:

| | |
| -------- | -------- |
| 1. __construct() y __destruct() | 5. __sleep() y __wakeup |
| 2. __get() y __set() | 6. __call() y __callStatic |
| 3. __isset() y __unset() | 7. __clone() |
| 4. __toString() | 8. __invoke() |

Suponemos que tenemos una aplicación en la que queremos **extraer tweets de la API de Twitter**. Extraemos en JSON los tweets del usuario y queremos **transformar cada tweet en un objeto de PHP** con el que trabajar.

### 1. __construct() y __destruct()

El **método mágico más utilizado en PHP** es _**__construct()**_, un método que es llamado automáticamente cuando se crea el objeto. Permite **inyectar parámetros y dependencias** para construir el objeto.

```
class Tweet {
    protected $id;
    protected $text;
    public function __construct ($id, $text){
        $this->id = $id;
        $this->text = $text;
    }
}
$tweet = new Tweet(54, "Hola que tal");
```

Cuando creamos una nueva instancia del objeto Tweet, podemos pasarle parámetros que se inyectarán en el método ___construct()_. No se puede llamar al método ya que es llamado automáticamente al crear el objeto.

Cuando se extiende una clase, la clase padre puede tener un **método constructor** con algún argumento. Desde la clase hija se puede nombrar al constructor padre para poder facilitar ese argumento:

```
class Entidad {
    protected $meta;
    public function __construct(array $meta){
        $this->meta = $meta;
    }
}
class Tweet extends Entidad {
    protected $id;
    protected $text;
    public function __construct ($id, $text, array $meta){
        $this->id = $id;
        $this->text = $text;
        parent::__construct($meta);
    }
}
```

Al igual que podemos construir cosas cuando instanciamos un objeto, también podemos **destruirlas cuando el objeto se destruye**. Si por ejemplo tenemos una conexión a la base de datos en el objeto y queremos asegurarnos de que se cierra al destruir el objeto, podemos hacer lo siguiente:

```
public function __destruct(){
    $this->connection->destroy();
}
```

Aunque el **destructor** no es muy utilizado en la práctica.

### 2. __get() y __set()

Cuando se trabaja con objetos, las propiedades pueden ser accedidas de esta forma si son _**public**_:

```
$tweet = new Tweet(54, "Hola que tal");
echo $tweet->text;
```

Pero en la práctica las propiedades lo normal y aconsejable es que sean _**protected**_ o _**private**_, por lo que no podemos acceder a ellas de esa forma.

El método mágico _**__get()**_ permite poder acceder a estas propiedades:

```
public function __get($property){
    if(property_exists($this, $property)) {
        return $this->$property;
    }
}
```

El método acepta el nombre de la propiedad que estabas buscando como argumento. No es necesario llamar al método ___get()_ ya que **PHP** lo llamará automáticamente cuando trates de acceder a una propiedad que no es _**public**_. También lo llamará cuando no existe una propiedad, por ejemplo:

```
class Datos {
    public function __get($propiedad){
        echo "Esta propiedad no existe!";
    }
}
$datos = new Datos;
$datos->noexisto; // Devuelve: Esta propiedad no existe!
```

Si lo que quieres es **establecer una propiedad que no es accesible**, puedes insertar un método ___set()_. Este método coge la propiedad a la cual intentabas acceder y el valor que intentabas establecer como sus dos argumentos.

```
public function __set($property, $value){
    if(property_exists($this, $property)) {
        $this->$property = $value;
    }
}
$tweet->text = "Estableciendo un tweet";
echo $tweet->text; // Devuelve: "Estableciendo un tweet"
```

También podemos establecer una propiedad que no existe con ___set()_:

```
class Datos {
    public function __set($propiedad, $valor){
        $this->propiedad = $valor;
    }
}
$datos = new Datos;
$datos->noexisto = "Ahora si que existo";
var_dump($datos);
/*
object(Datos)[1]
  public 'propiedad' => string 'Ahora si que existo' (length=19)
*/
```

Otra forma de uso es con una propiedad de tipo _**array**_:

```
class Datos {
    protected $datos = array();
    public function __set($nombre, $valor){
        $this->datos[$nombre] = $valor;
    }
    public function __get($nombre){
        return $this->datos[$nombre];
    }
}
$datos = new Datos;
$datos->primero = "Este es el primer dato";
$datos->segundo = "Este es el segundo dato";
$datos->tercero = "Este es el tercer dato";
echo $datos->primero . "<br>";
echo $datos->segundo . "<br>";
echo $datos->tercero;
```

Si eliminamos el método ___set()_ y añadimos el **ampersand de referencia &** en ___get()_, podemos establecer y obtener las propiedades:

```
class Datos {
    protected $datos = array();
    public function &__get($nombre){
        return $this->datos[$nombre];
    }
}
$datos = new Datos;
$datos->primero = "Este es el primer dato";
$datos->segundo = "Este es el segundo dato";
$datos->tercero = "Este es el tercer dato";
echo $datos->primero . "<br>";
echo $datos->segundo . "<br>";
echo $datos->tercero;
```

El uso de estos dos métodos a veces **no** es recomendado por varias razones: son más lentos, no facilitan el autocompletado de código en los IDE, hacen que el mantenimiento y refactorizar sea más largo y complicado, no es posible tener un método magic _protected_... En su lugar se pueden emplear _getters_ y _setters_.

### 3. __isset() y __unset()

Con _isset()_ se verifica la existencia de un elemento en un array o de una propiedad _public_ en un objeto.

Con **___isset()_** también es posible saber acerca de las propiedades _**protected**_ o _**private**_:

```
public function __isset($property){
return isset($this->$property);
}
var_dump(isset($tweet->id));
```

También podríamos utilizar la función _isset()_ dentro de la clase para obtener **true** o **false**.

Con _unset()_ se eliminan los elementos de un array o propiedades públicas de un objeto.

Con _**__unset()**_ también es posible hacerlo con propiedades _**protected**_ o _**private**_:

```
public function __unset($property){
    unset($this->$property);
}
```

### 4. __toString()

El método _**__toString()**_ permite devolver el objeto representado en forma de _string_.

```
public function __toString(){
    return $this->text;
}
$tweet = new Tweet(12, "Hola que tal");
echo $tweet;
```

Esto hace que cuando se llame al objeto como si fuera un string, como por ejemplo usando echo, el objeto devolverá lo que se defina en ___toString()_.

### 5. __sleep() y __wakeup()

El método _serialize()_ es una forma de guardar una **representación de un objeto**. Por ejemplo, si **guardamos un objeto en la base de datos**, primero tendríamos que **serializarlo**, guardarlo, y después si lo quisiéramos de vuelta tendríamos que usar _unserialize()_.

El método _**__sleep()**_ permite definir qué propiedades del objeto deberían serializarse ya que probablemente no quieras serializar cualquier objeto externo que no sea relevante en el momento de usar _unserialize()_.

Por ejemplo, creamos un nuevo objeto en el que necesitamos determinar una conexión a una base de datos:

```
$tweet = new Tweet(12, "Hola que tal", new PDO ('mysql:host=localhost;dbname=ejemplo', 'root', 'pass'));
```

Al serializar este objeto no nos interesa serializar también la conexión a la base de datos porque no nos será relevante.

El método _**__sleep()**_ es un método que recoge un array de propiedades que queremos que se serialicen:

```
public function __sleep() {
    return array('id', 'text');
}
```

Cuando queramos utilizar _unserialize()_ en el objeto, tendremos que establecer el objeto de nuevo a su estado normal. En este ejemplo hay que **reestablecer la conexión a la base de datos**, pero en realidad podría ser establecer cualquier cosa que el objeto debiera saber. Para ello se utiliza el método _**__wakeup()**_:

```
public function __wakeup() {
    $this->storage->connect();
}
```

### 6. __call() y __callStatic()

El método _**__call()**_ se activa cuando se intenta llamar a un método que no es accesible públicamente. Si tenemos un array de datos en el objeto que queremos mutar antes de devolver:

```
class Tweet {
    protected $id;
    protected $text;
    protected $meta;
    public function __construct($id, $text, array $meta){
        $this->id = $id;
        $this->text = $text;
        $this->meta = $meta;
    }
    protected function retweet(){
        $this->meta['retweets']++;
    }
    protected function favourite(){
        $this->meta['favourites']++;
    }
    public function __get($property){
        var_dump($this->$property);
    }
    public function __call($method, $parameters){
        if (in_array($method, array('retweet', 'favourite'))){
            return call_user_func_array(array($this, $method), $parameters);
        }
    }
}
$tweet = new Tweet(43, 'Hola que tal', array('retweets' => 23, 'favourites' => 17));
$tweet->retweet();
$tweet->meta; // array(2) { ["retweets"]=> int(24) ["favourites"]=> int(17) }
```

Las funciones _favourite()_ y _retweet()_son métodos _protected_. Con la función _**__call()**_ podemos acceder a ellos y opcionalmente pasarles parámetros con [call_user_func_array()](http://php.net/manual/es/function.call-user-func-array.php).

Otro escenario puede darse cuando hay un **objeto inyectado** que forma parte de la API públicamente accesible. Tenemos la clase Location:

```
class Location {
    protected $latitude;
    protected $longitude;
    public function __construct($latitude, $longitude){
        $this->latitude = $latitude;
        $this->longitude = $longitude;
    }
    public function getLocation(){
        return array(
            'latitude' => $this->latitude,
            'longitude' => $this->longitude
        );
    }
}

```

Y ahora la clase Tweet:

```
class Tweet {
    protected $id;
    protected $text;
    protected $location;
    public function __construct($id, $text, Location $location){
        $this->id = $id;
        $this->text = $text;
        $this->location = $location;
    }
    public function  __call($method, $parameters){
        if(method_exists($this->location, $method)) {
            return call_user_func_array(array($this->location, $method), $parameters);
        }
    }
}
```

En el ejemplo anterior hemos inyectado un objeto de la clase **Location** en **Tweet**, y podríamos hacer una llamada desde un objeto **Tweet** a _getLocation()_, delegando la llamada al objeto Location inyectado:

```
$tweet = new Tweet(34, 'Hola que tal', new Location('17.8912920598911', '-189.763546289918'));
var_dump($tweet->getLocation()); // array(2) { ["latitude"]=> string(16) "17.8912920598911" ["longitude"]=> string(17) "-189.763546289918" }
```

Si el **método** al que se quiere llamar es **static** se puede emplear _**__callStatic()**_. Este método funciona de la misma forma que __call() pero la sintaxis con la que llamar a los métodos será como a los [métodos estáticos](http://diego.com.es/late-static-binding-en-php), con **::**.

### 7. __clone()

Cuando se hace una **copia de un objeto en PHP** mediante su asignación a otra variable, ésta variable sigue haciendo referencia al mismo objeto. Esto significa que si cambias cualquier cosa un uno se cambiará en ambos dos:

```
$perro1 = new stdClass;
$perro2 = $objeto1;

$perro1->nombre = "Werthers";
$perro2->nombre = "John";

echo $perro1->nombre; // John
echo $perro2->nombre; // John
```

Para crear una copia del objeto independiente una de otra se usa **clone**:

```
$perro1 = new stdClass;
$perro2 = clone $perro1;

$perro1->nombre = "Werthers";
$perro2->nombre = "John";

echo $perro1->nombre; // Werthers
echo $perro2->nombre; // John
```

Pero cuando tenemos **objetos que están inyectados en el objeto**, esas dependencias se pasarán como referencia:

```
class Notification {
    protected $read = false;
    public function markAsRead(){
        $this->read = true;
    }
    public function isRead(){
        return $this->read == true;
    }
}
class Tweet {
    protected $id;
    protected $text;
    protected $notification;
    public function __construct($id, $text, Notification $notification){
        $this->id = $id;
        $this->text = $text;
        $this->notification = $notification;
    }
    public function  __call($method, $parameters){
        if(method_exists($this->notification, $method)) {
            return call_user_func_array(array($this->notification, $method), $parameters);
        }
    }
}

```

Probamos a clonar la clase Tweet:

```
// Creamos el tweet
$tweet1 = new Tweet(56, 'Hola que tal', new Notification);
// Clonamos el tweet
$tweet2 = clone $tweet1;
// Marcamos como leído el $tweet1
$tweet1->markAsRead();
// Vemos que ha afectado a los dos, los dos tweets se marcan como leídos
var_dump($tweet1->isRead()); // true
var_dump($tweet2->isRead()); // true
```

Para solucionar este problema podemos usar el método _**__clone()**_  para **clonar también cualquier objeto inyectado** cuando se use la clonación con _**clone**_:

```
class Tweet {
    protected $id;
    protected $text;
    protected $notification;
    public function __construct($id, $text, Notification $notification){
        $this->id = $id;
        $this->text = $text;
        $this->notification = $notification;
    }
    public function  __call($method, $parameters){
        if(method_exists($this->notification, $method)) {
            return call_user_func_array(array($this->notification, $method), $parameters);
        }
    }
    public function  __clone(){
        // Esto permitirá clonar tambien Notification cuando se haga clone
        $this->notification = clone $this->notification;
    }
}
```

Ahora obtendremos el resultado que queremos:

```
// Creamos el tweet
$tweet1 = new Tweet(123, 'Hello world', new Notification);
// Clonamos el tweet
$tweet2 = clone $tweet1;
// Marcamos el $tweet1 como leído
$tweet1->markAsRead();
// Comprovamos que sólo está leído el $tweet1
var_dump($tweet1->isRead()); // true
var_dump($tweet2->isRead()); // false
```

### 8. __invoke()

El método mágico _**__invoke()**_ permite **usar un objeto como si fuera una función**.

```
class User {
    protected $name;
    protected $timeline = array();
    public function __construct($name){
        $this->name = $name;
    }
    public function addTweet(Tweet $tweet){
        $this->timeline[] = $tweet;
    }
}
class Tweet {
    protected $id;
    protected $text;
    protected $read;
    public function __construct($id, $text){
        $this->id = $id;
        $this->text = $text;
        $this->read = false;
    }
    public function __invoke($user){
        $user->addTweet($this);
        return $user;
    }
}

```

Ahora podremos usar un objeto Tweet como **callable**:

```
// Array de usuarios
$users = array(new User('Pepa'), new User('Pepe'), new User('John'));
// Nuevo tweet
$tweet = new Tweet(53, 'Hola que tal');
// Aplica __invoke() en $users
$users = array_map($tweet, $users);
// Al mostrar los usuarios, todos tienen un array en el timeline con el mismo objeto, el tweet
var_dump($users);
// Aplicándolo sólo a un usuario
var_dump($tweet($users[0]));
```