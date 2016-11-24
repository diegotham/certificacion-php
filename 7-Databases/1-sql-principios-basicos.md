**SQL** (_**Structured Query Language**_) es el lenguaje estándar para acceder y manipular bases de datos. Se usa para encontrar, guardar y modificar datos en una base de datos relacional.

Para que un sitio web pueda mostrar datos de una base de datos es necesario un **RDBMS** (**Relational Database Management System**), que puede ser **MS Access**, **SQL Server**, **MySQL**... Además de usar un lenguaje de scripts del lado del servidor (como **PHP** o **ASP**).

Los **datos en los RDBMs** se guardan en **objetos de bases de datos** llamados **tablas**. Una tabla es un conjunto de entradas de datos relacionados que consiste en filas y columnas. Una base de datos normalmente contiene **una o más tablas**. Cada tabla se identifica con un nombre ("Clientes", "Pedidos"), y contienen filas donde se guardan los registros de datos.

La siguiente es una tabla de ejemplo que servirá para alguno de los **comandos SQL** a explicar:

| | | | | |
| -------- | -------- |
| **ClienteID** | **NombreCliente** | **Ciudad** | **Pais** | **Telefono** |
| 1 | Pedro Pérez | Madrid | España | 456667778 |
| 2 | María Carrasco | Barcelona | España | 477322899 |
| 3 | Louis Bárcenas | Madrid | España | 444123843 |
| 4 | Sonia López | Oporto | Portugal | 455533777 |

La mayoría de las acciones que se toman en una base de datos se realizan con **sentencias SQL**. La sentencia más importante es **SELECT**:

```
SELECT * FROM Clientes
```

El código anterior **selecciona todas las filas** de la tabla clientes. 

**SQL no es sensible a mayúsculas y minúsculas**, aunque se suelen escribir los comandos en mayúscula (SELECT, FROM...). Algunos sistemas de bases de datos requieren un punto y coma al final de cada sentencia. El **punto y coma es el estándar para separar cada sentencia SQL** en los sistemas de bases de datos que permiten más de una sentencia en la misma llamada al servidor.

El valor **NULL** no es el mismo que "**0**", "**false**" o un _**string**_ vacío. Representa un **valor que falta**.

Los **conceptos y comandos más importantes de SQL** son:

| | | |
| -------- | -------- |
| 1. SELECT | 10. LIKE | 16. CREATE DATABASE |
| 2. SELECT DISTINCT | 11. Wildcards | 17. CREATE TABLE |
| 3. WHERE | 12. IN | 18. Constraints |
| 4. AND y OR | 13. BETWEEN | 19. CREATE INDEX |
| 5. ORDER BY | 14. Aliases | 20. DROP |
| 6. INSERT INTO | 15. Data Types | 21. ALTER |
| 7. UPDATE | 22. AUTO INCREMENT | |
| 8. DELETE | 23. Views | |
| 9. SELECT TOP | |

### 1. SELECT

La sentencia **SELECT** se utiliza para seleccionar datos de una **base de datos**. El resultado se guarda en una tabla de resultados, llamada _**result-set**_. Si queremos seleccionar **columnas** de una tabla:

```
SELECT nombreColumna, nombreColumna
FROM nombreTabla;
```

Si queremos seleccionar **todas las columnas**:

```
SELECT * FROM nombreTabla;
```

Respecto a la tabla anterior de clientes, podemos hacer la siguiente consulta:

```
SELECT NombreCliente, Ciudad FROM Clientes;
```

La mayoría de **sistemas de bases de datos** permiten la navegación en el _**result-set**_ con **funciones de programación**: _Move-To-First-Record_, _Get-Record-Content_, etc.

Se suele acceder a la información mediante funciones desde el **lenguaje de programación** del lado del servidor (**PHP**, **ASP**...).

### 2. SELECT DISTINCT

La sentencia **SELECT DISTINCT** devuelve registros donde los valores de todas las columnas elegidas sean **diferentes**.

```
SELECT DISTINCT nombreColumna, nombreColumna
FROM nombreTabla;
```

En una tabla, una columna puede contener valores duplicados, y en ocasiones sólo deseas mostrar **valores únicos**.

```
SELECT DISTINCT Ciudad FROM Clientes;
```

### 3. WHERE

La cláusula **WHERE** se usa para **filtrar valores**. Se extraen sólo los datos que cumplen unos determinados criterios.

```
SELECT nombreColumna, nombreColumna
FROM nombreTabla
WHERE nombreColumna operador valor;
```

Los **operadores de WHERE** pueden ser:

