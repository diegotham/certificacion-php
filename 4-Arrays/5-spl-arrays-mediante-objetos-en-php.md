**Indice de contenido**

1.  Introducción
2.  ¿Por qué utilizar iteradores SPL?
3.  ArrayIterator
4.  DirectoryIterator
5.  ArrayObject

### 1. Introducción

La **biblioteca SPL**, **Standard PHP Library**, es un conjunto de **interfaces y clases para PHP**. Fue diseñada para recorrer estructuras agregadas: arrays, resultados de bases de datos, árboles XML, listados de directorios o cualquier otro tipo de listado.

SPL trabaja con **iteradores**. La **iteración** es el proceso de recorrer una lista de elementos. Un **iterador** es simplemente un objeto que recorre una lista. La biblioteca viene con un gran listado de clases, para verlas todas, puedes emplear la función _spl_classes()_:

```
foreach(spl_classes() as $key => $value)
{
    echo $key.' -> '.$value.'<br>';
}
```

### 2. ¿Por qué utilizar Iteradores SPL?

Los **objetos iteradores** realizan la misma función que cuando se itera con arrays, pero a diferencia de éstos, pueden albegar una gran cantidad de datos y organizarlos más eficientemente.

El **loop foreach** hace una copia de cualquier array que se le pase, por lo que si son muchos los datos que se tienen que iterar, hacer una copia de cada array cada vez que se usen en un foreach puede resultar muy costoso. Los **iteradores SPL encapsulan las listas de datos** y muestran un elemento cada vez haciéndolo mucho más eficiente. 

Los iteradores también facilitan el **lazy loading**, es decir, devuelven los datos sólo cuando se necesita. También se pueden manipular los datos antes de devolverlos al usuario. 

**Usar o no usar iteradores SPL** es una decisión que depende del **proyecto** en el que se esté trabajando. Normalmente cuando se trabaja con una gran cantidad de datos es más conveniente usarlos.

### 3. ArrayIterator

El constructor de la clase **ArrayIterator** recibe un array como parámetro y proporciona métodos con los que iterar mediante el objeto creado:

```
$animales = ["perro", "gato", "liebre", "toro", "conejo"];
// Se crea un nuevo ArrayIterator y se pasa el array
$iterador = new ArrayIterator($animales);
// Se hace loop con el objeto
foreach ($iterador as $key => $value) {
    echo $key . ":  " . $value . "<br>";
}
/*
0:  perro
1:  gato
2:  liebre
3:  toro
4:  conejo
*/
```

Normalmente se utiliza **ArrayObject**, una **clase que permite trabajar con objetos como si fueran arrays**. Esta clase crea directamente un **ArrayIterator** cuando se usa un _loop foreach_ o cuando se llama al método **ArrayIterator::getIterator()**. 

Conviene apuntar que tanto ArrayObjet como ArrayIterator son **objetos**, y no se pueden usar funciones de arrays en ellos.

ArrayIterator está limitado a arrays unidimensionales, de un sólo nivel. Para poder utilizarlo en arrays multidimensionales se puede utilizar **RecursiveArrayIterator**. 

Una forma de **mostrar los elementos anidados en un array con un foreach** es la siguiente:

```
$animales = [
    "perro" => ["Sparky"],
    "gato" => ["Bear"],
    "liebre" => ["Bunny"],
    "toro" => ["Hunk"],
    "conejo" => "Juliet"
];
// Loop a través del array
foreach ($animales as $key => $value) {
    // Comprobar si tiene subnivel
    if (is_array($value)) {
        foreach ($value as $k => $v) {
            echo $k . ": " . $v . "<br>";
        }
    }
    else {
        echo $key . ": " . $value . "<br>";
    }
}
/*
0: Sparky
0: Bear
0: Bunny
0: Hunk
conejo: Juliet
*/
```

Una forma más elegante de hacerlo es utilizando **RecursiveArrayIterator**:

