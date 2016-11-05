Obtuve la certificación el 01 de diciembre de 2015, y poco después he publicado este libro que llevaba tiempo preparando. En un principio era una guía de estudio propia, pero después ví que podría ser de utilidad a más gente. Todos los artículos salvo la recopilación y esta conclusión están disponibles en mi blog [diego.com.es](http://diego.com.es/certificado-php), donde se pueden hacer comentarios con dudas o sugerencias en cada capítulo. Intentaré responderlos siempre que pueda.

El examen consta de 70 preguntas que han de responderse en 90 minutos, y las preguntas son muy variadas. Una orientación de cómo serán las preguntas del examen puede verse en la propia [guía oficial de Zend para el examen](http://www.zend.com/en/services/certification/php-certification-study-guide) que cuesta 19,95$. Otra buena fuente de preguntas es el [pack de preparación con 70 preguntas de Lorna Mitchell](https://leanpub.com/zce), que cuesta un mínimo de 24$. Mediante Google también pueden encontrarse más fuentes.

Las preguntas son técnicas, y muchas veces buscan la confusión. Si se hace un seguimiento de este libro, recomiendo ejecutar cada código en un entorno de desarrollo, y variar los datos de cualquier forma: añadir elementos, modificar nombres de funciones o variables, mezclar conceptos de unos capítulos con otros, etc.

Desde mi experiencia podría decir que los **puntos más importantes** son:

*   **PHP Basics**. Operadores y su precedencia, operadores bit a bit, namespaces, estructuras de control y constuctores del lenguaje. Es necesario entender los comportamientos de los diferentes tipos y su conversión de unos a otros, así como diferenciar cuando un tipo está vacío, es false o es null. Puede haber preguntas con condicionales que parecen ser true, pero en realidad son false, y viceversa. Es la sección que más hay que dominar.
*   **Strings**. Conocimiento de un buen número de funciones de strings, entrecomillado y expresiones regulares.
*   **Arrays**. Conocimiento de un buen número de funciones de arrays, saber cómo se forman los keys de un array (por ejemplo, un float _1.4_ será igual a 1), iteración de arrays.
*   **OOP**. Es la segunda sección con más importancia. Es necesario entender bien cada capítulo, especialmente **Late Static Binding**.

En las funciones es importante saber si lo que una función devuelve es un string, 0, 1, integer, resource, array u objeto. No se pregunta sobre el orden de argumentos de una función, pero sí que existen algunas preguntas que requieren conocer el tipo de argumento del que se trata o incluso saber algunas constantes de los argumentos.

También suelen haber preguntas sobre funciones de **seguridad**, **archivos**, **PDO**, **SimpleXML**, **Streams** y **DateTime**.

Desde un punto de vista práctico de cara al futuro conviene centrarse en **REST**, **JSON** y **HTTP** para el desarrollo de **aplicaciones modernas**.

Existen opiniones diversas acerca de la validez de este examen. El examen no verifica que seas un buen desarrollador, pero sí que conoces bien el lenguaje PHP y sus posibilidades. A mi me ha servido para afianzar conocimientos, y darme cuenta de que conceptos que pensaba que entendía bien, realmente no los estaba entendiendo en su totalidad.