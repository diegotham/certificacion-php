SPL es un **conjunto de interfaces y clases diseñado para recorrer estructuras agregadas**: arrays, resultados de bases de datos, árboles XML, etc. **SPL** funciona con **iteradores**, cuya principal **ventaja** es que se puede trabajar con gran cantidad de datos de forma eficiente.

Anteriormente hemos explicado ArrayIterator, DirectoryIterator y ArrayObject, ahora vamos a profundizar un poco más en estas y otras opciones que nos facilita la **bibloteca estándar de PHP**.

**Indice de contenido**

| | |
| -------- | -------- |
| 1. ArrayAccess | 6. LimitIterator |
| 2. Iterator | 7. NoRewindIterator |
| 3. IteratorAggregate | 8. SplFileObject |
| 4. AppendIterator | 9. SimpleXMLIterator |
| 5. FilterIterator | |

### 1. ArrayAccess

**ArrayAccess** es una interface de PHP que permite dictar cómo se comportará PHP cuando **un objeto tenga una sintaxis de array** (corchetes después del objeto). ArrayAccess sólo tiene 4 funciones a implementar: _offsetExists_, _offsetGet_, _offsetSet_ y _offsetUnset_. Para implementar la interface sólo hay que declarar estos métodos en la clase:

```
class Animal implements ArrayAccess {
    protected $animales;
    public $lugar;
    public function offsetExists($index){
        return isset($this->animales[$index]);
    }
    public function offsetGet($index){
        if($this->offsetExists($index)) {
            return $this->animales[$index];
        }
        return false;
    }
    public function offsetSet($key, $value){
        if($key) {
            $this->animales[$key] = $value;
        } else {
            $this->animales[] = $value;
        }
        return true;
    }
    public function offsetUnset($index){
        unset($this->animales[$index]);
        return true;
    }
    public function getAnimales() {
        return $this->animales;
    }
}

```

Podemos usar las propiedades y métodos de forma normal como en cualquier objeto, pero además podemos usar la sintaxis de array para manipular _$animales_:

```
$animal = new Animal;
$animal->lugar = "Asia y Oceania";
$animal[] = "Oso panda";
$animal[] = "Koala";
$animales = $animal->getAnimales();
foreach ($animales as $anim){
    echo $anim . "<br>";
}
/*
Devuelve:
Oso panda
Koala
*/
```

