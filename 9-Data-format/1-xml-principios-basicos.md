**Indice de contenido**

| | |
| -------- | -------- |
| 1. Introducción | 7. ¿Qué es XPath? |
| 2. Reglas básicas de sintaxis | 8. ¿Qué es XLink? |
| 3. Elementos en XML | 9. ¿Que es XPointer? |
| 4. Atributos en XML | 10. Document Type Definition |
| 5. Namespaces en XML | 11. XML Schemas |
| 6. Mostrar archivos XML | 12. XML en el servidor |

### 1. Introducción

**XML** son las siglas de **Extensible Markup Language**, un **lenguage de marcado** para guardar y transportar datos **legible** para ordenadores y humanos. A pesar de que su diseño está focalizado a documentos, se usa especialmente para la representación de **estructuras de datos** como en los [web services](http://diego.com.es/introduccion-a-los-web-services).

```
<?xml version="1.0" encoding="UTF-8"?>
<selva>
    <animal>
        <nombre>Werthers</nombre>
        <tipo>Pantera</tipo>
        <color>Negro</color>
        <edad>12</edad>
        </animal>
    <animal>
        <nombre>Bun</nombre>
        <tipo>León</tipo>
        <color>Marrón</color>
        <edad>15</edad>
    </animal>
</selva>
```

**XML** se usa frecuentemente para **separar los datos de la presentación**. XML no indica cómo presentar la información, un mismo **documento XML** puede presentarse de muchas formas. Es muy utilizado junto a **aplicaciones HTML**, donde XML guarda o transporta los datos, y **HTML** les da formato y los muestra. Cuando se muestran los datos en HTML, no es necesario tener que editar el archivo HTML cuando los datos cambian, XML guarda los datos en archivos separados. Con los lenguajes de programación web como **PHP** o **Javascript** se pueden **leer archivos XML** y **actualizar el contenido de cualquier página HTML**.

La **sintaxis que se utiliza para iniciar un documento XML** es la siguiente:

```
<?xml version="1.0" encoding="UTF-8"?>
```

Se define la **versión XML** y la [codificación de caracteres](http://diego.com.es/codificacion-de-caracteres-en-programacion).

Los **documentos XML** forman una estructura de tipo árbol, comenzando desde la **raíz** (_root_), con ramas hacia las **hojas** (_leaves_). Vamos a ver el siguiente ejemplo de un archivo _cine.xml_:

```
<?xml version="1.0" encoding="UTF-8"?>
<cine>
    <pelicula categoria="accion">
        <titulo idioma="ingles">Mad Max</titulo>
        <director>George Miller</director>
        <estreno>15 mayo 2015</estreno>
        <reparto>Tom Hardy</reparto>
        <reparto>Charlize Theron</reparto>
        <reparto>Nicholas Hoult</reparto>
    </pelicula>
    <pelicula categoria="animacion">
        <titulo idioma="ingles">Inside Out</titulo>
        <director>Pete Docter</director>
        <estreno>17 junio 2015</estreno>
        <reparto>Amy Poehler</reparto>
        <reparto>Phyllis Smith</reparto>
        <reparto>Bill Hader</reparto>
    </pelicula>
</cine>
```

La **estructura en árbol** sería así:

![Estructura árbol XML](/uploads/articulos/arbolxml.png)

Los **documentos XML** están formados por árboles de elementos. Cada árbol comienza con un _**elemento root**_, del cual surgen ramas (_branches_) a elementos hijo. **Todos** los elementos pueden tener elementos hijo.

Se suelen emplear los términos _**parent**_, _**child**_ y _**sibling**_ para determinar el parentesco: padre, hijo y hermano.

Todos los elementos pueden tener _**contenido textual**_ ("Mad Max") y _**atributos**_ (categoria="accion").

### 2. Reglas básicas de sintaxis

Las reglas de sintaxis básicas de XML son muy simples:

*   Todos los **elementos XML** deben tener **un** **elemento root** que es padre de todos los demás (<cine>).
*   La primera línea del documento se llama **prolog**.
    `<?xml version="1.0" encoding="UTF-8" ?>`
    Es opcional, pero si se incluye ha de ir en la primera línea. **UTF-8** es la codificación de caracteres por defecto y la recomendable.
*   Todos los elementos han de tener su etiqueta de cierre (_</pelicula>_).
*   Las **etiquetas XML** son sensibles a mayúsculas y minúsculas. _<cine>_ es diferente a _<Cine>_.
*   Las etiquetas han de ir correctamente anidadas. Esto es incorrecto: _<pelicula><titulo>Mad Max</pelicula></titulo>_.
*   Los **atributos XML** deben ir entre comillas _<pelicula categoria="accion">_.
*   XML tiene caracteres especiales que han de utilizarse de distinta forma para evitar conflicto, son 5:

| | |
| -------- | -------- |
| $lt; | < |
| &gt; | > |
| &amp; | & |
| &apos; | ' |
| &quot; | " |

Sólo < y & están estrictamente prohibidos en XML, pero es recomendable utilizar las demás también.

*   La sintaxis para **escribir comentarios en XML** es como en HTML:
    `<!-- Esto es un comentario -->`
*   **XML** no trunca múltiples espacios (HTML sí lo hace):
    XML:   Hola             que tal
    HTML: Hola que tal
*   **XML** interpreta las nuevas líneas como LF (al igual que UNIX y Mac OSX).

### 3. Elementos en XML

Un elemento XML es cualquier cosa que esté incluída entre la etiqueta inicial y la final:

```
<director>George Miller</director>
```

Puede contener **texto**, **atributos**, **otros elementos** o una **mezcla de los anteriores**:

```
<?xml version="1.0" encoding="UTF-8"?>
<selva>
    <animal>
        <nombre>Werthers</nombre>
        <tipo>Pantera</tipo>
        <color>Negro</color>
        <edad>12</edad>
        </animal>
    <animal>
        <nombre>Bun</nombre>
        <tipo>León</tipo>
        <color>Marrón</color>
        <edad>15</edad>
    </animal>
</selva>
```

También es posible que no contenga nada:

```
<elemento></elemento>
```

Lo cual también se puede escribir así:

```
<elemento />
```

Los dos anteriores son elementos vacíos, y también pueden llevar atributos.

**Reglas para los elementos**:

*   Son sensibles a mayúsculas y minúsculas
*   Deben empezar con una letra o barra baja
*   No pueden empezar con las letras xml
*   Pueden contener letras, dígitos, guiones, barras bajas y puntos
*   No pueden contener espacios

Lo ideal es mantener unos **nombres sencillos**, evitando caracteres como guiones y puntos.

Si los **documentos XML** tienen una base de datos correspondiente, lo normal es usar los nombres de la base de datos para los elementos.

Si tienes un elemento como:

```
<animal>
        <nombre>Bun</nombre>
        <tipo>León</tipo>
    </animal>
```

Y queremos extenderlo a:

```
<animal>
        <nombre>Bun</nombre>
        <tipo>León</tipo>
        <color>Marrón</color>
        <edad>15</edad>
    </animal>
```

No hay ningún problema, XML es muy flexible y se extiende y adapta a los nuevos elementos.

### 4. Atributos en XML

Los **elementos XML** pueden tener **atributos**, como en **HTML**. Estan diseñados para contener datos relacionados con un elemento específico.

Han de ir siempre encerrados en **comillas**, ya sean **simples o dobles**:

```
<pelicula categoria="accion">
        <titulo idioma="ingles">Mad Max</titulo>
        <director>George Miller</director>
        <estreno>15 mayo 2015</estreno>
        <reparto>Tom Hardy</reparto>
        <reparto>Charlize Theron</reparto>
        <reparto>Nicholas Hoult</reparto>
    </pelicula>
```

Si el valor del atributo lleva comillas, se puede emplear cualquiera de los dos siguientes métodos:

```
<futbolista nombre='Gonzalo "Pipita" Higuaín'>
<futbolista nombre='Gonzalo &quot;Pipita&quot; Higuaín'>
```

Los **atributos** de alguna forma **están limitados**. Al contrario que los elementos, los atributos:

*   No pueden contener múltiples valores
*   No pueden contener estructuras en árbol
*   No se pueden expandir fácilmente (para futuros cambios)

Por ejemplo, si es posible elegir, es mejor evitar esto:

```
<mensaje fecha="10-10-2015">
    <asunto>Hola</asunto>
    <mensaje>Que tal estás</mensaje>
</mensaje>
```

Y aplicar la **fecha como otro elemento más**:

```
<mensaje>
    <fecha>10-10-2015</fecha>
    <asunto>Hola</asunto>
    <mensaje>Que tal estás</mensaje>
</mensaje>
```

O incluso como **varios elementos más**:

```
<mensaje>
    <fecha>
        <año>2015</año>
        <mes>10</mes>
        <dia>10</dia>
    </fecha>
    <asunto>Hola</asunto>
    <mensaje>Que tal estás</mensaje>
</mensaje>
```

A menudo los atributos se utilizan para **asignar ID's a elementos**, como si fuera el **ID en HTML**:

```
<mensaje id="142">
    <fecha>
        <año>2015</año>
        <mes>10</mes>
        <dia>10</dia>
    </fecha>
    <asunto>Hola</asunto>
    <mensaje>Que tal estás</mensaje>
</mensaje>
```

Lo ideal es guardar los metadatos (datos sobre datos) como **atributos**, y los datos en sí mismos como **elementos**.

### 5. Namespaces en XML

Los **espacios de nombres** o **namespaces** proporcionan una forma de evitar conflictos de nombres entre elementos.

Los nombres de elementos pueden coincidir entre diferentes documentos XML. Por ejemplo, el siguiente es un **documento XML con información de una tabla HTML**:

```
<table>
    <tr>
        <td>Léon</td>
        <td>Tigre</td>
    </tr>
</table>
```

Y el siguiente XML lleva el nombre de una mesa en concreto:

```
<table>
    <name>Mesa de café</name>
    <colour>Marrón</colour>
</table>
```

Si estos fragmentos de documentos se añaden juntos habría conflicto. Una **aplicación XML** no sabrá como resolver estas diferencias.

La solución es emplear un **prefijo**. Las modificaciones siguientes evitan el conflicto entre los dos fragmentos anteriores:

```
<h:table>
    <h:tr>
        <h:td>León</h:td>
        <h:td>Tigre</h:td>
    </h:tr>
</h:table>

<m:table>
    <m:name>Mesa de café</m:name>
    <m:colour>Marrón</m:colour>
</m:table>
```

Pero para emplear un prefijo, hay que establecer un **espacio de nombres**. El espacio de nombres se define con el atributo _**xmlns**_ en la etiqueta inicial del elemento. La sintaxis es: _xmlns:prefijo="URI"_:

```
<h:table xmlns:h="http://www.w3.org/TR/html5/">
    <h:tr>
        <h:td>León</h:td>
        <h:td>Tigre</h:td>
    </h:tr>
</h:table>
<m:table xmlns:m="http://www.ejemplo.com/muebles">
    <m:name>Mesa de café</m:name>
    <m:colour>Marrón</m:colour>
</m:table>
```

En el ejemplo anterior, el atributo **xmlns** en el primer <table> le asigna al prefijo _**h:**_ un namespace, y en el segundo table otro namespace al prefijo _**m:**_.

Cuando se define un namespace para un elemento, todos los elementos hijo con el mismo prefijo están asociados al mismo namespace.

También pueden **declararse namespaces en el elemento root**:

```
<root  xmlns:h="http://www.w3.org/TR/html5/"
       xmlns:m="http://www.ejemplo.com/muebles">
<h:table>
    <h:tr>
        <h:td>León</h:td>
        <h:td>Tigre</h:td>
    </h:tr>
</h:table>
<m:table >
    <m:name>Mesa de café</m:name>
    <m:colour>Marrón</m:colour>
</m:table>
</root>
```

La URI no es para buscar información en esa URL, sino que se utiliza para darle un **nombre único al namespace**. Se suele emplear una URL porque las empresas a veces proporcionan información sobre dicho namespace en esa URL.

**URI** viene de **Uniform Resource Identifier**, y es un string de caracteres que identifican una Internet Resource. La URI más común es la Uniform Resource Locator (URL) que identifica un dominio. Otro no tan común es el Universal Resource Name (URN).

### 6. Mostrar archivos XML

Los **archivos XML** se pueden leer directamente en la mayoría de navegadores, pero lo hacen prácticamente de la misma forma en la que son editados. Un ejemplo muy común es en los **sitemaps**, como el [sitemap de este mismo blog](http://diego.com.es/sitemap.xml):

```
<url>
    <loc>http://diego.com.es/</loc>
    <changefreq>weekly</changefreq>
    <priority>1.0</priority>
</url>
<url>
    <loc>http://diego.com.es/blog/symfony</loc>
    <priority>0.5</priority>
</url>
```

Se muestra de esta forma porque **los documentos XML no llevan información acerca de cómo mostrar los datos**.

Para formatear **XML** se puede emplear **PHP**, **Javascript**, o **XSLT**, entre otros. No es recomendable hacerlo en CSS.

**XSLT** son las siglas de **eXtensible Stylesheet Language Transformations**, y es el **leguaje de hojas de estilo de XML**.

**XSLT es más sofisticado que CSS**. Con XSLT se pueden añadir/eliminar elementos y atributos del archivo de salida. Se pueden también reordenar elementos, hacer tests y decidir qué elementos mostrar o quitar, entre otras muchas cosas. **XSLT utiliza XPath para encontrar información en un documento XML** (explicado en la siguiente sección).

Si partimos del ejemplo anterior del **documento XML** con el **elemento root** cine (_xmltest.xml_):

```
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="hojaxslt.xsl">
<cine>
    <pelicula categoria="accion">
        <titulo idioma="ingles">Mad Max</titulo>
        <director>George Miller</director>
        <estreno>15 mayo 2015</estreno>
        <reparto>Tom Hardy</reparto>
        <reparto>Charlize Theron</reparto>
        <reparto>Nicholas Hoult</reparto>
    </pelicula>
    <pelicula categoria="animacion">
        <titulo idioma="ingles">Inside Out</titulo>
        <director>Pete Docter</director>
        <estreno>17 junio 2015</estreno>
        <reparto>Amy Poehler</reparto>
        <reparto>Phyllis Smith</reparto>
        <reparto>Bill Hader</reparto>
    </pelicula>
</cine>
```

Se puede emplear el siguiente **documento XSLT** (_hojaxslt.xsl_) para **transformar XML en HTML** antes de mostrarlo al servidor:

```
<?xml version="1.0" encoding="UTF-8"?>
<html xsl:version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <body style="font-family:Arial;font-size:14pt;background-color:#EEEEEE">
        <xsl:for-each select="cine/pelicula">
            <div style="background-color:#000;color:white;padding:6px">
                <xsl:value-of select="titulo"/> - 
                <xsl:value-of select="director"/>
            </div>
            <div style="margin-left:20px;margin-bottom:1em;font-size:14pt">
                <p>
                    Reparto:
                    <xsl:for-each select="reparto">
                    <p> <xsl:value-of select="."/> </p>
                    </xsl:for-each>
                </p>
            </div>
        </xsl:for-each>
    </body>
</html>
```

El ejemplo anterior quedaría así:

![Ejemplo hoja xslt](/uploads/articulos/ejemplohojaxslt.png)

### 7. ¿Qué es XPath?

**XPath** es en **lenguaje de ruta de XML** para **encontrar información en un documento XML**.

XPath es una sintaxis para **definir partes de un documento XML**, fundamental para las **hojas XSLT**. XPath utiliza expresiones de ruta para navegar por el documento, y contiene una **librería con funciones estándar**. XPath es una **recomendación W3C** y se utiliza también en **XQuery**, **XSLT** y **XLink**:

![Relación entre XQuery, XLink, XSLT, XPointer y XPath](https://upload.wikimedia.org/wikipedia/commons/thumb/9/94/XML_languages.svg/440px-XML_languages.svg.png)

**XPath** utiliza **expresiones de ruta** para **seleccionar nodos y conjuntos de nodos** en un **documento XML**. Estas rutas son muy parecidas a las que se utilizan en los sistemas de ordenadores tradicionales.

Las **expresiones XPath** pueden utilizarse en JavaScript, Java, XML Schema, PHP, Python, C, C++...

Empleando el ejemplo anterior con el **elemento root** cine:

```
<cine>
    <pelicula categoria="accion">
        <titulo idioma="ingles">Mad Max</titulo>
        <director>George Miller</director>
        <ticket>8</ticket>
        <reparto>Tom Hardy</reparto>
        <reparto>Charlize Theron</reparto>
        <reparto>Nicholas Hoult</reparto>
    </pelicula>
    <pelicula categoria="animacion">
        <titulo idioma="ingles">Inside Out</titulo>
        <director>Pete Docter</director>
        <ticket>10</ticket>
        <reparto>Amy Poehler</reparto>
        <reparto>Phyllis Smith</reparto>
        <reparto>Bill Hader</reparto>
    </pelicula>
</cine>
```

Se podrían emplear las siguientes **expresiones XPath**:

| | |
| -------- | -------- |
| **Expresión XPath** | **Resultado** |
| **/cine/pelicula[1]** | Selecciona el primer elemento pelicula del elemento cine |
| **/cine/pelicula[last()]** | Selecciona el último elemento pelicula del elemento cine |
| **/cine/pelicula[last()-1]** | Selecciona el penúltimo elemento pelicula del elemento cine |
| **/cine/pelicula[position()<3]** | Selecciona los dos primeros elementos película del elemento cine |
| **//titulo[@idioma]** | Selecciona todos los elementos titulo que tienen el atributo idioma |
| **//titulo[@idioma='ingles']** | Selecciona todos los elementos titulo que tienen el atributo idioma con el valor ingles |
| **/cine/pelicula[ticket>8]** | Selecciona todos los elementos pelicula del elemento cine que tienen un elemento ticket mayor que 8 |
| **/cine/pelicula[ticket>8]/titulo** | Selecciona todos los elementos titulo de los elementos pelicula que tienen un elemento ticket cuyo valor es mayor que 8 |

### 8. ¿Qué es XLink?

**XLink se utiliza para crear hiperenlaces en documentos XML**. Cualquier elemento en un documento XML puede comportarse como un enlace, y con **XLink** los enlaces pueden definirse desde fuera de los archivos enlazados.

En HTML los enlaces se definen con **<a>**. En **XML** se puede utilizar **cualquier nombre de elemento** (lo que sería imposible para un navegador predecir qué elementos de enlaces existen en los documentos XML).

```
<?xml version="1.0" encoding="UTF-8"?>
<homepages xmlns:xlink="http://www.w3.org/1999/xlink">
<homepage xlink:type="simple" xlink:href="http://www.google.com">Google</homepage>
<homepage xlink:type="simple" xlink:href="http://www.w3.org">Visita W3C</homepage>
</homepages>
```

Para poder acceder a las **características de XLink** se ha de incluir el **namespace XLink**: "http://www.w3.org/1999/xlink".

Los atributos **xlink:type** y **xlink:href** vienen del namespace XLink.

**xlink:type="simple"** crea un enlace normal de HTML. **xlink:href** indica la URL a la que dirigirse.

```
<?xml version="1.0" encoding="UTF-8"?>
<cine xmlns:xlink="http://www.w3.org/1999/xlink">
<pelicula title="Mad Max">
    <description
            xlink:type="simple"
            xlink:href="/imagenes/madmax.gif"
            xlink:show="new">
        Descripción de la película Mad Max
    </description>
</pelicula>
<pelicula title="Inside Out">
    <description
            xlink:type="simple"
            xlink:href="/imagenes/insideout.gif"
            xlink:show="new">
        Descripción de la película Inside Out
    </description>
</pelicula>
```

El atributo _**xlink:show="new"**_ especifica que el enlace se debe abrir en una nueva ventana.

En los ejemplos anteriores se han mostrado enlaces simples. Con **XLink** se puede acceder a sitios remotos y _**resources**_.

Si por ejemplo en el atributo _**xlink:show**_  ponemos el valor **"_embed_"**, el resource se procesará en la misma página. Si este resource es otro **documento XML**, se puede crear una jerarquía de documentos XML.

También es posible especificar cuándo debe aparecer el resource, con el atributo _**xlink:actuate**_.

**Referencia de atributos XLink**:

| | | |
| -------- | -------- |
| **Atributo** | **Valor** | **Descripción** |
| **xlink:actuate** | onLoad, onRequest, other, none | Define cuando se ha de leer y mostrar el resource enlazado |
| **xlink:href** | URL | Especifica la URL a la que enlazar |
| **xlink:show** | embed, new, replace, other, none | Especifica donde abrir el enlace. Por defecto es "replace" |
| **xlink:type** | simple, extended, locator, arc, resource, title, none | Especifica el tipo de enlace |

### 9. ¿Qué es XPointer?

**XPointer** permite a los enlaces apuntar a **partes específicas de un documento XML**. XPointer utiliza **expresiones XPath** para navegar por el **documento XML**.

En el siguiente ejemplo se utiliza **XPointer junto con XLink** para apuntar a una parte específica del documento. Primero empezamos con el documento objetivo al que vamos a enlazar:

```
<?xml version="1.0" encoding="UTF-8"?>
<cine>
   <pelicula titulo="Mad Max" id="23">
      <imagen url="http://ejemplo.com/madmax.gif" />
      <description>Descripción de la película Mad Max</description>
   </pelicula>
   <pelicula titulo="Inside Out" id="34">
      <imagen url="http://ejemplo.com/insideout.gif" />
      <description>Descripción de la película Inside Out</description>
   </pelicula>
</cine>
```

Ahora el elemento película tiene un atributo llamado id.

En lugar de enlazar al documento entero como con **XLink**, **XPointer** permite enlazar partes específicas. Para ello se añade una **almohadilla #** y una **expresión XPointer** después de la URL en el atributo _**xlink:href**_, así:
```
_xlink:href="http://ejemplo.com/peliculas.xml#xpointer(id('23'))"_
```

También existe una forma más reducida de enlazar a un elemento con el atributo id:
```
_xlink:href="http://ejemplo.com/peliculas.xml#23"_
```

El siguiente **documento XML** contiene un **enlace con más información** de mi película favorita:

```
<cine>
   <mifavorita>
      <imagen url="http://ejemplo.com/madmax.gif" />
      <enlace xlink:type="simple" xlink:href="http://ejemplo.com/peliculas.xml#34">
         Enlace a mi película favorita</enlace>
   </mifavorita>
</cine>
```

### 10. Document Type Definition

A un documento XML con la **sintaxis correcta** se le denomina "well formed".

Un **documento XML válido** debe además respetar un DTD, **Document Type Definition**, que son reglas que definen los elementos y atributos permitidos. Un DTD puede llamarse DTD, que hace referencia al Document Type Definition original, o **XML Schema**, que es una alternativa a DTD basada en XML.

Un DTD permite que un grupo de personas acuerden utilizar un **DTD estándar** para el intercambio de datos y así poder asegurar una uniformidad.

Un **documento XML válido** es también **well formed**, es decir, que tiene la sintaxis correcta:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE pelicula SYSTEM "pelicula.dtd">
```

La declaración **!DOCTYPE** hace referencia a un **archivo DTD externo**. El contenido de _pelicula.dtd_ es el siguiente:

```
<!DOCTYPE pelicula
[
<!ELEMENT pelicula (titulo,estreno,director,reparto)>
<!ELEMENT titulo (#PCDATA)>
<!ELEMENT estreno (#PCDATA)>
<!ELEMENT director (#PCDATA)>
<!ELEMENT reparto (#PCDATA)>
]>
```

El DTD anterior se interpreta así:

*   **!DOCTYPE pelicula** define que el elemento root del documento es pelicula.
*   **!ELEMENT pelicula** define que el elemento pelicula debe contener los elementos titulo, estreno, director y reparto.
*   **!ELEMENT elemento (#PCDATA)** define el tipo de elemento. #PCDATA significa datos de texto interpretables.

Un **DTD** puede emplearse también para **definir caracteres especiales** en un documento.

### 11. XML Schemas

Un esquema XML o XML Schema **describe la estructura de un documento XML**, como un DTD. Es una alternativa a DTD basada en XML:

```
<xs:element name="pelicula">
   <xs:complexType>
      <xs:sequence>
         <xs:element name="titulo" type="xs:string"/>
         <xs:element name="director" type="xs:string"/>
         <xs:element name="reparto" type="xs:string"/>
      </xs:sequence>
   </xs:complexType>
</xs:element>
```

El XML Schema anterior se interpreta así:

*   _**<xs:element name="pelicula">**_ define el elemento llamado pelicula.
*   _**<xs:complexType>**_ el elemento pelicula es del tipo complex.
*   _**<xs:sequence>**_ el tipo complex es una secuencia de elementos.
*   _**<xs:element name="elemento" type="xs:string"/>**_ define que el elemento "elemento" ha de ser del tipo string.

Los **XML Schemas** son más potentes que los **DTD** porque están escritos en XML, se pueden extender y soportan tipos de datos y namespaces.

### 12. XML en el servidor

Los **archivos XML** son archivos de texto como los **archivos HTML**. Un archivo XML puede ser fácilmente guardado y generado por un servidor web.

Por ejemplo en **PHP**, sin utilizar ninguna [extensión PHP](http://diego.com.es/extensiones-en-php), se puede **generar un archivo xml** de la siguiente forma:

```
<?php
header("Content-type: text/xml");
echo "<?xml version='1.0' encoding='UTF-8'?>";
echo "<pelicula>";
echo "<titulo>Mad Max</titulo>";
echo "<director>George Miller</director>";
echo "<estreno>15 mayo 2015</estreno>";
echo "</pelicula>";
?>
```