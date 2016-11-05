En las funciones pueden incluirse argumentos. Los **argumentos de las funciones** son un conjunto de **expresiones** delimitas por comas evaluadas de izquierda a derecha que se emplean para pasar información a las **funciones**.

**Indice de contenido**

1.  Formas de pasar argumentos a las funciones
2.  Declaraciones de tipos
3.  Listas de argumentos de longitud variable

### 1. Formas de pasar argumentos a las funciones

Se pueden pasar argumentos por **valor**, por **referencia** y por **valor predeterminado**.

*   **Pasar argumentos por valor:**

```
<?php
// En este ejemplo vamos a pasar una variable array:
$array = ['perro', 'gato', 'avestruz'];
function mostrarAnimales($animales){
{
    foreach ($animales as $animal){
        echo $animal . "<br>";
    }
}
mostrarAnimales($array);
```

*   **Pasar argumentos por referencia**

Por defecto los argumentos de las funciones se pasan **por valor**, si el valor del argumento dentro de la función cambia, éste no cambia fuera de la función.

Para **permitir a una función que cambie un argumento**, se tiene que pasar por referencia, y se consigue anteponiendo el símbolo **ampersand &** en el argumento:

```
<?php
$array = ['perro', 'gato', 'avestruz'];
function cambiarAnimales(&$animales){
    $animales = ['mono', 'gorila', 'cebra'];
}

cambiarAnimales($array);
foreach($array as $ar){
    echo $ar . "<br>";
} // Muestra una lista con los nuevos animales: mono, gorila y cebra
```

*   **Valores de argumentos predeterminados**

Se puede predeterminar el valor de una función:

```
<?php
function verPelicula($titulo = "Avatar"){
    return "Vamos a ver la película $titulo" . "<br>";
}
echo verPelicula();
echo verPelicula("Braveheart"); 
```

También se pueden poner **arrays** y de tipo **null** como valores predeterminados:

```
function verPeliculas($titulos = array("Avatar"), $genero = null){
    $generoPelicula = is_null($genero) ? "" : " cuyo género es: $genero";
    return "Hoy vamos a ver: ".join(", ", $titulos) . $generoPelicula . "<br>";
}
echo verPeliculas();
echo verPeliculas(array("La jungla de cristal", "007", "Transporter"), "acción");
```

El valor predeterminado tiene que ser una **expresión constante** (no una variable por ejemplo), un miembro de una clase o una llamada a una función. 

**Los valores predeterminados van siempre a la derecha de los no predeterminados**.

### 2. Declaraciones de tipos

Las **declaraciones de tipos**, determinación de tipos, o **type hinting**, permiten a las funciones exigir que los parámetros proporcionados sean de un tipo determinado. Si el tipo proporcionado no es correcto, se generará un error (en PHP 5 el error es fatal).

Para especificar una declaración de tipo se antepone el nombre del tipo al nombre del parámetro. Se puede hacer que la declaración acepte valores **null** si el valor predeterminado del parámetro se establece a null.

**Tabla de tipos válidos en PHP5.***

| | |
| -------- | -------- |
| **Tipo** | **Descripción** |
| **nombre de clase**/**interfaz** | El parámetro ha de ser un _**instaceof**_ del nombre de la clase o interfaz proporcionada. Incluye a los padres de clase |
| **self** | El parámetro ha de ser una _**instanceof**_ de la misma clase donde está definido el método |
| **array** | El parámero ha de ser un array |
| **callable** | El parámetro ha de ser un callable válido |

*   Ejemplo de **type hinting con clase**:

```
class A {}

class B extends A {}
// C no extiende a A
class C {}

function prueba(A $a)
{
echo get_class($a) . "<br>";
}

prueba(new A); // Devuelve: A
prueba(new B); // Devuelve B
prueba(new C); // Catchable fatal error: Argument 1 must be an instance of A
```

*   Ejemplo de **type hinting con interface**:

```
interface Social {
    public function funcionRedes();
}
// Clase que implementa la interface
class Articulo implements Social {
    public function funcionRedes(){}
}
// Clase que no implementa la interface
class Otra {}

function prueba(Social $argumento){
    echo get_class($argumento) . "<br>";
}

prueba(new Articulo); // Devuelve: Articulo
prueba(new Otra); // Catchable fatal error, must implement interface Social
```

*   Ejemplo de type hinting con **tipo null**:

```
class Social {}

function redSocial(Social $network = null)
{
    var_dump($network);
}

redSocial(new Social); // Devuelve: object(Social)[1]
redSocial(null); // Devuelve: null
```

### 3. Listas de argumentos de longitud variable

**PHP** soporta listas de **argumentos de longitud variable** en funciones definidas por el usuario. En versiones PHP 5.5 y anteriores se utilizan las funciones [func_num_args()](http://php.net/manual/es/function.func-num-args.php), [func_get_arg()](http://php.net/manual/es/function.func-get-arg.php) y [func_get_args()](http://php.net/manual/es/function.func-get-args.php).

No se requiere de sintaxis especial para señalar que una **función** es **variádica**, pero se debe utilizar las funciones anteriores:

```
function suma() {
    $cuenta = 0;
    foreach(func_get_args() as $numero){
        $cuenta += $numero;
    }
    return $cuenta;
}

function media(){
    $cuenta = 0;
    foreach(func_get_args() as $numero){
        $cuenta += $numero;
    }
    $resultado = round($cuenta / func_num_args(), 2);
    return $resultado;
}

echo suma(2, 5, 8); // Devuelve 15
echo media(5, 4, 6, 2); // Devuelve 4.25
```