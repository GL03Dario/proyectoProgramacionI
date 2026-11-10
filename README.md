# proyectoProgramacionI
# Sistema de Planilla Hospitalaria

# Integrantes:
* Darío González López 208880524
* Adrián González Robles 402720521
* Ian García López 208850760



## 

## Características

* Registro, consulta, modificación y eliminación de empleados de tres tipos: **Médico**, **Enfermero** y **Administrativo**.
* Gestión de **departamentos** y de los **puestos** que contiene cada uno.
* Asignación de empleados a un departamento y puesto, con validación de existencia.
* **Desglose salarial** detallado: horas extra, bonificación nocturna, guardias, bonificación del puesto y deducción de ley.
* Generación de **comprobantes de pago** individuales o de toda la planilla de un periodo.
* Procesamiento de pagos (individual o masivo por periodo) y **resumen de totales** por periodo.
* Guardado automático tras cada operación, con escritura segura mediante archivo temporal.
* Colecciones propias (`Lista<T>` y `Nodo<T>`): lista enlazada genérica, sin uso de contenedores de la STL para el almacenamiento principal.



## Estructura del proyecto

Los `#include` del código usan rutas del tipo `"modelo/Empleado.h"`, por lo que los encabezados deben estar organizados en subcarpetas con estos nombres. La disposición esperada es:

```text
proyecto/
├── main.cpp
├── colecciones/
│   ├── Lista.h
│   └── Nodo.h
├── modelo/
│   ├── Empleado.h / .cpp
│   ├── Medico.h / .cpp
│   ├── Enfermero.h / .cpp
│   ├── Administrativo.h / .cpp
│   ├── FabricaEmpleado.h / .cpp
│   ├── Departamento.h / .cpp
│   ├── Puesto.h / .cpp
│   └── ComprobantePago.h / .cpp
├── controladores/
│   ├── ControladorEmpleados.h / .cpp
│   ├── ControladorDepartamentos.h / .cpp
│   └── ServicioPlanilla.h / .cpp
├── persistencia/
│   ├── AlmacenamientoArchivos.h / .cpp
│   ├── IAlmacenamientoEmpleados.h
│   ├── IAlmacenamientoDepartamentos.h
│   └── IAlmacenamientoComprobantes.h
└── vista/
	├── InterfazConsola.h / .cpp
	└── EntradaConsola.h / .cpp
```


## 

## Guía ejecutiva de uso

### Flujo recomendado en 5 pasos

|Paso|Acción|Ruta de menú|
|-|-|-|
|1|Crear los departamentos|`2 → 1` Crear departamento|
|2|Definir los puestos de cada departamento|`2 → 3` Agregar puesto|
|3|Registrar a los empleados (y asignarlos a su departamento)|`1 → 1` Registrar empleado|
|4|Generar la planilla del mes|`3 → 2` Generar planilla completa|
|5|Pagar y revisar totales|`3 → 6` Procesar pagos / `3 → 7` Resumen|

> Se puede registrar empleados sin departamento y asignarlos después con `1 → 5`.

### Menú principal

```text
=== SISTEMA DE PLANILLA HOSPITALARIA ===
	1. Gestion de empleados
	2. Departamentos y puestos
	3. Planilla y pagos
	0. Salir
```

Se navega escribiendo el número de la opción y presionando **Enter**. La opción `0` en cualquier submenú regresa al menú anterior. Tras cada operación el sistema pide presionar Enter para continuar.

### 1\. Gestión de empleados

|Opción|Función|Notas|
|-|-|-|
|1|Registrar empleado|Pide el tipo, identificación, nombre, datos salariales y el detalle propio del tipo|
|2|Listar empleados|Tabla con ID, tipo, departamento, salario neto y nombre|
|3|Ver detalle y desglose salarial|Muestra cada componente del salario|
|4|Modificar empleado|Permite cambiar un campo a la vez (nombre, salario, horas extra, turnos, guardias, tasa, detalle y bonificación del puesto)|
|5|Asignar departamento y puesto|Dejar el departamento vacío quita la asignación|
|6|Eliminar empleado|Requiere confirmación|

**Datos que solicita el registro de un empleado:**