| | |
| -------- | -------- |
| **Operador** | **Descripción** |
| **=** | Igual |
| **<>** | No igual (en algunas versiones de SQL es !=) |
| **>** | Mayor que |
| **<** | Menor que |
| **>=** | Mayor o igual que |
| **<=** | Menor o igual que |
| **BETWEEN** | Entre un rango de valores |
| **LIKE** | Buscar un patrón |
| **IN** | Especifica múltiples posibles valores para una columna |

Si los **valores** son textuales, se necesitan **comillas simples** (aunque la mayoría de sistemas de bases de datos aceptan también **comillas dobles**):

```
SELECT * FROM Clientes
WHERE Ciudad='Madrid';
```

Si los **valores** son numéricos, no se ponen comillas:

```
SELEC * FROM Clientes
WHERE ClienteID=2;
```

### 4. AND y OR

Los operadores **AND** y **OR** se usan para **filtrar resultados basándose en más de una condición**:

*   **AND**: muestra los resultados si se cumplen la primera y la segunda condición (si son **true**)

   
```
    SELECT * FROM Clientes
    WHERE Pais='España'
    AND Ciudad='Madrid';
   
```

*   **OR**: muestra los resultados si se cumple una de las dos condiciones (sólo una ha de ser **true**)

   
```
    SELECT * FROM Clientes
    WHERE Ciudad='Madrid'
    OR Ciudad='Barcelona';
   
```

Podemos combinar tantas condiciones **AND** y **OR** como necesitemos. Las expresiones, para evitar errores y para facilitar la lectura, se puede agrupar utilizando paréntesis:

```
SELECT * FROM Clientes
WHERE Pais='España'
AND (Ciudad='Madrid' OR Ciudad='Barcelona');
```

### 5. ORDER BY

La cláusula **ORDER BY** se usa para ordenar los resultados por una o más columnas. Por defecto se ordenan en orden ascendente **ASC**. Para ordenar los resultados en orden descendente se usa la palabra **DESC**.

```
SELECT nombreColumna, nombreColumna
FROM nombreTabla
ORDER BY nombreColumna ASC|DESC, nombreColumna ASC|DESC
```

Veamos varios ejemplos:

```
//// Ordenados según la columna NombreCliente
SELECT * FROM Clientes
ORDER BY NombreCliente;

//// Ordenados según la columna NombreCliente en orden descendente
SELECT * FROM Clientes
ORDER BY NombreCliente DESC;

//// Ordenados según las columnas NombreCliente y Ciudad
SELECT * FROM Clientes
ORDER BY NombreCliente, Ciudad;

//// Ordenados según orden ascendente de NombreCliente y descendente de Ciudad
SELECT * FROM Clientes
ORDER BY NombreCliente ASC, Ciudad DESC
```

Los resultados ordenados por varias columnas se ordenan como en las tablas de hojas de cálculo: se ordenan primero por la primera indicación de orden, y dentro de este mismo orden, se ordenan por la segunda indicación de orden, y así sucesivamente.

### 6. INSERT INTO

La sentencia **INSERT INTO** se utiliza para insertar nuevos valores en la tabla. Se pueden insertar de dos formas:

*   Sin especificar el **nombre de las columnas donde se insertarán los datos**:

   
```
    INSERT INTO nombreTabla
    VALUES (valor1, valor2, valor3...);
   
```

*   Especificando tanto las **columnas** como los **valores a insertar**:

   
```
    INSERT INTO nombreTabla (columna1, columna2, columna3...)
    VALUES (valor1, valor2, valor3...);
   
```

El valor de **ClienteID** no se facilita, es un valor que se actualiza automáticamente con un valor único para cada valor en la tabla (este es el comportamiento de un campo AUTO_INCREMENT en MySQL. En otros RDBMs puede variar):

```
INSERT INTO Clientes (NombreCliente, Ciudad, Pais)
VALUES ('John Mars', 'Paris', 'Francia'
```

| | | | | |
| -------- | -------- |
| **ClienteID** | **NombreCliente** | **Ciudad** | **Pais** | **Telefono** |
| 1 | Pedro Pérez | Madrid | España | 456667778 |
| 2 | María Carrasco | Barcelona | España | 477322899 |
| 3 | Louis Bárcenas | Madrid | España | 444123843 |
| 4 | Sonia López | Oporto | Portugal | 455533777 |
| 5 | John Mars | Paris | Francia | _**null**_ |

Pueden insertarse **valores en campos específicos si se quiere**, como en el ejemplo anterior donde no hemos insertado ningún teléfono. Los campos que no se rellenen aparecerán como _**null**_. 

### 7. UPDATE

La sentencia **UPDATE** se usa para actualizar datos en una tabla. 

```
UPDATE nombreTabla
SET columna1=valor1, columna2=valor2...
WHERE columnaEspecifica=valorEspecifico
```

La cláusula **WHERE** es **opcional**, pero **si no se indica, se actualizarán todos los valores de la tabla**. 

