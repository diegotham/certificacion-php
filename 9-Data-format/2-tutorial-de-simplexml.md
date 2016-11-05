PHP proporciona varias **formas de manejar archivos XML**. La forma más sencilla de hacerlo es con la [extensión SimpleXML](http://php.net/manual/es/simplexml.examples-basic.php).

**Indice de contenido**

| | |
| -------- | -------- |
| 1. Cargar un archivo XML | 5. Comparación de elementos XML |
| 2. Acceder a elementos XML | 6. XPath con SimpleXML |
| 3. Recorrer elementos XML | 7. Añadir y modificar elementos XML |
| 4. Utilizar atributos de elementos XML | 8. Otras funciones SimpleXML |

Algunos de los ejemplos de este tutorial se harán mediante el **archivo xml** _usuarios.xml_, que es el siguiente:

```
<?xml version="1.0" encoding="UTF-8"?>
<usuarios>
    <usuario>
        <nombre>Monnie</nombre>
        <apellido>Boddie</apellido>
        <direccion>Calle Pedro Mar 12</direccion>
        <ciudad>Mexicali</ciudad>
        <pais>Mexico</pais>
        <contacto>
            <telefono>44221234</telefono>
            <url>http://monnie.ejemplo.com</url>
            <email>monnie@ejemplo.com</email>
        </contacto>
    </usuario>
    <usuario>
        <nombre>Ted</nombre>
        <apellido>Gonzalez</apellido>
        <direccion>Calle Luis Angel 22</direccion>
        <ciudad>Buenos Aires</ciudad>
        <pais>Argentina</pais>
        <contacto>
            <telefono>44224664</telefono>
            <url>http://ted.ejemplo.com</url>
            <email>ted@ejemplo.com</email>
        </contacto>
    </usuario>
</usuarios>
```

### 1. Cargar un archivo XML

La carga de un archivo XML se hace con una simple función llamada _simplexml_load_file()_. Una vez que el archivo se pasa a esta función, se puede acceder a los datos:

```
if(!$xml = simplexml_load_file('usuarios.xml')){
    echo "No se ha podido cargar el archivo";
} else {
    echo "El archivo se ha cargado correctamente";
}
```

Si los datos en formato XML están en un _string_, se puede utilizar la función: _simplexml_load_string($string)_:

```
<?php
$string = '<usuarios>
    <usuario>
        <nombre>Monnie</nombre>
        <apellido>Boddie</apellido>
        <direccion>Calle Pedro Mar 12</direccion>
        <ciudad>Mexicali</ciudad>
        <pais>Mexico</pais>
        <contacto>
            <telefono>44221234</telefono>
            <url>http://monnie.ejemplo.com</url>
            <email>monnie@ejemplo.com</email>
        </contacto>
    </usuario>
    </usuarios>';
if(!$xml = simplexml_load_string($string)){
    echo "No se ha podido cargar el archivo";
} else {
    echo "Se ha cargado correctamente";
}
```

### 2. Acceder a elementos XML

Con el archivo cargado correctamente, se puede acceder a los datos contenidos en cada uno de los elementos, para mostrarlos, asignarlos a variables, manipularlos...

Transformamos cada elemento en su correspondiente nombre de variable, cuyo valor será el valor del interior del elemento. En caso de que haya **valores anidados**, como en el elemento _contacto_, se proporciona la ruta de los elementos correspondiente:

```
if(!$xml = simplexml_load_file('usuarios.xml')){
    echo "No se ha podido cargar el archivo";
} else {
    foreach ($xml as $usuario){
        echo 'Nombre: '.$usuario->nombre.'<br>';
        echo 'Apellido: '.$usuario->apellido.'<br>';
        echo 'Dirección: '.$usuario->direccion.'<br>';
        echo 'Ciudad: '.$usuario->ciudad.'<br>';
        echo 'País: '.$usuario->pais.'<br>';
        echo 'Teléfono: '.$usuario->contacto->telefono.'<br>';
        echo 'Url: '.$usuario->contacto->url.'<br>';
        echo 'Email: '.$usuario->email->nombre.'<br>';
    }
}
```

También se puede emplear la [extensión DOM](http://diego.com.es/tutorial-de-dom-en-php) para importar un string que contiene xml:

```
<?php
$string = '<usuarios>
    <usuario>
        <nombre>Monnie</nombre>
        <apellido>Boddie</apellido>
        <direccion>Calle Pedro Mar 12</direccion>
        <ciudad>Mexicali</ciudad>
        <pais>Mexico</pais>
        <contacto>
            <telefono>44221234</telefono>
            <url>http://monnie.ejemplo.com</url>
            <email>monnie@ejemplo.com</email>
        </contacto>
    </usuario>
    </usuarios>';
// Nuevo objeto DOM
$dom = new DOMDocument;
// Cargar el string XML
$dom->loadXML($string);
$sxe = simplexml_import_dom($dom);
// Mostrar un valor
echo $sxe->usuario[0]->ciudad; // Devuelve Mexicali
```

El ejemplo anterior primero carga el string con el método **DomDocument::loadXML**. Este objeto ahora puede importarse directamente a SimpleXML con la función _simplexml_import_dom()_. El resultado es el mismo que llamando a _simplexml_load_file()_ o _simplexml_load_string()_.

El objeto cargado antes era realmente un objeto de la clase **SimpleXMLElement**. Esta clase es la base de la [extensión SimpleXML](http://php.net/manual/es/book.simplexml.php). Vamos a iniciar el archivo xml instanciando esta clase de ahora en adelante. Al **constructor de la clase** se le ha de pasar la variable que contiene el **string xml**, o la **ruta de un archivo**, pero para esta segunda forma hay que especificar dos argumentos más:

```
<?php
$string = '<usuarios>
    <usuario>
        <nombre>Monnie</nombre>
        <apellido>Boddie</apellido>
        <direccion>Calle Pedro Mar 12</direccion>
        <ciudad>Mexicali</ciudad>
        <pais>Mexico</pais>
        <contacto>
            <telefono>44221234</telefono>
            <url>http://monnie.ejemplo.com</url>
            <email>monnie@ejemplo.com</email>
        </contacto>
    </usuario>
    </usuarios>';
// Nuevo objeto SimpleXMLElement al que se le pasa el string
$usuarios = new SimpleXMLElement($string);
// Mostrar un valor
echo $usuarios->usuario->ciudad; // Devuelve Mexicali
// Nuevo objeto SimpleXMLElement al que se le pasa ruta de archivo
$usuarios = new SimpleXMLElement('usuarios.xml', 0, true);
echo $usuarios->usuario[1]->ciudad; // Devuelve Buenos Aires
```

### 3. Recorrer elementos XML

Cuando existen **elementos con hijos** se utilizan las **técnicas típicas de iteración de elementos**:

```
<?php
// Nuevo objeto SimpleXMLElement al que se le pasa ruta de archivo
$usuarios = new SimpleXMLElement('usuarios.xml', 0, true);
foreach ($usuarios as $usuario){
    echo "Nombre: ". $usuario->nombre ."<br>";
foreach($usuario->contacto as $contact){
    echo "Teléfono: ". $contact->telefono . "<br>";
    echo "Email: ".$contact->email . "<br>";
    }
}
```

Se puede comprobar que lo que se crean son objetos, por lo que la **iteración** se realiza con **objetos**. Si lo que se quiere es **obtener un array para iterar con arrays**, se puede emplear el siguiente método:

```
<?php
​$xml = simplexml_load_file('usuarios.xml');
$jsonString = json_encode($xml);
$array = json_decode($jsonString, true);
```

### 4. Utilizar atributos de elementos XML

SimpleXML también puede acceder fácilmente a los **atributos de los elementos xml**. Para acceder a ellos se hace mediante sintaxis de array. Vamos a modificar el ejemplo xml anterior y añadir las **opciones de contacto como atributos** (aunque en la práctica fuera preferible hacerlo mediante elementos):

```
$string = '<usuarios>
    <usuario>
        <nombre>Monnie</nombre>
        <apellido>Boddie</apellido>
        <direccion>Calle Pedro Mar 12</direccion>
        <ciudad>Mexicali</ciudad>
        <pais>Mexico</pais>
        <contacto tipo="telefono">44221234</contacto>
        <contacto tipo="url">http://monnie.ejemplo.com</contacto>
        <contacto tipo="email">monnie@ejemplo.com</contacto>
    </usuario>
    </usuarios>';
// Nuevo objeto SimpleXMLElement al que se le pasa el string xml
$usuarios = new SimpleXMLElement($string);
echo "Nombre de usuario:" . $usuarios->usuario[0]->nombre . "<br>";
foreach($usuarios->usuario[0]->contacto as $contacto) {
        switch((string) $contacto['tipo']){
            case 'telefono':
                echo "Telefono: " . $contacto . "<br>";
                break;
            case 'email':
                echo "Email: " . $contacto . "<br>";
        }
}
```

### 5. Comparación de elementos XML

Para **comparar un elemento o atributo con un string o pasarlo a una función que requiera un string** conviene hacer una conversión empleando (_string_). Sino, PHP tratará al elemento como un objeto (aunque funcione el tratamiento del objeto como string con __toString):

```
// Seguimos con el string xml del ejemplo anterior
// Nuevo objeto SimpleXMLElement al que se le pasa el string xml
$usuarios = new SimpleXMLElement($string);
if((string) $usuarios->usuario->nombre == 'Monnie') {
    echo 'El usuario es efectivamente Monnie';
} else {
    echo 'El usuario NO es Monnie';
}
echo htmlentities((string) $usuarios->usuario->nombre);
// Podemos ver que si hacemos var_dump:
var_dump($usuarios->usuario->nombre);
/*
Devuelve:
object(SimpleXMLElement)[2]
  public 0 => string 'Monnie' (length=6)
*/
```

Dos elementos **SimpleXMLElement** se consideran **distintos incluso cuando ambos apuntan al mismo elemento**:

```
// Nuevo objeto SimpleXMLElement al que se le pasa el string xml
$usuarios1 = new SimpleXMLElement($string);
$usuarios2 = new SimpleXMLElement($string);
var_dump($usuarios1 == $usuarios2); // Devuelve false
```

### 6. XPath con SimpleXML

Mediante el método **SimpleXMLElement::xpath** es posible ejecutar una petición [XPath](http://diego.com.es/xml-principios-basicos#QueEsXPath) sobre los datos XML:

```
// Nuevo objeto SimpleXMLElement al que se le pasa un archivo xml
$usuarios = new SimpleXMLElement('usuarios.xml', 0, true);
// Se puede especificar la ruta reducida y acceder directamente a un elemento:
foreach ($usuarios->xpath('//usuario') as $usuario){
    echo $usuario->apellido . "<br>";
}
// O especificar la ruta absoluta:
foreach ($usuarios->xpath('/usuarios/usuario') as $usuario){
    echo $usuario->nombre . "<br>";
}
```

### 7. Añadir y modificar elementos XML

Para **modificar un elemento** simplemente se cambia su valor:

```
// Nuevo objeto SimpleXMLElement al que se le pasa un archivo xml
$usuarios = new SimpleXMLElement('usuarios.xml', 0, true);
// Usuario 0 antes de cambiar:
echo "Nombre: " . $usuarios->usuario[0]->nombre . "<br>";
// Usuario 0 cambiando el nombre:
$usuarios->usuario[0]->nombre = "María";
echo "Nombre: " . $usuarios->usuario[0]->nombre . "<br>";
```

Para **añadir un elemento hijo** se emplea _addChild()_ y para añadir un atributo _addAttribute()_:

```
// Nuevo objeto SimpleXMLElement al que se le pasa un archivo xml
$usuarios = new SimpleXMLElement('usuarios.xml', 0, true);
// Añadimos usuario, elementos y atributo
$nuevoUsuario = $usuarios->addChild('usuario');
$nuevoUsuario->addAttribute('sexo', 'Hombre');
$nuevoUsuario->addChild('nombre', 'Bernard');
$nuevoUsuario->addChild('apellido', 'Madoff');
var_dump($usuarios->usuario[3]);
/*
Devuelve:
object(SimpleXMLElement)[5]
  public '@attributes' =>
    array (size=1)
      'sexo' => string 'Hombre' (length=6)
  public 'nombre' => string 'Bernard' (length=7)
  public 'apellido' => string 'Madoff' (length=6)
*/
```

Si ahora queremos ver los datos de nuevo el documento _usuarios.xml_ en un string con formato xml podemos emplear el método _asXML()_ o _saveXML()_ que es su alias:

```
$nuevoXML = $usuarios->asXML();
var_dump($nuevoXML); // Devuelve un string con los datos en XML
```

### 8. Otras funciones SimpleXML

Existen algunas **funciones para obtener información de un archivo xml cualquiera**:

*   _SimpleXMLElement::children()_. Muestra los hijos de un elemento. Pueden obtenerse resultados jerárquicamente:

```
// Nuevo objeto SimpleXMLElement al que se le pasa un archivo xml
$usuarios = new SimpleXMLElement('usuarios.xml', 0, true);
// foreach para acceder a hijos de elementos
foreach ($usuarios->children() as $usuario){
    echo "Nombre de usuario: " . $usuario->nombre . "<br>";
    foreach ($usuario->children() as $contact){
        if (isset($contact->telefono)){
        echo "Telefono: " . $contact->telefono . "<br>";
            }
        }
    }
```

*   _SimpleXMLElement::count()_. Cuenta los hijos de un elemento:

```
// Nuevo objeto SimpleXMLElement al que se le pasa un archivo xml
$usuarios = new SimpleXMLElement('usuarios.xml', 0, true);
// Contar número de usuarios
echo "Hay un total de: " . $usuarios->count() . " usuarios en el archivo xml";
```

*   _SimpleXMLElement::getName()_. Devuelve el nombre de un elemento XML:

```
// Nuevo objeto SimpleXMLElement al que se le pasa un archivo xml
$usuarios = new SimpleXMLElement('usuarios.xml', 0, true);
echo $usuarios->getName(); // usuarios
echo $usuarios->usuario->getName(); // usuario
// Elementos disponibles en usuario:
foreach ($usuarios->usuario[0] as $user){
    echo $user->getName() . "<br>";
}
```

*   _SimpleXMLElement::attributes()_. Identifica el atributo de un elemento:

```
$string = <<<XML
<animales>
    <animal tipo="mamifero">
       <nombre>Tigre</nombre>
    </animal>
    <animal tipo="anfibio">
        <nombre>rana</nombre>
</animal>
</animales>
XML;
// Cargamos el archivo:
$xml = simplexml_load_string($string);
// Se puede obtener el atributo así:
$xml->animal[0]->attributes(); // Devuelve: mamífero
$xml->animal[1]->attributes(); // Devuelve: anfibio
```