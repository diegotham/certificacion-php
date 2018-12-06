Un **bit** (**B**inary dig**IT**) es la unidad básica de **información** almacenada en un **sistema informático** que existe en dos posibles estados, representados como **ON** y **OFF**. El 1 representa ON y el 0 representa OFF. Estos valores, 0 y 1, se llaman **digitos binarios** y están especificados en un **sistema numérico específico**, el **sistema binario**, cuya base es 2.

**Indice de contenido**

| | |
| -------- | -------- |
| 1. ¿Qué es un byte? | 4. Operador XOR |
| 2. Operador AND | 5. Operador NOT |
| 3. Operador OR | 6. Operadores de desplazamiento |

### 1. ¿Qué es un byte?

Un byte es una secuencia de bits. Un byte son 8 bits. El **máximo valor que puede tomar es 255**, cuando todos los bits están activos => **11111111**:

| | | | | | | | | | |
| -------- | -------- |
| | 8º | 7º | 6º | 5º | 4º | 3º | 2º | 1º |
| **Valor de posición** | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
| **Cálculo** | 2<sup>7</sup> | 2<sup>6</sup> | 2<sup>5</sup> | 2<sup>4</sup> | 2<sup>3</sup> | 2<sup>2</sup> | 2<sup>1</sup> | 2<sup>0</sup> | Total = 255 |

Podemos representar un valor cualquiera, por ejemplo, el 54:

| | | | | | | | | | |
| -------- | -------- |
| **Valor de posición** | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
| **Cálculo** | 2<sup>7</sup> | 2<sup>6</sup> | 2<sup>5</sup> | 2<sup>4</sup> | 2<sup>3</sup> | 2<sup>2</sup> | 2<sup>1</sup> | 2<sup>0</sup> |
| **Número a representar** | 0 | 0 | 1 | 1 | 0 | 1 | 1 | 0 | Total = 54 |


Esto es: 2<sup>5</sup> + 2<sup>4</sup> + 2<sup>2</sup> + 2<sup>1</sup> = 32 + 16 + 4 + 2 = 54.

Los operadores bit a bit actúan sobre **integers**.

### 2. Operador bit a bit AND

```
<?php
$x = 10;
$y = 34;
echo $x & $y; // El valor es: 2
```

El **operador AND** toma el valor de los bits que $x e $y tienen **en común**:

| | | | | | | | | | |
| -------- | -------- |
| **Valor de posición** | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
| **Valor de $x** | 0 | 0 | 0 | 0 | 1 | 0 | 1 | 0 | Total = 10 |
| **Valor de $y** | 0 | 0 | 1 | 0 | 0 | 0 | 1 | 0 | Total = 34 |

El único bit que comparten estas variables está en segunda posición, y su valor es 2.

Pongamos un ejemplo en el que comparten más de 1 bit:

```
<?php
$x = 95;
$y = 203;
echo $x & $y; // El valor será: 75
```

| | | | | | | | | | |
| -------- | -------- |
| **Valor de posición** | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
| **Valor de $x** | 0 | 1 | Total = 95 |
| **Valor de $y** | 1 | 1 | Total = 203 |

En este caso coinciden 4 bits. El valor que tomará será la **suma de los bits que comparten**, esto es 64 + 8 + 2 + 1 = 75.

### 3. Operador bit a bit OR

```
<?php
$x = 7;
$y = 14;
echo $x | $y; // El valor será: 15
```

| | | | | | | | | | |
| -------- | -------- |
| **Valor de posición** | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
| **Valor de $x** | 0 | 0 | 0 | 0 | 0 | 1 | 1 | Total = 7 |
| **Valor de $y** | 0 | 0 | 0 | 0 | 1 | 1 | 0 | Total = 14 |

El valor que tomará esta vez será la sumar de los bits de cualquiera de los dos, coincidan o no, esto es 8 + 4 + 2 + 1 = 15.

### 4. Operador bit a bit XOR

El **operador Xor** realiza una comparación bit a bit entre dos **expresiones numéricas** y establece el correspondiente bit en el resultado. Cuando **sólo una** expresión es **true** el resultado es **true**.

| | | | |
| -------- | -------- |
| **Bit en $x** | **Bit en $y** | **Resultado** | **Valor** |
| 0 | 0 | 0 | False |
| 0 | 1 | 1 | True |
| 1 | 0 | 1 | True |
| 1 | 1 | 0 | False |

