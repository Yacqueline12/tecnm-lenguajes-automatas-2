# Bitácora de Requerimientos y Auditoría IA — Sesión 15
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Bloques Anidados y Ciclo de Vida  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347]  
**Track:** [ C ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Operaciones Push y Pop de ámbito  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> Al realizar el código manualmente, me di cuenta de que debía tener cuidado al utilizar los apuntadores y la memoria dinámica, ya que necesitaba crear nuevos ámbitos sin perder la relación con su padre. También tuve que asegurarme de liberar correctamente la memoria al cerrar un bloque y evitar eliminar el ámbito raíz.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Liberación de memoria al cerrar bloque |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contexto: estoy realizando la práctica de la Sesión 15 de la materia Lenguajes y Autómatas II, donde debo implementar el manejo de ámbitos anidados en lenguaje C. Necesito desarrollar las funciones push_ambito() y pop_ambito() para controlar la creación y eliminación de ámbitos.

Rol: actúa como un programador experto en lenguaje C, especializado en estructuras, apuntadores y administración de memoria dinámica.

Especificación: desarrolla las funciones push_ambito() y pop_ambito() utilizando una solución diferente a mi código manual. Debes utilizar una función auxiliar para crear los ámbitos, validar los valores NULL, relacionar cada ámbito con su padre y evitar liberar el ámbito raíz. También debes liberar correctamente la memoria dinámica y cumplir con las condiciones establecidas en api_sesion15.h.

Output (Salida): entrega el código completo para el archivo ia_sesion15_c.c, sin incluir la función main(). El código debe ser compatible con C11 y compilar sin advertencias utilizando -Wall -Wextra -Werror. Además, debe superar las seis pruebas del archivo harness_sesion15_c.c.]
```

---

## 3. Código Devuelto por el Modelo
```c
%%writefile ia_sesion15_c.c
/* ia_sesion15_c.c - Fase 2: El Cientifico
 *
 * Pega aqui el codigo que te devolvio la IA, con la MISMA firma
 * que declara api_sesion15.h. No puede traer main().
 */
#include "api_sesion15.h"
#include <stdlib.h>

/* Construye un ámbito y establece su relación con el padre. */
static Ambito *crear_ambito(const char *nombre, Ambito *padre) {
    Ambito *nuevo = malloc(sizeof *nuevo);

    if (nuevo == NULL) {
        return NULL;
    }

    *nuevo = (Ambito){nombre, padre};

    return nuevo;
}

Ambito *push_ambito(Ambito *actual, const char *nombre) {
    if (actual == NULL || nombre == NULL) {
        return NULL;
    }

    return crear_ambito(nombre, actual);
}

Ambito *pop_ambito(Ambito *actual) {
    if (actual == NULL || actual->padre == NULL) {
        return NULL;
    }

    Ambito *padre = actual->padre;
    free(actual);

    return padre;
}
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Aislamiento de variables fuera de bloque

                === AUDITORIA SESION 15 - Push y pop de ambitos ===

Caso                               esperado:        obtenido:         resultado

[ 1] nombre del ambito nuevo       esperado: if     obtenido: if           OK

[ 2] profundidad con dos push      esperado: 3      obtenido: 3            OK

[ 3] pop devuelve el padre         esperado: 1      obtenido: 1            OK

[ 4] pop sobre la raiz             esperado: 1      obtenido: 1            OK

[ 5] push con padre NULL           esperado: 1      obtenido: 1            OK

[ 6] profundidad tras pop          esperado: 2      obtenido: 2            OK


RESULTADO: 6/6 pruebas superadas

TODO EN VERDE. Documenta tu auditoria en auditorias/auditoria_sesion15.md


* [✓] Caso base y límites superados sin *Crash*.
* [ ] No existen accesos fuera de límites ni *Memory Leaks*.
* [✓] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> La IA no consideró probar qué sucede cuando no hay memoria disponible para crear un nuevo ámbito. Aunque el código contempla esa situación devolviendo NULL, las pruebas del programa no verificaron ese caso. Por eso considero importante revisar también las situaciones que no aparecen en el Test Harness para evitar errores.

---
**Firma digital:** [Yacqueline 12]
