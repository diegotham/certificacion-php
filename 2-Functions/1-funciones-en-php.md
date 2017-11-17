Las **funciones** son paquetes de código con instrucciones para ejecutar una acción. En las instrucciones puede incluirse cualquier código válido, incluyendo clases y otras funciones.

```
function suma($numero1, $numero2)
{
    $resultado = $numero1 + $numero2;
    return $resultado;
}
echo suma(3, 4);
```

Las funciones siguen las mismas reglas que las demás etiquetas en PHP: comienzan con una letra o barra baja, seguido de cualquier número de letras, números o barras bajas. Son insensibles a mayúsculas y minúsculas, aunque se recomienda el formato **_camelCase_**_._

Pueden ser **referenciadas antes de definirse**, a no ser que lo impida una condición. Cuando una función está definida condicionalmente, sus definiciones se deben procesar antes de ser llamadas:

```
$var1 = true;

/* No podemos llamar a activar() desde aquí
   ya que no existe aún,
   pero podemos llamar a prueba() */

prueba();

if ($var1) {
    function activar()
    {
        echo "No existo hasta que la ejecución del programa llegue hasta mí.\n";
    }
}

/* Ahora podemos llamar de forma segura a activar()
ya que $var1 se evaluó como verdadero */

if ($var1) activar();

function prueba()
{
    echo "Existo desde el momento inmediato que comenzó el programa.\n";
}

// Devuelve primero prueba() y después activar()
```

Otro ejemplo:

```
function externa()
{
    function interna()
    {
        echo "No existo hasta que se llame a externa()";
    }
}

// No se puede llamar aún a interna()
externa();

// Ahora ya podemos llamar a interna()
interna();
```

**Todas las funciones y clases en PHP tienen ámbito global**. Se pueden llamar desde fuera de una función incluso si fueron definidas dentro, y viceversa. 

No se puede sobrecargar funciones, ni redefinirlas ni desdefinirlas.

Existe la recursividad en las funciones:

```
function recursiva($var)
{
    if ($var < 50) {
        echo $var . "<br>";
        recursiva($var + 2);
    }
}

recursiva(12); // Imprime los pares del 12 al 48
```