Vamos a actualizar la tabla anterior cambiando la **ciudad** a John Mars y añadiéndole un **teléfono**:

```
UPDATE Clientes
SET Ciudad='Nantes', Telefono='455644888'
WHERE NombreCliente='John Mars'
```

La tabla quedará ahora así:

| | | | | |
| -------- | -------- |
| **ClienteID** | **NombreCliente** | **Ciudad** | **Pais** | **Telefono** |
| 1 | Pedro Pérez | Madrid | España | 456667778 |
| 2 | María Carrasco | Barcelona | España | 477322899 |
| 3 | Louis Bárcenas | Madrid | España | 444123843 |
| 4 | Sonia López | Oporto | Portugal | 455533777 |
| 5 | John Mars | Nantes | Francia | 455644888 |

### 8. DELETE

La sentencia **DELETE** se utiliza para **eliminar filas** de una tabla.

```
DELETE FROM NombreTabla
WHERE columnaEspecifica=valorEspecifico
```

La cláusula **WHERE** es **opcional**, pero **si no se indica, se BORRARÁN todos los valores de la tabla**.

```
DELETE FROM Clientes
WHERE NombreCliente='John Mars'
```

Si lo que se quiere es **borrar todos los datos de una tabla**, se puede hacer de dos formas:
Utilizando **DELETE**
```
DELETE FROM Clientes;
////
DELETE * FROM Clientes;
```
Utilizando **TRUNCATE** (Ligeramente más rápido que un DELETE)
```
TRUNCATE table Clientes;
////
TRUNCATE table Clientes;
```

### 9. SELECT TOP

La cláusula **SELECT TOP** especifica el **número de filas a devolver**, lo que puede ser muy útil en bases de datos con miles de filas. Devolver un número muy elevado de filas tiene un **impacto importante en el rendimiento**. 

No todos los sistemas de bases de datos soportan la cláusula SELECT TOP, y dependiendo del **sistema de bases de datos** la **sintaxis es distinta**.

En **SQL Server / MS Access:**

```
SELECT TOP numero\porcentaje nombreColumna(s)
FROM nombreTabla;
```

En **MySQL:**

```
SELECT nombreColumna(s)
FROM nombreTabla
LIMIT numero;
```

Ponemos un **ejemplo en SQL Server**, con la tabla anterior de clientes:

```
SELECT TOP 2 * FROM Clientes;
```

Nos devolverá las dos primeras filas de la tabla clientes. Si añadimos un porcentaje, **50%**, los devolverá la primera mitad de la tabla:

```
SELECT TOP 50 PERCENT * FROM Clientes;
```

### 10. LIKE

El **operador LIKE** se usa en una **cláusula WHERE** para buscar un **patrón** específico en una columna. Por defecto este operador no es sensible a mayúsculas.

```
SELECT nombreColumna(s)
FROM nombreTabla
WHERE nombreColumna LIKE patron;
```

El siguiente ejemplo muestra a todos los clientes cuya **ciudad** empieza por M:

```
SELECT * FROM Clientes
WHERE Ciudad LIKE 'm%';
```

El siguiente muestra a todos los clientes cuyo nombre **no** contiene las letras "ez":

```
SELECT * FROM Clientes
WHERE NombreCliente NOT LIKE '%ez%';
```

El % se utiliza para definir _**wildcards**_, que son letras o palabras concretas para encontrar o evitar (**LIKE** o **NOT LIKE**).

### 11. Wildcards

Los _**wildcards**_ son caracteres que pueden servir para **sustituir** por cualquier otro caracter en un _**string**_. Se utilizan con el operador LIKE, y en **SQL** sirven para encontrar datos en una tabla.

| | |
| -------- | -------- |
| **Wildcard** | **Descripción** |
| **%** | Sustituye por cero o más caracteres |
| **_** | Sustituye por un sólo carácter |
| _**[caracteres]**_ | Establece y crea rangos de caracteres a **encontrar** |
| _**[^caracteres]**_ ó _**[!caracteres]**_ | Establece y crea rangos de caracteres a **evitar** |

Ya hemos visto como funciona **%**, vamos a ver con **_** los clientes cuya **ciudad** empieza por un carácter cualquiera, seguido de **po**, seguido de otro carácter cualquiera y acabado en **to**:

```
SELECT * FROM Clientes
WHERE Ciudad LIKE '_po_to';
```

