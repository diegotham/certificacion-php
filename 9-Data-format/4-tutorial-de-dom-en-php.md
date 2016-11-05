**Indice de contenido**

| | |
| -------- | -------- |
| 1. Introducción | 5. Añadir un nuevo elemento |
| 2. Crear una clase para el DOM | 6. Eliminar un elemento |
| 3. Crear y destruir un objeto DOM | 7. Segmentación de elementos |
| 4. Devolver un elemento por su ID | |

### 1. Introducción

**PHP DOM** es otra extensión de PHP que al igual que [SimpleXML](http://diego.com.es/tutorial-de-simplexml) se basa en un estilo de análisis en árbol (_tree style parsing_) mediante el que se tiene que cargar el **documento XML** entero en la memoria. Este estilo permite acceder aleatoriamente al documento y editarlo, al contrario que los sistemas con un estilo de análisis _stream_, que van analizando nodo por nodo y no pueden editar el documento.

El **estándar de DOM**, según **W3C**, es "_una plataforma e interfaz de lenguajes que permite a los programas y scripts acceder y actualizar dinámicamiente el contenido, la estructura y el estilo de los documentos_". La librería _**libxml2**_ implementa el DOM junto a todos sus métodos en C. Todas las **extensiones XML de PHP5** están basadas en dicha librería, por lo que hay completa interoperatividad entre extensiones. Esto hace que potencie su funcionalidad, permitiendo por ejemplo utilizar **XMLReader** como _stream parser_ para obtener un elemento, importarlo al **DOM** y extraer los datos mediante **XPath**. 

PHP DOM pasa toda la información a la aplicación **creando objetos en forma de árbol** que duplican extactamente los elementos en forma de árbol del **documento XML**. Para construir el árbol tiene que cargar el documento entero, por lo que utiliza bastante memoria. Con **DOM** es posible construir, modificar, consultar, validar y transformar **documentos XML**.

**SimpleXML** permite **trabajar con documentos XML rápida y fácilmente**, y a veces suele bastar con esa funcionalidad, pero cuando se necesita algo un poco más complejo suele ser necesario emplear **PHP DOM**. Existen dos funciones para pasar de uno a otro: de un nodo **DOM** a un objeto **SimpleXML**: _simplexml_import_dom()_, de un objeto **SimpleXML** a un nodo **DOM**: _dom_import_simplexml()_.

Para los ejemplos vamos a emplear el siguiente archivo _usuarios.xml_:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE usuarios SYSTEM "usuarios.dtd">
<usuarios>
    <usuario id="u23">
        <nombre>Monnie</nombre>
        <apellido>Boddie</apellido>
        <ciudad>Mexicali</ciudad>
        <pais>Mexico</pais>
        <contacto>
            <telefono>44221234</telefono>
            <email>monnie@ejemplo.com</email>
        </contacto>
    </usuario>
    <usuario id="u33">
        <nombre>Ted</nombre>
        <apellido>Gonzalez</apellido>
        <ciudad>Buenos Aires</ciudad>
        <pais>Argentina</pais>
        <contacto>
            <telefono>44224664</telefono>
            <email>ted@ejemplo.com</email>
        </contacto>
    </usuario>
</usuarios>
```

Junto con el doctype _usuarios.dtd_:

```
<!ELEMENT usuarios (usuario*)>
<!ELEMENT usuario (nombre, apellido, ciudad, pais, contacto*)>
    <!ATTLIST usuario id ID #REQUIRED>
<!ELEMENT nombre (#PCDATA)>
<!ELEMENT apellido (#PCDATA)>
<!ELEMENT ciudad (#PCDATA)>
<!ELEMENT pais (#PCDATA)>
<!ELEMENT contacto (telefono, email)>
<!ELEMENT telefono (#PCDATA)>
<!ELEMENT email (#PCDATA)>
```

El **concepto de nodo en DOM** es cualquier objeto individualizable dentro del **documento XML**. Esto significa que tanto los elementos como los atributos son nodos.

**DOMNode** es una clase que extiende **DOMDocument** que proporciona métodos para manipular diferentes aspectos del documento: _appendChild()_, _cloneNode()_, _hasAttributes()_, etc. **DOMElement** y **DOMAttr** también son hijas de DOMNode. El hecho de que partan de DOMNode permite que tengan métodos y propiedades comunes a todos los nodos.

### 2. Crear una clase para el DOM

La clase **Usuarios** nos permitirá utilizar las funcionalidades que se han nombrado antes. Todas las secciones posteriores de este artículo se centran en explicar cada funcionalidad de esta clase:

```
class Usuarios {
    private $xmlPath;
    private $domDocument;
    public function __construct($xmlPath){
        // Instancia de DOMDocument
    }
    public function __destruct() {
        // Librará la memoria del DOMDocument
    }
    public function getUserById($id){
        // Devuelve un array con los datos de usuario
    }
    public function addUser($id, $nombre, $apellido, $ciudad, $pais, $contacto){
        // Añade un usuario
    }
    public function deleteUser($id){
        // Elimina un usuario
    }
    public function findUserByCountry($pais){
        // Devuelve un array de usuarios del país
    }
}
```

Este clase es sencilla porque sólo se trata de mostrar las posibilidades de DOM. Existiría una mejor forma de trabajar con los errores, pero en este caso lanzaremos un [Exception](http://diego.com.es/excepciones-en-php) sin capturar.

### 3. Crear y destruir un objeto DOM

El constructor está diseñado para recibir la **ruta del documento XML**, y es necesario hacerle algunos tests.

Lo primero es comprobar que el documento cargado emplea el doctype _usuarios_. Cada **DOMDocument** tiene la propiedad pública _**doctype**_, que devuelve el doctype utilizado por el documento XML. En este ejemplo la propiedad doctype tiene el valor de "_usuarios_". 

Lo segundo es asegurarse de que la definición utilizada está definida de forma correcta mediante las propiedades públicas _**systemId**_ o _**publicId**_. El XML utilizado aquí está definido por un [DTD](http://diego.com.es/xml-principios-basicos#DocumentTypeDefinition) _usuarios.dtd_, por lo que se comprueba comparándolo con la propiedad _**systemId**_.

Lo siguiente es asegurarse de que el documento es válido junto con el DTD. Si el XML tiene DTD, es obligatorio validarlo con _validate()_.

Una vez comprobado todo, guarda la **referencia del documento cargado** y la **ruta del archivo XML** como propiedades internas para poder utilizarlas en otros métodos de la clase. Si cualquiera de las comprobaciones falla, se lanza una **excepción**.

```
public function __construct($xmlPath){
    // Cargar el documento
    $doc = new DOMDocument;
    $doc->load($xmlPath);
    // Comprobar si es un xml usuarios:
    if($doc->doctype->name != "usuarios" ||
    $doc->doctype->systemId != "usuarios.dtd"){
        throw new Exception("Tipo de documento incorrecto");
    }
    // Comprobar si es válido y well formed
    if($doc->validate()){
        $this->domDocument = $doc;
        $this->xmlPath = $xmlPath;
    } else {
        throw new Exception("El documento no es válido");
    }
}
```

El **método destructor** libera la memoria utilizada por _$domDocument_. Es simplemente una llamada a unset para deshacer la variable:

```
public function __destruct() {
    // Librará la memoria del DOMDocument
    unset($this->domDocument);
}
```

### 4. Devolver un elemento por su ID

El método _getUserById()_ obtiene los **detalles de un usuario en concreto**. Se proporciona un ID como _string_ y el método devuelve un _array_ con los datos del usuario.

**PHP DOM** proporciona una función para devolver un elemento específico basado en su ID: _getElementById()_, que devuelve el objeto **DOMElement**. Para que funcione es necesario tener un atributo ID en el DTD:
```
<!ATTLIST usuario id ID #REQUIRED>
```

El método _getElementById()_ funciona si el documento se ha validado con un DTD o con el método _setIdAttribute()_, sino la función no detectará que ese elemento tiene un ID.

Otra forma de obtener elementos de un documento es con el método _getElementsByTagName()_. Este método devuelve una colección de nodos con el tag específico. La colección devuelta es de la clase **DOMNodeList**, que es _traversable_.

Los objetos **DOMNodeList** también pueden obtenerse por su posición en la lista con _item()_. Porque **DTD** define un usuario que sólo puede tener un nombre, sabemos que el DOMNodeList contendrá un nodo que podrá ser accedido con _item(0)_. El DTD fortalece este hecho, y si fuera diferente en el **documento xml** no se validaría correctamente al crear el objeto Usuarios.

Una vez encontrado el node que se buscaba, se puede obtener su valor mediante nodeValue.

Para acceder a **atributos** se pueden emplear los **atributos de la propiedad pública DOMNode**, que devuelve **DOMNameNodeMap**. Este es similar a **DOMNodeList**, es traversable también, pero puedes escoger un atributo específico utilizando el método _getNamedItem()_ y pasar el nombre del atributo como _string_. El valor devuelto es un **DOMNode**. En este ejemplo no se ha añadido porque no se han puesto atributos a los elementos (aparte de "id").

```
public function getUserById($id){
    // Obtener un elemento usuario desde el ID proporcionado
    $usuario = $this->domDocument->getElementById($id);
    // Si no se obtiene ningún usuario:
    if(!$usuario){
        throw new Exception("El usuario no existe");
    }
    $arrayUsuario = array();
    $arrayUsuario["id"] = $id;

```

Ahora se obtienen los datos de los elementos en función de los nombres. Estos DOMNodeLists sólo devolerán un item porque el DTD lo dice así (seguimos en la función _getUserById($id)_):

```
// Nombre
    $arrayUsuario["nombre"] = $usuario->getElementsByTagName("nombre")->item(0)->nodeValue;
    // Apellido
    $arrayUsuario["apellido"] = $usuario->getElementsByTagName("apellido")->item(0)->nodeValue;
    // Ciudad
    $arrayUsuario["ciudad"] = $usuario->getElementsByTagName("ciudad")->item(0)->nodeValue;
    // Pais
    $arrayUsuario["pais"] = $usuario->getElementsByTagName("pais")->item(0)->nodeValue;
    // Contacto
    $contacto = $usuario->getElementsByTagName("contacto");
    $arrayContacto = array();
    // Iterar sobre los elementos de contacto
    foreach($contacto as $contact){
        // Telefono
        $telefono = $contact->getElementsByTagName("telefono")->item(0)->nodeValue;
        // Email
        $email = $contact->getElementsByTagName("email")->item(0)->nodeValue;
        // Creamos un array con los valores de las formas de contacto
        $arrayC["telefono"] = $telefono;
        $arrayC["email"] = $email;
        // Asignamos el array al array que pasaremos finalmente a arrayUsuario
        $arrayContacto[] = $arrayC;
    }
    $arrayUsuario["contacto"] = $arrayContacto;
    return $arrayUsuario;
}
```

**Identificar y extraer datos de un documento XML** es relativamente simple. Lo más complejo es entender el concepto de nodo. Una vez comprendido, obtener datos no tiene mayor complicación.

### 5. Añadir un nuevo elemento

El método _addUser()_ añade un nuevo usuario a la **base de datos XML**. El método coge las propiedades y un array de _contacto_ para añadir.

Una forma de hacerlo es usar el método _createElement()_ para **añadir un nuevo nodo al documento** y establecer una referencia para poder operar con el objeto desde el momento de crearlo en adelante. Cuando se crea un elemento se debe añadir el documento. Con el método _createElement()_ no se añade automáticamente. Asocia el elemento con el documento, pero no va mas allá.

Se utiliza la propiedad **documentElement** para identificar el **elemento root** del **documento XML**. Si no se hace esto y lo añadimos directamente al documento, estaríamos añadiendo un hijo al final del documento (fuera del elemento usuarios), lo que resultaría en un error de validación.

El elemento usuario debe contener un **id**, asi que se ha de **añadir un atributo al nuevo elemento creado**. Hay dos formas de hacerlo, la más sencilla es con el método _setAttribute()_ que coge el **nombre del atributo** y el **valor del atributo** como argumentos. La segunda forma es crear un objeto **DOMAttr** y añadirlo al elemento. **DOMAttr** es una subclase de **DOMNode**, por lo que hereda todas las propiedades y métodos de ésta.

_setAttribute()_ y _setAttributeNode()_ se encargan de **añadir y actualizar atributos asociados a un elemento**. Si el atributo no existe se creará. Si existe se actualizará.

Para proporcionar el valor de un elemento textual se recomienda usar _**DOMCdataSection()**_. Si un elemento contiene datos que se desean analizar como CDATA, no se puede indicar directamente como tal. Hay que declararlo como PCDATA, y el contenido que se quiera interpretar tiene que ir de la forma: **<![CDATA[...]]>**. Si se quiere añadir HTML directamente en un nodo, caracteres como **<** o **&** se convertirán en sus entidades correspondientes **&lt;** y **&amp;**. Esto es porque estos caracteres tienen un significado especial en **XML**. **DOM** los sustituye para que no causen ningun **problema en la interpretación cuando se carga o valida el documento**.

El último paso es guardar el nuevo documento en el archivo, mediante el método _save()_.

```
public function addUser($id, $nombre, $apellido, $ciudad, $pais, $telefono, $email){
    // Crear un nuevo elemento que representa el nuevo usuario
    $nuevoUsuario = $this->domDocument->createElement("usuario");
    // Añadir el nuevo elemento creado
    $this->domDocument->documentElement->appendChild($nuevoUsuario);
    /*
     * Establecer el atributo id se puede hacer de dos formas:
     * 1\. $nuevoUsuario->setAttribute("id", $id);
     * 2\. Se expone a continuación:
     */
    $atributoId = new DOMAttr("id", $id);
    $nuevoUsuario->setAttributeNode($atributoId);

```

Ahora crearemos cada uno de los elementos con _createElement()_ (en la misma función _addUser()_):

```
// Nombre
    $nombre = $this->domDocument->createElement("nombre", $nombre);
    $nuevoUsuario->appendChild($nombre);
// Apellido
    $apellido = $this->domDocument->createElement("apellido", $apellido);
    $nuevoUsuario->appendChild($apellido);
// Ciudad
    $ciudad = $this->domDocument->createElement("ciudad", $ciudad);
    $nuevoUsuario->appendChild($ciudad);
// País
    $pais = $this->domDocument->createElement("pais", $pais);
    $nuevoUsuario->appendChild($pais);
// Contacto
    $contacto = $this->domDocument->createElement("contacto");
    $nuevoUsuario->appendChild($contacto);
// Telefono
    $telefono = $this->domDocument->createElement("telefono", $telefono);
    $contacto->appendChild($telefono);
// Email
    $email = $this->domDocument->createElement("email", $email);
    $contacto->appendChild($email);
// Guardar todo lo anterior
    $this->domDocument->save($this->xmlPath);
}
```

### 6. Eliminar un elemento

El siguiente método es para borrar un usuario. En éste se identifica qué elemento se quiere eliminar y se usa el método _removeChild()_. Dos aspectos importantes:

*   No es posible remover un hijo de una instancia **DOMDocument** directamente. Primero hay que acceder al **documentElement** y eliminar el hijo de ahí. Esto es por la misma razón por la que hay que referenciar a documentElement cuando queremos añadir un usuario.
*   Eliminar un elemento del documento solo lo elimina de la memoria. Si quieres culminar la eliminación, se ha de guardar en el archivo.

```
public function deleteUser($id){
    // Obtener el usuario según el ID
    $usuario = $this->domDocument->getElementById($id);
    // Eliminar el hijo
    $this->domDocument->documentElement->removeChild($usuario);
    // Guardarlo en el archivo
    $this->domDocument->save($this->xmlPath);
}
```

### 7. Segmentación de elementos

El método que encuentra un usuario en función de su país de origen utiliza [XPath](http://diego.com.es/xml-principios-basicos#QueEsXPath) para obtener los resultados. _**getElementById()**_ es una forma conveniente para sacar objetos del DOM cuando existe un atributo ID dentro de un **DTD**. Pero si queremos consultar cualquier otro dato podemos emplear **DOMXPath**. La sintaxis para encontrar cualquier usuario basándonos en el país es:
```
//usuarios/usuario/pais[text() = "pais"]/..
```

Esta consulta primero dice que queremos acceder a un usuario en la ruta _**//usuarios/usuario**_. Las dos barras al principio indican que usuarios es el **elemento root**, y la barra individual que usuario es un hijo de usuarios y pais es un hijo de usuario. _**[text() = "pais"]**_ indica el texto que buscamos dentro de pais. Por último, _**/..**_ indica que lo que queremos obtener realmente es el usuario, no el país.

**XPath** es una buena forma de localizar nodos en una estructura. Si estás en una situación en la que iteras varias veces con **DOMNodeLists** y probando **nodeValues** para ver los valores es posible que sea más conveniente usar **XPath** que será más rápido y sencillo de leer.

```
public function findUserByCountry($pais){
    // Utilizamos XPath para encontrar el libro que buscamos:
    $query = '//usuarios/usuario/pais[text() = "' . $pais . '"]/..';
    // Creamos un nuevo objeto XPath y lo asociamos con el documento
    $xpath = new DOMXPath($this->domDocument);
    $resultado = $xpath->query($query);
    $arrayUsuarios = array();
    //iterar los resultados:
    foreach ($resultado as $usuario){
        // Añade el nombre de usuario al array
        $arrayUsuarios[] = $usuario->getElementsByTagName("nombre")->item(0)->nodeValue;
    }
    return $arrayUsuarios;
}
```

Para comprobar todo lo anterior, vamos a obtener, crear, eliminar y segmentar usuarios:

```
$user = new Usuarios('usuarios.xml');
$u23 = $user->getUserById("u23");
// Mostrar datos de un usuario
echo "El usuario " . $u23["nombre"] . " " . $u23["apellido"] . " es de " . $u23["pais"];
// Devuelve: El usuario Monnie Boddie es de Mexico
// Añadir un usuario:
$user->addUser("u44", "Jefferson", "West", "Córdoba", "Argentina", "123123", "jeff@ejemplo.com");
// Eliminar un usuario:
$user->deleteUser("u23");
// Encontrar un usuario por país
$argentinos = $user->findUserByCountry("Argentina");
var_dump($argentinos);
/*
* Devuelve:
* array (size=2)
  0 => string 'Ted' (length=3)
  1 => string 'Jefferson' (length=9)
*/
```

Lo más importante es el concepto de nodo, el bloque más básico de un **documento XML para DOM**.