|Campo|Descripción|
|-|-|
|Tipo|Médico, Enfermero o Administrativo|
|Identificación|Única en todo el sistema|
|Nombre completo|Obligatorio|
|Salario base|Monto mayor o igual a 0|
|Horas extra y valor por hora extra|Horas del periodo y su tarifa|
|Turnos nocturnos y bonificación por turno|Cantidad entera y monto por turno|
|Guardias y bonificación por guardia|Cantidad entera y monto por guardia|
|Tasa de deducción de ley|Se ingresa como porcentaje (0–100)|
|Detalle del puesto|Según el tipo (ver tabla siguiente) y su bonificación|

|Tipo de empleado|Detalle que se solicita|
|-|-|
|Médico|Especialidad|
|Enfermero|Área asignada|
|Administrativo|Cargo|

### 2\. Departamentos y puestos

|Opción|Función|Efecto secundario|
|-|-|-|
|1|Crear departamento|Código único y nombre|
|2|Listar departamentos y puestos|Vista general de la estructura|
|3|Agregar puesto|Código de puesto único dentro del departamento|
|4|Eliminar puesto|Los empleados con ese puesto quedan sin puesto asignado|
|5|Eliminar departamento|Se eliminan sus puestos y los empleados quedan sin departamento|

Las eliminaciones muestran el impacto (por ejemplo, cuántos empleados quedarán sin departamento) y piden confirmación antes de ejecutarse.

### 3\. Planilla y pagos

|Opción|Función|Notas|
|-|-|-|
|1|Generar comprobante de un empleado|Pide identificación y periodo|
|2|Generar planilla completa de un periodo|Emite un comprobante por cada empleado que aún no lo tenga|
|3|Listar comprobantes|Número, periodo, empleado, neto y estado (Pagado/Pendiente)|
|4|Ver un comprobante|Por número de comprobante|
|5|Procesar pago de un comprobante|Muestra el comprobante y pide confirmación|
|6|Procesar todos los pagos de un periodo|Marca como pagados todos los pendientes del periodo|
|7|Resumen de un periodo|Total bruto, total de deducciones y total neto|

* **Formato de periodo:** `AAAA-MM`, por ejemplo `2026-11`.
* Un empleado solo puede tener **un comprobante por periodo**; intentar generar otro muestra un error.
* Los comprobantes conservan una copia del nombre del empleado y del departamento, de modo que siguen siendo consultables aunque el empleado o el departamento se eliminen después.

### Ejemplo de comprobante

```text
------------------------------------------
COMPROBANTE DE PAGO No. 1
------------------------------------------
Periodo:         2026-11
Fecha emision:   2026-11-09
Estado:          Pendiente de pago
Empleado:        Ana Rojas (101)
Tipo:            Medico
Departamento:    URG - Urgencias

Salario base:             1500000.00
Horas extra:              100000.00
Bonificacion nocturna:    80000.00
Bonificacion guardias:    60000.00
Bonificacion del puesto:  50000.00
Salario bruto:            1790000.00
Deduccion de ley:         179000.00
SALARIO NETO:             1611000.00
------------------------------------------
```

*(Valores ilustrativos.)*

### Mensajes de error comunes

Los errores de operación se muestran como `Error: ...` y **no cierran** el programa. Ejemplos: identificación duplicada, departamento o puesto inexistente, periodo con formato inválido, comprobante duplicado en un periodo, valores negativos.

### Salir del sistema

Use la opción `0` del menú principal. Los datos ya están guardados, pues cada cambio se escribe a disco en el momento. Si la entrada estándar se cierra (por ejemplo, **Ctrl + D** en Linux/macOS o **Ctrl + Z** en Windows), el programa termina de forma ordenada.

\---

## Reglas de cálculo salarial

```text
Pago horas extra        = horas extra × valor por hora extra
Bonificación nocturna   = turnos nocturnos × bonificación por turno
Bonificación guardias   = guardias × bonificación por guardia
Salario bruto           = salario base + horas extra + bonificación nocturna
                          + bonificación guardias + bonificación del puesto
Deducción de ley        = salario bruto × tasa de deducción
Salario neto            = salario bruto − deducción de ley
```

* Todos los montos deben ser mayores o iguales a 0.
* La tasa de deducción se ingresa en porcentaje (0–100) y se almacena internamente como fracción (0–1).
* La bonificación del puesto depende del tipo de empleado: especialidad (Médico), área asignada (Enfermero) o cargo (Administrativo).

