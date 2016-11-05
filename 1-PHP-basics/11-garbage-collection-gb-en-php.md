El **garbage collection**, o **recolección de basuras**, es un sistema que permite a PHP limpiar la memoria cuando así lo considere. También es posible activar y desactivar esta característica desde el código para precisar tiempos y reducir la memoria en aplicaciones concretas.

**Indice de contenido**

1.  Almacenamiento de variables en la memoria
2.  Valores de tipo compuesto
3.  Problemas de limpieza (anteriores a PHP 5.3)
4.  Recolección de referencias cíclicas

### 1. Almacenamiento de variables en la memoria

Una variable en PHP se almacena en un **contenedor** llamado **_zval_**. Éste contiene el tipo de variable, su valor y dos bits de información:

*   El primer bit es _is_ref _que contiene un **boolean** que indice si la variable es parte o no del conjunto de referencias. Con este bit el **motor de PHP** diferencia entre **variables** normales y **referencias**.
*   El segundo bit es _refcount_, que contiene el **número** de variables (también llamadas _**símbolos**_) que apuntan a este contenedor _zval_. **PHP** permite al usuario definir referencias (con **&**) y el contador permite optimizar el uso de memoria.

Los símbolos se almacenan en una tabla de **símbolos**, una por cada ámbito (hay un ámbito para el script principal, y otro por cada función y método).

Un **contenedor zval** se crea cuando se crea una variable con **valor constante**, por ejemplo:

```
<?php
$x = "hola";
```