```
$animales = [
    "perro" => ["Sparky"],
    "gato" => ["Bear"],
    "liebre" => ["Bunny"],
    "toro" => ["Hunk"],
    "conejo" => "Juliet"
];
$iterador = new RecursiveArrayIterator($animales);
foreach(new RecursiveIteratorIterator($iterador) as $key => $value) {
    echo $key . ": " . $value . "<br>";
}
/*
0: Sparky
0: Bear
0: Bunny
0: Hunk
conejo: Juliet
*/
```

Es necesario crear una instancia de **RecursiveIteratorIterator** y pasarle el objeto **RecursiveArrayIterator** que se ha creado con anterioridad. 

Cuando se trabaja con arrays multidimensionales conviene usar **RecursiveArrayIterator**. **RecursiveIteratorIterator** es el **decorador** que permite iterar por los diferentes niveles, itera el array de RecursiveArrayIterator e itera también cualquier otro array que se vaya encontrando como valor. Se puede **obtener la profundidad de los niveles de un array** con **RecursiveIteratorIterator::getDepth()**. Hay que tener en cuenta que **los objetos también son iterables**, por lo que si un elemento del array es un objeto, será iterado.

### 4. DirectoryIterator

Para **recorrer directorios y sus archivos en PHP** ya existen funciones específicas como _scandir()_ o _glob()_, pero también es posible utilizar **DirectoryIterator**.

```
// Se crea un nuevo objeto DirectoryIterator
$directorio = new DirectoryIterator("/Mi/directorio");
// Se hace un loop a través del directorio
foreach ($directorio as $item) {
    echo $item . "<br>";
}
```

Con este **iterador**, al igual que con cualquiera de los **iteradores SPL**, se pueden utilizar excepciones de esta forma:

```
try {
    $directorio = new DirectoryIterator("/un/directorio/que/no/existe");
    foreach ($directorio as $item) {
        echo $item . "<br>";
    }
}
catch (Exception $e) {
    echo get_class($e) . ": " . $e->getMessage();
}
/*
UnexpectedValueException: DirectoryIterator::__construct(/un/directorio/que/no/existe):
failed to open dir: No such file or directory
 */
```

Existen métodos muy útiles para tener información básica de cualquier directorio: **DirectoryIterator::isDot()**, **DirectoryIterator::getType()** o **DirectoryIterator::getSize()**. Se puede combinar también **DirectoryIterator** con otros iteradores como **FilterIterator** o **RegexIterator** para devolver archivos con unos criterios específicos:

```
class FileExtensionFilter extends FilterIterator
{
    // Lista de extensiones permitidas
    protected $ext = ["php", "txt"];
    // Método abstract que debe ser implementado en la subclase
    public function accept() {
        return in_array($this->getExtension(), $this->ext);
    }
}
// Crear un nuevo iterador:
$directorio = new FileExtensionFilter(new DirectoryIterator("/mi/directorio"));
// Mostrar sólo los archivos php y txt
foreach ($directorio as $key){
    echo $key . "<br>";
}
```

**SPL** también proporciona **RecursiveDirectoryIterator**, que se puede usar de la misma forma que **RecursiveArrayIterator**. RecursiveDirectoryIterator puede facilitar el trabajo de iterar directorios recursivamente, además de que queda un código mucho más limpio. La única pega es que no muestra directorios vacíos, sólo los que tienen archivos:

```
// Se crea un nuevo objeto RecursiveDirectoryIterator
$iterador = new RecursiveDirectoryIterator("/mi/directorio/");
// Se crea instancia de RecursiveIteratorIterator
foreach (new RecursiveIteratorIterator($iterador) as $item) {
    echo $item . "<br>";
}
```

### 5. ArrayObject

**ArrayObject** permite el **recorrido externo de arrays** y **crear instancias de ArrayIterator**. Podemos ver los métodos de la clase de esta forma:

```
foreach(get_class_methods(new ArrayObject()) as $key=>$method)
{
    echo $key.' -> '.$method.'<br>';
}
```

Los métodos de los que dispone ArrayObject son los siguientes:

* 0 -> __construct
* 1 -> offsetExists
* 2 -> offsetGet
* 3 -> offsetSet
* 4 -> offsetUnset
* 5 -> append
* 6 -> getArrayCopy
* 7 -> count
* 8 -> getFlags
* 9 -> setFlags
* 10 -> asort
* 11 -> ksort | 12 -> uasort
* 13 -> uksort
* 14 -> natsort
* 15 -> natcasesort
* 16 -> unserialize
* 17 -> serialize
* 18 -> getIterator
* 19 -> exchangeArray
* 20 -> setIteratorClass
* 21 -> getIteratorClass 

**ArrayIterator** tiene los 15 primeros métodos iguales a **ArrayObject**, se diferencian en los seis últimos. **ArrayIterator** tiene: rewind(), current(), key(), next(), valid() y seek(), lo que le permite iterar por sí solo. **ArrayObject** necesita crear una instancia de **ArrayIterator** para iterar, y lo hace mediante _getIterator()_.

Veamos un ejemplo:

```
$animales = array('perro', 'gato', 'conejo', 'cerdo', 'caballo');
// Se crea el objeto array:
$objeto = new ArrayObject($animales);
// Podemos añadir otro elemento al array
$objeto->append('canguro');
// También podemos ordenarlo como queramos, por ejemplo, alfabéticamente:
$objeto->natcasesort();
// Se itera a través del array:
for($iterador = $objeto->getIterator();
    // Comprobar si es válido
    $iterador->valid();
    // Moverse al siguiente elemento
    $iterador->next())
{
    // Mostrar el key y value actuales
    echo $iterador->key() . ' => ' . $iterador->current() . '<br />';
}
```

En el ejemplo anterior se ha recorrido externamente el objeto del array con _getIterator()_. Podríamos haber usado un _**foreach**_ y el método _getInstance()_ se hubiera llamado automáticamente. Los métodos _key()_ y _current()_ pertenecen a la instancia de **ArrayIterator.**

**ArrayObject::getIterator** devuelve una instancia de **ArrayIterator** desde la instancia original de **ArrayObject**.

Al ordenar los resultados con _natcasesort()_ podemos ver que el resultado ha ordenado los valores alfabéticamente, pero los keys se han mantenido tal cual:
```
4 => caballo
5 => canguro
3 => cerdo
2 => conejo
1 => gato
0 => perro

```

Podemos emplear la función _count()_ para contar el número de items:

```
$animales = array('perro', 'gato', 'conejo', 'serpiente', 'caballo');
$objeto = new ArrayObject($animales);
echo $objeto->count(); // Devuelve 5
```

También es posible **remover un ítem del array** anterior empleando _offsetUnset($elemento)_, donde _$elemento_ es el ítem que queremos eliminar. Vamos a eliminar el único que no es mamífero:

```
$animales = array('perro', 'gato', 'conejo', 'serpiente', 'caballo');
$objeto = new ArrayObject($animales);

$objeto->offsetUnset(3);

for($iterador = $objeto->getIterator(); $iterador->valid(); $iterador->next()){
    echo $iterador->key() . " -> " . $iterador->current() . "<br>";
}
/*
0 -> perro
1 -> gato
2 -> conejo
4 -> caballo
 */
```

Vemos que el array no se reindexa, desaparece serpiente pero los demás _keys_ mantienen su número. Para comprobar que existe un offset se emplea _offsetExists()_:

```
$animales = array('perro', 'gato', 'conejo', 'serpiente', 'caballo');
$objeto = new ArrayObject($animales);

if ($objeto->offsetExists(3)) echo "Offset existe"; // Devuelve Offset existe
```

También podemos **cambiar el valor de cualquier elemento**, con _offsetSet()_. Vamos a insertar un nuevo animal mamífero en el lugar que ha quedado vacío:

```
$animales = array('perro', 'gato', 'conejo', 'serpiente', 'caballo');

try {
    $objeto = new ArrayObject($animales);

    $objeto->offsetSet(3, "oso");

    for($iterador = $objeto->getIterator(); $iterador->valid(); $iterador->next())
    {
        echo $iterador->key() . ' -> ' . $iterador->current() . '<br>';
    }
}
catch (Exception $e)
{
    echo $e->getMessage();
}
```

También existe _offsetGet()_ para **obtener el valor de un elemento del array**:

