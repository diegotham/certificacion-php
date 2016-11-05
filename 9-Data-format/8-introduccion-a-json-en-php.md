**JSON** (_JavaScript Object Notation_) es un formato de intercambio de datos ligero y a la vez legible para humanos (como [XML](http://diego.com.es/xml-principios-basicos) pero sin el marcado). Su sintaxis es un subconjunto del lenguaje **JavaScript** que fue estandarizado en 1999.

Lo bueno de JSON es que **se puede manejar de forma nativa en JavaScript**, por lo que actúa como un pegamento entre el servidor y la aplicación del lado del cliente. Además, como sintácticamente es muy simple, se tienen que transmitir menos **bytes** en cada transacción. En **aplicaciones modernas JSON** ha sustituído en bastantes situaciones a XML, y **frameworks** del lado del cliente como _backbone.js_ lo utilizan mucho en sus estructuras.

**Indice de contenido**

1.  Introducción a JSON
2.  Codificar JSON en PHP
3.  Decodificar JSON en PHP
4.  Manejo de errores y testing

### 1. Introducción a JSON

Al ser **JSON** un subconjunto del lenguaje **JavaScript**, comparte algunas construcciones del lenguaje. En JSON se pueden guardar combinaciones desordenadas de _**keys**_ y _**values**_ en objetos o utilizar _**arrays**_ para preservar el orden de los valores. Esto lo hace fácil de analizar y de leer, pero también tiene algunas limitaciones. Ya que JSON sólo define una cantidad pequeña de _**data types**_, no se pueden transmitir tipos como **fechas** de forma nativa (se puede, pero es necesario transformarlos en un _string_ o en un _unix timestamp_ como un integer).

Los **datatypes** que soporta **JSON** son: _strings_, _números_, _booleanos_ y _null_, además de soportar objetos y arrays como valores.

```
{
  "titulo": "Este es un artículo",
  "visitas": 345,
  "publicado": true,
  "categoria": null,
  "comentarios": [
    {
      "autor": "Luisa López",
      "mensaje": "Muy buen artículo"
    },
    {
      "autor": "Carlos Pérez",
      "mensaje": "Artículo muy malo"
    }
  ]
}
```

El ejemplo anterior contiene más o menos todas las **posibilidades de JSON**. Es necesario asegurarse de que el documento **JSON está codificado en UTF-8**, lo haremos después con PHP. Se desarrolló también BSON (Binary JSON), diseñado para ser más eficiente y proveer características como soporte de fechas. BSON se utiliza por ejemplo en MongoDB. 

Ya que **PHP tiene un manejo de tipos bastante más extenso que JSON**, es necesario codificar y decodificar de forma adecuada los documentos JSON en PHP con las modificaciones pertinentes. Por ejemplo si queremos transportar objetos fecha, hay que elegir entre enviar un unix timestamp o preformatear la fecha en un _string_ con alguna función como _strftime()_.

### 2. Codificar JSON en PHP

Con [json_encode](http://php.net/manual/es/function.json-encode.php) se puede traducir cualquier cosa codificada en **UTF-8** (salvo _**resources**_) de PHP a un _string_ JSON. Todo salvo _arrays_ puros (éstos en PHP son arrays con un index numérico y ordenado) se convierte en un **objeto** con _keys_ y _values_.

```
json_encode (mixed $value, int $options = 0);
```

Las opciones _$options_ son un **bitmask**, que permite cambiar la forma en que se codifican los datos. En los siguientes ejemplos no lo vamos a emplear, por lo que se utilizan los valores por defecto.

Empezando con los **tipos básicos**:

```
json_encode(array("Oso", "Perro", "Gato"));
// Devuelve: ["Oso", "Perro", "Gato"];
json_encode(array(2=>"dos", 10=>"diez"));
// Devuelve {"2":"dos", "10"=>"diez"}
json_encode(array("Oso"=> true, "Gato"=>null));
// Devuelve: {"Oso":true, "Gato":null}
```

Cómo se traducen los _arrays_ depende del index que se utilice. Puede verse que _json_encode()_ traduce los tipos correctamente en el caso de _**booleano**_ y _**null**_. 

```
class Usuario {
    public $nombre = "";
    public $apellido = "";
    public $fechaNacimiento = "";
}
$usuario = new Usuario;
$usuario->nombre = "Angelina";
$usuario->apellido = "Jolie";
json_encode($usuario);
// Devuelve: {"nombre": "Angelina", "apellido": "Jolie"}
$usuario->fechaNacimiento = new DateTime();
json_encode($usuario);
/*
Devuelve:
{
    "nombre":"Angelina",
    "apellido":"Jolie",
    "fechaNacimiento":{
        "date":"2015-10-10 10:31:22",
        "timezone_type":3,
        "timezone":"Europe\/Berlin"
    }
}
*/
```

Los objetos se inspeccionan y sus atributos públicos se convierten. Esto ocurre de forma recursiva, por lo que en el ejemplo anterior los atributos del objeto DateTime también se traducen en JSON. Esta es una forma de transmitir fácilmente fechas en JSON, el lado del cliente podrá tomar el **tiempo real** y el **timezone**. 

```
class Usuario {
    // Nombre publico, apellido vacío y fecha de nacimiento sin establecer
    public $nombre = "Angelina";
    public $apellido = "";
    public $fechaNacimiento;
    // Direccion protected
    protected $direccion = "Calle Hispanidad";
    // Pais private
    private $pais = "Mexico";
    // Funcion closure
    public $funcion;
    // Constructor
    public function __construct(){
        $this->funcion = function(){
            return "Hey!";
        };
    }
}
$usuario = new Usuario;
var_dump(json_encode($usuario));
/*
Devuelve:
string '{"nombre":"Angelina","apellido":"","fechaNacimiento":null,"funcion":{}}'
*/
```

Se puede comprobar que sólo se utilizan los atributos públicos, variables no iniciadas se traducen en _null_ y los [_closures_](http://diego.com.es/funciones-anonimas-y-clausuras-en-php) de un atributo público se codifican como un **objeto vacío**.

#### **El bitmask $option**

Los **bitmasks** se utilizan para establecer ciertas banderas **On** y **Off** en una llamada a una función. Si quieres establecer una opción, simplemente añade la constante como argumento. Si quieres combinar dos o más opciones, combínalas con el operador **||** o **OR**. 

```
json_encode(array("Braveheart", "1995"), JSON_NUMERIC_CHECK | JSON_FORCE_OBJECT);
// Devuelve: {"0": "Braveheart", "1": 1995}
```

**JSON_FORCE_OBJECT** fuerza el array a ser traducido en un objeto y **JSON_NUMERIC_CHECK** convierte números dentro de strings en números. La lista de bitmasks es [esta](http://php.net/manual/en/json.constants.php). Muchos de ellos sirven para tratar con caracteres como <, >, & o "". También hay una constante que sirve para ver los resultados más claros con espacios en blanco, **JSON_PRETTY_PRINT** (no recomendable en producción ya que añade caracteres innecesarios).

### 3. Decodificar JSON en PHP

Decodificar JSON en PHP es tan sencillo como codificar. PHP proporciona la función [_json_decode_](http://php.net/manual/es/function.json-decode.php) se encarga de ello. Simplemente pasando un **string JSON válido** en el método se obtiene un objeto **stdClass**. 

```
$string = '{"nombre": "Angelina", "apellido": "Jolie"}';
$resultado = json_decode($string);
// Vemos el resultado de la decodificación:
var_dump($resultado);
/*
* object(stdClass)[3]
  public 'nombre' => string 'Angelina' (length=8)
  public 'apellido' => string 'Jolie' (length=5)
*/
echo $resultado->nombre; // Angelina
echo $resultado->apellido; // Jolie
```

Si lo que quieres es obtener un _**array asociativo**_, simplemente hay que poner un segundo parámetro como _true_:

```
$string = '{"nombre": "Angelina", "apellido": "Jolie"}';
$resultado = json_decode($string, true);
// Vemos el resultado de la decodificación:
var_dump($resultado);
/*
* array (size=2)
  'nombre' => string 'Angelina' (length=8)
  'apellido' => string 'Jolie' (length=5)
*/
echo $resultado['nombre']; // Angelina
echo $resultado['apellido']; // Jolie
```

Si se espera un **archivo JSON muy extenso**, se puede limitar la **profundidad de recursión** a un nivel determinado. La función devolverá null y dejará de analizar si el documento es más profundo que el nivel marcado:

```
$string = '{"nombre": {"Angelina": {"apellido": "Jolie"}}}';
$resultado = json_decode($string, true, 2);
// Vemos el resultado de la decodificación:
var_dump($resultado); // Devuelve: null
```

El último argumento funciona de la misma forma que en _json_encode_, pero sólo está la opción de **JSON_BIGINT_AS_STRING**, que convierte bigints en strings. 

### 4. Manejo de errores y testing

Si el **valor JSON** no se ha podido analizar o la profundidad de recursión es mayor de la indicada (como en el ejemplo anterior), se obtiene null de json_decode. Esto significa que no surge ninguna [excepción](http://diego.com.es/excepciones-en-php) directamente de _json_encode_ o _json_decode_. 

Para el manejo de errores de emplea la función _json_last_error_, que devuelve un integer con un código de error que puede ser uno de los siguientes:

*   **JSON_ERROR_NONE**: No ha habido ningún error.
*   **JSON_ERROR_DEPTH**: El máximo nivel de profundidad se ha excedido.
*   **JSON_ERROR_STATE_MISMATCH**: JSON inválido o mal formado.
*   **JSON_ERROR_CTRL_CHAR**: Error de control de caracteres, posiblemente incorrectamente codificado.
*   **JSON_ERROR_SYNTAX**: Error de sintaxis.
*   **JSON_ERROR_UTF8**: Caracteres UTF-8 mal formados, posiblemente incorrectamente codificado.

Con dicha información se puede escribir un **método _helper_** que proporciona una descripción cuando ocurre un error:

```
class JsonHandler
{
    // Array con los mensajes de error
    protected static $mensajes = array(
        JSON_ERROR_NONE           => 'No ha habido ningún error',
        JSON_ERROR_DEPTH          => 'Se ha alcanzado el máximo nivel de profundidad',
        JSON_ERROR_STATE_MISMATCH => 'JSON inválido o mal formado',
        JSON_ERROR_CTRL_CHAR      => 'Error de control de caracteres, posiblemente incorrectamente codificado',
        JSON_ERROR_SYNTAX         => 'Error de sintaxis',
        JSON_ERROR_UTF8           => 'Caracteres UTF-8 mal formados, posiblemente incorrectamente codificado'
    );
    // Codificar
    public static function encode($value, $options = 0){
        $result = json_encode($value, $options);
        if ($result) {
            return $result;
        }
        throw new RuntimeException(static::$mensajes[json_last_error()]);
    }
    // Decodificar
    public static function decode($json, $assoc = false){
        $result = json_decode($json, $assoc);
        if ($result) {
            return $result;
        }
        throw new RuntimeException(static::$mensajes[json_last_error()]);
    }
}
```

Ahora podemos utilizar la clase:

```
$string = '{"nombre}}';
$resultado = JsonHandler::decode($string);
var_dump($resultado);
// Devuelve: Fatal error: Uncaught exception 'RuntimeException' with message 'Error de sintaxis'
```