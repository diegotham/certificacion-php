Los **iteradores en PHP** son muy útiles pero a veces puede resultar tedioso a la hora de implementar las numerosas **interfaces** para crear el objeto a iterar. Desde **PHP 5.5** están disponibles los **generadores**.

Un **generador** es como una **función** que actúa como un **iterador** y que devuelve un **array**. Un generador permite emplear _**foreach**_ para iterar datos de forma que no se necesite cargar el array en la memoria o se requiera mucho tiempo para generarse. En su lugar se escribe una **función generadora**, que es como una función normal.

En lugar de usar la palabra _return_ usa _yield_. _Yield_ significa producir/ceder (en este contexto). _Yield_ funciona de forma similar a _return_ ya que devuelve un valor, pero en lugar de remover la función de la pila, _yield_ guarda su estado, de esta forma puede continuar por donde estaba si se le llama otra vez. No es posible devolver un valor desde un generador pero si que se puede emplear _**return**_ sin un valor para terminar su ejecución.

Por ejemplo en la función _range()_ **PHP** tiene que generar un array con cada uno de los valores y devolverlo, lo que puede resultar en grandes arrays y consumir muchos recursos. Como alternativa se puede generar un generador _xrange()_ que sólo necesitará memoria para crear un objeto **Iterator** y controlar el estado actual del generador.

```
function xrange($start, $limit, $step = 1){
    if ($start < $limit){
        if($step <= 0){
            throw new LogicException("Step tiene que ser positivo");
        }
        for ($i = $start; $i <= $limit; $i += $step){
            yield $i;
        }
    } else {
        if($step >= 0){
            throw new LogicException("Step tiene que ser negativo");
        }
        for ($i = $start; $i <= $limit; $i += $step){
            yield $i;
        }
    }
}

```

Tanto _range()_ como _xrange()_ producirán la misma salida:

```
echo "Números impares de una cifra con range():";
foreach(range(1, 9, 2) as $numero){
    echo "$numero, ";
}
echo "<br>";
echo "Numero impares de una cifra con xrange():";
foreach(xrange(1, 9, 2) as $numero){
    echo "$numero, ";
}
```

Cuando se llama a una función _**generator**_, devuelve un objeto con el que se puede iterar. Este objeto es de la clase **Generator** e implementa la interface **Iterator** de la misma forma en que lo hace un **objeto iterador**. Cuando iteras sobre ese objeto, **PHP** llama al generador cada vez que necesita un valor. El estado se guarda cuando el **generador** hace _yield_ por lo que puede resumirse después cuando se necesite el próximo valor. Un ejemplo más sencillo:

```
function numeros(){
    echo "El generador ha empezado <br>";
    for ($i = 0; $i < 5; ++$i){
        yield $i;
        echo "Se ha hecho yield en $i <br>";
    }
    echo "El generador ha terminado <br>";
}
foreach(numeros() as $numero);
/*
Devuelve:
El generador ha empezado
Se ha hecho yield en 0
Se ha hecho yield en 1
Se ha hecho yield en 2
Se ha hecho yield en 3
Se ha hecho yield en 4
El generador ha terminado
*/
```

Los generadores no son un concepto nuevo, ya existen en **C#**, **Python**, **JavaScript** y **Ruby** (_enumerators_), y se suelen identificar con el uso de la palabra _yield_. Ahora vamos a emplear un ejemplo para recorrer las líneas de un archivo:

```
function lineasDeArchivo($nombreArchivo){
    $archivo = fopen($nombreArchivo, 'r');
    while (($linea = fgets($archivo)) !== false){
        yield $linea;
    }
    fclose($archivo);
}
foreach (lineasDeArchivo("archivo.txt") as $linea){
    echo $linea . "<br>";
}
```

La función _generator_ abre el archivo y hace _yield_ en cada línea del archivo cuando se le ordena. Cada vez que se llama a _generator_, continúa desde donde lo dejó. Cuando se han leído todas las líneas el generador termina junto con el loop.

Los **iteradores en PHP** están formados por parejas _key_/_value_. En el ejemplo anterior sólo se ha devuelto _value_, las _keys_ eran numéricas (es así por defecto). Para devolver un array asociativo simplemente hay que usar la misma sintaxis que con los arrays asociativos:

```
foreach (lineasDeArchivo($nombreArchivo) as $key => $value) {
...
}
```

Pero yield no sólo devuelve valores, puede recibirlos también. Esto se hace mediante el método _send()_ del objeto _**generator**_ con el valor que quieras pasar. Este valor puede usarse para computar o para cualquier otra cosa:

```
function numeros(){
    for ($i = 0; $i < 10; ++$i){
        // Obtener un valor de llamada
        $cmd = (yield $i);
        if ($cmd == 'parar'){
            return; // Salir del generador
        }
    }
}
$generador = numeros();
foreach($generador as $valor){
    // Sólo queremos mostrar hasta 5
    if($valor == 5){
        $generador->send('parar');
    }
    echo "$valor, ";
}
// Devuelve: 0, 1, 2, 3, 4, 5
```

Los generadores son especialmente **útiles para calcular grandes conjuntos de datos** y no se quiere emplear excesiva memoria para mostrar todos al mismo tiempo o cuando no se sabe si se necesitarán todos los resultados. Debido a la forma en que se procesan los resultados con los generadores, **el uso de memoria se puede reducir al mínimo asignando la memoria sólo al resultado actual**.

Vamos a poner un ejemplo de la función _file()_, que devuelve todas las líneas de un archivo como un _**array**_, frente a la función que hemos creado antes, _lineasDeArchivo()_:

```
// Usando lineasDeArchivo()
$memoria = memory_get_peak_usage();
foreach (lineasDeArchivo("archivo.txt") as $a);
echo memory_get_peak_usage() - $memoria . "<br>"; // 10400
// Usando file()
$memoria = memory_get_peak_usage();
foreach(file("archivo.txt") as $a);
echo memory_get_peak_usage() - $memoria . "<br>"; // 25536
```

Si se emplea con pocas líneas emplea un poco más de memoria _lineasDeArchivo()_, pero cuando el archivo comienza a tener un número de líneas considerable, _file()_ aumenta mucho su uso y _lineasDeArchivo()_ mantiene su valor.