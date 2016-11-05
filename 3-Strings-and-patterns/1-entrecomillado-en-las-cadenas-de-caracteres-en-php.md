Un _**string**_ o **cadena de caracteres** es una serie de caracteres donde cada carácter equivale a un _**byte**_. Esto conlleva a que PHP sólo admite un conjunto de 255 caracteres, y por tanto no hay **soporte nativo** para [Unicode](https://es.wikipedia.org/wiki/Unicode). 

El **tamaño máximo de un _string_** son 2GB (2147483647 bytes).

Existen varias formas de mostrar un _string_:

1.  Entrecomillado simple
2.  Entrecomillado doble
3.  Análisis de variables

Otra forma de mostrar strings es mediante **Heredoc y Nowdoc**.

### 1. Entrecomillado simple

Es la forma más sencilla de delimitar un _string_: '_string_'.

Si se quiere especificar una **comilla simple dentro de un _string_**, se escapa con una barra invertida \, y se hace lo mismo para especificar una **barra invertida dentro de un _string_** \\. Cualquier otra secuencia de escape o variable se mostrará literalmente, por ejemplo: **\r**, **\n**, **$variable**...

**Algunos ejemplos:**

```
<?php
echo 'Cadena simple';

echo 'Las nuevas líneas
pueden formarse de
esta forma
';

echo 'En inglés "do not" se puede escribir como "don\'t"';

echo 'En Windows el directorio base es C:\\';

echo 'No funcionan ni las variables como $variable ni
otras sentencias de escape para caracteres especiales como \n';
```

### 2. Entrecomillado doble

Si el string está delimitado con **comillas dobles** "_string_", **PHP** interpretará más sentencias de escape para caracteres especiales:

| | |
| -------- | -------- |
| **Secuencia** | **Significado** |
| \n | avance de línea |
| \r | retorno de carro |
| \t | tabulador horizontal |
| \v | tabulador vertical |
| \e | escape |
| \f | avance de página |
| \\ | barra invertida |
| \$ | signo de dólar |
| \" | comillas dobles |
| \[0-7]{1,3} | secuencia de caracteres que coincida con la expresión regular, carácter en notación octal |
| \x[0-9A-Fa-f]{1,2} | secuencia de caracteres que coincida con la expresión regular, carácter en notación hexadecimal |

Lo más significativo del entrecomillado doble de un string es el hecho de que **se expanden los nombres de las variables**. 

### 3. Análisis de variables

Si un _string_ se especifica con **comillas dobles** o **heredoc**, las variables que se nombren en su interior se analizarán. Existen dos tipos de sintaxis: **simple** y **compleja**. En la **compleja** los elementos van delimitados por llaves.

#### **Sintaxis simple**

La sintaxis **simple** es la más empleada en la práctica y ofrece una forma sencilla de embeber una variable, un valor de array o una propiedad de un objeto:

```
$animal = "perro";
echo "Yo tengo un $animal" . PHP_EOL;

$animales = ['perro', 'gato', 'jabali', 'insecto'=>'cucaracha'];
echo "Yo tengo un $animales[1]" . PHP_EOL;
echo "Yo tengo un $animales[0]" . PHP_EOL;
echo "Yo quiero tener una $animales[insecto] como mascota" . PHP_EOL;
// Nótese que insecto no lleva comillas

class Usuario {
    public $carlos = "Carlos Perez";
    public $sonia = "Sonia Fernandez";
}
$usuario = new Usuario();

echo "El usuario $usuario->carlos tiene un $animales[0]" . PHP_EOL;
echo "El usuario $usuario->sonia tiene 2 $animales[1]s" . PHP_EOL;
```

#### **Sintaxis compleja**

La sintaxis compleja permite **expresiones complejas**. Cualquier **variable**, **array** o **propiedad de objeto** con una representación _string_ se puede incluir con esta sintaxis. Se escribe de la misma forma que la anterior pero delimitándola con { y }. Los corchetes no pueden ser escapados, por lo que se reconoce sólo cuando le siga un símbolo del dólar $ (sin espacio de por medio). Para un {$ literal se utiliza {\$. Ejemplos:

* # Acceso a una simple variable

```
$color = "verde";
echo "Ese color es { $color}" . PHP_EOL; // Muestra: { verde}
echo "Ese color es {$color}" . PHP_EOL; // Muestra: verde
echo "Ese color es ${color}" . PHP_EOL; // Muestra: verde
```

* # Acceso a propiedades de clases

```
class Coche {
  public $color = "azul";
  public $tipo = array("Marca" => "SEAT", "Modelo" => "Toledo");
}
$miCoche = new Coche;
```

Para acceder a un valor de propiedad puede usarse sintaxis compleja o simple:

```
echo "Ese coche es {$miCoche->color}" . PHP_EOL;
echo "Ese coche es $miCoche->color" . PHP_EOL;
```

Si se trata de valores de array dentro de propiedades, necesitamos la compleja:

```
echo "Los coches {$miCoche->tipo['Marca']} son de buena calidad" . PHP_EOL;
```

Funciona sin comillas, pero genera un Notice:

```
echo "Marca de coche: {$miCoche->tipo[Marca]}" . PHP_EOL; // Notice "Undefined constant"
```

* # Acceso a array multidimensional

```
$coche = array(
    "Marca" => array (
        0 => "Toyota",
        1 => "Mercedes"
    )
);
```

De nuevo accedemos con comillas si el array es asociativo:

```
echo "La marca de mi coche es: {$coche["Marca"][0]}" . PHP_EOL;
```

También es posible con arrays multidimensionales en propiedades:

```
echo "String a mostrar: {$objeto->variables[2]->nombre}" . PHP_EOL;
```

* # Variables variables

```
$animal = "perro";
$perro ="Bobby";
echo "Nombre de mi perro: {${$animal}}" . PHP_EOL; // Devuelve: Nombre de mi perro: Bobby
```

Lo siguiente devuelve la variable con el nombre devuelto por _getName()_:

```
function getName(){
  return "getFullName";
}
$getFullName = "Bobby McAllister";
echo "Nombre completo de mi perro: {${getName()}}" . PHP_EOL;
```

Ocurriría lo mismo con un método:

```
echo "Nombre completo de mi perro: {${$objeto->getName()}}";
```

Con una propiedad de clase:

```
class Clase {
  public $var = 'Soy una variable';
}
$clase = new Clase();
$miVariable = 'var';
echo "{$clase->$miVariable}\n"; // Devuelve: "Soy una variable"
$miArray = array('var', 'novar');
echo "{$clase->$miArray[0]}\n"; // Devuelve: "Soy una variable"
```

Con la **sintaxis compleja {$}** el valor se interpreta como el nombre de la variable en el ámbito en el que está definido el string. Esto significa que no se puede acceder a valores devueltos por funciones o métodos. Tampoco a constantes o variables estáticas dentro de clases:

```
class SEAT {
    const modelo = "toledo";
    public static $color = "tono";
}

$toledo = "Hybrid";
$tono = "Oscuro";

echo "La versión del SEAT Toledo es {${SEAT::modelo}}" . PHP_EOL; // Devuelve: La versión del SEAT Toledo es Hybrid
echo "El color del SEAT Toledo {${SEAT::modelo}} es {${SEAT::$color}}"; // Devuelve: El color del SEAT Toledo Hybrid es Oscuro
```

Lo que se devuelve en el ejemplo anterior es un **nombre de variable**, el cual buscará en el entorno en el que se encuentre la llamada.