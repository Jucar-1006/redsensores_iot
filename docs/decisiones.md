# Decisiones de diseño — Semana 3

## 1. Punto de entrada

El proyecto mantiene un único punto de entrada:

```text
IngestaSensores.main()
```

No se crean aplicaciones independientes por semana.

`BancoDePruebas` es una clase auxiliar y no contiene `main`.

## 2. Búsqueda por timestamp

Se utilizan dos estrategias:

- Búsqueda lineal: no requiere ordenamiento.
- Búsqueda binaria: requiere que el arreglo esté ordenado por timestamp.

Los datos sintéticos de `GeneradorDatos` se generan en orden cronológico, por lo que la búsqueda binaria por timestamp cumple su precondición.

## 3. Búsqueda por PM2.5

No se asume que los datos estén ordenados por PM2.5.

Por tanto, la búsqueda binaria por PM2.5 se conserva como experimento para demostrar el efecto de una precondición incumplida.

## 4. Comparación de String

Los identificadores de estación se comparan mediante:

```java
equals()
```

y no mediante:

```java
==
```

porque se necesita comparar contenido.

## 5. Medición

La comparación principal entre algoritmos utiliza el número de comparaciones.

El tiempo en milisegundos se conserva como evidencia experimental, pero no es la única medida utilizada.

## 6. Evolución del proyecto

La Semana 3 agrega una nueva capacidad a la misma plataforma:

```text
Sensores
   ↓
Ingesta
   ↓
Repositorio
   ↓
Búsqueda
   ↓
Medición de eficiencia
```

La Semana 4 podrá extender esta misma arquitectura para estudiar ordenamiento.

## 7. Preguntas HTML
### 1. ¿Por qué insertar al inicio es más económico en una lista enlazada que en un 
arreglo?


En un arreglo hay que mover todos los elementos un espacio a la derecha, 
lo que exige mucho esfuerzo y tiempo. En una lista enlazada, solo conectas 
el nuevo dato al principio ajustando un enlace de los nodos, lo cual es casi 
instantáneo y no mueve el resto de la información.

### 2. ¿Por qué el acceso a una posición arbitraria es más costoso en una lista?

Porque la lista no usa índices numéricos. Para llegar a una posición específica 
toca empezar obligatoriamente desde el principio e ir pasando de nodo en 
nodo hasta encontrarla. En cambio, en un arreglo vas directamente al dato exacto.

### 3. ¿Qué diferencia existe entre un experimento de rendimiento y una prueba unitaria?

La prueba unitaria valida que la lógica del código funcione correctamente y haga 
exactamente lo que se espera. El experimento de rendimiento simplemente 
mide qué tan eficiente es el código al ejecutar una tarea, contando los 
tiempos y movimientos.

### 4. ¿Qué caso límite consideras más importante probar en eliminar() y por qué?

Eliminar un nodo en el medio. Si no se reconectan bien los elementos 
sea dejando el enlace vacío, la cadena se rompe por completo. 
Esto hace que pierdas el acceso a todos los datos que iban después y la 
lista quede dañada.

### 5. Para el histórico de lecturas de sensores, ¿qué estructura elegirías si predominan las inserciones al inicio? ¿Qué elegirías si predominan las consultas por posición? Justifica ambas decisiones.

**Para inserciones al inicio:** La lista enlazada. Solo necesitas cambiar un 
enlace para conectar el nuevo dato, evitando el pesado trabajo de mover todas 
las lecturas anteriores una por una, como pasaría con un arreglo.

**Para consultas por posición:** El arreglo. Permite ir directamente a la 
lectura exacta utilizando su índice, evitando tener que caminar por toda la 
cadena desde el inicio cada vez que buscas algo.

## 8. Experimentos

### Experimento 1

Resultados:

```java
=== EXP 1: EL MISMO CODIGO, DOS MECANISMOS ===
Arreglo          tamano: 5   primera: 0000000000
Lista enlazada   tamano: 5   primera: 0000000000

Ni una linea del ciclo de arriba sabe que hay adentro.
Eso es el contrato de la semana 2, cumplido.


Process finished with exit code 0
```
### Experimento 2
Resultados:

```java
=== EXP 2: LA CUENTA QUE NO CUADRA ===
Antes de eliminar:
   tamano() dice:        10
   nodos que hay:        10
Despues de eliminar la posicion 5:
   tamano() dice:        9
   nodos que hay:        9

Se pidio eliminar UNA lectura. Cuenta cuantas desaparecieron.


Process finished with exit code 0

```

### Experimento 3
Resultados:

```java
            lecturas |                    ARREGLO |             LISTA ENLAZADA
                     |    movimientos          ms |         saltos          ms
            10.000   |     49.995.000          37 |              0           1 
            50.000   |  1.249.975.000         793 |              0           1
            100.000  |  4.999.950.000       3.741 |              0           0

Process finished with exit code 0
```

### Experimento 4
Resultados:

```java
=== EXP 4: RECORRER POR POSICION ===
  lecturas |  arreglo: acc.       ms |    lista: saltos       ms
     1.000 |          1.000        0 |          499.500        1
    10.000 |         10.000        0 |       49.995.000       43
    50.000 |         50.000        0 |    1.249.975.000    1.022

El ciclo 'for (i=0; i<n; i++) obtener(i)' se ve identico en los dos casos.
Compara la columna de saltos con la de accesos.

Process finished with exit code 0
```

### Experimento 5
Resultados:

```java
=== EXP 5: BUSQUEDA BINARIA SOBRE UNA CADENA ===
Buscando la ultima lectura de 50.000:
   Busqueda LINEAL  -> posicion 49999, saltos entre nodos: 50.000
   Busqueda BINARIA -> posicion 49999, saltos entre nodos: 749.990

La binaria hace 16 comparaciones, igual que sobre el arreglo.
Mira lo que le cuesta LLEGAR a cada una de esas 16 posiciones.


Process finished with exit code 0
```
