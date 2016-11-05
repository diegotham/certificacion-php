La **extensión XML Parser de PHP** proporciona una serie de **herramientas para analizar documentos XML** (no validarlos). Soporta tres tipos de [codificación de caracteres](http://diego.com.es/codificacion-de-caracteres-en-programacion#CodificacionEnPHP): **UTF-8**, **US-ASCII** e **ISO-8859-1**. La codificación de entrada se detecta automáticamente, por lo que si se especifica la codificación en el argumento de una función para crear un recurso xml será para el elemento de salida, ya que el de entrada no se puede modificar.

Si existen **caracteres de entrada** que no se pueden codificar aparece un error. Si no se consiguen **codificar caracteres de salida** se transforman en "?".

**XML Parser** al igual que **XMLReader** sólo carga el elemento actual en la **memoria**, por lo que hay que ir definiendo _**handlers**_ para elementos específicos que se ejecutarán cuando el analizador se tope con ellos. Esto hace que sea más rápido y utilice menos recursos que un analizador basado en **extensiones DOM** como **SimpleXML** que tiene que cargar el documento XML entero en la memoria.

La **extensión XML Parser** utiliza **Expat XML parser**. Expat es un analizador basado en eventos, observa un **archivo xml** como una serie de eventos. Cuando un evento ocurre, llama a una determinada función para tomar una acción. Al no validar datos, ignora cualquier [DTD](http://diego.com.es/xml-principios-basicos#DocumentTypeDefinition) enlazado al documento. Si el documento no es _**well formed**_ emitirá un error.

Para crear un **XML Parser** se emplea la función _xml_parser_create()_, que devuelve un _**resource**_ para poder ser usado por otras funciones. Si se quiere que tenga soporte para namespaces se emplea _xml_parser_create_ns()_.

Para ver un ejemplo vamos a utilizar el siguiente archivo _**usuarios.xml**_:

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

**Ejemplo de XML Parser en PHP:**

*   Iniciamos el analizador

```
$parser = xml_parser_create();
```

*   Especificamos las funciones a emplear con el parser

```
// Función a utilizar al inicio de un elemento:
function start($parser,$elemento,$atributos) {
    switch($elemento) {
        case "usuario":
            echo "Datos de usuario: <br>";
            break;
        case "nombre":
            echo "Nombre: ";
            break;
        case "apellido":
            echo "Apellido: ";
            break;
        case "ciudad":
            echo "Ciudad: ";
            break;
        case "pais":
            echo "País: ";
    }
}
// Función a emplear al final de un elemento:
function stop($parser,$elemento) {
    echo "<br>";
}
// Función que se emplea cuando se encuentra un carácter
function char($parser,$data) {
    echo $data;
}
```

*   Especificamos handlers, leemos datos y liberamos

```
// Especificar el handler de elementos
xml_set_element_handler($parser,"start","stop");
// Especificar el handler de datos
xml_set_character_data_handler($parser,"char");
// Abrir un archivo xml
$fp = fopen("usuarios.xml","r");
// Leer los datos
while ($data = fread($fp,4096)) {
    xml_parse($parser,$data,feof($fp)) or
    die (sprintf("Error XML: %s en la línea %d",
        xml_error_string(xml_get_error_code($parser)),
        xml_get_current_line_number($parser)));
}
// Liberar el analizador
xml_parser_free($parser);
```