**Indice de contenido**

1.  Introducción
2.  Componentes de los Web Services
3.  Arquitectura de los Web Services
4.  Como funciona un Web Service
5.  Componentes de los servidores en una aplicación Web Service

### 1. Introducción

Un **Web Service**, o **Servicio Web**, es un **método de comunicación entre dos aparatos electrónicos en una red**. Es una **colección de protocolos abiertos y estándares** usados para **intercambiar datos entre aplicaciones o sistemas**. Las aplicaciones escritas en varios lenguajes de programación que funcionan en plataformas diferentes pueden utilizar web services para intercambiar información a través de una red. La interoperatividad, por ejemplo entre **Java** y **Python** o **Windows** y **Linux** se debe al uso de **estándares abiertos**.

Como sistema de mensajes se utiliza **XML estandarizado**. El protocolo más simple para el intercambio de información entre ordenadores es **XML-RPC**, que emplea XML para llevar a cabo **RPCs**. RPC, **Remote Procedure Call**, es un protocolo de red que permite a un programa a ejecutar código en una máquina remota. Los XML-RPC requests son una combinación entre **contenido XML** y **headers HTTP**. La simpleza de los XML-RPC hizo que el estándar evolucionase a **SOAP**, uno de los componentes básicos de los Web Services.

La base de comunicación entre web services es por tanto el **lenguaje XML** y el **protocolo HTTP**.

### 2. Componentes de los Web Services

![Web Services](/uploads/articulos/webservices.png)

Los **web services estandarizados** funcionan con los siguientes **componentes**:**​**

*   **SOAP** - _Simple Object Access Protocol_

**SOAP** es un protocolo escrito en XML para el intercambio de información entre aplicaciones. Es un formato para enviar mensajes, diseñado especialmente para servir de comunicación en Internet, pudiendo extender los **HTTP headers**. Es una forma de definir **qué información se envía y cómo mediante XML**. Básicamente es un **protocolo para acceder a un Web Service**.

*   **WSDL** - Web Services Description Language

**WSDL** es un lenguaje basado en XML para describir los servicios web y cómo acceder a ellos. Es el formato estándar para describir un web service, y fue diseñado por **Microsoft** e **IBM**. **WSDL** es una parte integral del estándar** UDDI**, y es el lenguaje que éste utiliza.

*   **UDDI** - Universal Description, Discovery and Integration

**UDDI** es un **estándar XML** para describir, publicar y encontrar servicios web. Es un directorio donde las compañías pueden registrar y buscar servicios web. Es un **directorio de interfaces de servicios web** descritos en **WSDL** que se comunican mediante SOAP.

### 3. Arquitectura de los Web Services

*   **Service Discovery**. Responsable de centralizar servicios web en un directorio común de registro y proveer una funcionalidad sencilla para publicar y buscar. **UDDI** se encarga del Service Discovery.
*   **Service Description**. Uno de los aspectos más característicos de los web services es que se autodescriben. Esto significa que una vez que se ha localizado un Web Service nos proporcionará información sobre que operaciones soporta y cómo activarlo. Esto se realiza a través del Web Services Description Language (**WSDL**).
*   **Service Invocation**. Invocar a un Web Service implica pasar mensajes entre el cliente y el servidor. **SOAP** (_Simple Object Access Protocol_) especifica cómo deberíamos formatear los mensajes request para el servidor, y cómo el servidor debería formatear sus mensajes de respuesta.
*   **Transport**. Todos estos mensajes han de ser transmitidos de alguna forma entre el servidor y el cliente. El protocolo elegido para ello es HTTP (HyperText Transfer Protocol). Se pueden utilizar otros protocolos pero HTTP es actualmente el más usado.

### 4. Como funciona un Web Service

1.  El **Service Provider** genera el **WSDL** describiendo el Web Service y registra el WSDL en el directorio **UDDI** o Service Registry.
2.  El **Service Requestor** o la aplicación del cliente requiere un Web Service y se pone en contacto con el UDDI para localizar el Web Service.
3.  El cliente, basándose en la descripción descrita por el **WSDL**, envía un request para un servicio particular al Web Service Listener, que se encarga de recibir y enviar los mensajes en formato SOAP.
4.  El Web Service analiza el **mensaje SOAP del request** e invoca una operación particular en la aplicación para procesar el request. El resultado se escribe de nuevo en SOAP en forma de respuesta y se envía al cliente.
5.  El cliente analiza el mensaje de respuesta SOAP y lo interpreta o genera un error si ha habido alguno.

### 5. Componentes de los servidores en una aplicación Web Service

![Componentes de los web services](/uploads/articulos/httpwebserver.png)

*   **Web Service**. Es el software o componente que realiza las operaciones. Si está escrito en Java, estas operaciones se realizarán en lenguaje Java. Los clientes invocarán estas operaciones enviando mensajes SOAP.
*   **SOAP Engine**. El Web Service no sabe interpretar **SOAP requests** y crear **SOAP responses**. Para hacer esto hace falta un SOAP engine, un software que se encarga del manejo de estos mensajes. _Apache Axis_ es un ejemplo.
*   **Application Server**. Para funcionar como un servidor que puede recibir requests desde diferentes clientes, el SOAP engine normalmente funciona dentro de un application server. Este es otro software que proporciona un espacio libre para aplicaciones que han de ser accedidas por múltiples clientes. El SOAP engine funciona como una aplicación dentro del application server. Ejemplos son _Apache Tomcat server_, _Java Servlet_ y _JSP container_.
*   **HTTP Server**. Algunos application servers incluyen funcionalidades HTTP, por lo que se pueden tener Web Services funcionando instalando simplemente un SOAP engine y un application server. Sin embargo cuando un application server carece de funcionalidad HTTP es necesario también un **HTTP server**, más comúnmente llamado **Web Server**. Es un software que sabe cómo manejar mensajes HTTP. Los dos más populares en la actualidad son _Apache HTTP Server_ y _Nginx_.