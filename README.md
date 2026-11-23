# Simulador de autómatas

Proyecto académico para la asignatura de Lenguajes Formales y Autómatas. Permite
modelar y simular:

- **AFD**: autómatas finitos deterministas.
- **AFND**: autómatas finitos no deterministas.
- **APD**: autómatas de pila deterministas.
- **APND**: autómatas de pila no deterministas.

El proyecto incluye validación estructural, simulación de cadenas con trazas,
tablas de transición y análisis de estados alcanzables. Para los AFD también
incluye la posibilidad de completar el autómata con un estado trampa.

## Requisitos

- Python 3.10 o posterior.
- No se requieren dependencias externas.

## Estructura del proyecto

```text
AFD/                  Implementación del autómata finito determinista
AFND/                 Implementación del autómata finito no determinista
APD/                  Implementación del autómata de pila determinista
APND/                 Implementación del autómata de pila no determinista
GestionAutomatas/     Registro y gestión de autómatas cargados
ManejoArchivos/       Lectura de autómatas y cadenas
pruebas/              Autómatas y cadenas de ejemplo
main.py               Punto de entrada previsto del proyecto
```

## Formato de los archivos de autómata

Los archivos usan campos `CLAVE=VALOR` y una sección obligatoria
`TRANSICIONES:`. Los elementos de las listas se separan con comas.

### AFD y AFND

```text
NOMBRE=AFD_Termina_En_b
TIPO=AFD
ESTADOS=q0,q1
ALFABETO=a,b
INICIAL=q0
FINALES=q1
TRANSICIONES:
q0,a,q0
q0,b,q1
q1,a,q0
q1,b,q1
```

Cada transición tiene el formato:

```text
estado_origen,simbolo,estado_destino
```

En un AFND se pueden indicar varios destinos separados por espacios. Los
símbolos epsilon no forman parte del alfabeto de los AFND.

### APD y APND

Los autómatas de pila requieren además `ALFABETO_PILA` e `INICIAL_PILA`:

```text
NOMBRE=APD_a_n_b_n
TIPO=APD
ESTADOS=q0,q1,q2
ALFABETO=a,b
ALFABETO_PILA=Z0,A
INICIAL=q0
INICIAL_PILA=Z0
FINALES=q2
TRANSICIONES:
q0,a,Z0,q0,A;Z0
q0,a,A,q0,A;A
q0,b,A,q1,EPS
q1,b,A,q1,EPS
q1,EPS,Z0,q2,Z0
```

Las transiciones de un APD siguen el formato:

```text
estado_origen,entrada,tope,estado_destino,reemplazo
```

En un APND se pueden indicar alternativas con `|`, por ejemplo:

```text
q0,a,Z0,q1,A | q2,EPS
```

`EPS` representa un reemplazo vacío o una transición sin consumir entrada.
También se aceptan `eps`, `epsilon`, `ε`, `lambda` y `λ` para representar una
cadena vacía al evaluar entradas.

## Ejemplos incluidos

Los archivos de prueba se encuentran en [`pruebas/`](pruebas/):

- `pruebas/AFD/`: autómatas y cadenas para AFD.
- `pruebas/AFND/`: autómatas y cadenas para AFND.
- `pruebas/APD/`: autómatas y cadenas para APD.
- `pruebas/APND/`: autómatas y cadenas para APND.
- `pruebas/validacion/`: casos con errores de formato o estructura.

## Estado de ejecución

`main.py` conserva el punto de entrada previsto y delega el arranque en el
módulo `Interfaz`. La implementación actual del repositorio no incluye todavía
ese módulo, por lo que la interfaz de usuario no puede iniciarse directamente
con `python main.py`. Las clases y el cargador de autómatas pueden utilizarse
desde Python, por ejemplo:

```python
from ManejoArchivos.cargarAutomata import cargar_automata_desde_archivo

resultado = cargar_automata_desde_archivo(
    "pruebas/AFD/automatas/afd_01_termina_b.txt"
)

if resultado.exitoso:
    automata = resultado.instancia
    automata.validarCadena("aab")
else:
    print("\n".join(resultado.errores))
```

Las clases de AFD y AFND usan nombres de métodos en español con `camelCase` y
`snake_case`, respectivamente; APD y APND exponen principalmente métodos en
`snake_case`.
