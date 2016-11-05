Desde **PHP 5** es posible utilizar el **type hinting**, o la **determinación de tipos**, que permite especificar el **tipo de datos** que se espera de un **argumento** en la declaración de una función. Cuando se llama a la función, **PHP** comprobará si los argumentos son del tipo especificado, y sino lo son se emitirá un error y la ejecución se detendrá.

Los **tipos soportados** por esta característica (en PHP 5.5) son: _arrays_, _clases_, _interfaces_, y _callables_.

El **objetivo de la determinación de tipos** es organizar mejor el código y mejorar el manejo de los errores.

### ¿Cuándo utilizar type hinting?

Cuando queremos **forzar a una función a aceptar sólo argumentos de un tipo**. Su uso más común es con objetos, indicando el nombre de la clase:

```
class Aguacate {
    public $calorias = 100;
}
class Persona {
    public $energia;
    public function comerAguacate (Aguacate $aguacate) {
        $this->energia += $aguacate->calorias;
    }
    public function mostrarEnergia(){
        return "Energía actual: " . $this->energia . "<br>";
    }
}
$sonia = new Persona;
$almuerzo = new Aguacate;
$sonia->comerAguacate($almuerzo);
$sonia->comerAguacate($almuerzo);
echo $sonia->mostrarEnergia(); // Devuelve: Energía actual 200
```

En el ejemplo anterior, hemos forzado a que el argumento que se pasa a la función _comerAguacate()_ sea un objeto de la clase **Aguacate**. Si el _$almuerzo_ no fuera un objeto de la clase Aguacate, no se podría emplear la función _comerAguacate()_. Si por ejemplo en el ejemplo anterior cambiamos el almuerzo por un _**string**_ kiwi:

```
$almuerzo = "Kiwi";
$sonia->comerAguacate($almuezo);
// Devuelve: Catchable fatal error: Argument 1 passed to Persona::comerAguacate() must be an instance of Aguacate, string given
```

Es necesario crear una instancia de Aguacate para que _comerAguacate()_ acepte el argumento.

Vamos a poner **otro ejemplo**, ahora queremos que el argumento que se acepte sea del tipo _**array**_:

```
class Persona {
    public $energiaMedia;
    public $comidas;
    public function calcularEnergia(array $comidas)
    {
        $numeroComidas = count($comidas);
        $energiaTotal = $numeroComidas * $this->energiaMedia;
        return $energiaTotal;
    }
}
$sonia = new Persona;
// Energía media que proporciona cada comida:
$sonia->energiaMedia = 480;
// Total de comidas:
$comidas = array("Desayuno", "Almuerzo", "Comida", "Cena");
// Calcular la energía:
echo $sonia->calcularEnergia($comidas); // Devuelve 1920
```

La función _calcularEnergia()_ exige que el argumento sea un _**array**_. Si cambiamos el argumento y ponemos un _**integer**_, dará error:

```
$comidas = 4;
echo $sonia->calcularEnergia($comidas);
// Catchable fatal error: Argument 1 passed to Persona::calcularEnergia() must be of the type array, integer given 
```