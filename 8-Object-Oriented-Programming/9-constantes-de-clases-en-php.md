Las **constantes de clases** permiten definir un valor en una clase y mantenerlo invariable durante la ejecución del script. Es como las constantes de PHP pero dentro de una clase.

Se diferencian de las variables en que no utilizan un **símbolo de dólar $** al declararlas o emplearlas, y su valor ha de ser siempre una **expresión constante** (no una variable, propiedad o callback).

Las [interfaces](http://diego.com.es/interfaces-en-php) y clases abstractas también pueden tener **constantes**.

```
class ClaseA {
    const EJEMPLO = 'valor invariable';
    function mostrarConstante(){
        echo self::EJEMPLO . "<br>";
    }
}
// 4 formas de acceder a la constante EJEMPLO:
// FORMA 1:
echo ClaseA::EJEMPLO . "<br>"; // Devuelve: valor invariable
// FORMA 2:
$clase = "ClaseA";
echo $clase::EJEMPLO . "<br>";// Devuelve: valor invariable
// FORMA 3:
$clase = new ClaseA;
$clase->mostrarConstante();// Devuelve: valor invariable
// FORMA 4:
$clase = new ClaseA;
echo $clase::EJEMPLO;// Devuelve: valor invariable
```

También es posible utilizar la sintaxis [Heredoc y Nowdoc](http://diego.com.es/heredoc-y-nowdoc-en-php) para **definir constantes de clases**:

```
class Perro {
    const LADRIDO = <<<"HEY"
GUAU!
HEY;
}
class Gato {
    const MAULLIDO = <<<'HEY'
MIAU!
HEY;
}
echo Perro::LADRIDO . "<br>"; // Devuelve: GUAU!
echo Gato::MAULLIDO; // Devuelve: MIAU!
```