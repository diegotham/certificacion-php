Los **arrays asociativos** son arrays cuyos keys son strings personalizados. Para **acceder a los valores de un array asociativo** se hace de la misma forma que con **arrays numéricos**, mediante **corchetes**:

```
$animales = [
    "Muffinhead" => 14,
    "Peter" => 4,
    "Monnie" => 7,
    "Banh" => 10
];
echo $animales["Muffinhead"] . PHP_EOL; // Devuelve 14
echo $animales["Monnie"] . PHP_EOL; // Devuelve 7
```

Las keys son sensibles a mayúsculas, minúsculas y acentos.

Añadir o modificar elementos de un array asociativo se hace de la misma forma que en arrays numéricos:

```
$animales["Farton"] = 12; // Se crea la key Farton
$edad["Monnie"] = 8; // Se modifica la key Monnie
```

A los **arrays asociativos** se puede acceder utilizando **comillas dentro de los corchetes** o sin utilizarlas. No utilizarlas funciona, aunque es incorrecto (o más bien se desaconseja totalmente su uso):

```
$animales = array(
    "perro" => "Muffinhead",
    "gato" => "Cuddles"
);
echo $animales[perro] . PHP_EOL; // Notice: Use of undefined constant perro, assumed 'perro'
echo $animales['gato'] . PHP_EOL;
```

En este caso hay una constante indefinida _perro_, en lugar del string "perro". Funciona porque **PHP** automáticamente convierte un **string puro** (sin comillas) en un string que contiene ese string puro. Si no se ha definido la constante _perro_, PHP lo reemplazará por el string 'perro' y lo usará.

Cuando se emplean **variables como keys** no hay que utilizar **comillas simples**, pues interpretará literalmente la variable: 

```
$array = array(1, 2);
$count = count($array);
for ($i = 0; $i < $count; $i++) {
    echo "\nRevisando $i: \n";
    echo "Mal: " . $array['$i'] . "\n"; // Genera Notice: Undefined index
    echo "Bien: " . $array[$i] . "\n";
    echo "Mal: {$array['$i']}\n"; // Genera Notice: Undefined index
    echo "Bien: {$array[$i]}\n";
}
```

Si se emplean **comillas dobles** en las keys **PHP** interpretará correctamente sus valores.

Si se ha creado una **constante**, y se accede con esa _key_ a un array, no genera un **E_NOTICE**:

```
define('animal', 'perro');
$animales = array (
    "perro" => "Pancake"
);
echo $animales[animal]; // Devuelve: Pancake;
echo "Nombre -> {$animales[animal]}"; // Devuelve: Nombre -> Pancake
```

En el segundo ejemplo se han de emplear los corchetes ya que se trata de **sintaxis compleja** al emplear una **constante como key**. Si no se emplean los corchetes genera un E_NOTICE.

Lo que se incluye en los **corchetes** debe ser una **expresión**. Puede ser una variable, una constante, el return de una función, etc.