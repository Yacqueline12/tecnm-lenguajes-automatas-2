# Bitácora de Requerimientos y Auditoría IA — Sesión 13
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Manejo de Ámbitos (Scope Global/Local)  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347]  
**Track:** [ C ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Pila de tablas de símbolos (Stack Scopes)  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> En lo personal, considero que una de las principales dificultades al realizar el código manualmente fue comprender cómo se debían recorrer los diferentes ámbitos para encontrar una variable. También tuve que prestar atención a los espacios disponibles en el arreglo de nombres, ya que no se debe intentar guardar información cuando el ámbito está lleno.
Otra dificultad fue evitar que se repitieran los nombres dentro de un mismo ámbito, pero permitir que se utilizaran nuevamente en otros bloques. La cual considero que fue una parte importante para entender cómo funciona el ocultamiento de variables (Shadowing).

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Ocultamiento de variables (Shadowing) |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contexto: estoy trabajando en la actividad de Lenguajes y Autómatas II, donde necesito realizar un programa en lenguaje C que permita manejar ámbitos globales y locales. Quiero entender cómo se pueden declarar variables con el mismo nombre en diferentes bloques y cómo se puede encontrar la variable correcta dependiendo del lugar donde se encuentre el programa.


Rol: quiero que actúes como un profesor de programación en C y me ayudes a desarrollar una solución que sea fácil de entender, con explicaciones claras y sin utilizar instrucciones demasiado complicadas.


Especificación: necesito implementar dos funciones llamadas `declarar()` y `buscar()`.
- La función `declarar()` debe guardar los nombres de las variables en el ámbito actual.
- No debe permitir que se repita un nombre dentro del mismo ámbito.
- Debe permitir utilizar el mismo nombre en diferentes ámbitos.
- Debe devolver 1 cuando el nombre se registre correctamente, 0 cuando ya exista y -1 cuando haya algún dato incorrecto o no exista espacio.
- La función `buscar()` debe comenzar buscando en el ámbito actual y después continuar por los ámbitos padres hasta encontrar el nombre.
- Si encuentra el nombre, debe devolver el nombre del ámbito donde está declarado.
- Si no encuentra el nombre, debe devolver NULL.
También necesito que el código respete las funciones declaradas en `api_sesion13.h`, que no tenga una función `main()` y que compile sin advertencias.


Output: quiero recibir el código completo en lenguaje C, con comentarios que expliquen las partes principales y que sea sencillo de comprender. También necesito que pueda compilarse con el harness de la actividad para comprobar que las seis pruebas funcionen correctamente.
]
```

---

## 3. Código Devuelto por el Modelo
```c
%%writefile ia_sesion13_c.c
/* ia_sesion13_c.c - Fase 2: El Cientifico
 *
 * Pega aqui el codigo que te devolvio la IA, con la MISMA firma
 * que declara api_sesion13.h. No puede traer main().
 */

/* Solucion de IA - Sesion 13
   Busqueda y declaracion de identificadores */

#include "api_sesion13.h"
#include <string.h>

/* Funcion auxiliar para revisar nombres */
static int nombreRegistrado(const Ambito *a, const char *nombre) {
    int posicion = 0;

    while (posicion < a->total) {
        if (a->nombres[posicion] != NULL &&
            strcmp(a->nombres[posicion], nombre) == 0) {
            return 1;
        }

        posicion++;
    }

    return 0;
}

/* Agregar un identificador al ambito */
int declarar(Ambito *ambito, const char *nombre) {

    if (ambito == NULL || nombre == NULL || nombre[0] == '\0') {
        return -1;
    }

    if (ambito->total < 0 || ambito->total > AMBITO_MAX) {
        return -1;
    }

    if (nombreRegistrado(ambito, nombre)) {
        return 0;
    }

    if (ambito->total == AMBITO_MAX) {
        return -1;
    }

    ambito->nombres[ambito->total++] = nombre;

    return 1;
}

/* Localizar el ambito donde se encuentra el identificador */
const char *buscar(const Ambito *ambito, const char *nombre) {
    const Ambito *nivel = ambito;

    if (nombre == NULL || nombre[0] == '\0') {
        return NULL;
    }

    while (nivel) {

        if (nivel->total >= 0 &&
            nivel->total <= AMBITO_MAX &&
            nombreRegistrado(nivel, nombre)) {

            return nivel->nombre;
        }

        nivel = nivel->padre;
    }

    return NULL;
}


```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Búsqueda jerárquica de identificadores

=== AUDITORIA SESION 13 - Pila de ambitos y busqueda jerarquica ===

Caso                               esperado:              obtenido:              resultado

[ 1] declarar(&g, "x")                  esperado: 1            obtenido: 1            OK

[ 2] buscar en el mismo ambito          esperado: global       obtenido: global       OK

[ 3] buscar subiendo al padre           esperado: global       obtenido: global       OK

[ 4] shadowing en el hijo               esperado: bloque       obtenido: bloque       OK

[ 5] tres niveles hasta la raiz         esperado: raiz         obtenido: raiz         OK

[ 6] nombre no declarado                esperado: (nulo)       obtenido: (nulo)       OK


RESULTADO: 6/6 pruebas superadas
TODO EN VERDE. Documenta tu auditoria en auditorias/auditoria_sesion13.md


* [ ] Caso base y límites superados sin *Crash*.
* [ ] No existen accesos fuera de límites ni *Memory Leaks*.
* [ ] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> En lo personal, considero que la IA realizó correctamente el código, pero fue importante revisar los límites del arreglo y validar los datos recibidos para evitar posibles errores. Finalmente, comprobé su funcionamiento y obtuve 6/6 pruebas superadas.

---
**Firma digital:** [Yacqueline12]
