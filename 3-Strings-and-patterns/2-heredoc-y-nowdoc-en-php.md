Heredoc se comporta de la misma forma que las **comillas dobles** pero tiene una sintaxis diferente, y Nowdoc se comporta de la misma forma que las **comillas simples** pero con otra sintaxis.

### Heredoc

La **sintaxis Heredoc** es la siguiente:

```
<?php
$hola = <<<EOD
HOLA HOLA
HOLA
EOD;

echo $hola;
```

Para delimitar un _string_, se inicia el operador **<<<**, se proporciona un **identificador** y después una nueva línea (en el ejemplo anterior el identificador es EOD). El identificador debe seguir las mismas normas que las **etiquetas en PHP**: sólo puede contener caracteres alfanuméricos y guiones bajos, empezando por un carácter alfabético o un guión bajo.

Seguidamente se escribe el _string_ que queremos mostrar. 

Para cerrar el _string_, se debe colocar el **identificador** de nuevo seguido de un **punto y coma**, en una nueva línea y sin ningún espacio, tabulación o carácter a su izquierda o derecha. El primer carácter antes del identificador de cierre debe ser un salto de línea definido por el sistema operativo local, y también ha de seguirle un salto de línea después (el punto y coma puede ir pegado o en la siguiente línea).

El **texto heredoc se comporta como un string entre comillas dobles**. No es necesario escapar las comillas en un **heredoc**, pero se pueden seguir empleando los códigos de la tabla de sentencias de escape para caracteres especiales.

```
<?php
class Clase {
    public $var;
    public $miArray;

    function miFuncion()
    {
        $this->var = 'Programación en PHP';
        $this->miArray = array('Perro', 'Gato', 'Liebre');
    }
}

$miClase = new Clase;
$miClase->miFuncion();
$nombre = "Diego";

echo <<<EOT
Mi nombre es $nombre. Esto es un artículo de $miClase->var.
Tengo un {$miClase->miArray[0]} y un {$miClase->miArray[1]}.
Lo siguiente es una D mayúscula: \x44
EOT;
```

Es posible pasar datos como **argumentos** de una función:

```
<?php
// Ejemplo en argumento
var_dump(array(<<<BLA
Este es un string
BLA
));
// Otro ejemplo
function saludar($frase){
    echo "Este es el saludo: " . $frase;
}
$saludo = <<<BLA
Hola que tal, ¿Cómo estás?
BLA;

saludar($saludo);
```

Se pueden iniciar **variables estáticas** y **propiedades/constantes** de clase mediante la sintaxis **Heredoc**:

```
// Variables estáticas
function miFuncion()
{
    static $var = <<<EOT
Esto es un string
EOT;
     return $var;
}

// Constante y propiedad de clase
class miClase {
    const Constante = <<<EOD
Esto sería una constante
EOD;
    public $variable = <<<BLA
Esto sería una propiedad
BLA;
}
```

Por último, también es posible **entrecomillar el identificador de apertura en Heredoc**:

```
<?php
echo <<<"BLA"
Esto es un string
BLA;
// Importante ver que son DOBLES comillas, si son SIMPLES es un nowdoc
```

### Nowdoc

Un **Nowdoc** se especifica de la misma forma que un **Heredoc**, pero no se realiza ningún tipo de **análisis dentro del nowdoc**. Nowdoc es a los strings con comillas simples lo mismo que Heredoc es a las comillas dobles. 

Se utiliza para embeber código PHP y textos grandes sin necesidad de escaparlos.

Al igual que con **heredoc**, iniciamos el **nowdoc** con <<<, pero el identificador que le sigue está delimitado por **comillas simples**. Las reglas aplicables a los identificadores de heredoc también se aplican a las de nowdoc, como las del identificador de cierre.

```
$string = <<<'HEY'
Esto es un texto
en nowdoc
que es como si fuera con
comillas simples ''
HEY;
// Como con las comillas simples, no interpreta variables ni clases

class Comunicacion{
    public $saludo = "hola";
}
$comunicacion = new Comunicacion();
$saludo = "buen día";

echo <<<'HEY'
Cuando saludo digo $saludo,
o a veces también $comunicacion->saludo
HEY;
// Devuelve el texto literalmente sin interpretar las variables
// Si se quiere que las interprete, se usa heredoc
```