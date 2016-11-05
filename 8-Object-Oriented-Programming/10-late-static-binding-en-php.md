**Late Static Binding**, o **Enlace Estático de Ejecución**, es una funcionalidad que permite **hacer referencias a una clase dentro de un contexto de herencia estática**. En este artículo se explican primero las propiedades y métodos estáticos, que utilizan el mismo **operador ::**, y después el **late static binding**.

**Indice de contenido**

1.  La palabra reservada _static_
2.  Late Static Binding
3.  Funciones para Late Static Binding

### 1. La palabra reservada _static_

En el contexto de clases y objetos la palabra reservada _**static**_ permite definir **métodos y propiedades estáticos** (lo que no tiene que ver con el concepto de [variables estáticas](http://diego.com.es/variables-en-php#AmbitoDeLasVariables)).

Un **método o propiedad estático** es accesible sin la necesidad de instanciar la clase en la que se encuentra. Esto provoca que si la clase se instancia, una **propiedad estática** no puede ser accedida desde ese objeto (aunque no ocurre lo mismo con los **métodos**, que aunque sean **estáticos** si pueden ser accedidos desde objetos).

#### **Métodos estáticos**

Ya que los **métodos estáticos** pueden ser llamados sin tener una instancia de la clase, la pseudovariable _**$this**_ no está disponible en estos métodos.

```
class MiClase {
    public static function miMetodo(){
        echo "Hola este es mi método <br>";
    }
}
// Forma 1 de llamar al método estático
$miclase = new MiClase;
$miclase->miMetodo(); // Devuelve: Hola este es mi método
// Forma 2
echo MiClase::miMetodo(); // Devuelve: Hola este es mi método
// Forma 3
$clase = "MiClase";
echo $clase::miMetodo(); // Devuelve: Hola este es mi método
```

Si se intenta llamar a un método no estático de forma estática genera una **advertencia de nivel E_STRICT**.

#### **Propiedades estáticas**

A las propiedades estáticas no se puede acceder mediante el **operador ->**.

```
class MiClase {
    public static $miEstatica = "Esta es mi variable estática <br>";
    public function mostrarMiEstatica() {
        return self::$miEstatica;
    }
}
class MiOtraClase extends MiClase {
    public function mostrarMiEstaticaDeNuevo(){
        return parent::$miEstatica;
    }
}
```

Vamos a ver varios ejemplos:

*   Mostramos la variable estática:

```
echo MiClase::$miEstatica; // Devuelve: Hola, esta es mi variable estática
```

*   Llamamos a una función que muestra la variable estática mediante self:

```
$miClase = new MiClase;
echo $miClase->mostrarMiEstatica(); // Devuelve: Hola, esta es mi variable estática
```

*   Si intentamos acceder a la propiedad estática como una propiedad normal:

```
echo $miClase->miEstatica; // Strict standards: Accessing static property MiClase::$miEstatica as non static
// y también un Notice: Undefined property: MiClase::$miEstatica
```

*   Llamamos a la variable estática desde un objeto:

```
echo $miClase::$miEstatica; // Devuelve: Hola, esta es mi variable estática
```

*   Llamamos a la variable estática desde la clase especificada mediante una variable:

```
$miVariable = 'MiClase';
echo $miVariable::$miEstatica; // Devuelve: Hola, esta es mi variable estática
```

*   También podemos acceder a la estática desde la clase hija:

```
echo MiOtraClase::$miEstatica; // Devuelve: Hola, esta es mi variable estática
$miOtraClase = new MiOtraClase;
echo $miOtraClase->mostrarMiEstaticaDeNuevo(); // Devuelve: Hola, esta es mi variable estática
```

### 2. Late Static Binding

El **Late Static Binding** permite **hacer referencias a una clase dentro de un contexto de herencia estática**. Lo que hace esta funcionalidad es almacenar el nombre de clase de la última llamada.

```
class A {
    public static function miFuncion(){
        // Mostrará el nombre de la clase actual:
        echo __CLASS__;
    }
    public static function otraFuncion(){
        // Empleará la función desde donde sea llamada
        static::miFuncion();
    }
}
class B extends A {
    public static function miFuncion(){
        // Mostrará el nombre de la clase actual:
        echo __CLASS__;
    }
}
B::otraFuncion(); // Devuelve B
```

En el ejemplo anterior se ha llamado a _otraFuncion()_ que es un método de **A**, desde la clase **B**. _otraFuncion()_ simplemente **devuelve estáticamente** la función _miFuncion()_, es decir, que la devuelve desde donde se ha llamado, que es desde **B**. Es por ello que al mostrar **__CLASS__**, muestra la clase desde donde se hace la llamada, **B**.

Si en este mismo ejemplo anterior cambiamos _**static::**_ por _**self::**_ en la función _**otraFuncion()**_, devolverá A, porque _**self::**_ se resuelve empleando el nombre de la clase a la que pertenece la función, no desde donde se ha llamado:

```
class A {
    public static function miFuncion(){
        // Mostrará el nombre de la clase actual:
        echo __CLASS__;
    }
    public static function otraFuncion(){
        // Empleará la función desde A
        self::miFuncion();
    }
}
class B extends A {
    public static function miFuncion(){
        // Mostrará el nombre de la clase actual:
        echo __CLASS__;
    }
}
B::otraFuncion(); // Devuelve A
```

_**static::, self::**_ y _**parent::**_ sólo pueden hacer referencia a _**propiedades y métodos estáticos**_ o instanciar clases (new static, new self, new parent).

El siguiente ejemplo puede aclarar mejor las tres opciones:

```
class Abuelo {
    function className(){
        return __CLASS__;
    }
    function selfName(){
        return self::className();
    }
    function staticName(){
        return static::className();
    }
}
class Padre extends Abuelo {
    function parentName(){
        return parent::className();
    }
    function className(){
        return __CLASS__;
    }
}
class Nieto extends Padre {
    function parentName(){
        return parent::className();
    }
    function className(){
        return __CLASS__;
    }
}
```

Vamos a hacer llamadas a métodos desde un objeto Nieto:

```
// Hacemos llamadas desde nieto
$nieto = new Nieto();

// Llamamos a selfName()
echo $nieto->selfName() . '<br/>'; // Devuelve Abuelo

// Llamamos a parentName()
echo $nieto->parentName() . '<br/>'; // Devuelve Padre

// Por último llamamos a staticName()
echo $nieto->staticName(); // Devuelve Nieto
```

El siguiente ejemplo es un poco más complejo, ya que mezcla los conceptos:

```
class A {
    public static function miFuncion() {
        static::className();
    }
    public static function className() {
        echo __CLASS__."\n";
    }
}
class B extends A {
    public static function prueba() {
        A::miFuncion();
        parent::miFuncion();
        self::miFuncion();
    }
    public static function className() {
        echo __CLASS__."\n";
    }
}
class C extends B {
    public static function className() {
        echo __CLASS__."\n";
    }
}
C::prueba();

```

Devuelve A C C:

1.  Llama a _A::miFuncion()_, el origen de la llamada es A, por lo que al llamar a _miFuncion()_, _static::className()_ devolverá el nombre de clase de A.
2.  Llama a _parent::miFuncion()_, el origen de la llamada es C, que llama a _parent::miFuncion()_ que estáticamente llama a _className()_. Como el origen es C, devolverá C.
3.  Llama a _self::miFuncion()_, de nuevo el origen es C, que llama a _self::miFuncion()_ y de forma estática llama a _className()_, que devolverá de nuevo C.

### 3. Funciones para Late Static Binding

*  forward_static_call

Llama a un **método estático** con los argumentos que se añadan para pasar al método.
```
mixed forward_static_call (callable $function [, mixed $parameter [, mixed $... ]] )
```

```
class Coche
{
    const NAME = 'Coche es';
    public static function caracteristicas() {
        $args = func_get_args();
        echo static::NAME, " " . join(' ', $args) . "<br/>";
    }
}
class Audi extends Coche
{
    const NAME = 'Audi es';
    public static function caracteristicas() {
        echo self::NAME, "<br/>";
        forward_static_call(array('Coche', 'caracteristicas'), 'mi', 'coche', 'favorito');
        forward_static_call('caracteristicas', 'el', 'coche favorito', 'de mi', 'hermano');
    }
}
Audi::caracteristicas();
function caracteristicas() {
    $args = func_get_args();
    echo "Mercedes es " . join(' ', $args);
}
/*
Devuelve:
Audi es
Audi es mi coche favorito
Mercedes es el coche favorito de mi hermano
*/
```

*  get_called_class

Obtiene un string con el nombre de la clase que hace la llamada.
```
string get_called_class ( void )
```

```
class A {
    static public function prueba(){
        var_dump(get_called_class());
    }
}
class B extends A {
}
A::prueba(); // Devuelve string 'A'
B::prueba(); // Devuelve string 'B'
```