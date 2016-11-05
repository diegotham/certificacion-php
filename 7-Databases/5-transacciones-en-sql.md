**Indice de contenido**

1.  ¿Qué son las transacciones?
2.  Propiedades de las transacciones
3.  Control de las transacciones

### 1. ¿Qué son las transacciones?

Las **transacciones en SQL** son unidades o secuencias de trabajo realizadas de forma ordenada y separada en una base de datos. Normalmente representan cualquier cambio en la base de datos, y tienen dos objetivos principales:

*   Proporcionar **secuencias de trabajo fiables** que permitan poder **recuperarse fácilmente ante errorres** y mantener una **base de datos consistente incluso frente a fallos del sistema**.
*   Proporcionar **aislamiento entre programas accediendo a la vez** a la base de datos.

Una transacción es una **propagación de uno o más cambios** en la base de datos, ya sea cuando se crea, se modifica o se elimina un registro. En la práctica suele consistir en la **agrupación de consultas SQL** y su ejecución como parte de una **transacción**.

### 2. Propiedades de las transacciones

Las transacciones siguen **cuatro propiedades básicas**, bajo el acrónimo **ACID** (Atomicity, Consistency, Isolation, Durability):

*   **Atomicidad**: aseguran que todas las operaciones dentro de la secuencia de trabajo **se completen satisfactoriamente**. Si no es así, la transacción se abandona en el punto del error y las operaciones previas retroceden a su estado inicial.
*   **Consistencia**: aseguran que la base de datos **cambie estados** en una transacción exitosa.
*   **Aislamiento**: permiten que las operaciones sean **aisladas** y **transparentes** unas de otras.
*   **Durabilidad**: aseguran que el resultado o efecto de una transacción completada **permanezca en caso de error del sistema**.

### 3. Control de las transacciones

Existen tres comandos básicos de control en las **transacciones SQL**:

*   **COMMIT**. Para **guardar los cambios**.
*   **ROLLBACK**. Para **abandonar la transacción y deshacer los cambios** que se hubieran hecho en la transacción.
*   **SAVEPOINT**. Crea **checkpoints**, puntos concretos en la transacción donde poder deshacer la transacción hasta esos puntos.

Los comandos de control de transacciones se usan sólo con **INSERT**, **DELETE** y **UPDATE**. **No pueden utilizarse creando tablas o vaciándolas** porque las operaciones se guardan automáticamente en la base de datos.