El nuevo **nombre de símbolo**, x, se crea en el **ámbito actual**. Se crea un nuevo **contenedor de variable** de **tipo _string_** y **valor** _hola_. El bit _is_ref_ será falso pues no contiene ninguna **referencia**. El bit _refcount_ vale 1 ya que sólo hay un **símbolo** que haga uso de este contenedor de variables (nótese que si _refcount_ vale 1, _is_ref_ siempre será **false**). Si tienes instalado [Xdebug](http://xdebug.org/) puedes mostrar esta información mediante la función _**xdebug_debug_zval():**_

```
<?php
xdebug_debug_zval('x'); // muestra x: (refcount=1, is_ref=0), string 'hola'
```

Al asignar la variable a otra se incrementa _refcount_:

```
<?php
$x = "hola";
$y = $x;
xdebug_debug_zval('x'); // muestra x: (refcount=2, is_ref=0), string 'hola'
```

_refcount_ vale 2 ya que el **mismo contenedor de variables** está vinculado tanto por x como por y. PHP no copia el contenedor de variable cuando no es necesario. Los contenedores de variables se detruyen cuando _refcount_ se queda a cero. _refcount_ decrece en uno cuando alguno de los símbolos que lo vinculan abandona su ámbito (como cuando finaliza una función) o cuando se llama a _unset()_:

```
<?php
$x = "hola";
$y = $z = $x;
xdebug_debug_zval('x'); // x: (refcount=3, is_ref=0), string 'hola'
unset($y, $z);
xdebug_debug_zval('x'); // x: (refcount=1, is_ref=0), string 'hola'
```

Si se llama a _unset($x)_ el contenedor de variable, tanto el tipo como el valor, se eliminan de la memoria.

### 2. Valores de tipo compuesto

Con **arrays** y **objetos** esto es algo más complejo. En lugar de un **valor escalar**, los arrays y objetos almacenan sus propiedades en su propia **tabla de símbolos**. 

```
<?php
$x = array('animal' => 'perro', 'edad'=> 10);
xdebug_debug_zval('x');
/* Devuelve:
x: (refcount=1, is_ref=0), array(
    'animal' => (refcount=1, is_ref=0), string 'perro'
    'edad' => (refcount=1, is_ref=0), int 10
)
```

Se crean tres contenedores: x, animal y edad. Las normas son similares a la hora de aumentar y disminuir _refcounts_. Si añadimos otro elemento, y fijamos su valor a un elemento ya existente:

```
<?php
$x = array('animal' => 'perro', 'edad' => 10);
$x['servivo'] = $x['animal'];
xdebug_debug_zval('x');
/* El resultado ahora sería
x: (refcount=1, is_ref=0), array(
    'animal' => (refcount=2, is_ref=0), string 'perro'
    'edad' => (refcount=1, is_ref=0), int 10
    'servivo' => (refcount=2, is_ref=0), string 'perro'
)
```

Tanto _animal_ como _servivo_ apuntan al mismo contenedor, cuyo _refcount_ vale 2 (aunque xdebug muestre dos **contenedores zval** de valor _perro_, se refiere al mismo). 

Eliminar un elemento del _array_ viene a ser lo mismo que eliminar un **símbolo** de un determinado **ámbito**. Cuando se hace, el _refcount_ del contenedor al que apunta el elemento del array decrece. Al igual que antes, si _refcount_ llega a cero, se elimina de la memoria. 

```
<?php
$x = array('animal' => 'perro', 'edad' => 10);
$x['servivo'] = $x['animal'];
unset($x['animal'], $x['edad']);
xdebug_debug_zval('x');
/* Devuelve:
x: (refcount=1, is_ref=0), array(
    'servivo' => (refcount=1, is_ref=0),string 'perro'
```

Utilizando el operador de referencia, la cosa cambia:

```
<?php
$x = array('uno');
$x[] = &$x;
xdebug_debug_zval('x');
/* Devuelve:
x: (refcount=2, is_ref=1), array(
    0=>(refcount=1, is_ref=0, string 'uno'
    1=>(refcount=2, is_ref=1),&array
)
```

Tanto la variable de tipo array _**x**_ como su segundo elemento _**1**_ apuntan a un **contenedor de variables** que tiene un _refcount_ de 2. 

### 3. Problemas de limpieza (anteriores a PHP 5.3)

Si eliminamos una variable se elimina el símbolo, y el contador de referencias del contenedor de variables al que apunta decrece en uno. Si eliminamos _$x_, el contador de referencias del contenedor de variables al que apuntan _**x**_ y **1** decrece en uno.

A pesar de que no hay ningún símbolo que apunte a esta estructura, no se puede limpiar porque el elemento **1** del array todavía apunta al mismo array. Al no haber ningún símbolo externo que apunte a él, no hay ninguna forma con la que el usuario pueda eliminar esta estructura, por lo que ocurre una **fuga de memoria**. PHP limpiará esta estrutura de datos al finalizar la petición, pero antes ocupará un espacio en la memoria. Es frecuente que ocurra con **objetos** ya que siempre se usan por referencia. 

Esto no resulta un problema cuando ocurre pocas veces, pero cuando ocurre miles, comienza a ser un problema. 

Con la **recolección de referencias cíclicas**, a partir de PHP 5.3, esto se solventa.

### 4. Recolección de referencias cíclicas

El algoritmo se encuentra en [este artículo](http://researcher.watson.ibm.com/researcher/files/us-bacon/Bacon01Concurrent.pdf). 

Si se incrementa un _refcount_ significa que se sigue usando el contenedor, no es **basura**. Si decrece y alcanza el valor de 0, se elimina automáticamente, lo que significa que la recolección de ciclos sólo se lleva a cabo cuando un parámetro _refcount_ decrece a un valor que no sea cero.

Es posible comprobar qué partes son basura comprobando si se puede decrementar en uno sus _refcount_, para después comprobar cuáles han alcanzado a cero. 

Para evitar llamar al comprobador de ciclos de basura en cada decremento de un _refcount_, el algoritmo pasa todos los posibles **roots** (zvals) al **root buffer**. Sólo cuando el root buffer está lleno, comienza el proceso de comprobación de todos los **zvals** que están dentro.

Por defecto, el recolector de basuras en PHP está habilitado (se puede cambiar en php.ini con _zend.enable_gc_). Cuando está habilitado, el algoritmo que busca ciclos se ejecuta cada vez que se llena el **root buffer**. Éste tiene un tamaño de 10.000 posibles roots. 

Desde el código es posible habilitar o deshabilitar el mecanismo recolector de basura mediante las funciones [_gc_enable()_](http://php.net/manual/es/function.gc-enable.php) o [_gc_disable()_](http://php.net/manual/es/function.gc-disable.php). La llamada a estas funciones tiene el mismo efecto que habilitar o deshabilitar el mecanismo en la propia configuración. También se puede forzar la recolección de ciclos sin que esté lleno el root buffer, llamando a [_gc_collect_cycles()_](http://php.net/manual/es/function.gc-collect-cycles.php), que devuelve el número de ciclos recolectados por el algoritmo.

La razón por la que se puede habilitar o deshabilitar el mecanismo, o iniciar ciclos de recolección a mano, es porque puede que haya partes en una aplicación que necesiten mucha precisión de tiempo. Lo más prudente si se llama a _gc_disable()_ es llamar a _gc_collect_cycles()_ para que libere la memoria por posibles raíces ya registradas en el **root buffer**.