**ArrayAccess** es extendida por [ArrayObject](http://php.net/manual/es/class.arrayobject.php), por eso cuando se crea un objeto ArrayObject está disponible esta funcionalidad, además de [IteratorAggregate](#IteratorAggregate) (que veremos después), **Countable** y **Serializable**.

### 2. Iterator

Hay ocasiones en las que los iteradores no cubren unas necesidades específicas y es necesario crear un **iterador customizado**. **SPL** proporciona **interfaces** con las que poder hacerlo.

Para que un **objeto sea transitable en un loop foreach** debe ser una instancia de **Traversable**. No es posible implementar esta interface directamente, por lo que se utilizan las interfaces _**Iterator**_ o _**IteratorAggregate**_.

_**Iterator**_ permite crear objetos para ser iterados directamente o para crear iteradores externos. Especifica 5 métodos que hay que implementar: _rewind()_, _current()_, _key()_, _next()_ y _valid()_. Vamos a construir por partes la clase Animal:

```
class Animal implements Iterator {
    // Puntero que indica la posición actual
    protected $position = 0;
    // Array de animales
    protected $animales = [
        "Perro",
        "Gato",
        "Oso",
        "León"
    ];
```

El método _rewind()_ devuelve el puntero al principio para reiniciar la iteración:

```
public function rewind(){
    echo "Reiniciando <br>";
    $this->position = 0;
}
```

El método _current()_ devuelve el valor actual del elemento en la posición actual:

```
public function current(){
    echo "Posición actual <br>";
    return $this->animales[$this->position];
}
```

El método _key()_ devuelve el valor actual del puntero:

```
public function key(){
    echo "Key <br>";
    return $this->position;
}
```

El método _next()_ mueve el puntero a la próxima posición:

```
public function next(){
    echo "Next <br>";
    ++$this->position;
}
```

Y finalmente el método _valid()_ devuelve un booleano indicando si hay datos en la posición actual:

```
    public function valid(){
        echo "Valido <br>";
        return isset($this->animales[$this->position]);
    }
}
```

Instanciamos la clase y hacemos un foreach:

```
$animales = new Animal;
foreach ($animales as $key=>$value){
    echo $key . ":" . $value . "<br>";
}
```

Cuando la iteración comienza, PHP llama a _rewind()_ para llevar el puntero al inicio. Entonces comprueba si hay algún **dato válido** en ese punto llamando a _valid()_. Si es **true**, llama a _current()_ para mostrar el **valor** en ese punto de la iteración. Como estamos usando el $key también en el foreach, PHP llama a _key()_ para obtener el valor del **key** actual. PHP llamará entonces a _next()_ para **avanzar el puntero** y a _valid()_ para comprobar si es **válido** este nuevo elemento. El ciclo continúa hasta que _valid()_ devuelve **false**.

Iterator posibilita el poder implementar iteradores muy potentes. Para el ejemplo anterior que es un simple array, sería mejor emplear **IteratorAggregate**.

### 3. IteratorAggregate

**IteratorAggregate** requiere implementar sólo un método, _getIterator()_. Este método devuelve un iterador externo que se usará para la **iteración**. Si escribimos el ejemplo anterior empleando _**IteratorAggregate**_:

```
class Animal implements IteratorAggregate {
    protected $animales = [
        "Perro",
        "Gato",
        "Oso",
        "León"
    ];
    // Devuelve un Iterator de los datos
    public function getIterator(){
        echo "getIterator <br>";
        return new ArrayIterator($this->animales);
    }
}
$animales = new Animal();
foreach ($animales as $key=>$value){
    echo $key . ":" . $value . "<br>";
}
```

Al principio de la iteración, PHP llama a _getIterator()_, que devuelve un iterador, en este caso el iterador SPL ArrayIterator. PHP entonces hace las llamadas pertinentes en ese iterador. Básicamente dejas los datos en manos del iterador externo que hace el trabajo por tí.

**Iterator** e **IteratorAggregate** tienen la misma funcionalidad, **Iterator** permite definir el comportamiento de la iteración para ocasiones en las que se necesita mayor customización e **IteratorAggregate** es un recurso más simple y rápido.

### 4. AppendIterator

Cuando se tienen múltiples elementos sobre los que se quiere iterar de forma conjunta se emplea **AppendIterator**. El siguiente ejemplo crea dos objetos **ArrayObject** y un iterador **AppendIterator**.

```
$domesticos = array("perro", "gato", "conejo");
$salvajes = array("oso", "tiburón", "tigre");
$objetoDom = new ArrayObject($domesticos);
$objetoSal = new ArrayObject($salvajes);
$iterador = new AppendIterator();
$iterador->append($objetoDom->getIterator());
$iterador->append($objetoSal->getIterator());
foreach ($iterador as $animal){
    echo $animal . "<br>";
}

```

Con **AppendIterator** se pueden recolectar elementos desde diferentes objetos y mostrarlos de vez.

Un ejemplo algo más complejo es en un supuesto **CMS** donde cada **tipo** (enlace, artículo, etc) extiende una **clase abstracta Elemento** (ya que todos los tipos en este CMS han de ser del mismo elemento), se crean colecciones de elementos que coleccionan N elementos y nos proporcionan un iterador para iterar sobre los tipos que se puedan añadir. Vamos a crear por partes las clases:

*   Clase abstracta Element y las clases Link y Article:

```
abstract class Element {
    public $title;
}
class Link extends Element {
    public function __construct($title) {
        $this->title = $title;
    }
}
class Article extends Element {
    public function __construct($title) {
        $this->title = $title;
    }
}
```

*   Clase abstracta ElementCollection:

```
abstract class ElementCollection implements IteratorAggregate {
    private $elements;
    public function __construct() {
        $this->elements = new ArrayObject();
    }
    public function addElement(Element $module) {
        $this->elements->append($module);
    }
    public function getIterator() {
        return $this->elements->getIterator();
    }
}
```

*   Clase ArticleCollection:

```
class ArticleCollection extends ElementCollection {
    public function __construct() {
        parent::__construct();
        $this->collectArticles();
    }
    private function collectArticles() {
        // Crear artículos
        $a = new Article("Primer artículo");
        $b = new Article("Segundo artículo");
        $this->addElement($a);
        $this->addElement($b);
    }
}
```

*   Y finalmente la clase LinkCollection:

```
class LinkCollection extends ElementCollection {
    public function __construct() {
        parent::__construct();
        $this->collectLinks();
    }
    private function collectLinks() {
        // Crear enlaces
        $a = new Link("Primer enlace");
        $b = new Link("Segundo enlace");
        $this->addElement($a);
        $this->addElement($b);
    }
}
```

Ahora vamos a crear dos colecciones y utilizamos AppendIterator para usar _append()_ en las dos y unirlas. Ahora podemos iterar sobre las dos colecciones y mostrar el elemento común _$title_.

```
$article = new ArticleCollection();
$link = new LinkCollection();
$it = new AppendIterator();
$it->append($article->getIterator());
$it->append($link->getIterator());
foreach($it as $module){
    echo $module->title . "<br>";
}
/*
Devuelve:
Primer artículo
Segundo artículo
Primer enlace
Segundo enlace
*/
```

### 5\. FilterIterator

**FilterIterator** es una clase abstracta que nos permite devolver sólo los elementos que queramos, mediante el método _accept()_.

```
class Filter extends FilterIterator {
    // FilterIterator necesita un iterador como parámetro
    public function __construct(Iterator $it){
        parent::__construct($it);
    }
    // Cuando iteremos sobre el array se llamará a la siguiente función
    // para comprobar si aceptar o no la key actual
    public function accept(){
        // Sólo queremos los animales que empiecen por c:
        if(preg_match("/^c/", $this->getInnerIterator()->current())){
            return true;
        }
        return false;
    }
}
$animales = array("perro", "conejo", "pantera", "cerdo", "gato", "caballo");
$objeto = new ArrayObject($animales);
$iterador = new Filter($objeto->getIterator());
foreach ($iterador as $key=>$value){
    echo "$key : $value" . "<br>";
}
```

Nos ha devuelto sólo conejo, cerdo y caballo. FilterIterator es muy útil si se quiere comprobar el elemento antes de devolverlo. Observar que se obtienen los mismos resultados si en lugar de emplear ArrayObject se emplea ArrayIterator (de esta forma no hay que llamar a getIterator):

```
$objeto = new ArrayIterator($animales);
$iterador = new Filter($objeto);
```

### 6. LimitIterator

Cuando **tenemos muchos resultados** pero sólo queremos mostrar parte de ellos podemos crear un **LimitIterator**, una clase que controla cuantos elementos hay en el iterador y muestra el número que especifiques en el constructor:

```
$animales = array("perro", "conejo", "pantera", "cerdo", "gato", "caballo");
$objeto = new ArrayObject($animales);
/*
El LimitIterator toma un iterador como primer parámetro, después
dónde deseas empezar y finalmente cuántos deseas mostrar
*/
$iterador = new LimitIterator($objeto->getIterator(), 2, 3);
foreach($iterador as $key=>$value){
    echo "$key : $value" . "<br>";
}
/*
Devuelve:
2 : pantera
3 : cerdo
4 : gato
*/
```

Vamos a crear ahora tres páginas donde mostramos en cada una sólo un **animal**:

```
$animales = array("perro", "conejo", "pantera", "cerdo", "gato", "caballo");
$objeto = new ArrayObject($animales);
$pagina = 0;
$porPagina = 1;
for ($i = 0; $i < 5; $i++){
    echo "Pagina: $pagina <br>";
    $iterador = new LimitIterator($objeto->getIterator(), $pagina, $porPagina);
    foreach ($iterador as $key=>$value){
        echo "$key : $value <br>";
    }
    $pagina++;
}
/*
Devuelve:
Pagina: 0
0 : perro
Pagina: 1
1 : conejo
Pagina: 2
2 : pantera
Pagina: 3
3 : cerdo
Pagina: 4
4 : gato
*/
```

### 7. NoRewindIterator

**NoRewindIterator** es un iterador que no llama a _rewind()_, por lo que sólo se puede usar una vez:

```
$animales = array("perro", "conejo", "pantera");
$objeto = new ArrayObject($animales);
$iterador = new NoRewindIterator($objeto->getIterator());
foreach ($iterador as $animal){
    echo $animal . "<br>";
}
foreach ($iterador as $animal){
    echo $animal . "<br>";
}
foreach ($iterador as $animal){
    echo $animal . "<br>";
}
/*
Devuelve:
perro
conejo
pantera
*/
```

Sólo devuelve el _**array**_ una vez. Si hubiéramos hecho el foreach con _$objeto_ en lugar de con _$iterador_, el array se hubiera mostrado tres veces.

### 8. SplFileObjet

**SplFileObject** proporciona una interface orientada a objetos para manejar archivos.

Para iterar sobre un archivo se puede hacer así:

```
$lineas = file("archivo.txt");
foreach ($lineas as $num => $lin){
    echo "Linea: $num -> $lin" . "<br>";
}
```

Con **SplFileObject** podemos tratar el archivo como si fuera un objeto y usar iteradores SPL para recorrer el archivo:

```
try {
    $file = new SplFileObject("archivo.txt");
    while ($file->valid()){
        echo $file->current() . "<br>";
        $file->next();
    }
} catch (Exception $e){
    echo $e->getMessage();
}
```

[SplFileObject](http://php.net/manual/es/class.splfileobject.php) también dispone de muchas otras funciones disponibles para archivos: _getFilename_, _getPath_, _getPerms_, _getSize_, _isWritable_, _isDir_, _openFile_, etc.

### 9. SimpleXMLIterator

El iterador **SimpleXMLIterator** acepta un **documento XML** y lo recorre como cualquier iterador con otra estructura agregada. Extiende a **SimpleXMLElement**, y es un **iterador recursivo**. Si empleamos el siguiente código podremos ver las funciones de las que dispone:

```
foreach (get_class_methods('SimpleXMLIterator') as $key => $metodo){
    echo $key . '->'. $metodo . "<br>";
}
```

La **lista de funciones de SimpleXMLIterator** es la siguiente:

* 0 -> rewind
* 1 -> valid
* 2 -> current
* 3 -> key
* 4 -> next
* 5 -> hasChildren
* 6 -> getChildren
* 7 -> __construct
* 8 -> asXML
* 9 -> saveXML
* 10 -> xpath | 11 -> registerXPathNamespace
* 12 -> attributes
* 13 -> children
* 14 -> getNamespaces
* 15 -> getDocNamespaces
* 16 -> getName
* 17 -> addChild
* 18 -> addAttribure
* 19 -> __toString
* 20 -> count

Vamos a poner un ejemplo. El string XML es como sigue:

```
$xmlString = <<<XML
<?xml version = "1.0" encoding="UTF-8" standalone="yes"?>
<document>
<animal>
<categoria id="2">
<tipo>Koala</tipo>
<nombre>Bruce</nombre>
</categoria>
</animal>
<animal>
<categoria id="4">
<tipo>Gato</tipo>
<nombre>Whiskas</nombre>
</categoria>
</animal>
</document>
XML;
```

Ahora vamos a emplear SimpleXMLIterator sobre el string XML:

```
<?php
try {
    $iterador = simplexml_load_string($xmlString, 'SimpleXMLIterator');
    foreach(new RecursiveIteratorIterator($iterador, 1) as $nombre => $dato){
        echo "$nombre -- $dato <br>";
    }
} catch (Exception $e){
    echo $e->getMessage();
}
/*
Devuelve:
animal --
categoria --
tipo -- Koala
nombre -- Bruce
animal --
categoria --
tipo -- Gato
nombre -- Whiskas
*/
?>
```

**SimpleXMLIterator** es recursivo, por lo que hemos necesitado un iterador mediante **RecursiveIteratorIterator**, que itera sobre un iterador recursivo. 

Normalmente necesitaremos más control:

```
try {
    $sxi = new SimpleXMLIterator($xmlString);
    foreach($sxi as $nodo){
        foreach($nodo as $key=>$value){
            echo $value->nombre . "<br>";
        }
    }
} catch (Exception $e){
    echo $e->getMessage();
}
/*
Devuelve:
Bruce
Whiskas
*/
```

En el ejemplo anterior hemos instanciado la clase **SimpleXMLIterator**. Después hemos tenido que emplear dos loops foreach para devolver el nombre correcto de los animales.

Podemos hacerlo de otra forma:

```
try {
    $sxs = simplexml_load_string($xmlString, 'SimpleXMLIterator');
    for ($sxs->rewind(); $sxs->valid(); $sxs->next()){
        if($sxs->hasChildren()){
            foreach ($sxs->getChildren() as $el=>$val){
                echo $val->nombre . "<br>";
            }
        }
    }
} catch (Exception $e){
    echo $e->getMessage();
}
/*
Devuelve:
Bruce
Whiskas
*/
```

Pero esta forma todavía es más lenta y engorrosa. En lugar de los dos métodos anteriores se puede usar el método _**SimpleXMLIterator::xpath()**_, que permite una iteración directa sobre un árbol XML especificando el xpath al nodo. Similar a un directorio de un ordenador, el xpath del nombre de los animales es: _animal/categoria/nombre_.

Especificar el xpath de un particular nodo permite que podamos ahorrarnos muchas iteraciones del árbol XML, lo que ahorra ciclos CPU y hace scripts más rápidos. El siguiente código devolverá lo mismo que los dos anteriores, pero utilizará menos recursos:

```
try {
    $sxi = new SimpleXMLIterator($xmlString);
    $xpath = $sxi->xpath('animal/categoria/nombre');
    foreach ($xpath as $key=>$value){
        echo $value . "<br>";
    }
} catch (Exception $e){
    echo $e->getMessage();
}
/*
Devuelve:
Bruce
Whiskas
*/
```