```
$animales = array('perro', 'gato', 'conejo', 'serpiente', 'caballo');

try {
    $objeto = new ArrayObject($animales);

    echo $objeto->offsetGet(4); // Devuelve caballo
}
catch (Exception $e)
{
    echo $e->getMessage();
}

```

Si queremos **copiar un array para editar o hacer comparaciones**, podemos emplear _getArrayCopy()_:

```
$animales = array('perro', 'gato', 'conejo', 'serpiente', 'caballo');
$objeto = new ArrayObject($animales);
$copiaArray = $objeto->getArrayCopy();
```

Hay que tener en cuenta que lo anterior es una **copia del array**, no es una copia del objeto, por lo que dará **error** si queremos hacer algo como _**$copiaArray->getIterator()**_. Para ello deberemos crear de nuevo un objeto con la copia:

```
$animales = array('perro', 'gato', 'conejo', 'serpiente', 'caballo');
$objeto = new ArrayObject($animales);
$copiaObjeto = new ArrayObject($objeto->getArrayCopy());
```

La clase también viene con algunas _**flags**_ o banderas: **STD_PROP_LIST** y **ARRAY_AS_PROPS**, que permiten configurar el registro y la salida de los datos en la clase **ArrayObject**.

STD_PROP_LIST nos dice como leer, y ARRAY_AS_PROPS nos dice como debemos escribir los elementos creados en el array de la clase **ArrayObject**. Si guardamos elementos a través de la notación normal de **array[]**, siempre funcionará de la misma forma, independientemente de los flags:

```
$a = new ArrayObject();
$a['key'] = 'Valor del elemento array';
$a->propiedad = 'Valor de la propiedad de objeto';
var_dump($a);
echo "Foreach:" . "<br>";
foreach ($a as $wa){
    echo $wa . "<br>";
}
/*
object(ArrayObject)[1]
  public 'propiedad' => string 'Valor de la propiedad de objeto' (length=31)
  private 'storage' =>
    array (size=1)
      'key' => string 'Valor del elemento array' (length=24)
Foreach:
Valor del elemento array
*/
```

Cuando configuramos **ARRAY_AS_PROPS** significa que cuando guardas **propiedades** no se guardarán en el objeto como se haría con objetos normales, se guardarán como **elementos internos**. Esto hace que aparezcan también en un _**foreach()**_. Si no se configura esta bandera, se guardará la propiedad en el objeto del array (como en el ejemplo anterior). Veamos el ejemplo con ARRAY_AS_PROPS:

```
$b = new ArrayObject(array(), ArrayObject::ARRAY_AS_PROPS);
$b['key'] = 'Valor del elemento array';
$b->propiedad = 'Valor de la propiedad de objeto';
var_dump($b);
echo "Foreach:" . "<br>";
foreach ($b as $we){
    echo $we . "<br>";
}
/*
object(ArrayObject)[1]
  private 'storage' =>
    array (size=2)
      'key' => string 'Valor del elemento array' (length=24)
      'propiedad' => string 'Valor de la propiedad de objeto' (length=31)
Foreach:
Valor del elemento array
Valor de la propiedad de objeto
*/
```

La bandera **STD_PROP_LIST** decide qué devolver bajo ciertas condiciones, sobre todo con _**var_dump()**_, aunque existen otros casos. Si se configura **ArrayObject** con STD_PROP_LIST, devolverá las propiedades que se han establecido en **ArrayObject** o **ArrayIterator**. Si no se han establecido, var_dump() devolverá una lista de los elementos internos que se hayan guardado. 

```
$c = new ArrayObject(array(), ArrayObject::STD_PROP_LIST);
$c['key'] = 'Valor del elemento array';
$c->propiedad = 'Valor de la propiedad de objeto';

var_dump($c);
echo "Foreach:" . "<br>";
foreach ($c as $wi){
    echo $wi . "<br>";
}
/*
object(ArrayObject)[1]
  public 'propiedad' => string 'Valor de la propiedad de objeto' (length=31)
  private 'storage' =>
    array (size=1)
      'key' => string 'Valor del elemento array' (length=24)
Foreach:
Valor del elemento array
*/
```