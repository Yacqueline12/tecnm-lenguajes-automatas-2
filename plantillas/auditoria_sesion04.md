# Bitácora de Requerimientos y Auditoría IA — Sesión 04
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Anatomía de un Árbol Sintáctico (AST)  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347]  
**Track:** [ C ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Estructura de nodo binario (ASTNode)  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> Al realizar el código manualmente, tuve que tener cuidado al reservar memoria para cada nodo y al guardar correctamente sus datos. También se me hizo importante conectar bien los hijos izquierdo y derecho, porque si los colocaba incorrectamente, la operación podía dar un resultado diferente. Otro punto que tuve que considerar fue revisar si el nodo existía antes de utilizarlo para evitar errores al ejecutar el programa.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Gramática E -> E + T | T con tipos de nodo |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contexto: Estoy trabajando en una práctica de la asignatura de Lenguajes y Autómatas sore árboles sintácticos abstractos (AST). Necesito crear un programa en lenguaje C que permita guardar números y operaciones.

Rol: Quiero que actúes como un asistente que me ayude a entender y realizar el código de manera sencilla, explicándome qué hace cada parte.

Especificación: Necesito que el programa tenga las funciones crear_numero, crear_binario y evaluar, respetando el archivo api_sesion04, quiero que crear_numero guarde números, crear_binario conecte los nodos y evaluar realice las operaciones de suma, resta y multiplicación. También debe regresar -1 si el nodo está vacío, quiero que el código sea sencillo, funcione correctamente y no tenga main(), porque ya viene en el harness.

Output: Quiero que me entregues el código completo de ia_sesion04_c.c, con comentarios sencillos y explicaciones que pueda entender.
]
```

---

## 3. Código Devuelto por el Modelo
```c
// Código devuelto por el LLM
```
%%writefile ia_sesion04_c.c

#include "api_sesion04.h"
#include <stdlib.h>

/* Preparar un nodo con sus datos iniciales */
static ASTNode *nuevo_nodo(TipoNodo tipo, int valor, char op,
                           ASTNode *izq, ASTNode *der) {
    ASTNode *nodo = calloc(1, sizeof(ASTNode));

    if (nodo != NULL) {
        nodo->tipo = tipo;
        nodo->valor = valor;
        nodo->op = op;
        nodo->izq = izq;
        nodo->der = der;
    }

    return nodo;
}

/* Guardar un valor numerico */
ASTNode *crear_numero(int v) {
    return nuevo_nodo(NODO_NUM, v, '\0', NULL, NULL);
}

/* Guardar la operacion y sus dos ramas */
ASTNode *crear_binario(char op, ASTNode *izq, ASTNode *der) {
    return nuevo_nodo(NODO_BINOP, 0, op, izq, der);
}

/* Calcular el resultado de cada nodo */
int evaluar(ASTNode *n) {
    int a;
    int b;

    if (n == NULL) {
        return -1;
    }

    switch (n->tipo) {
        case NODO_NUM:
            return n->valor;

        case NODO_BINOP:
            if (n->izq == NULL || n->der == NULL) {
                return -1;
            }

            a = evaluar(n->izq);
            b = evaluar(n->der);

            switch (n->op) {
                case '+':
                    return a + b;

                case '-':
                    return a - b;

                case '*':
                    return a * b;

                default:
                    return -1;
            }

        default:
            return -1;
    }
}
---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Árbol vacío y evaluación de nodo raíz

* [✓] Caso base y límites superados sin *Crash*.
* [✓] No existen accesos fuera de límites ni *Memory Leaks*.
* [✓] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> Un problema que tomé en cuenta fue cuando la función evaluar() recibe un nodo vacío (NULL), ya que esto podría causar un error si el programa intenta utilizarlo. Para solucionarlo, agregué una condición que revisa si el nodo existe y, si no existe, devuelve -1. También revisé que cada operación tuviera sus dos hijos antes de realizar el cálculo.

---
**Firma digital:** [YACQUELINECANCHE]
