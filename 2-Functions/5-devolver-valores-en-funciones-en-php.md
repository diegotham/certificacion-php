Para **devolver valores en las funciones de PHP** se utiliza la sentencia return, que es una **estructura de control**. Se puede devolver cualquier variable que se quiera, ya sea un integer, un string, un array, un objeto, etc.

La palabra _return_ establece el **valor de respuesta** de una función a cualquier variable, y después PHP sale de la función. Si se usa sólo _return_, se sale de la función sin devolver ningún valor.

Si por ejemplo escribimos la siguiente función:

```
function prueba()
{
    print "Esta es mi función";
    return 1;
    print "Ahora saldré de la función";
}

print prueba(); //  Devolverá: Esta es mi función1

```

Nunca se verá "Ahora saldré de la función" porque ya se ha salido de la función.

Si se quiere devolver más de un valor, es necesario utilizar un **array**:

```
function numeros(){
    return array (1, 2, 3);
}

list($uno, $dos, $tres) = numeros();
```

Podemos devolver una referencia desde una función con **&** tanto en la declaración de la función como cuando se asigna el valor devuelto a una variable:

```
function &devolverReferencia()
{
    return $unaReferencia;
}

$nuevaReferencia =& devolverReferencia();
```

También se puede **devolver un valor de una sentencia condicional**:

```
return $x > 50;
```

Si $x es mayor que 50, el **operador >** devolverá 1, lo que se traduce en **return 1**, que es **return true**.

Un ejemplo:

```
return ($db->affected_rows() == 1) ? true : false;
```

En este ejemplo se devolverá **true** sólo si se ha afectado a una fila en la base de datos.