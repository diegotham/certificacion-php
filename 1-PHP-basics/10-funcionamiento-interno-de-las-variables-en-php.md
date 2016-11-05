Entender **cómo funcionan las variables internamente en PHP** permite tener una visión más clara del lenguaje y entender cómo funcionan algunos **algoritmos de ahorro de memoria** como **garbage collection**.

**Indice de contenido**:

1.  Estructura de una variable en PHP
2.  Cómo PHP maneja las variables
3.  Copia en escritura
4.  Referencias
5.  Garbage Collection

### 1. Estructura de una variable en PHP

Las variables en PHP se representan internamente en un **contenedor** llamado **zval**:

```
struct _zval_struct {
    /* Variable information */
    zvalue_value value;    /* value */
    zend_uint refcount__gc;  /* reference counter */
    zend_uchar type;        /* value type */
    zend_uchar is_ref__gc; /* reference flag */
};
```

*   _value_: representa el **valor** de la variable.
*   _refcount_: es un **integer** que indica cuántos símbolos apuntan a este zval (normalmente **un símbolo es una variable PHP**, por ejemplo: x en $x).
*   type: indica el **tipo** de variable.
*   _is_ref_: es un ínteger que representa un valor **booleano**. Por defecto es 0, que indica que el zval aún no ha sido afectado por una referencia (**&$x**). Cuando es 1 indica que el zval es una referencia, lo que cambia totalmente el comportamiento de PHP respecto a esa variable.

El **contenedor** que se crea al definir una nueva variable depende del tipo de valor que se le asigne: 

*   Si definimos una **variable $x**:

```
<?php
$x = 'hola';
```

La representación de su **contenedor zval** es:

```
x:
(refcount=1, is_ref=0),string 'hola' (length=4)
```

*   Si definimos un **array $x**:

```
<?php
$x = array('saludo' => 'hola', 13);
```

Se crean 3 contenedores zval, el _string_ "hola", el _integer_ 13 y el _array_ que los recoge:

```
x:
(refcount=1, is_ref=0),
array (size=2)
  'saludo' => (refcount=1, is_ref=0),string 'hola' (length=4)
  0 => (refcount=1, is_ref=0),int 13
```

Los _keys_ del array **no** son **zvals**, sólo lo son sus valores. 

*   Si definimos un **objeto $objeto**:

```
<?php
class Clase {
    public $x = 13;
    protected $y = 'hola';
}
$objeto = new Clase;

```

También creamos 3 zvals: uno por el **objeto** bajo el símbolo $objeto y otro por cada una de sus **propiedades**: $x, $y:

```
objeto:

(refcount=1, is_ref=0),

object(Clase)[1]

  public 'x' => (refcount=2, is_ref=0),int 13

  protected 'y' => (refcount=2, is_ref=0),string 'hola' (length=4)
```

Los arrays se comportan de la misma forma que los objetos: como **tipos compuestos**. Cada vez que se crea un objeto o array se crea un contenedor, y uno más por cada elemento.

### 2. Cómo PHP maneja las variables

Vamos a crear una variable _$x_ y asignarla a una variable _$y_ (de momento sin usar referencias):

```
<?php
$x = "hola";
$y = $x;
```

En este caso se crea **un sólo contenedor zval**:

```
x:
(refcount=2, is_ref=0),string 'hola' (length=4)
y:
(refcount=2, is_ref=0),string 'hola' (length=4)
```

Las variables $x e $y son **símbolos**, que apenas tienen influencia en la memoria y hacen referencia al mismo contenedor. Lo que realmente consume es la creación de contenedores zval. PHP automáticamente señala al mismo **contenedor** desde diferentes símbolos, evitando así una duplicación.

El _refcount_ aumenta en función del número de símbolos que apuntan a un contenedor, en este caso 2, y es la clave para la administración de la memoria en las variables PHP.

### 3. Copia en escritura

**Copia en escritura**, _Copy On Write, COW_, es una forma de ahorrar memoria. PHP copia la memoria (o la relocaliza en una nueva región de memoria) cuando modificas un símbolo que estaba referenciando a un zval:

```
<?php
// Mismo ejemplo anterior
$x = "hola";
$y = $x;
?>
x:
(refcount=2, is_ref=0),string 'hola' (length=4)
y:
(refcount=2, is_ref=0),string 'hola' (length=4)
<?php
// Ahora modificamos el símbolo:
$x = 13;
?>
x:
(refcount=1, is_ref=0),int 13
y:
(refcount=1, is_ref=0),string 'hola' (length=4)
```

En el siguiente ejemplo se puede ver como si se asigna _$x_ a un nuevo valor y luego se le vuelve a asignar el mismo que _$y_, no hacen referencia al mismo zval:

```
<?php
$x = "hola";
$y = $x;
?>
x:
(refcount=2, is_ref=0),string 'hola' (length=4)
y:
(refcount=2, is_ref=0),string 'hola' (length=4)
<?php
$x = 13;
?>
x:
(refcount=1, is_ref=0),int 13
y:
(refcount=1, is_ref=0),string 'hola' (length=4)
<?php
$x = "hola";
?>
x:
(refcount=1, is_ref=0),string 'hola' (length=4)
y:
(refcount=1, is_ref=0),string 'hola' (length=4)
<?php
$y = $x;
?>
x:
(refcount=2, is_ref=0),string 'hola' (length=4)
y:
(refcount=2, is_ref=0),string 'hola' (length=4)

```

Si continuamos este ejemplo y añadimos una tercera variable **$z**:

```
<?php
$z = $y;
?>
x:
(refcount=3, is_ref=0),string 'hola' (length=4)
y:
(refcount=3, is_ref=0),string 'hola' (length=4)
z:
(refcount=3, is_ref=0),string 'hola' (length=4)
<?php
$y = "adios";
?>
x:
(refcount=2, is_ref=0),string 'hola' (length=4)
y:
(refcount=1, is_ref=0),string 'adios' (length=5)
z:
(refcount=2, is_ref=0),string 'hola' (length=4)
<?php
unset($x);
?>
x: no such symbol
y:
(refcount=1, is_ref=0),string 'adios' (length=5)
z:
(refcount=1, is_ref=0),string 'hola' (length=4)
```

Se puede ver como **PHP** va jugando con el valor de _refcount_, aumentándolo o disminuyéndolo en función de si un **símbolo** apunta o deja de apuntar a un **contenedor zval**.

El constructor del lenguaje _unset()_ no libera memoria, simplemente disminuye el número _refcount_ en 1, y sólo cuando _refcount_ alcanza 0, **PHP** sabe que no hay más símbolos apuntando al **zval** por lo que lo liberará automáticamente. 

**is_ref** será siempre 0 si no empleamos referencias.

Otro ejemplo, ahora con un **array**. Se comprueba que el los _values_ del array son los **zvals** (no sus _keys_):

```
<?php
$x = array("saludo" => "hola", 1=>22);
?>
x:
(refcount=1, is_ref=0),
array (size=2)
  'saludo' => (refcount=1, is_ref=0),string 'hola' (length=4)
  1 => (refcount=1, is_ref=0),int 22
<?php
$y = $x["saludo"];
?>
x:
(refcount=1, is_ref=0),
array (size=2)
  'saludo' => (refcount=2, is_ref=0),string 'hola' (length=4)
  1 => (refcount=1, is_ref=0),int 22
y:
(refcount=2, is_ref=0),string 'hola' (length=4)
<?php
$z = $y;
?>
x:
(refcount=1, is_ref=0),
array (size=2)
  'saludo' => (refcount=3, is_ref=0),string 'hola' (length=4)
  1 => (refcount=1, is_ref=0),int 22
y:
(refcount=3, is_ref=0),string 'hola' (length=4)
z:
(refcount=3, is_ref=0),string 'hola' (length=4)
<?php
$y = 17;
?>
x:
(refcount=1, is_ref=0),
array (size=2)
  'saludo' => (refcount=2, is_ref=0),string 'hola' (length=4)
  1 => (refcount=1, is_ref=0),int 22
y:
(refcount=1, is_ref=0),int 17
z:
(refcount=2, is_ref=0),string 'hola' (length=4)

```

Si ahora deshacemos el elemento _saludo_ de **$x**:

```
<?php
unset($x['saludo']);
?>
x:
(refcount=1, is_ref=0),
array (size=1)
  1 => (refcount=1, is_ref=0),int 22
y:
(refcount=1, is_ref=0),int 18
z:
(refcount=1, is_ref=0),string 'hola' (length=4)
<?php
$x[1] = $y;
?>
x:
(refcount=1, is_ref=0),
array (size=1)
  1 => (refcount=2, is_ref=0),int 18
y:
(refcount=2, is_ref=0),int 18
z:
(refcount=1, is_ref=0),string 'hola' (length=4)
```

En estos ejemplos ocurre el mismo comportamiento, y la misma norma principal: **sólo cuando el _refcount_ de un zval alcanza 0 el zval es liberado**.

### 4. Referencias

Una variable se asigna por referencia cuando se añade & delante:

```
<?php
$x = "hola";
$y =& $x;
$y = "adios";
```

Ahora $x e $y tienen el mismo valor: el _string_ "adios". Cuando hemos usado la referencia ambos valores se han atado al mismo **contenedor zval**. Realmente ha ocurrido lo mismo que anteriormente cuando no asignábamos por referencia $y = $x, el resultado es el mismo para este ejemplo sencillo. Pero el **funcionamiento interno de las referencias** afecta al **comportamiento de la copia en escritura**:

```
<?php
$x = "hola";
?>
x:
(refcount=1, is_ref=0),string 'hola' (length=4)
<?php
$y =& $x;
?>
x:
(refcount=2, is_ref=1),string 'hola' (length=4)
y:
(refcount=2, is_ref=1),string 'hola' (length=4)
<?php
$y = "adios";
x:
(refcount=2, is_ref=1),string 'adios' (length=5)
y:
(refcount=2, is_ref=1),string 'adios' (length=5)
```

Se puede observar como _refcount_ aumenta en 1, indicando el mismo número de símbolos apuntando al zval.

_is_ref_  hace que PHP tenga un comportamiento distinto cuando cambia un símbolo: **PHP** no separa el valor del **zval**, directamente lo cambia, y parece que $x e $y están atados.

Ahora veamos un ejemplo asignando una variable con referencia y otra sin:

```
<?php
$x = "hola";
$y =& $x;
?>
x:
(refcount=2, is_ref=1),string 'hola' (length=4)
y:
(refcount=2, is_ref=1),string 'hola' (length=4)
<?php
$z = $y;
?>
x:
(refcount=2, is_ref=1),string 'hola' (length=4)
y:
(refcount=2, is_ref=1),string 'hola' (length=4)
z:
(refcount=1, is_ref=0),string 'hola' (length=4)
```

Cuando se asigna $z a $y, PHP tiene que asignarla en un nuevo bloque de memoria, y duplicar el string 'hola', debido a la referencia anterior. Sin la referencia, sólo habría un contenedor zval, y el _refcount_ sería de 3.

La referencia fuerza a PHP a duplicarla. Es por esta razón por la que las referencias no necesariamente ahorran memoria, incluso puede ocurrir lo contrario. Depende del uso que se les de. 

### 5. Garbage collection

**Garbage collection** es un mecanismo que rastrea el uso de objetos y libera la memoria si no se encuentran en el scope actual o todavía no están siendo usados por el programador.

PHP ya empleaba este concepto desde el principio, pero hasta PHP 5.3 no liberaba la memoria de forma automática. A partir de PHP 5.3 surgió **Zend Garbage Collector**, o **Zend GC**. Lo más importante de esta nueva característica es el tratamiento de las **referencias circulares**.

Las referencias circulares aparecen con tipos compuestos, como arrays y objetos. Cuando dos tipos compuestos contienen referencias el uno al otro o cuando un tipo compuesto tiene una referencia a si mismo, se produce una referencia circular. Así la cosa se complica a la hora de liberar memoria, y antes de PHP 5.3 no se liberaba correctamente.

Ahora PHP rastrea automáticamente referencias circulares y las libera de vez en cuando. Si sabes que tu código tiene referencias circulares y quieres forzar a PHP a que las libere, puedes utilizar la función [gc_collect_cycles()](http://php.net/manual/es/function.gc-collect-cycles.php).