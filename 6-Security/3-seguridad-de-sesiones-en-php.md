**Indice de contenido**

1.  Introducción
2.  Opciones de configuración
3.  Prevenir ataque session fixation
4.  Prevenir ataque session hijacking
5.  Diferencia entre session hijacking y session fixation

### 1. Introducción

Aunque con las **sesiones** y las **cookies** no se pueda quebrantar la **seguridad de la aplicación** de forma directa, mediante el robo de sesiones se pueden comprometer las **cuentas de los usuarios**, y si éstos tienen permisos especiales las consecuencias pueden ser peor de lo esperado.

La mayoría de las veces PHP guardará una cookie en el ordenador del cliente llamada **PHPSESSID** (puede cambiarse al nombre que se desee) cuando se usen sesiones. Esta **cookie** guardará un valor, un **identificador de sesión**, que está asociado con algún tipo de datos en el servidor. Si el usuario tiene una **session ID válida**, los datos asociados con la sesión se incluirán en el **superglobal** _array_ **$_SESSION**. Las sesiones pueden también transferirse a través de URL. En ese caso sería algo como _**?PHPSESSID=id_aqui**_.

La **sessión ID** funciona de forma parecida a la **llave de un cajón en un banco**, con la que puedes acceder a lo que sea que haya en ese cajón. La llave de tu cajón puede ser robada al igual que puede serlo la sessión ID de usuarios (robada o interceptada).

Cuando el atacante roba una session ID y trata de usarla para entrar en la aplicación como si fuera el verdadero usuario se le llama **session hijacking**. Cuando el atacante establece la session ID para la sesión de un usuario se denomina **session fixation**. Estos ataques no se pueden evitar en su totalidad pero se pueden tomar medidas para prevenirlos.

El problema de la seguridad en sesiones aumenta cuando se utiliza un **hosting compartido**, es decir, usar el **mismo servidor** que otros usuarios. En un server **Linux**, por defecto las sesiones de guardan en el directorio _/tmp_, que guarda archivos temporales y ha de ser legible y escribible para todo el mundo. Si tus sesiones se guardan ahí, otros usuarios podrían encontrar tus datos.

### 2. Opciones de configuración

La **administración de sesiones HTTP** es una parte fundamental de la seguridad web. Algunas de las configuraciones más importantes que pueden manipularse en PHP son las siguientes:

*   _session.cookie_lifetime = 0_. El cero tiene un significado especial, le dice a los navegadores que no guarden una cookie permanentemente. Así pues, si se cierra el navegador, la session ID se borra inmediatamente. Se desaconseja variar este valor, y si se quiere una **aplicación con autologin**, existen alternativas mucho más seguras.
*   _session.use_cookies = On_ y _session.use_only_coockies = On_. Aunque las HTTP cookies tienen algunos problemas, es la forma más recomendable de manejar sesiones.
*   _session.use_strict_mode = On_. Esto previene al **módulo de sesión** iniciar una **sessión ID** sin inicializar. El módulo de sesión sólo acepta **session ID** válidas generadas por él mismo, rechazando cualquier sesson ID proporcionada por usuarios. Sessión ID injection puede hacerse con cookie injection a través de JavaScript. Se recomienda mantelerla en On.
*   _session.cookie_httponly = On_. Rechaza el **acceso a la cookie de sesión vía JavaScript**. Esto previene el **robo de cookies** a través de **JavaScript injection**. Se puede usar **session ID** como _**CSRF protection key**_, pero no es recomendable. 
*   _session.cookie_secure = On_. Permite el acceso a la cookie session ID sólo cuando el protocolo es HTTPS. Si tu aplicación es sólo HTTPS, es recomendable tener activada esta opción.
*   _session.gc_maxlifetime = [elegir el menor posible]_. Número de segundos tras los cuales los datos serán considerados basura y limpiados con posterioridad. Garbage Collection puede ocurrir al inicio de sesión mediante probabilidad. Esta opción no garantiza la eliminación de sesiones antiguas. Aunque el desarrollador no ha de fiarse del todo de esta configuración, se recomienda ajustarla al menor valor posible. Es mejor ajustar _**session.gc_probability**_ y _**session.gc_divisor**_ para que sesiones obsoletas se eliminen con la frecuencia apropiada. 
*   _session.use_trans_sid = Off_. El tener esta opción de manejo de session ID transparentes desactivada mejora la seguridad de sesiones eliminando la posibilidad de session ID injection y session ID leak. De todas formas, pueden emplearse session ID transparentes si es necesario.
*   _session.referer_check = [tu url original]_. Si está activala la opción anterior, session.use_trans_sid, el uso de esta opción es recomendable, ya que reduce el riesgo de ID injection.
*   _session.cache_limiter = nocache_. Asegúrate de que los **contenidos HTTP** no son cacheados para **sesiones autentificadas**. "_private_" suele usarse para cuando no hay ningún dato de seguridad en el contenido HTTP, y "_public_" cuando no contiene ningún dato privado en general.
*   _session.hash_function = "sha256"_. Una **función hash** más fuerte generará una **session ID** más fuerte. Cuanto más complejo sea el cifrado mejor: sha384, sha512...

