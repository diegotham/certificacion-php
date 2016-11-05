**Indice de contenido**

1.  ¿Qué es SSL?
2.  Encriptación en PHP con OpenSSL

### 1. ¿Qué es SSL?

**SSL** son las siglas de **Secure Sockets Layer**, un **protocolo de seguridad** utilizado en Internet. Los navegadores suelen indicar que el sitio que estás visitando está **asegurado con SSL/TSL** mediante un candado verde en la barra de direcciones. **TLS** significa **Transport Layer Security**, un protocolo más amplio de seguridad muy similar a SSL, del cual es sucesor.

SSL es una forma de **encriptar los datos** que se envían desde un navegador. Al principio se utilizaba sólo para **cuentas bancarias** y otra información importante, pero actualmente se usa en muchas más situaciones, incluso Google tiene en cuenta si un sitio utiliza HTTPs o no para el posicionamiento.

Sin **ningún tipo de encriptación**, cualquier información enviada desde el navegador al servidor puede ser interceptada por un atacante. Con SSL es muy difícil acceder a los datos encriptados.

Para una **conexión SSL** se utilizan dos llaves, una pública y una privada. Los navegadores utilizan la **llave pública** para encriptar los datos y el servidor utiliza la **llave privada** para desencriptarlos. De esta forma la conexión resulta fácil, cualquiera puede saber la llave pública del host para poder enviarle datos, pero sólo la llave privada puede desencriptarlos. Todo el mundo podrá **codificar mensajes con la misma llave**, pero nadie podrá descodificarlos sin la llave privada, que está en el lado del servidor.

El **proceso** es el siguiente:

*   El navegador hace un _**request**_ en un sitio web
*   El servidor envía la **llave pública** y un **certificado SSL**
*   El navegador analiza el certificado SSL
*   El navegador crea una **llave simétrica** y la envía al servidor
*   El servidor web desencripta la llave simétrica con su **llave privada**
*   El servidor envía al navegador la página con la llave simétrica
*   El navegador **desencripta los datos** y muestra la página 

Un **certificado SSL** está firmado por un autoridad de confianza y se usa para que el navegador sepa que un dominio se ha validado y que realmente son quien dicen ser. La validación la realiza una **Certificate Authority**, como _ssl.com_, después de que hayas comprado un certificado. Con esta forma garantizas que se pueda confiar en tu sitio web. Existen diferentes tipos de certificados para diferentes situaciones. Aunque tu sitio web no sea un e-commerce, si puedes es mejor tener un certificado SSL.

### 2. Encriptación en PHP con OpenSSL

[OpenSSL](http://www.openssl.org/) es una herramienta open source para **implementar los protocolos TLS y SSL** además de ser una completa librería de encriptación. PHP implementa OpenSSL mediante un [módulo](http://php.net/manual/es/book.openssl.php). Aunque no están disponibles todas las posibilidades que OpenSSL ofrece (podrían añadirse en el futuro), permite hacer uso de esta herramienta para sus usos más básicos.

La cantidad de datos que se pueden encriptar y el tamaño de los datos encriptados se determinan por el tamaño de la llave. El **tamaño de los datos encriptados en la llave** son el número de bytes redondeados hacia arriba. Una **llave de 1024-bit** serán **128 bytes** (1024 dividido para 8), y aunque sólo se vaya a encriptar 1 bit, el tamaño será el mismo. La cantidad máxima de datos que se pueden encriptar son 11 bytes menos que su cantidad máxima, en este caso serían **117 bytes los que pueden ser encriptados**.

No se pueden **encriptar grandes cantidades de datos de una vez**, por lo que se divide en porciones. En el ejemplo que sigue, **dividimos los datos en porciones antes de encriptarlos**, y después se combinan los datos encriptados y se envían. El receptor despieza los datos encriptados de nuevo en porciones y los desencripta. Los **pasos necesarios** para ello son los siguientes:

1.  **Generar llaves públicas y privadas**
    Primero hay que generar la llave pública y la privada:

   
```
    $privateKey = openssl_pkey_new(array(
        'private_key_bits' => 2048,      // Tamaño de la llave
        'private_key_type' => OPENSSL_KEYTYPE_RSA,
    ));
    // Guardar la llave privada en el archivo private.key. No compartir este archivo con nadie
    openssl_pkey_export_to_file($privateKey, 'private.key');

    // Generar la llave pública para la llave privada
    $a_key = openssl_pkey_get_details($privateKey);

    // Guardar la llave pública en un archivo public.key.
    // Envía este archivo a cualquiera que quiera enviarte datos encriptados
    file_put_contents('public.key', $a_key['key']);

    // Libera la llave privada
    openssl_free_key($privateKey);
   
```

2.  **Encriptación de datos**

   
```
    // Datos a enviar
    $texto = 'Lorem ipsum dolor sit amet, consectetur adipiscing elit. Aenean eleifend vestibulum nunc sit amet mattis. Nulla at volutpat nulla. Pellentesque sodales vel ligula quis consequat. Suspendisse dapibus dolor nec viverra venenatis. Pellentesque blandit vehicula eleifend.';
    echo 'Texto plano: ' . $texto;
    // Comprimir los datos a enviar
    $texto = gzcompress($texto);
    // Obtener la llave pública
    $publicKey = openssl_pkey_get_public('file:///path/to/public.key');
    $a_key = openssl_pkey_get_details($publicKey);
    // Encriptar los datos en porciones pequeñas, combinarlos y enviarlo
    $chunkSize = ceil($a_key['bits'] / 8) - 11;
    $output = '';
    while ($texto)
    {
        $chunk = substr($texto, 0, $chunkSize);
        $texto = substr($texto, $chunkSize);
        $encrypted = '';
        if (!openssl_public_encrypt($chunk, $encrypted, $publicKey))
        {
            die('Ha habido un error al encriptar');
        }
        $output .= $encrypted;
    }
    openssl_free_key($publicKey);
    // Estos son los datos encriptados finales a enviar:
    $encrypted = $output;
   
```

3.  **Desencriptación de datos**
    Una vez que el servidor recibe los datos encriptados con su llave pública, puede desencriptarlos con la llave privada:

   
```
    // Obtener la llave privada
    if (!$privateKey = openssl_pkey_get_private('file:///path/to/private.key'))
    {
        die('No se ha podido obtener la llave privada');
    }
    $a_key = openssl_pkey_get_details($privateKey);
    // Desencriptar los datos de las porciones
    $chunkSize = ceil($a_key['bits'] / 8);
    $output = '';
    while ($encrypted)
    {
        $chunk = substr($encrypted, 0, $chunkSize);
        $encrypted = substr($encrypted, $chunkSize);
        $decrypted = '';
        if (!openssl_private_decrypt($chunk, $decrypted, $privateKey))
        {
            die('Fallo al desencriptar datos');
        }
        $output .= $decrypted;
    }
    openssl_free_key($privateKey);
    // Descomprimir los datos
    $output = gzuncompress($output);
    echo '<br /><br /> Datos sin encriptar: ' . $output;
   
```