# Bitácora de Requerimientos y Auditoría IA — Sesión 05
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Recorridos del AST y Notación Polaca  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347]  
**Track:** [ C ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Impresión en Postorden / RPN  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> Al realizar el código del Artesano, se me dificultó un poco entender cómo recorrer el árbol en el orden correcto y cómo guardar los números y operadores en el buffer sin que quedaran espacios de más. También tuve que tener cuidado con el tamaño del buffer, porque si se llena puede provocar errores al escribir los datos. Por eso fui revisando el código y comprobando que el resultado saliera como lo pedía la actividad.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Invariantes de recorrido recursivo |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[ Contexto: estoy trabajando en la sesión 05 de Lenguajes y Autómatas II. La actividad consiste en recorrer un árbol AST para obtener una expresión en notación polaca inversa. Estoy aprendiendo a programar en C y necesito que el código sea fácil de entender.

 Rol: quiero que actúes como un programador que me ayude a resolver el ejercicio y que me explique las instrucciones de una manera sencilla, sin utilizar cosas demasiado complicadas.

 Especificación: necesito crear la función postorden en C para recorrer el árbol primero por la izquierda, luego por la derecha y al final la raíz. El resultado debe guardarse en el buffer con espacios entre los números y operadores, respetando su capacidad, funcionando con un árbol NULL y sin utilizar printf ni main().

 Output: dame el código completo para pegarlo en ia_sesion05_c.c. Utiliza una solución diferente a la que hice manualmente, pero que cumpla con las mismas condiciones. Explícame brevemente cómo funciona y revisa que pueda compilarse con -std=c11 -Wall -Wextra -Werror.]
```

---

## 3. Código Devuelto por el Modelo
```c
%%writefile ia_sesion05_c.c
#include "api_sesion05.h"
#include <stdio.h>
#include <string.h>

static void agregar_nodo(ASTNode *n, char *buf, int cap, int *pos) {
    char token[32];
    int escritos;
    size_t largo;
    size_t espacio;

    if (n == NULL || *pos >= cap - 1) {
        return;
    }

    agregar_nodo(n->izq, buf, cap, pos);
    agregar_nodo(n->der, buf, cap, pos);

    if (*pos >= cap - 1) {
        return;
    }

    if (n->tipo == NODO_NUM) {
        escritos = snprintf(token, sizeof(token), "%d", n->valor);
    } else {
        escritos = snprintf(token, sizeof(token), "%c", n->op);
    }

    if (escritos < 0 || (size_t)escritos >= sizeof(token)) {
        return;
    }

    largo = strlen(token);
    espacio = (*pos > 0) ? 1u : 0u;

    /* Comprobar que el token completo cabe en el buffer */
    if ((size_t)*pos + espacio + largo >= (size_t)cap) {
        *pos = cap - 1;
        buf[*pos] = '\0';
        return;
    }

    if (espacio > 0) {
        buf[(*pos)++] = ' ';
    }

    memcpy(buf + *pos, token, largo + 1);
    *pos += (int)largo;
}

void postorden(ASTNode *n, char *buf, int cap) {
    int pos = 0;

    if (buf == NULL || cap <= 0) {
        return;
    }

    buf[0] = '\0';
    agregar_nodo(n, buf, cap, &pos);
}
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Validación contra desbordamiento de pila
```text
=== AUDITORIA SESION 05 - Recorrido postorden y RPN ===

Caso                          esperado:              obtenido:              resultado

[ 1] hoja 42                  esperado: 42           obtenido: 42           OK

[ 2] 3+(5*2)                  esperado: 3 5 2 * +    obtenido: 3 5 2 * +    OK

[ 3] arbol NULL               esperado:              obtenido:              OK

[ 4] (1+2)*(3+4)

esperado: 1 2 + 3 4 + *

obtenido: 1 2 + 3 4 + *

     -> OK

[ 5] 8-3                      esperado: 8 3 -        obtenido: 8 3 -        OK

RESULTADO: 5/5 pruebas superadas
TODO EN VERDE. Documenta tu auditoria en auditorias/auditoria_sesion05.md
```

* [✓] Caso base y límites superados sin *Crash*.
* [ ] No existen accesos fuera de límites ni *Memory Leaks*.
* [✓] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> Al revisar el código que me dio la IA, me di cuenta de que debía tener mucho cuidado con el tamaño del buffer, porque si no hay suficiente espacio se pueden presentar errores al guardar los números y operadores, Para solucionarlo, revisé las condiciones del espacio disponible y realicé las pruebas del programa para verificar que los resultados fueran correctos.

---
**Firma digital:** [YACQUELINECANCHE]
