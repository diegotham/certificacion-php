Los **ataques Email Injection** se producen por la misma causa por la que se producen los [ataques SQL Injection](http://diego.com.es/ataques-sql-injection-en-php): embeber código de otro **lenguaje de programación**. Cuando la aplicación tiene algún formulario cuyo resultado es **enviar un email**, el atacante puede manipular el **formato MIME** e introducir código adicional para modificar el comportamiento, pudiendo cambiar emisores, receptores, contenidos, etc. El formato MIME utiliza un salto de línea para delimitar la información en un mensaje, y el [mensaje raw](http://help.altn.com/mdaemon/es/raw_the_raw_message_specification.htm) determina su destinación, por lo que añadiendo saltos de línea a los datos del formulario puede hacer que se envíen miles de emails, que es lo que haría un **spammer**. 

El **ataque Email Injection en PHP** es un tipo de ataque que va directo a la **función de email de PHP**. Permite al atacante inyectar contenido extra en cualquiera de los campos como BCC, CC, Asunto, etc, y **enviar miles de emails desde el servidor de la víctima**. También se le llama **mail form spamming**. Esta vulnerabilidad no está limitada a PHP, afecta a **cualquier lenguaje que acepte formularios de usuarios para enviar emails**. 

La [función mail() de PHP](http://php.net/manual/es/function.mail.php) es la siguiente:
```
bool mail (string $to, string $subject, string $message [, string $additional_headers [, string $additional_parameters ]] )
```

Tiene 3 parámetros obligatorios y dos opcionales.

Vamos a ver un formulario de envío de mail con **código vulnerable**:

```
<form method="POST" action="<?=$_SERVER['PHP_SELF'];?>">
From: <input type="text" name="sender"> <br>
Subject : <input type="text" name="subject"> <br>
Message : <textarea name="message" rows="10" cols="60" lines="20"></textarea> <br>
Enviar : <input type="submit" name="send" value="Send">
</form>
<?php
   // Email de la persona que recibirá el email
   $to = "ejemplo@ejemplo.com";
   if(isset($_POST["send"])){
   $subject = $_POST['subject'];
   $message = $_POST['message'];
   $from = $_POST['sender'];
   // Enviar email :
   if (mail($to, $_POST['subject'], $_POST['message'], "From: $from\n")){
     echo "Tu email se ha enviado correctamente";
} else {
    echo "Ha ocurrido un error y no se ha podido enviar el email.";
   }
}
```

_$from_ va como parámetro opcional en la función mail, pero cuando se envía un correo siempre ha de haber un From, puede establecerse también desde el php.ini. Si no está establecido en _mail()_ o en php.ini dará error.

El **principal problema de cualquier ataque Injection** es confiar en los contenidos que el usuario va a introducir en el formulario, que hace que no se **validen** ni **filtren** correctamente. En el formulario anterior la función mail recibe los argumentos sin ningún tipo de validación previa: subject, message y from. Suponiendo que se rellena el formulario sin malas intenciones, la función podría recibir los siguientes argumentos:

```
mail("ejemplo@ejemplo.com", "Esto es un saludo", "Hola que tal, ¿Como estas?", "From: amigo@otraweb.com");
```

Desde el punto de vista del atacante, existen un gran **número de campos adicionales que se pueden inyectar en el header del email**. Se puede obtener más información sobre ello en el [RFC 822](http://www.ietf.org/rfc/rfc822.txt). Por ejemplo puede añadir más CC que permite al atacante añadir más receptores, para ello tiene que añadir una nueva línea que separa cada campo (en los siguientes ejemplos el salto de línea es en formato hexadecimal 0x0A).

Inyectar más de un email como destinatarios:
```
From:amigo@otraweb.com**%0A**Cc:otro@dominio.com,**%0A**Bcc:otromas@otrodominio.com
```

Inyectar un argumento **to**: (el mensaje se enviará tanto al receptor original como al email del atacante)
```
From:amigo@otraweb.com**%0A**To:atacante@example.com
```

Inyectar un **subject**: (se añadirá al subject original)
```
From:amigo@otraweb.com**%0A**Subject:esto%20es%20un%20ejemplo
```

**Cambiar** el cuerpo del mensaje:
```
From:amigo@otraweb.com**%0A%0A**esto%20es%20otro%20ejemplo
```

### Como evitar un ataque Email Injection

*   Utilizar algún mecanismo para **validar los datos**. Por ejemplo para validar que es un email correcto, se puede emplear la función filter_var(): 

    `filter_var($email, FILTER_VALIDATE_EMAIL)`

*   Utilizar **expresiones regulares para filtrar los datos**. Por ejemplo se puede buscar **\r** o **\n** en el string de entrada.
*   Utilizar componentes externos o **librerías para el envío de emails** que incluyen ya protección por defecto, como [SwiftMailer](http://swiftmailer.org/). Esta sería la opción más cómoda y segura. Además suelen tener multitud de opciones preconfiguradas para exprimir al máximo las posibilidades del envío de emails.
*   Utilizar algún **firewall** externo a nivel de servidor, como [ModSecurity](https://www.modsecurity.org/), que trabaja directamente sobre POST y GET.
*   No permitir open relays en el servidor.
*   Abrir el puerto **SMTP** sólo si es necesario.
*   También se puede emplear alguna [estrategia Tarpit](https://en.wikipedia.org/wiki/Tarpit_(networking)), que haga que **hacer spam desde el servidor** resulte incómodo, ya que **retrasa las comunicaciones por email**.