Ejemplo:

```
<?php
$x = 14;
$y = 8;
echo $x ^ $y; // El valor será: 6
```

| | | | | | | | | | |
| -------- | -------- |
| **Valor de posición** | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
| **Valor de $x** | 0 | 0 | 0 | 0 | 1 | 1 | 1 | 0 | Total = 14 |
| **Valor de $y** | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | Total = 8 |

En este caso se excluye el único bit que coincide, y el valor será la suma de los demás bits que están en $x o $y, esto es: 4 + 2 = 6.

### 5. Operador bit a bit NOT

El operador **NOT** (**~**) devuelve **true** cuando el bit de una expresión no está establecido en otra expresión.

#### Ejemplo de **NOT usando AND después**:

```
<?php
$x = 14;
$y = 11;
echo $x & ~ $y; // El valor será: 4
```

| | | | | | | | | | |
| -------- | -------- |
| **Valor de posición** | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
| **Valor de $x** | 0 | 0 | 0 | 0 | 1 | 0 | Total = 14 |
| **Valor de $y** | 0 | 0 | 0 | 0 | 1 | 1 | Total = 11 |

En este caso la expresión devolverá el los bits que estén en $x pero no en $y, esto es 4.

#### Ejemplo de **NOT usando AND antes**:

Utilizando este mismo ejemplo anterior, pero cambiando el lugar del **NOT**:

```
<?php
$x = 14;
$y = 11;
echo ~ $x & $y; // El valor será : 1
```

El valor será sólo 1, ya que es el único bit que aparece en $y pero no en $x.

### 6. Operadores bit a bit de desplazamiento

Si **a** y **b** son dos números, el **desplazamiento de bits** (_bit shifting_) desplaza **a** bits un número **b** de posiciones. Cada posición es multiplicar por dos si es **desplazamiento a la izquierda** (_bit shift left_). Si es **desplazamiento a la derecha** es (_bit shift right_), cada posición se refiere a dividir por dos.

#### **Desplazamientos a la izquierda**

```
<?php
$x = 4;
$y = 2;
echo $x << $y; // El valor será: 16
```

| | | | | | | | | | |
| -------- | -------- |
| **Valor de posición** | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
| **Valor de $x** | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | Total = 4 |
| **Resultado** | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | Total = 16 |

En este ejemplo, un valor de $x de 8 se tiene que mover un número $y de posiciones, en este caso dos, lo que resulta en multiplicar 2 veces por 2, resultando 16.

#### **Desplazamientos a la izquierda avanzados**

```
<?php
$x = 24;
$y = 2;
echo $x << $y; // El valor será: 96
```

| | | | | | | | | | |
| -------- | -------- |
| **Valor de posición** | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
| **Valor de $x** | 0 | 0 | 0 | 1 | 1 | 0 | 0 | 0 | Total = 24 |
| **Resultado** | 0 | 1 | 1 | 0 | 0 | 0 | 0 | 0 | Total = 96 |

En este caso el valor de $x es 24, que será multiplicado por 2 un número $y de veces, en este caso dos, resultará un valor de 96: 24 x 2 x 2 = 96.

#### **Desplazamientos a la derecha**

```
<?php
$x = 16;
$y = 2;
echo $x >> $y; // El valor será: 4
```

| | | | | | | | | | |
| -------- | -------- |
| **Valor de posición** | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
| **Valor de $x** | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | Total = 16 |
| **Resultado** | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | Total = 4 |

El valor de $x es 16, desplazamos a la derecha un número $y de veces, dividiendo dos veces para 2, resultando así un valor de 4. 

#### **Desplazamientos a la derecha avanzados**

```
<?php
$x = 48;
$y = 2;
echo $x >> $y; // El valor será 12
```

| | | | | | | | | | |
| -------- | -------- |
| **Valor de posición** | 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 |
| **Valor de $x** | 0 | 0 | 1 | 1 | 0 | 0 | 0 | 0 | Total = 48 |
| **Resultado** | 0 | 0 | 0 | 0 | 1 | 1 | 0 | 0 | Total = 12 |

El valor de $x es 48, desplazamos a la derecha un número $y de veces, dividiendo dos veces para 2, resultando así un valor de 12.

*Cuando se hacen desplazamientos a la derecha un número $y de veces que excede del límite del primer bit, el resultado será 0.
