PHP es considerado un lenguaje **loosely typed** o **dynamically typed**, lo que significa que el **tipo** de las variables se determinan por contexto. Si por ejemplo escribimos _$variable = 5_, PHP reconoce que es un _**integer**_. Por el contrario, si escribimos _$variable = "5"_, PHP lo identifica como un _**string**_. No es necesario indicar el tipo de variable que va a ser porque PHP lo va a identificar.

Cuando queremos **especificar el tipo de variable del que se trata**, o convertir de un tipo a otro de una variable ya existente, empleamos lo que se llama _**type casting**_. Esto es simplemente especificar el tipo de variable que se quiere obtener.

**Indice de contenido**

1.  Conversión de un array a un objeto
2.  Conversión de un objeto a un array
3.  Dificultades de conversión

### 1. Conversión de un array a un objeto

Cuando un _**array asociativo**_ se convierte en un _**object**_, las _**keys**_ serán el nombre de las propiedades y los _**values**_ serán los valores de dichas propiedades. Si se trata de un _**array numérico**_, las keys serán inaccesibles a no ser que sean recorridas.

```
$animales = [
    "perro" => "Spoofy",
    "gato" => "Lenders",
    "conejo" => "Tobby",
    "caballo" => "Trotter"
];
$objeto = (object) $animales;
var_dump($objeto);
foreach ($objeto as $animal => $nombre){
    echo ucfirst($animal) . ": " . $nombre . "<br>";
}
/*
object(stdClass)[1]
  public 'perro' => string 'Spoofy' (length=6)
  public 'gato' => string 'Lenders' (length=7)
  public 'conejo' => string 'Tobby' (length=5)
  public 'caballo' => string 'Trotter' (length=7)
Perro: Spoofy
Gato: Lenders
Conejo: Tobby
Caballo: Trotter
*/
```

Cualquier tipo de variable que se convierta a _**object**_, creará una **instancia de la clase stdClass**. Este nombre de clase está reservado, ya que es el objeto por defecto de PHP para este tipo de casos. 

Cuando se trata de arrays multidimensionales, no vale con el método anterior, es necesario hacerlo de forma recursiva:

```
$animales = [
    "perro" => ["nombre" => "Spoofy", "edad" => 10],
    "gato" => ["nombre" => "Lenders", "edad" => 9],
    "conejo" => ["nombre" => "Tobby", "edad" => 4],
    "caballo" => ["nombre" => "Trotter", "edad" => 10]
];
function arrayToObject($array) {
    if (is_array($array)) {
        return (object) array_map(__FUNCTION__, $array);
    }
    else {
        // Return object
        return $array;
    }
}
$objeto = arrayToObject($animales);
var_dump($objeto);
echo "Nombre del perro: " . $objeto->perro->nombre . "<br>";
echo "Edad del perro: " . $objeto->perro->edad . "<br>";
```

El ejemplo anterior devolverá un objeto con cada animal, cuyas propiedades public serán _nombre_ y _edad_.

### 2. Conversión de un objeto a un array

Para **convertir un objecto a un array**, se emplea el mismo método de _**type casting**_:

```
class Animales {
    public $perro = "Spoofy";
    public $gato = "Lenders";
    public $conejo = "Tobby";
    public $caballo = "Trotter";
}
$animales = new Animales;
$array = (array) $animales;
var_dump($array);
/*
 array (size=4)
  'perro' => string 'Spoofy' (length=6)
  'gato' => string 'Lenders' (length=7)
  'conejo' => string 'Tobby' (length=5)
  'caballo' => string 'Trotter' (length=7)
*/
```

De nuevo, para **convertir objetos en arrays multidimensionales**, es necesario emplear una **función recursiva**:

```
function objectToArray($objeto) {
    if (is_object($objeto)) {
        $objeto = get_object_vars($objeto);
    }
    if (is_array($objeto)) {
        return array_map(__FUNCTION__, $objeto);
    }
    else {
        return $objeto;
    }
}
$animales = new stdClass;
$animales->perro = "Spoofy";
$animales->gato = new stdClass;
$animales->gato->nombre = "Lenders";
$animales->gato->edad = "9";

$array = objectToArray($animales);
var_dump($array);

echo "El nombre del gato es: {$array["gato"]["nombre"]} <br>";// El nombre del gato es: Lenders
echo "La edad del gato es: {$array["gato"]["edad"]} <br>";// La edad del gato es: 9
```

### 3. Dificultades de conversión

Para cualquier tipo: _integer_, _float_, _string_, _boolean_ y _resource_, convertir un valor a un array resulta en un array con un sólo elemento de índice 0.

Si se **convierte un objeto en un array**, el resultado es un **array cuyos elementos son las propiedades del objeto**. Los _keys_ serán los nombres de las propiedades, con excepciones: las **variables privadas** tienen el nombre de la clase al comienzo del nombre de la variable, y las **variables protegidas** tienen un carácter * al comienzo del nombre de la variable. Ambas generan dos bytes adicionales a los lados del nombre, lo que causa comportamientos inesperados como la muestra del carácter de reemplazo de unicode � :

```
class ClaseUno {
    private $var = "Soy var de Uno"; //  Este campo se convertirá en '\0A\0A'
}
class ClaseDos extends ClaseUno {
    private $var = "Soy var de Dos"; // Este campo se convertirá en '\0B\0A'
    public $vor = "Soy vur"; // Este campo se convertirá en 'AA'
    protected $vur = "Soy vur";
}
var_dump((array) new ClaseDos());
/*
 Se genera:
array (size=4)
  '�ClaseDos�var' => string 'Soy var de Dos' (length=15)
  'vor' => string 'Soy vor' (length=7)
  '�*�vur' => string 'Soy vur' (length=7)
  '�ClaseUno�var' => string 'Soy var de Uno' (length=14)
 */
```

Para evitar esto se puede emplear un método para obtener las propiedades del objeto mediante [get_object_vars()](http://php.net/manual/es/function.get-object-vars.php):

```
class ClaseUno {
    private $a = "Soy a";
    protected $b = "Soy b";
    public $c = "Soy c";
    public function getProp(){
        return get_object_vars($this);
    }
}
$uno = new ClaseUno;
$vars = $uno->getProp();
foreach ($vars as $var){
    echo $var . "<br>";
} // Devuelve los valores de las tres propiedades: $a, $b y $c
```