**Indice de contenido**

1.  Introducción
2.  Conceptos básicos
3.  Codificaciones multibyte
4.  Interpretación de la codificación
5.  Codificación en PHP

### 1. Introducción

Un ordenador no puede almacenar letras, números, imágenes o cualquier otro tipo de elemento. Lo único que puede guardar son **bits de información**, cuyos valores son 0 y 1\. El ordenador trabaja con electricidad, y un bit es básicamente un punto de energía que está ahí o no. La presencia o no de estos puntos se representa con unos y ceros, respectivamente.

Para que los bits representen algo se utilizan **reglas** para convertir una secuencia de bits en algo diferente como letras o números utilizando un **esquema de codificación**. Por ejemplo, en [ASCII](https://es.wikipedia.org/wiki/ASCII) la letra b es 01100010, la letra s es 01110011\. Una secuencia de ocho bits forma un **byte**, y representa una letra. En [este enlace](http://www.ascii-code.com/) puedes ver la **tabla completa de bits en ASCII**. 

En el **esquema de codificación ASCII existen 128 caracteres**, entre los cuales se encuentran las letras de la A a la Z en mayúsculas y en minúsculas, los números del 1 al 9, puntuaciones, símbolos (dólar **$**, ampersand **&**...), espacio, tabulador... 128 es el número máximo de combinaciones diferentes de ceros y unos que se pueden obtener con 7 bits (0000001, 0000011, 0101110...). Para decir "Hola Mundo" con código ASCII sería:

01001000 01101111 01101100 01100001 01001101 01110101 01101110 01100100 01101111

### 2. Conceptos básicos

**Codificar** significa utilizar algo para representar algo diferente.

Una **codificación** es un conjunto de reglas con las cuales convertir algo desde una representación a otra. 

Un **character set** o **charset** es un conjunto de caracteres que pueden ser codificados. La codificación ASCII está compuesta por 128 caracteres.

El **code page** es la tabla de códicos que asignan cada carácter a cada secuencia de bits. 

Un **string** o **cadena de caracteres** es un conjunto de bits, una secuencia. 

Existen diferentes formas de escribir números: por ejemplo, en **ASCII**, la letra A en **binario** es 01000001, en **octal** 101, en **hexadecimal** 41, en **decimal** 65. 

En la tabla de representación de caracteres de **ASCII** se puede observar que 128 bits no son suficientes para **trabajar con diferentes idiomas**: alemán, ruso, chino mandarín, japonés... Como ASCII sólo emplea 7 bits y en cada ordenador se trabaja con **8 bits por byte**, si se utiliza ese bit restante es posible ampliar a 255 combinaciones diferentes, lo que aumenta el rango. Así se hizo, y la **versión extendida de ASCII** incluye caracteres especiales del latín. Pero aún así 255 caracteres no son suficientes ni para cubrir las **variaciones de letras y puntuaciones de los idiomas de los países europeos**. 

### 3. Codificaciones multibyte

Aquí es donde entran las **codificaciones multibyte**. 

Con el tiempo, se han ido creando diferentes **esquemas de codificación** para albergar las **decenas de miles de caracteres que existen en todos los idiomas del mundo**. Todo era un caos hasta que se decidió organizarlo todo y crear un estándar para **unificar todos los esquemas**. De aquí surgió **Unicode**, que puede albergar hasta 1.114.112 puntos para representar cualquier tipo de símbolo o letra de cualquier idioma. 

Unicode define una tabla de puntos de código para caracteres: A es 65, B es 66... Cómo éstos puntos de código se codifican es muy complejo. Desde luego para poder representar más de un millón de caracteres se necesitan más de 3 bytes (aunque se usan 4 porque se trabaja mejor que con 3).

Normalmente existen diferentes formas de codificar Unicode: **UTF-32 utiliza todos los puntos posibles de Unicode** usando **32 bits**, es decir , **4 bytes por carácter**. Esto emplea mucho espacio en muchos casos. UTF-16 y UTF-8 son codificaciones de **amplitud variable**. Esto significa que si un carácter se puede representar con un sólo byte, UTF-8 empleará sólo un byte. Si requiere dos bytes, usará dos, y así sucesivamente.

| | | |
| -------- | -------- |
| **Carácter** | **Codificación** | **Bits** |
| A | UTF-8 | 01000001 |
| A | UTF-16 | 00000000 01000001 |
| A | UTF-32 | 00000000 00000000 00000000 01000001 |

Los caracteres son referenciados por su **Unicode code point**, que son escritos en números hexadecimales, precedidos de U+. Ejemplo: U+1E00, es el carácter número 7.680 de la tabla Unicode.

Cualquier carácter se puede codificar con secuencias muy diferentes de bits, y **cualquier secuencia de bits particular puede representar diferentes caracteres**, dependiendo de que codificación se utiliza para escribirlos o leerlos. Esto es porque **diferentes codificaciones utilizan diferente número de bits por carácter** y diferentes valores para representar diferentes caracteres. 

### 4. Interpretación de la codificación

Cuando abres un documento de texto en un editor y aparecen caracteres como â€™ y otros caracteres extraños es que hay un **error en la interpretación de la codificación**.

El problema es básicamente que el **editor** o **navegador** que está intentando leer el **documento** está utilizando una codificación errónea. Para solucionarlo, simplemente se selecciona el esquema de codificación correcto. Pero a veces el problema es que se ha guardado un documento con una codificación diferente a la que realmente era, y no hay forma de traducirlo a la forma anterior a no ser que se disponga del archivo original y se codifique correctamente para su lectura. Frecuentemente aparece el carácter � (U+FFFD), que es el **carácter de reemplazo de Unicode**, cuando el programa no sabe interpretar algunas secuencias.

Para convertir de una codificación a otra, existen diferentes herramientas para hacerlo. Por ejemplo _**iconv**_, que identificará  y actualizará los bits de una coficación a otra y mantendrá el mismo carácter.

Lo más importante de UTF-8 es que es compatible binariamente con ASCII. Todos los caracteres disponibles en ASCII sólo utilizan un byte en UTF-8 y son los mismos bits. Cualquier otro carácter que no está en ASCII, emplea dos o más bytes en UTF-8\. En la mayoría de **lenguajes de programación que esperan interpretar ASCII** se puede **incluir texto en UTF-8** directamente en los programas.

### 5. Codificación en PHP

PHP no soporta de forma nativa a Unicode, pero UTF-8 es totalmente compatible con ASCII, que es todo lo que PHP necesita.

Todo texto que lees está codificado mediante algún esquema de codificación. Cuando escribes en un editor en un **lenguaje de programación**, también está utilizando un **esquema de codificación**, y lo mismo ocurre con las bases de datos, archivos...

Cuando un **lenguaje de programación no soporta nativamente Unicode**, se refiere a que el lenguaje asume que un carácter equivale a un byte. Por ejemplo, PHP permite acceso directo a los caracteres de un string mediante la notación de arrays: 

```
echo $cadena[0];
```

Si _$cadena_ está codificada con codificación **monobyte**, nos da el primer carácter, que equivale a un byte. PHP nos devuelve el primer byte sin pensar acerca de caracteres. **Los strings son secuencias de bytes para PHP**, nada más.

El hecho de que PHP no soporte de forma nativa a Unicode simplemente significa que **la mayoría de funciones PHP interpretan un byte por carácter**, lo que produce que los caracteres multibyte se corten. Esto no significa que no se pueda usar Unicode en PHP o que todo string en Unicode tenga que ser traducido con _**utf8_encode**_.

Las funciones _**utf8_encode**_ y _**utf8_decode**_ son inútiles si no se quiere convertir específicamente de ISO-8859-1 a UTF-8  y viceversa (si se quiere convertir entre cualquier otro tipo de esquema, hay que utilizar [iconv](http://www.php.net/manual/en/function.iconv.php)).

Es por ello que existe la extensión **mbstring**, o [Multibyte String extension](http://www.php.net/manual/en/book.mbstring.php), que hace una réplica de todas las **funciones de strings importantes de PHP en modo multibyte**. En cada función de mbstring, como _**mb_substr**_, se puede especificar el esquema de codificación:
```
<tt>mb_substr($string, 0, 1, 'UTF-8');</tt>

```

**PHP** no hace nada con los strings mas que almacenarlos en la **memoria**. No necesita conocer acerca de caracteres o codificaciones (a no ser que tenga que manipular strings). Los strings sólo son conjuntos de bytes para PHP que almacena. El **único requisito que necesita PHP es que el código fuente de PHP se guarde en un esquema de codificación compatible con ASCII**.

El **intérprete de PHP** busca caracteres en concreto que le dicen qué hacer: $ (00100100) inicia una variable, = (00111101) una asignación, etc. Cualquier otra cosa que no tenga un significado para el intérprete se ejecuta como una secuencia literal de bytes.

El único momento en el que hay que estar atento es **cuando se manipulan strings**, como cortar, extraer, contar... Y otras operaciones que han de ocurrir a nivel de caracteres en lugar de a nivel de bytes. Si los strings sólo se leen o muestran no se tiene ningún problema con el soporte de esquemas de PHP que no se puedan tener en cualquier otro lenguaje.