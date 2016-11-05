Como norma general es una buena idea **guardar las clases PHP en archivos separados**, con **una clase por cada archivo**. Por ejemplo si tenemos un foro, tendríamos la clase User dentro del archivo User.php, y la clase Topic en el archivo Topic.php. Lo normal es poner estos archivos de clases en una carpeta de la aplicación:
```
classes/
    User.php
    Topic.php
```

Antes de que apareciera **autoload**, si se necesitaba usar una de las clases anteriores, primero se incluía con [_include_](http://diego.com.es/estructuras-de-control-en-php#include-include_once) o [_require_](http://diego.com.es/estructuras-de-control-en-php#require-require_once):

```
require_once("classes/User.php");
$usuario = new User();
```

Muy probablemente _User.php_ no sería el único archivo a incluir, por lo que se solían generar largas listas con _include_ y _require_.

El mantener las clases separadas y organizadas permite utilizar el **autoloading**. Para ello, se crea una **función __autoload()** en el arranque de la aplicación. Entonces, cuando **el código intente crear una instancia que PHP desconoce todavía**, PHP llamará automáticamente a la función <u>**__autoload()**</u>, pasándole el nombre de la clase que está intentando instanciar. El objetivo de la función es localizar e incluir el archivo de la clase. PHP ahora podrá entonces cargar la clase que antes desconocía, y crear el objeto.

Ejemplo de función autoload:

```
function __autoload($nombreClase) {
    $nombreClase = strtolower($nombreClase);
    $directorio = "classes/{$nombreClase}.php";
    if(file_exists($directorio)) {
        require_once($directorio);
    } else {
        die("El archivo {$nombreClase}.php no se ha podido encontrar.");
    }
}
```

Ahora todas las clases que se encuentren en el directorio /_classes_ se cargarán (siempre y cuando el archivo tenga el mismo nombre que la clase a la cual se llama). 

Existe una **alternativa mejor y más flexible** mediante la función _spl_autoload_register()_ (de hecho es posible que la función __autoload() quede obsoleta y sea eliminada en el futuro). _spl_autoload_register()_ acepta una **función callable** como parámetro donde se puede definir una **lógica concreta para la carga de clases**. Con _spl_autoload_register()_ es posible **definir varias funciones para la carga de clases** (lo que no es posible con ___autoload()_):

```
function miAutoloadUno($clase){
    require "/directorio/.$clase.php";
}
function miAutoloadDos($clase){
    require "/otroDirectorio/.$clase.php";
}
spl_autoload_register("autoloadUno");
spl_autoload_register("autoloadDos");
```

Algunas otras funciones útiles de _**spl_autoload**_:

*   [spl_autoload_extensions()](http://php.net/manual/es/function.spl-autoload-extensions.php), permite especificar las **extensiones de los archivos que se quieren cargar**.
*   [spl_autoload_unregister()](http://php.net/manual/es/function.spl-autoload-unregister.php), permite **desregistrar una función** dada como implementación de __autoload().
*   [spl_autoload_functions()](http://php.net/manual/es/function.spl-autoload-functions.php), devuelve todas las **funciones __autoload()** registradas.

Desde PHP 5.3 es posible el uso de [namespaces en PHP](http://diego.com.es/namespaces-en-php). Un **namespace** permite crear un nombre de directorio para un archivo y luego éste puede ser referenciado desde cualquier parte del script.

El estándar [PSR-4](http://www.php-fig.org/psr/psr-4/) permite **enlazar los namespaces con directorios de carpetas reales**. En la actualidad es lo más cómodo utilizar** composer** (o un autoloader de algún framework) para el manejo de la carga de clases automática, y usar el estándar PSR-4 (actualmente el más recomendado). Si por ejemplo tenemos la siguiente estructura:
```
/app
/public
/src
    Usuario.php
    Topic.php
/vendor
```

Suponemos que el namespace **Proyecto** se encuentra en _/src_. El directorio /src representaría el nivel más alto del namespace. Si utilizamos composer para cargar las clases, simplemente hay que decirle a composer cómo cargarlas. Vamos a indicarle que utilice el estándar PSR-4 desde composer.json:

```
{
    "autoload": {
        "psr-4":{
            "Proyecto\\": "src/"
        }
    }
}
```

Para más información del autoloading en composer, puedes visitar la [documentación](https://getcomposer.org/doc/01-basic-usage.md#autoloading).