Los **corchetes** nos permiten poder seleccionar filas con unas determinadas palabras en una posición determinada, o un rango de palabras (algo así como los [corchetes en las expresiones regulares](http://diego.com.es/expresiones-regulares-en-php#corchetes)). Buscamos los clientes cuya ciudad empieza por **o**, por **b**, o por **m**:

```
SELECT * FROM Clientes
WHERE Ciudad LIKE '[obm]%';
```

Ahora queremos encontrar los clientes cuya **ciudad** acaba en un **rango** de la **a** a la **j** (las posibilidades son: a, b, c, d, e, f, g, h, i, j):

```
SELECT * FROM Clientes
WHERE Ciudad LIKE '%[a-j]';
```

Para **evitar varios caracteres o rangos**, se hace lo mismo añadiendo **^** o **!**:

```
SELECT * FROM Clientes
WHERE Ciudad LIKE '[!obm]%';
```

Nota: En MySQL es necesario usar el operador **REGEXP** para filtrar usando expresiones regulares.
Fuente [[Regular Expressions](http://dev.mysql.com/doc/refman/5.7/en/regexp.html)]

### 12. IN

El operador **IN** permite especificar múltiples valores en la cláusula **WHERE**:

```
SELECT nombreColumna
FROM nombreTabla
WHERE nombreColumna IN (valor1, valor2...);
```

Vamos a seleccionar los clientes que tienen como **ciudad** asignada **Madrid** o **Barcelona**:

```
SELECT * FROM Clientes
WHERE Ciudad IN ('Madrid', 'Barcelona');
```

### 13. BETWEEN

El operador **BETWEEN** se usa para **seleccionar valores dentro de un rango**. Los valores pueden ser **números**, **texto** o **fechas**.

```
SELECT nombreColumna
FROM nombreTabla
WHERE nombreColumna BETWEEN valor1 AND valor2;
```

Si por ejemplo tenemos la siguiente **tabla de productos**:

| | | | | | |
| -------- | -------- |
| **ProductoID** | **NombreProducto** | **Categoría** | **Precio** | **Stock** | **UltimaVenta** |
| 1 | Gafas de sol | 1 | 30 | 6 | 10/10/2015 |
| 2 | Gorra | 1 | 10 | 18 | 2/10/2015 |
| 3 | Camiseta | 2 | 10 | 16 | 13/10/2015 |
| 4 | Pantalón | 2 | 15 | 10 | 16/9/2015 |
| 5 | Zapatillas | 3 | 25 | 12 | 29/9/2015 |

Vamos a seleccionar los productos que tienen un **precio** entre 10 y 20 euros:

```
SELECT * FROM Productos WHERE Precio BETWEEN 10 AND 20;
```

Productos cuyo **precio** no está entre 10 y 20 euros:

```
SELECT * FROM Productos WHERE Precio NOT BETWEEN 10 AND 20;
```

Productos cuyo **nombreProducto** empieza con alguna letra entre la A y la M:

```
SELECT * FROM Productos WHERE nombreProducto BETWEEN A AND M;
```

Productos cuya **UltimaVenta** NO se ha producido en el mes de **octubre de 2015**:

```
SELECT * FROM Productos WHERE UltimaVenta NOT BETWEEN '01/10/2015' AND '31/10/2015';
```

Ejemplo para MySQL (formato yyyy-mm-dd):
```
SELECT * FROM Productos WHERE UltimaVenta NOT BETWEEN '2015-10-01' AND '2015-10-31';
```

Dependiendo del **sistema de bases de datos**, el **BETWEEN** puede devolver los valores delimitadores, excluirlos, o incluir sólo el primer valor delimitador.

### 14. Aliases

Los **alias SQL** se usan para **renombrar temporalmente** una tabla o columna, de esta forma son más legibles, especialmente para consultas algo más complejas. 

Para **columnas**:

```
SELECT nombreColumna AS alias
FROM nombreTabla
```

Para **tablas**:

```
SELECT nombreColumna
FROM nombreTabla AS alias
```

Los **alias** son útiles cuando: hay **más de una tabla** involucrada, se usan **funciones SQL**, los **nombres** de las columnas son **largos o poco legibles** o **se combinan dos o más columnas**. Vamos a ver algunos ejemplos con las siguientes tablas de **clientes** y **pedidos**:

**Clientes**:

| | | | | | |
| -------- | -------- |
| **ClienteID** | **NombreCliente** | **Ciudad** | **Pais** | **Continente** | **Telefono** |
| 1 | Pedro Pérez | Madrid | España | Europa | 456667778 |
| 2 | María Carrasco | Barcelona | España | Europa | 477322899 |
| 3 | Louis Bárcenas | Madrid | España | Europa | 444123843 |
| 4 | Sonia López | Marrakech | Marruecos | Africa | 455533777 |

**Pedidos**:

| | | | |
| -------- | -------- |
| **PedidoID** | **ClienteID** | **Factura** | **FechaPedido** |
| 23 | 3 | 150 | 10/8/2015 |
| 24 | 2 | 25 | 2/9/2015 |
| 25 | 4 | 50 | 4/10/2015 |
| 26 | 2 | 70 | 10/10/2015 |

#### **Alias para columnas:**

La siguiente **sentencia SQL** especifica dos alias, uno para **NombreCliente** y otro para **Telefono**. Si el alias tiene espacios, es necesario emplear **corchetes** o **comillas dobles**:

```
SELECT NombreCliente AS Cliente, Telefono AS [Tel Contacto]
FROM Clientes
```

En la siguiente **sentencia**, se **combinan dos columnas** para crear el alias **lugar**:

```
SELECT NombreCliente, Ciudad+', '+Pais+', '+Continente AS lugar
FROM Clientes
```

Para **combinar columnas en MySQL** se hace lo siguiente:

```
SELECT nombreCliente, CONCAT(Ciudad,', ',Pais,', ',Continente) AS lugar
FROM Clientes
```

#### **Alias para tablas:**

La siguiente **sentencia SQL** selecciona todos los **pedidos** del **cliente con el ID 2**. En este caso se utilizan las dos tablas **clientes** y **pedidos**, y se les da a cada uno los alias **c** y **p** respectivamente:

```
SELECT p.PedidoID, p.FechaPedido, c.NombreCliente
FROM Clientes AS c, Pedidos AS p
WHERE c.NombreCliente="María Carrasco" AND c.ClienteID=p.ClienteID
```

La **sentencia anterior sin alias** es así:

```
SELECT Pedidos.PedidoID, Pedidos.FechaPedido, Clientes.NombreCliente
FROM Clientes, Pedidos
WHERE Clientes.NombreCliente="María Carrasco" AND Clientes.ClienteID=Pedidos.ClienteID
```

### 15. Data Types

Un **data type** define el **tipo de valor que una columna puede tener**.

Cada columna en una base de datos necesita un **nombre** y un **data type**. Los **desarrolladores SQL** deben decidir que tipo de datos se guardarán dentro de cada columna cuando crean una **tabla SQL**. El data type es una **etiqueta** y una **pauta** en SQL para entender que tipo de datos se esperan en cada columna, y también identifica como SQL **interactúa** con los datos guardados.

Los **data types** más generales son:

| | |
| -------- | -------- |
| **Data Type** | **Descripción** |
| CHARACTER(n) | String de **caracteres** de tamaño fijo n |
| VARCHAR(n) | String de **caracteres** de tamaño variable n (n es tamaño máximo) |
| BINARY(n) | String **binario** de tamaño fijo n |
| VARBINARY(n) | String **binario** de tamaño variable n (n es tamaño máximo) |
| BOOLEAN | Guarda valores **true** o **false** |
| INTEGER(p) | **Integer** numérico (no decimal) de precisión p |
| SMALLINT | **Integer** numérico (no decimal) de precisión 5 |
| INTEGER | **Integer** numérico (no decimal) de precisión 10 |
| BIGINT | **Integer** numérico (no decimal) de precisión 19 |
| FLOAT(p) | **Numérico aproximado** de precisión p. Un número **float** en notación exponencial de base 10\. El argumento de precisión especifica la **precisión mínima** |
| FLOAT | **Numérico aproximado**, precisión 16 |
| DECIMAL(p,d) | **Numérico exacto** de precisión p y con d decimales. Util para cantidades monetarias |
| DATE | Guarda los valores año, mes, y día |
| TIME | Guarda los valores hora, minuto y segundo |
| TIMESTAMP | Guarda los valores año, mes, día, hora, minuto y segundo |

Dependiendo del **sistema de bases de datos**, los **data types** se definen de forma diferente. Conviene leer la documentación del sistema que vayas a utilizar.

### 16. CREATE DATABASE

Para crear una **base de datos** se utiliza la sentencia **CREATE DATABASE**:

```
CREATE DATABASE mi_base_de_datos;
```

### 17. CREATE TABLE

Para crear tablas en una base de datos se utiliza la sentencia **CREATE TABLE**:

```
CREATE TABLE nombreTabla (
nombreColumna1 dataType(size),
nombreColumna2 dataType(size),
nombreColumna3 dataType(size),
...
);
```

El parámetro **nombreColumna** define el nombre de cada una de las columnas.

El parámetro **dataType** especifica el tipo de datos que tendrá la columna (VARCHAR, BOOLEAN, INTEGER...).

El parámetro **size** especifica la longitud máxima.

Para crear una tabla clientes con las columnas ClienteID, NombreCliente, Ciudad, y Teléfono:

```
CREATE TABLE Clientes (
ClienteID INT,
NombreCliente VARCHAR(40),
Ciudad VARCHAR(20),
Telefono INTEGER(15)
);
```

El ClienteID se puede crear (y es lo más común) para que sea un **número automático incremental**, con **Auto Increment** que se verá más abajo.

### 18. Constraints

Las **restricciones SQL**, o **constraints**, se usan para especificar **reglas para los datos de la tabla**. Si hay cualquier anomalía entre la restricción y los datos que se vayan a insertar o modificar, se aborta la inserción o modificación de los datos.

Se pueden especificar cuando se crea la tabla o después (utilizando la **sentencia ALTER TABLE**, explicada más abajo):

```
CREATE TABLE nombreTabla (
nombreColumna1 dataType(size) nombreConstraint,
nombreColumna2 dataType(size) nombreConstraint,
nombreColumna3 dataType(size) nombreConstraint,
);
```

En **SQL** existen las siguientes **restricciones o constraints**:

| | |
| -------- | -------- |
| **Restricción** | **Descripción** |
| **NOT NULL** | Indica que una columna no puede contener un valor NULL |
| **UNIQUE** | Indica que cada fila de la columna debe tener un valor único |
| **PRIMARY KEY** | Combinación entre NOT NULL y UNIQUE. Asegura que una o más columnas tienen una identidad única lo que ayuda a encontrar un dato más rápida y fácilmente |
| **FOREIGN KEY** | Asegura la integridad referencial de los datos de una tabla para que coincidan con los datos de otra tabla |
| **CHECK** | Asegura que el valor de una columna cumple con una condición específica |
| **DEFAULT** | Establece un valor por defecto en una columna |

Veamos un ejemplo de NOT NULL, UNIQUE y PRIMARY KEY **creando una tabla** (SQL Server, Oracle, MS Access):

```
CREATE TABLE Clientes (
ClienteID INT NOT NULL PRIMARY KEY,
NombreCliente VARCHAR(255) NOT NULL,
Ciudad VARCHAR(255) NOT NULL,
Telefono INT(15) NOT NULL UNIQUE,
);
```

El mismo ejemplo en **MySQL**:

```
CREATE TABLE Clientes (
ClienteID INT NOT NULL,
NombreCliente VARCHAR(255) NOT NULL,
Ciudad VARCHAR(255) NOT NULL,
Telefono INT(15) NOT NULL,
UNIQUE (Telefono),
PRIMARY KEY (ClienteID)
);
```

La mayoría de tablas deberían tener una **PRIMARY KEY**, y **cada tabla sólo puede tener una** PRIMARY KEY. Pero es posible **definir una PRIMARY KEY en múltiples columnas**, como en el siguiente ejemplo (sirve para SQL Server, Oracle, MS Access y MySQL):

```
CREATE TABLE Clientes (
ClienteID INT NOT NULL,
NombreCliente VARCHAR(255) NOT NULL,
Ciudad VARCHAR(255) NOT NULL,
Telefono INT(15) NOT NULL,
CONSTRAINT PersonaID PRIMARY KEY (ClienteID, NombreCliente)
);
```

En el ejemplo anterior sólo hay un PRIMARY KEY (PersonaID). Sin embargo, **el valor del PRIMARY KEY está formado por dos columnas**: ClienteID y NombreCliente. Esto también sirve para **nombrar** al PRIMARY KEY.

Tanto UNIQUE como PRIMARY KEY garantizan valores únicos para una columna o varias columnas. **Una PRIMARY KEY tiene automáticamente una restricción UNIQUE incorporada**, y se pueden tener múltiples restricciones UNIQUE por tabla, pero sólo una PRIMARY KEY.

También se puede **crear un nombre** o **definir varias columnas** como UNIQUE (sirve para SQL Server, Oracle, MS Access y MySQL):

```
CREATE TABLE Clientes (
ClienteID INT NOT NULL,
NombreCliente VARCHAR(255) NOT NULL,
Ciudad VARCHAR(255) NOT NULL,
Telefono INT(15) NOT NULL,
CONSTRAINT PersonaID UNIQUE (ClienteID, NombreCliente)
);
```

Como se ha podido ver la **sintaxis para el uso de restricciones SQL** también varía ligeramente de unos sistemas de bases de datos a otros.

Cuando una tabla ya está creada y queremos **alterar un _constraint_ de una columna**, se utiliza **ALTER TABLE** (sirve para SQL Server, Oracle, MS Access y MySQL):

```
ALTER TABLE Clientes
ADD PRIMARY KEY (ClienteID);
```

Hemos añadido un PRIMARY KEY en ClienteID, para añadirlo en varias columnas (sirve para SQL Server, Oracle, MS Access y MySQL):

```
ALTER TABLE Clientes
ADD CONSTRAINT PersonaID PRIMARY KEY (ClienteID, NombreCliente);
```

Para **eliminar un _constraint_** se utiliza **DROP** junto a **ALTER TABLE** (el siguiente ejemplo en MySQL):

```
ALTER TABLE Clientes
DROP PRIMARY KEY;
```

Y en caso de SQL Server, Oracle y MS Access:

```
ALTER TABLE Clientes
DROP CONSTRAINT PersonaID;
```

(De la misma forma que con PRIMARY KEY, **se cambian con ALTER TABLE y DROP las restricciones** con UNIQUE, pero cambiando la palabra PRIMARY KEY por UNIQUE).

Vamos a explicar ahora el **constraint FOREIGN KEY**, que actúa en dos tablas, de forma que la FOREIGN KEY se una tabla apunta al PRIMARY KEY de otra. Vamos a coger las tablas de **Clientes** y **Pedidos** de antes:

**Clientes**:

| | | | | | |
| -------- | -------- |
| **ClienteID** | **NombreCliente** | **Ciudad** | **Pais** | **Continente** | **Telefono** |
| 1 | Pedro Pérez | Madrid | España | Europa | 456667778 |
| 2 | María Carrasco | Barcelona | España | Europa | 477322899 |
| 3 | Louis Bárcenas | Madrid | España | Europa | 444123843 |
| 4 | Sonia López | Marrakech | Marruecos | Africa | 455533777 |

**Pedidos**:

| | | |
| -------- | -------- |
| **PedidoID** | **ClienteID** | **Factura** |
| 23 | 3 | 150 |
| 24 | 2 | 25 |
| 25 | 4 | 50 |
| 26 | 2 | 70 |

Nótese que en la tabla de pedidos hay una columna ClienteID, que hace referencia al ClienteID de la tabla Clientes. 

La columna ClienteID es el **PRIMARY KEY** de la tabla Clientes.

La columna ClienteID es el **FOREIGN KEY** de la tabla Pedidos.

La restricción FOREIGN KEY se usa para **prevenir acciones que podrían romper los enlaces entre las tablas**. También evita la inserción de datos inválidos en la columna donde está el FOREIGN KEY, porque tiene que ser un valor contenido en la tabla a la que apunta.

Veamos un ejemplo creando una tabla en MySQL:

```
CREATE TABLE Pedidos (
PedidoID INT NOT NULL,
Factura INT NOT NULL,
ClienteID INT NOT NULL,
PRIMARY KEY (PedidoID),
FOREIGN KEY (ClienteID) REFERENCES Clientes(ClienteID)
);
```

En SQL Server, Oracle y MS Access:

```
CREATE TABLE Pedidos (
PedidoID NOT NULL PRIMARY KEY,
Factura INT NOT NULL,
ClienteID INT FOREIGN KEY REFERENCES Clientes (ClienteID)
);
```

Para **nombrar una restricción FOREIGN KEY o definirlo en múltiples columnas** (sirve para SQL Server, Oracle, MS Access y MySQL):

```
CREATE TABLE Pedidos (
PedidoID INT NOT NULL,
Factura INT NOT NULL,
ClienteID INT,
PRIMARY KEY (PedidoID),
CONSTRAINT PorPedidos FOREIGN KEY (ClienteID) REFERENCES Clientes(ClienteID)
);
```

Se puede también alterar la tabla para **añadir FOREIGN KEYs o eliminarlos** (sirve para SQL Server, Oracle, MS Access y MySQL):

```
ALTER TABLE Pedidos
ADD FOREIGN KEY (ClienteID) REFERENCES Clientes(ClienteID);
```

Para **nombrar o definir múltiples FOREIGN_KEYs**:

```
ALTER TABLE Clientes
ADD CONSTRAINT PorPedidos
FOREIGN KEY (ClienteID) REFERENCES (ClienteID);
```

Para **eliminar FOREIGN KEYS en MySQL**:

```
ALTER TABLE Pedidos
DROP FOREIGN KEY PorPedidos;
```

En SQL Server, Oracle y MS Access:

```
ALTER TABLE Pedidos
DROP CONSTRAINT PorPedidos;
```

### 19. CREATE INDEX

La sentencia **CREATE INDEX** permite **crear índices en las tablas**. Permiten a la base de datos de una aplicación **encontrar datos rápidamente**, sin leer la tabla entera.

Los usuarios no pueden ver los índices, simplemente **se usan para mejorar el rendimiento de las búsquedas**.

**Actualizar una tabla con índices lleva más tiempo que actualizar una tabla sin índices** (porque los índices también tendrán que actualizarse). Por defecto se crea un índice en el campo(o campos) que forman la primary key. El resto de índices debemos crearlos en las columnas  que **se utilicen para filtar con más frecuencia**.

```
CREATE INDEX nombreIndex
ON nombreTabla (nombreColumna);
```

Se pueden crear **índices únicos**, donde no se permiten valores duplicados:

```
CREATE UNIQUE INDEX nombreIndex
ON nombreTabla (nombreColumna);
```

De nuevo, dependiendo del sistema de bases de datos, puede variar la sintaxis.

Vamos a crear un índice en la tabla Clientes:

```
CREATE INDEX CIndex
ON Clientes (NombreCliente);
```

También es posible **crear un index en varias columnas**:

```
CREATE INDEX CIndex
ON Clientes (NombreCliente, Pais);
```

### 20. DROP

Índices, tablas y bases de datos pueden eliminarse con la sentencia **DROP**.

Para borrar un índice, DROP INDEX varía bastante entre diferentes sistemas, en MySQL es:

```
ALTER TABLE nombreTabla DROP INDEX nombreIndex;
```

Para borrar una tabla:

```
DROP TABLE nombreTabla;
```

Para borrar una base de datos:

```
DROP DATABASE nombreDB;
```

Si sólo queremos **borrar los datos de una tabla**, se una TRUNCATE TABLE:

```
TRUNCATE TABLE nombreTabla
```

### 21. ALTER

La sentencia **ALTER TABLE** se usa para **añadir, eliminar o modificar columnas en una tabla ya existente**.

Para añadir una columna en una tabla:

```
ALTER TABLE nombreTabla
ADD nombreColumna dataType;
```

Para borrar una columna en una tabla:

```
ALTER TABLE nombreTabla
DROP COLUMN nombreColumna;
```

Para modificar el **data type** de una columna, la sintaxis varía en función del sistema utilizado:

```
//// SQL Server / MS Access:
ALTER TABLE nombreTabla
ALTER COLUMN nombreColumna dataType;

//// Mysql / Oracle (veriones anteriores a 10G)
ALTER TABLE nombreTabla
MODIFY COLUMN nombreColumna dataType;

//// Oracle 10G y superiores
ALTER TABLE nombreTabla
MODIFY nombreColumna dataType;
```

### 22. AUTO INCREMENT

AUTO INCREMENT permite generar un **único** número **automáticamente** cuando se inserta un nuevo registro. 

Muy frecuentemente se utiliza **junto al valor PRIMARY KEY** de una tabla para que se cree de forma automática cada vez que se inserta un nuevo registro.

Varía en cada uno de los sistemas de bases de datos, por lo que sólo voy a poner el ejemplo en MySQL:

```
CREATE TABLE Clientes (
ClienteID INT NOT NULL AUTO_INCREMENT,
NombreCliente VARCHAR(255) NOT NULL,
Ciudad VARCHAR(255) NOT NULL,
Telefono INT(15) NOT NULL,
PRIMARY KEY (ClienteID)
);
```

Por defecto, **el valor inicial de AUTO_INCREMENT es 1**, e incrementará de 1 en 1 por cada nuevo registro.

Para hacer que comience en un valor distinto de 1:

```
ALTER TABLE Clientes AUTO_INCREMENT=100;
```

Para insertar un nuevo valor en la tabla Clientes, no tendremos que especificar un valor para la columna ClienteID, se añadirá un valor único automáticamente:

```
INSERT INTO Clientes (NombreCliente, Ciudad, Telefono)
VALUES ('Luisa Gómez', 'Cáceres', '453222736');
```
En gestores de base de datos como Oracle o PostgreSQL se necesitará una secuencia que genere el siguiente valor.

### 23. Views

Una **view** es una **tabla virtual basada en el result-set de una sentencia SQL**.

**Una view contiene filas y columnas**, como una tabla real. Los campos de una view son campos de una o más tablas reales de la base de datos.

Se pueden añadir **funciones SQL**, o cláusulas como **WHERE** y **JOIN** y presentar los datos como si vinieran de una sóla tabla.

La sintaxis es la siguiente:

```
CREATE VIEW nombreView AS
SELECT nombreColumna(s)
FROM nombreTabla
WHERE condicion
```

**Una view siempre muestra los datos actuales**. El motor de la base de datos actualiza los datos cada vez que un usuario realiza una consulta.

Vamos a poner un ejemplo de una tienda online que quiere **listar todos los productos en stock** de la tabla **Productos**:

```
CREATE VIEW [Productos en Stock] AS
SELECT ProductoID, NombreProducto
FROM Productos
WHERE EnStock=Si
```

Ahora podemos llamar a esa consulta de la siguiente forma:

```
SELECT * FROM [Productos en Stock]
```

Podemos **actualizar la View** con la siguiente sintaxis:

```
CREATE OR REPLACE VIEW nombreView AS
SELECT nombreColumna(s)
FROM nombreTabla
WHERE condicion
```

En el ejemplo anterior, queremos también poder acceder a la categoría del producto, por lo que la añadimos:

```
CREATE OR REPLACE VIEW [Productos en Stock] AS
SELECT ProductoID, NombreProducto, Categoria
FROM Productos
WHERE EnStock=Si
```
