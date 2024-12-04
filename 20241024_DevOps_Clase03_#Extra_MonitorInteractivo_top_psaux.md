# Clase 3 - Monitoreo Interactivo

## En esta actividad vamos a aprender a utilizar el comando top para realizar un monitoreo interactivo de los procesos que se están ejecutando en el sistema.

### Paso 1: Ejecutar el comando top

Muchas veces necesitamos monitorear los procesos que se están ejecutando en el sistema, para esto podemos utilizar el comando top.

```bash
top
```

### Paso 2: Modificar el tiempo de refresh

Indicar a top que actulize la informacion cada 5 segundos.

```bash
top -d 5
```

### Paso 3: Mostrar los procesos de un usuario

Si necesitamos ver los procesos de un usuario puntual podemos utilizar el siguiente comando.

```bash
top -u usuario
```

### Paso 4: Ordenar por el uso de memoria

Podemos ordenar los procesos por el uso de memoria.

```bash
top -o %MEM
```

### Paso 6: Ordenar por el uso de CPU

Podemos ordenar los procesos por el uso de CPU.

```bash
top -o %CPU
```

### Paso 7: Filtrar por nombre de proceso

Si necesitamos filtrar los procesos por un nombre en particular.

```bash
ps aux |grep <nombre_proceso>
```

### Paso 8: Filtrar por PID

Si necesitamos filtrar los procesos por un PID en particular.

```bash
top -p PID
```