**SOAP** es un protocolo que permite desarrollar software en cualquier lenguaje y éste ser interpretado por cualquier otro sistema o aplicación a través de una red mediante XML. SOAP establece reglas para enviar y recibir **Remote Procedure Calls (RPC)**, como las de los mensajes **HTTP request** y **HTTP response**, por lo que no está ligado a ningún sistema operativo o lenguaje de programación.

Para utilizar **SOAP** a partir de **PHP 5** se pueden emplear funciones integradas con [PHP SOAP](http://php.net/manual/en/book.soap.php) y la clase [SoapClient](http://php.net/soapclient), aunque no generarán automáticamente el archivo WSDL.

**Indice de contenido**

1.  Estructura de un mensaje SOAP
2.  Estructura del archivo WSDL

### 1. Estuctura de un mensaje SOAP

**SOAP está basado en XML** por lo que se considera legible para humanos, pero ha de ir adherido a un esquema específico. Un mensaje SOAP es como sigue:

```
<?xml vesion="1.0"?>
<soap:Envelope
xmlns:soap="http://www.w3.org/2001/12/soap-envelope"
soap:encodingStyle="http://www.w3.org/2001/12/soap-encoding">
<soap:Header>
...
</soap:Header>
<soap:Body>
...
<soap:Fault>
...
</soap:Fault>
</soap:Body>
</soap:Envelope>
```

Parece un **archivo XML normal**, pero lo que hace que sea un **mensaje SOAP** es el **elemento root _Envelope_** con el **namespace soap** _http://www.w3.org/2001/12/soap-envelope_. El atributo **soap:encodingStyle** determina los data types usados en el archivo, pero SOAP por si mismo no tiene una codificación determinada.

_**soap:Envelope**_ es obligatorio, pero el siguiente elemento _**soap:Header**_ es opcional, y normalmente contiene información relevante sobre el manejo de **autentificaciones** y **sesiones**. El **protocolo SOAP** no ofrece ninguna autentificación por defecto, pero permite a los desarrolladores incluirlo en la etiqueta header.

Después viene el elemento _**soap:Body**_ que contiene el **mensaje RPC**, incluyendo nombres de métodos y, en el caso de una respuesta, los valores devueltos del método. El elemento _**soap:Fault**_ es opcional. Si está presente contiene cualquier mensaje de error o información de estado para el mensaje SOAP y debe ser un hijo de _soap:Body_.

Un **mensaje request en SOAP** es como sigue:

```
<?xml vesion="1.0"?>
<soap:Envelope
xmlns:soap="http://www.w3.org/2001/12/soap-envelope"
soap:encodingStyle="http://www.w3.org/2001/12/soap-encoding">
<soap:Body xmlns:m="http://www.ejemplo.com/stock">
<m:GetStockPrice>
<m:StockName>Google</m:StockName>
</m:GetStockPrice>
</soap:Body>
</soap:Envelope>
```

El ejemplo anterior es un request para obtener el precio de mercado de una compañía en concreto. Dentro del **soap:Body** se encuentra en elemento **GetStockPrice** que es específico para la aplicación. No es un elemento SOAP, y lleva el nombre que llevará la función en el servidor al que será enviado el request. **StockName** es también específico, y es un argumento de la función.

El **mensaje de respuesta de esta aplicación en SOAP** es como sigue:

```
<?xml verson="1.0"?>
<soap:Envelope
xmlns:soap="http://www.w3.org/2001/12/soap-envelope"
soap:encodingStyle="http://www.w3.org/2001/12/soap-encoding">
<soap:Body xmlns:m="http://www.ejemplo.com/stock">
<m:GetStockPriceresponse>
<m:Price>201.34</m:Price>
</m:GetStockPriceresponse>
</soap:Body>
</soap:Envelope>
```

Dentro del elemento _**soap:Body**_ hay un elemento **GetStockPriceResponse** con un hijo que es **Precio** que contiene el valor devuelto, ambos específicos de la aplicación.

### 2. Estructura del archivo WSDL

Los archivos **Web Services Description Language** (**WSDL**) son **documentos XML** que proporcionan **metadatos** para un **SOAP service**. Contienen información acerca de las funciones o métodos que la aplicación ofrece y que argumentos utilizar. Con los archivos WSDL se proporciona información para que los clientes envíen **requests válidos** y exactamente como se quiere que sean. Es como un **contrato para comunicarse con la aplicación**. 

Al igual que los **mensajes SOAP**, los **archivos WSDL** tienen un esquema específico al que adherirse, y elementos específicos que deben estar presentes para que sea válido:

```
<description>
<types>
...
</types>
<interface>
...
</interface>
<binding>
...
</binding>
<service>
...
</service>
</description>
```

El **elemento root** del **archivo WSDL** es _**description**_. Tiene sentido ya que lo que hace el archivo es definir el **Web Service**. El elemento _**types**_ describe el tipo de datos utilizados, los cuales en el caso de **WSDL** son **XML**. El elemento _**interface**_ define las operaciones que se pueden llevar a cabo con el **Web Service** y los mensajes _**request**_ y _**response**_ que se utilizan. Dentro del elemento **binding** se encuentra el protocolo y la especificación del formato de datos para un _**interface**_ particular. Finalmente se encuentra el elemento _**service**_ que contiene la URI del servicio.

En este enlace se puede ver la documentación oficial de la [versión 2.0 de WSDL](http://www.w3.org/TR/wsdl20-primer/).