El **módulo de sesión no puede garantizar que la información guardada en una sesión sólo sea vista por el usuario que creó la sesión**. Es necesario tomar más medidas para proteger totalmente la confidencialidad de la sesión.

Existen diferentes formas de que se **filtre una session ID exitente a terceros**. Si se produce una _**leaked session ID**_, posibilita a la tercera persona acceder a todos los recursos asociados con esa ID. Formas de que esto ocurra:

*   **URLs llevando una session ID**. Si enlazas a un sitio externo desde una URL que contiene una session ID, ésta aparecerá en el _**referrer log**_ del sitio externo.
*   **Un atacante más activo puede estar atento al tráfico de red**. Si éste no está encriptado, las **session IDs** pueden viajar en texto plano por la red. La solución es implementar **SSL en el servidor** y hacerlo obligatorio a los usuarios. Se debería usar **HSTS**.

Cuando la opción _**session.use_strict_mode**_ está On, una session ID no iniciada se rechaza y se crea una nueva session ID. Esto protege ataques que fuerzan a los usuarios a usar una session ID. Por ejemplo, un atacante puede pasar URLs que contienen una session ID: http://example.com/page.php?PHPSESSID=23411\. Si session.use_trans_sid está activado, la víctima iniciará la sesión con la session ID proporcionada por el atacante. _**session.use_strict_mode**_ reduce el riesgo, aunque no asegura. 

Para la autentificación de usuarios es muy recomendable añadir _**session_regenerate_id()**_, y debe ser llamada antes de establecer información de autentificación a **$_SESSION**. Esta función se asegura de que nuevas sesiones contienen información de autentificación almacenada sólo en una nueva sesión.

La **session ID debería ser regenerada** por lo menos cada vez que el usuario es identificado. No se ha de confiar en la expiración de la session ID. Los atacantes pueden acceder a las session ID de las víctimas periódicamente para evitar la expiración. Es recomendable implementar algún sistema propio para manejar sesiones antiguas.

_**session_regenerate_id()**_ por defecto no elimina una sesión antigua, y puede estar disponible para su uso. Para ello ha de ser destruída, añadiendo el parámetro _**delete_old_session**_ TRUE a la función, aunque esto puede tener consecuencias inesperadas. Una sesión puede ser destruída cuando hay conexiones simultáneas a la aplicación o la red es inestable. En lugar de destruir la sesión antigua inmediatamente, se puede establecer un tiempo corto de expiración en $_SESSION. Si el usuario intenta acceder a la sesión antigua, deniega el acceso a ella.

No se deben usar **session IDs** muy duraderas para **autologin** porque incrementa el riesgo de robo de sesión. Una _**autologin key**_ debe protegerse lo más posible, para ello se pueden utilizar **atributos _/httponly/_**. 

### 3. Prevenir ataque session fixation

Cuando el atacante establece la session ID para la sesión de un usuario se denomina **session fixation**. Una vez que el atacante proporciona una URL al usuario con la session ID establecida y éste accede, el atacante, al conocer la session ID con la que se ha accedido, puede hacerse pasar por el usuario. Para prevenir el **session fixation** se deben tomar las siguientes medidas:

*   Establecer _**session.use_trans_sid = 0**_ en el _php.ini_. esto le dice a PHP que no incluya el session ID en la URL, y no leer la URL en busca de identificadores.
*   Establecer _**session.use_only_cookies = 1**_ en el php.ini. Esto le dice a PHP que nunca use URLs con session IDs.
*   Regenerar la session ID siempre que el estado de la sesión cambie. Ejemplos: autentificación de usuario, guardar información importante en la sesión, cambiar cualquier cosa de la sesión...

### 4. Prevenir ataque session hijacking

_**Session hijacking**_ es cuando el atacante roba una session ID y trata de usarla para entrar en la aplicación como si fuera el verdadero usuario. Como el atacante tiene el session ID, el servidor no puede distinguir cual es el verdadero usuario. Para prevenir el **session hijacking** se deben tomar las siguientes medidas:

*   **Usar un identificador hash muy potente**. Directiva _session.hash_function_ en php.ini. Lo ideal es _session.hash_function = sha256_ o _session.hash_function = sha512_.
*   **Enviar un hash potente**. Directiva _session.hash_bits_per_character_ en php.ini. Configura esta opción a _session.hash_bits_per_character = 5_. Es una traba más para cuando el atacante trate de adivinar el session ID. El ID será más corto pero usará más caracteres.
*   **Establece una entropía adicional** con _session.entropy_file_ y _session.entropy_length_ en php.ini. Por ejemplo _session.entropy_file = /dev/urandom_ y _session.entropy_length = 256_, el número de bytes que serán leídos del archivo entropy.
*   **Cambia el nombre por defecto de la sesión PHPSESSID**. Este valor se establece con la función [session_name()](http://us2.php.net/manual/en/function.session-name.php), con el valor de identificación propio como parámetro, antes de llamar a _**session_start()**_. 
*   **Rotar el nombre de la sesión**, pero ten en cuenta que todas las sesiones serán invalidadas si cambias esto, por ejemplo si se configura de forma que dependa en el tiempo.
*   **Rota el session ID a menudo**. No es recomndable hacerlo en cada request (a no ser que se necesite un nivel de seguridad extremo), pero si en intervalos aleatorios. Si el atacante hace _**session hijacking**_ no se desea que pueda utilizar una sesión durante demasiado tiempo.
*   **Incluir el user agent desde $_SERVER['HTTP_USER_AGENT'] en la sesión**. Cuando la sesión comience, guardala en _**$_SESSION['user-agent']**_. Entonces en cada request posterior comprueba que coincide. Este valor puede ser falso, por lo que no es 100% seguro, pero es mejor que nada.
*   **Incluir la IP de usuario de $_SERVER['REMOTE_ADDR'] en la sesión**. Cuando la sesión comience, guárdala en _**$_SESSION['remote_ip']**_. Esto puede ser problemático para ciertos **ISPs** que usan direcciones IP múltiples para sus usuarios. Pero si se usa puede ser mucho más seguro. La única forma de que un atacante pueda **falsear la IP** es comprometiendo la red en algún punto entre el usuario real y tu servidor. Y si un atacante consigue comprometer una red, puede hacer cosas mucho peores que session hijacking. 
*   **Incluir un token en la sesión y en el navegador que incrementas y comparas frecuentemente**. Para cada _**request**_, haz $_SESSION['counter']++ en el lado del servidor. Crea algo en JS en el lado del navegador haciendo lo mismo (usando almacenamiento local). Entonces cuando se envía un request, coge el **nonce de un token** y verifica que el nonce es el mismo que en el servidor. Haciendo esto, se puede detectar una **session hijacked** ya que el atacante no tendrá el número exacto, y si lo tiene habrá dos sistemas transmitiendo el mismo número. Esto no funcionará en todas las aplicaciones, pero es otra manera de protegerse. 

### 5. Diferencia entre session hijacking y session fixation

La diferencia entre session fixation y session hijacking reside simplemente en cómo la session ID es comprometida. En **session fixation**, el identificador se establece a un valor que el atacante conoce de antemano. En **session hijacking** se adivina o se roba del usuario. Una vez que el session ID es robado, los efectos de ambos dos son los mismos.