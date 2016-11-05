La **extensión DateTime de PHP** es un conjunto de clases que permiten lidiar con todas las tareas relacionadas con las fechas y las horas. Está disponible desde **PHP 5.2** e introdujo una serie de clases que se pueden esquematizar de la siguiente forma:

*   Una **fecha** o una **hora** se representa con el objeto **DateTime**.
*   Un **timezone** se representa por el objeto **DateTimeZone**.
*   Un **intervalo** se representa con el objeto **DateInterval**. No se basa en una fecha u hora específicos, simplemente especifica un intervalo, por ejemplo de tres días.
*   Un **periodo** entre dos fechas se representa por el objeto **DatePeriod**.

**Indice de contenido**

1.  Formatos de fecha y hora
2.  Mostrar fechas en español
3.  DateTime
4.  DateTimeZone
5.  DatePeriod

### 1. Formatos de fecha y hora

La tabla entera con los **formatos de fecha y hora disponibles** se pueden ver en la tabla de la función [date()](http://php.net/manual/es/function.date.php). Aquí van algunos de ellos:

| | | |
| -------- | -------- |
| **Formato de carácter** | **Descripción** | **Valores devueltos** |
| **Dia** | | |
| d | Día del mes, dos dígitos con ceros iniciales | 01 a 31 |
| D | Representación textual de un día en tres letras | Mon hasta Sun |
| j | Día del mes sin ceros iniciales | 1 a 31 |
| l (l minúscula) | Representación textual del día de la semana | Sunday hasta Saturday |
| z | Día del año comenzando de 0 | 0 hasta 365 |
| **Semana** | | |
| W | Número de semana del año (las semanas comenzando en lunes) | 0 a ~52 |
| **Mes** | | |
| m | Mes del año, dos dígitos con ceros iniciales | 01 a 12 |
| M | Representación textual de un mes en tres letras | Jan hasta Dec |
| n | Mes del año sin ceros iniciales | 1 a 12 |
| F | Representación textual completa de un mes | January hasta December |
| t | Número de días del mes dado | 28 a 31 |
| **Año** | | |
| Y | Año con cuatro dígitos | 1999, 2004, 2014... |
| y | Año con dos dígitos | 99, 04, 14... |
| **Hora** | | |
| g | Formato de 12 horas sin ceros iniciales | 1 a 12 |
| G  | Formato de 24 horas sin ceros iniciales | 0 a 23 |
| h | Formato de 12 horas con ceros iniciales | 01 a 12 |
| H | Formato de 24 horas con ceros iniciales | 00 a 23 |
| i | Minutos con ceros iniciales | 00 a 59 |
| s | Segundos, con ceros iniciales | 00 a 59 |
| u | Microsegundos | 645127 |

### 2. Mostrar fechas en español

La función _setlocale()_ permite indicar la configuración regional para determinadas tareas: 
```
string setlocale (int $category, string $locale [, string $...]
```

_$category_ es una constante que especifica las funciones que se verán afectadas:

*   **LC_ALL** todas las siguientes
*   **LC_COLLATE** para comparación de strings
*   **LC_CTYPE** para clasificación y conversión de caracteres
*   **LC_MONETARY** para aspectos relacionados con la moneda
*   **LC_NUMERIC** separador decimal y signos
*   **LC_TIME** formato de fecha y hora
*   **LC_MESSAGES** para las respuestas del sistema

_$locale_ establece el localismo con el que se quiere trabajar es_ES, es_MX, es_PE, es_RA...

Hay que tener en cuenta que dependiendo del servidor o el sistema de bases de datos pueden surgir algunos resultados inesperados, por lo que siempre hay que ir realizando pruebas para ver los resultados. 

La función _strftime()_ se utiliza para obtener un string expresado en el lenguaje de la configuración local:
```
string strftime (string $format [, int $timestamp = time() ])
```

Los nombres del mes, del día de la semana y demás strings traducibles se mostrarán según lo indicado en el servidor o en _setlocale()_.

La tabla de formatos disponibles para esta función es parecido a la **tabla de formatos para fecha y hora**:

| | | |
| -------- | -------- |
| **Formato de carácter** | **Descripción** | **Valores devueltos** |
| **Dia** | | |
| %d | Día del mes, dos dígitos con ceros iniciales | 01 a 31 |
| %a | Representación textual de un día en tres letras | dom hasta sáb |
| %e | Día del mes sin ceros iniciales | 1 a 31 |
| %A | Representación textual del día de la semana | domingo hasta sábado |
| %j | Día del año, tres dígitos con ceros iniciales | 001 hasta 365 |
| **Semana** | | |
| %W | Número de semana del año (las semanas comenzando en lunes) | 0 a ~52 |
| **Mes** | | |
| %m | Mes del año, dos dígitos con ceros iniciales | 01 a 12 |
| %b | Representación textual de un mes en tres letras | ene hasta dic |
| %B | Representación textual completa de un mes | enero hasta diciembre |
| **Año** | | |
| %Y | Año con cuatro dígitos | 1999, 2004, 2014... |
| %y | Año con dos dígitos | 99, 04, 14... |
| **Hora** | | |
| %l (l minúscula) | Formato de 12 horas sin ceros iniciales | 1 a 12 |
| %k | Formato de 24 horas sin ceros iniciales | 0 a 23 |
| %I | Formato de 12 horas con ceros iniciales | 01 a 12 |
| %H | Formato de 24 horas con ceros iniciales | 00 a 23 |
| %M | Minutos con ceros iniciales | 00 a 59 |
| %S | Segundos, con ceros iniciales | 00 a 59 |
| u | Microsegundos | 645127 |

Por último, la función _date_default_timezone_set()_ establece la zona horaria predeterminada usada por **todas** las funciones de fecha y hora en un **script**:

```
bool date_default_timezone_set (string $timezone_identifier ) 
```

Y para comprobar cual es el **formato la configuración actual del servidor**: _date_default_timezone_get()_.

Con la configuración adecuada pueden obtenerse los formatos de fecha y hora que deseemos:

```
setlocale(LC_ALL, 'es_ES');
$fecha = strftime("Hoy es %A día %d de %B");
echo $fecha; // Devuelve: Hoy es viernes día 06 de noviembre
// Ahora establecemos una fecha cualquiera:
$date = new Datetime("2014-6-7");
$fecha = strftime("La fecha es del año %y y su mes es %B", $date->getTimestamp());
var_dump($fecha); // Devuelve: La fecha es del año 14 y su mes es junio
```

### 3. DateTime

Cuando se quiere **mostrar una fecha** se puede utilizar sencillamente _date()_, pasando el **formato de fecha** que se quiera mostrar. Pero la función es algo engorrosa de manipular, especialmente para formatear fechas y horas según un **timezone**.

La clase **DateTime** no sólo devuelve fechas formateadas, sino que permite personalizarlas fácilmente:

```
$date = new DateTime();
```

El **constructor de DateTime** acepta un parámetro _string_ que por defecto es "_now_", la **fecha y hora actuales**. Para crear un objeto con la fecha específica, se han de incluir la fecha y la hora específicas:

```
$date = new DateTime('2015, May 12');
var_dump($date);
/* Se crea un objeto con tres propiedades:
* object(DateTime)[1]
  public 'date' => string '2015-05-21 20:15:00.000000' (length=26)
  public 'timezone_type' => int 3
  public 'timezone' => string 'Europe/Madrid' (length=13)
*/
// Fecha en formato con guiones
$date = new DateTime('2015-06-19'); // Fecha marcada, hora 00:00:00
// Número de días
$date = new DateTime('+10 days'); // 10 días posteriores misma hora
// Fecha en palabra
$date = new DateTime('Yesterday'); // Fecha de ayer, hora 00:00:00
```

Si **PHP no entiende el formato recibido**, lanzará una [excepción](http://diego.com.es/excepciones-en-php). Todos los formatos se pueden ver [aquí](http://www.php.net/manual/en/datetime.formats.php).

Si no hay un formato que se adapte a unas necesidades específicas, se puede utilizar _DateTime::createFromFormat()_ para especificar un formato propio:

```
public static DateTime datetime::createFromFormat (string $format, string $time [, DateTimeZone $timezone ])
```

Devuelve un objeto **DateTime**, _$format_ es el formato en el que debe estar el string que se proporciona con _$time_.

```
$date = DateTime::createFromFormat('d-M-y', '02-Jan-15');
var_dump($date);
/*
* object(DateTime)[1]
  public 'date' => string '2015-01-02 13:07:36.000000' (length=26)
  public 'timezone_type' => int 3
  public 'timezone' => string 'Europe/Madrid' (length=13)
*/
```

Una vez que tenemos un objeto DateTime, podemos trabajar con la fecha:

```
$date = new DateTime();
$date->getTimestamp();// Devuelve el timestamp, por ejemplo: 1446811771
$date->setDate(2015, 10, 10); // Establecer fecha específica
$date->modify('tomorrow'); // Establecer fecha con palabras
$date->setTimestamp(1446811771); // Establece un timestamp
```

Cuando se establecen fechas u horas que se pasan de rango, PHP las modificará. Por ejemplo, si establecemos el número de día 35 con _$date->setDate(2015, 12, 35)_, generará 2016-01-04, y lo mismo ocurrirá con las horas.

#### **Trabajar con múltiples fechas**

Si por ejemplo queremos comparar dos fechas concretas:

```
$maria = new DateTime('1985-4-5');
$carlos = new DateTime('1982-08-15');
if ($maria > $carlos){
    echo "María es más joven que Carlos";
} else {
    echo "María es más mayor que Calos";
}
```

También podemos hacer una comparación con el método _diff()_, que devolverá un objeto **DateInterval**:

```
$diferencia = $maria->diff($carlos);
echo "María y Carlos se llevan {$diferencia->y} años y $diferencia->m meses de diferencia";
// El objeto completo:
var_dump($diferencia);
/*
* object(DateInterval)[4]
  public 'y' => int 2
  public 'm' => int 7
  public 'd' => int 21
  public 'h' => int 0
  public 'i' => int 0
  public 's' => int 0
  public 'weekday' => int 0
  public 'weekday_behavior' => int 0
  public 'first_last_day_of' => int 0
  public 'invert' => int 1
  public 'days' => int 964
  public 'special_type' => int 0
  public 'special_amount' => int 0
  public 'have_weekday_relative' => int 0
  public 'have_special_relative' => int 0
*/
```

También puede especificarse el formato partiendo del objeto **DateInterval**:

```
echo $diferencia->format("María y Carlos se llevan %Y años y %m meses de diferencia");
```

Con **DateInterval** también es posible añadir el intervalo a otro objeto **DateTime**:

```
// Obtenemos la diferencia de edad:
$diferencia = $maria->diff($carlos);
// Si a María le sumamos esa diferencia de edad:
$maria->add($diferencia); // Ahora $maria tiene la misma edad que $carlos
```

Puede comprobarse que esta modificación no ha creado un nuevo objeto DateTime sino que ha modificado el anterior.

Los objetos **DateInterval** también pueden crearse sin tener que utilizar _diff()_ en un DateTime:

```
$intervalo = new DateInterval('P2M2D');
var_dump($intervalo);
/*
* object(DateInterval)[5]
  public 'y' => int 0
  public 'm' => int 2
  public 'd' => int 2
  public 'h' => int 0
  public 'i' => int 0
  public 's' => int 0
  public 'weekday' => int 0
  public 'weekday_behavior' => int 0
  public 'first_last_day_of' => int 0
  public 'invert' => int 0
  public 'days' => boolean false
  public 'special_type' => int 0
  public 'special_amount' => int 0
  public 'have_weekday_relative' => int 0
  public 'have_special_relative' => int 0
*/
```

El periodo del intervalo se especifica con una P seguido de Y, M, D, W, H, M o S, siendo años, meses, días, semanas, horas, minutos y segundos respectivamente, puede verse mejor en la [documentación](http://www.php.net/manual/en/dateinterval.construct.php).

### 4. DateTimeZone

Cuando se crean **objetos DateTime**, el segundo argumento del constructor define el _**timezone**_. Si no establecemos este argumento, un timezone por defecto se obtendrá de la configuración en php.ini de _**date.timezone**_. Ya hemos explicado que este valor puede modificarse con la función _date_default_timezone_set()_.

Los timezones válidos pueden verse [aquí](http://www.php.net/manual/en/timezones.php).

Esto también puede realizarse con el objeto **DateTimeZone**:

```
$carlos = new DateTime('1982-08-15 15:23:48');
var_dump($carlos);
/*
* object(DateTime)[3]
  public 'date' => string '1982-08-15 15:23:48.000000' (length=26)
  public 'timezone_type' => int 3
  public 'timezone' => string 'Europe/Madrid' (length=13)
*/
$timezone = new DateTimeZone('America/Bogota');
$carlos->setTimezone($timezone);
var_dump($carlos);
/*
* object(DateTime)[3]
  public 'date' => string '1982-08-15 08:23:48.000000' (length=26)
  public 'timezone_type' => int 3
  public 'timezone' => string 'America/Bogota' (length=14)
*/
// También puede crearse un DateTime y asignarle el timezone en la creación
$maria = new DateTime('1985-4-5 09:12:43', $timezone);
```

Nótese que **setTimezone** modifica el objeto original DateTime.

### 5. DatePeriod

Un **DatePeriod** permite la **iteración sobre un conjunto de fechas y horas** mediante la repetición de **intervalos regulares** sobre un **periodo determinado**.

La clase **DatePeriod** nos permite generar un conjunto de **DateTimes** utilizando dos días que definimos entre un **intervalo**. Le pasamos una **fecha de inicio**, un **intervalo** y una **fecha fin**. En cada intervalo se crea un nuevo objeto **DateTime**.

```
// Este es el día de nacimiento de María
$maria = new DateTime('1985-4-5');
// Creamos un intervalo de un año
$intervalo = new DateInterval('P1Y');
// Creamos un periodo:
$periodo = new DatePeriod($maria, $intervalo, new DateTime());
// Vamos a ver el día de la semana que cumplió años en cada año hasta hoy
foreach($periodo as $sem){
  echo $sem->format('Y-m-d - D') . "<br>";
}
/*
* Devuelve:
1985-04-05 - Fri
1986-04-05 - Sat
1987-04-05 - Sun
1988-04-05 - Tue
1989-04-05 - Wed
...
*/
```

También podemos **saltarnos el primer dato**, el de la fecha de inicio del periodo, estableciendo el **cuarto argumento del constructor**:

```
$periodo = new DatePeriod($maria, $intervalo, new DateTime(), DatePeriod::EXCLUDE_START_DATE);
```

Podemos por ejemplo calcular así el número de cumpleaños celebrados hasta hoy:

```
$carlos = new DateTime('1982-08-15');
$intervalo = new DateInterval('P1Y');
$periodo = new DatePeriod($carlos, $intervalo, new DateTime(), DatePeriod::EXCLUDE_START_DATE);
echo iterator_count($periodo); // 33 cumpleaños ha celebrado Carlos
```

#### **Conclusión**

Es frecuente extender la **clase DateTime** con un [método mágico __toString()](http://diego.com.es/metodos-magicos-en-php#toString) para imprimir adecuadamente un **objeto DateTime** sin tener que llamar a format.

La clase DateTime es mucho más extensa, y se pueden hacer más combinaciones con el resto de clases disponibles que se pueden ver en la [documentación](http://php.net/manual/es/book.datetime.php).