## Persistencia de datos

El sistema usa tres archivos de texto plano en el directorio de trabajo:

|Archivo|Contenido|
|-|-|
|`empleados.txt`|Todos los empleados registrados|
|`departamentos.txt`|Departamentos y sus puestos|
|`planilla.txt`|Comprobantes de pago emitidos|

**Formato:**

* Un registro por línea, con los campos separados por el carácter `|`.
* Las líneas vacías o que comienzan con `#` se ignoran (útil para comentarios).
* El carácter `|` dentro de un texto se reemplaza por `/` y los saltos de línea por espacios al guardar.
* Cada guardado escribe primero un archivo temporal `.tmp` y luego lo renombra, para evitar archivos corruptos si el proceso se interrumpe.

**Orden de los campos en `empleados.txt`:**

```text
tipo|identificacion|nombre|salarioBase|horasExtra|valorHoraExtra|turnosNocturnos|
bonifTurnoNocturno|guardias|bonifGuardia|tasaDeduccion|idDepartamento|codigoPuesto|
detallePuesto|bonifPuesto
```

*(En el archivo real cada registro va en una sola línea.)*

**Respaldo:** para respaldar la información basta con copiar los tres archivos `.txt`. Para reiniciar el sistema desde cero, elimínelos con el programa cerrado.

Si un archivo contiene una línea inválida, el sistema se detiene al iniciar con un mensaje del tipo `Error fatal: ... , linea N`, indicando el archivo y la línea a corregir.

## Arquitectura

El proyecto sigue una separación por capas, con inyección de dependencias manual en `main.cpp`:

```text
InterfazConsola  (vista)
	│
	├── ControladorEmpleados ──┐
	├── ControladorDepartamentos ──┤──► IAlmacenamiento\*  (interfaces)
	└── ServicioPlanilla ──────┘            │
												▼
									AlmacenamientoArchivos
```

|Capa|Responsabilidad|
|-|-|
|`modelo/`|Entidades del dominio: `Empleado` (clase base) y sus derivadas `Medico`, `Enfermero`, `Administrativo`; `Departamento`, `Puesto`, `ComprobantePago`|
|`modelo/FabricaEmpleado`|Patrón fábrica: crea el tipo correcto de empleado a partir de `DatosEmpleado`. Para añadir un nuevo tipo basta con crear la clase derivada y registrarla aquí|
|`controladores/`|Reglas de negocio y validaciones cruzadas (empleado ↔ departamento ↔ puesto ↔ planilla)|
|`persistencia/`|Interfaces `IAlmacenamiento\*` y su implementación en archivos; permite sustituir el almacenamiento (por ejemplo, por una base de datos) sin tocar los controladores|
|`colecciones/`|`Lista<T>` y `Nodo<T>`: lista enlazada genérica que es dueña de los objetos que contiene y los libera al destruirse|
|`vista/`|Interfaz de consola y utilidades de lectura validada (`EntradaConsola`)|

**Nota de diseño:** `Lista<T>` almacena punteros y es **propietaria** de ellos: `eliminar`, `limpiar` y el destructor liberan los objetos. No se debe liberar manualmente un elemento que haya sido agregado a una lista.

## Solución de problemas

|Síntoma|Causa probable|Solución|
|-|-|-|
|`fatal error: modelo/Empleado.h: No such file or directory`|Falta la ruta de inclusión|Agregue `-I.` (o la carpeta que contiene `modelo/`, `controladores/`, etc.)|
|`undefined reference to ...` al enlazar|Faltó compilar algún `.cpp`|Verifique que se compilen todos los `.cpp` de cada subcarpeta|
|`Error fatal: ... linea N` al iniciar|Archivo de datos con una línea mal formada|Corrija o elimine esa línea en el `.txt` indicado|
|Los datos "desaparecen" entre ejecuciones|El programa se lanzó desde otra carpeta|Ejecútelo siempre desde el mismo directorio|
|`No se pudo actualizar <archivo>`|Sin permisos de escritura o archivo abierto en otro programa|Revise permisos y cierre el archivo|
|Caracteres con tilde se ven mal en Windows|Codificación de la consola|Ejecute `chcp 65001` antes de iniciar el programa|



