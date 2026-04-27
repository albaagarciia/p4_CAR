Alba García Calvete y Ricardo López Moya

## *PARALELISMO A NIVEL DE HILOS*

### <span style="color:blue">TAREA 0.1 ENTRENAMIENTO PREVIO OPENMP

**0.1.1 ¿Para qué sirve la variable chunk?**

La variable chunk define el tamaño del bloque de iteraciones que se asigna a cada hilo de forma contigua. En este programa, tiene un valor de 100. Al usar una planificación dinámica, cuando un hilo termina sus 100 iteraciones asignadas, solicita el siguiente "chunk" disponible hasta completar el bucle.

**0.1.2 Explica completamente el pragma**

#pragma omp parallel shared (a,b,c,chunk) private (i) 

    shared (a,b,c,chunk): Se utiliza para indicar que estas variables son comunes a todos los hilos. Todos los hilos acceden a la misma dirección de memoria para leer los vectores a y b, escribir en c y consultar el valor de chunk. Es eficiente ya que evita duplicar grandes estructuras de datos.

    private (i): Se etiqueta como privada porque cada hilo necesita su propio contador de bucle independiente. Si i fuera compartida, los hilos interferirían entre sí al intentar incrementar el mismo índice simultáneamente, provocando condiciones de carrera (race conditions).

**0.1.3 ¿Para qué sirve schedule? ¿Qué otras posibilidades hay?**

La cláusula schedule determina cómo se reparten las iteraciones del bucle entre los hilos.

    dynamic: Las iteraciones se distribuyen en tiempo de ejecución. Los hilos solicitan nuevos bloques (chunks) conforme terminan los anteriores, lo cual es ideal para cargas de trabajo desequilibradas.

    Otras posibilidades:

        static: Los bloques se asignan de forma fija antes de empezar el bucle.

        guided: Similar al dinámico, pero el tamaño del bloque disminuye progresivamente.

        runtime: La decisión se pospone hasta la ejecución mediante una variable de entorno.

**0.1.4 Tiempos y medidas de rendimiento**

En secciones paralelizadas con OpenMP se pueden medir:

    Tiempo de pared (Wall-clock time): Usando omp_get_wtime().

    Ganancia en velocidad (Speed-up): Relación entre el tiempo secuencial y el paralelo.

    Eficiencia: Speed-up dividido por el número de hilos usados.

### <span style="color:blue">TAREA 0.2 ENTRENAMIENTO PREVIO std::async

**0.2.1 ¿Para qué sirve el parámetro std::launch::async?**

    Este parámetro es una política de lanzamiento que obliga al sistema a ejecutar la función en un nuevo hilo de ejecución separado de forma inmediata.

**0.2.2 Diferencia de tiempos entre launch::async y launch::deferred**

    std::launch::async: El tiempo total será aproximadamente el de la tarea más larga (aprox. 3000ms), ya que task1 (2000ms) y task2 (3000ms) se ejecutan en paralelo.

    std::launch::deferred: El tiempo total será la suma de ambas (aprox. 5000ms). La función no se ejecuta hasta que se llama explícitamente a wait() o get(), haciéndolo de forma secuencial en el hilo principal.

**0.2.3 Diferencia entre wait y get de std::future**

    wait(): Bloquea el hilo actual hasta que la tarea asíncrona finalice, pero no devuelve ningún valor.

    get(): También bloquea hasta que la tarea finalice, pero además devuelve el resultado de la función y libera el estado compartido. Solo puede llamarse una vez por cada future.

**0.2.4 Ventajas de std::async frente a std::thread**

    Gestión de resultados: std::async facilita la obtención de valores de retorno mediante futures.

    Abstracción: El programador se enfoca en "tareas" en lugar de gestionar hilos manualmente (creación, join/detach).

    Propagación de excepciones: Si la tarea lanza una excepción, esta se captura y se relanza al llamar a get().

### <span style="color:blue">TAREA 0.3 ENTRENAMIENTO PREVIO std::vector

**0.3.1 Eficiencia en la inicialización**

La segunda forma (inicializar con tamaño definido v2(10000) y acceso directo v2[i]=i) es más eficiente.

    Razón: push_back (primera forma) puede provocar múltiples reasignaciones de memoria y copias de elementos cada vez que el vector excede su capacidad actual. Al reservar el tamaño desde el inicio, se realiza una única asignación de memoria.

**0.3.2 Problemas al paralelizar los bucles***

    Bucle con push_back: Sí hay problemas. push_back no es una operación atómica ni segura para hilos (thread-safe); varios hilos intentando redimensionar el vector o modificar el puntero de fin de datos simultáneamente causarían corrupción de memoria.

    Bucle con acceso directo: Se puede paralelizar de forma segura (ej. con #pragma omp for) siempre que cada hilo escriba en una posición de índice i distinta, evitando solapamientos.


```python

```
