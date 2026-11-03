# Bitácora de Requerimientos y Auditoría IA — Sesión 07
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Evaluación de Expresiones Aritméticas  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347]  
**Track:** [ C ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Evaluador recursivo de operaciones  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> Al desarrollar la función de manera manual, se me dificulto el comprender cómo propagar los errores que se generaban en los nodos hijos sin seguir evaluando el resto del árbol. También tuve que tener cuidado con las operaciones de división y módulo para verificar primero que el divisor no fuera cero y evitar errores durante la ejecución.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Manejo estricto de división entre cero |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contexto: estoy desarrollando una función en lenguaje C para una práctica de Lenguajes y Autómatas II. La función debe evaluar expresiones aritméticas representadas mediante un árbol sintáctico (AST). También debe detectar errores como divisiones o módulos entre cero y reportarlos sin provocar fallos en la ejecución.

Rol: actúa como un programador con experiencia en lenguaje C y estructuras de datos recursivas. Genera una solución clara, sencilla y compatible con programación académica.

Especificación: implementa la función evaluar(ASTNode *n, int *error) para recorrer y evaluar un árbol de expresiones aritméticas de forma recursiva. La función debe devolver el valor calculado, detectar divisiones o módulos entre cero asignando error = 1, y reportar error = 2 cuando el nodo sea inválido (NULL). Además, cualquier error encontrado en los nodos hijos debe propagarse sin seguir evaluando el árbol. La implementación debe respetar la estructura dada, no incluir main() y compilar correctamente con gcc -std=c11 -Wall -Wextra -Werror.

Output: devuelve únicamente el código completo de la función evaluar() en lenguaje C, respetando exactamente la firma proporcionada.]
```

---

## 3. Código Devuelto por el Modelo
```c
%%writefile ia_sesion07_c.c
#include "api_sesion07.h"

int evaluar(ASTNode *n, int *error)
{
    int izq;
    int der;

    if (!error)
    {
        return 0;
    }

    if (!n)
    {
        *error = 2;
        return 0;
    }

    if (n->tipo == NODO_NUM)
    {
        return n->valor;
    }

    izq = evaluar(n->izq, error);

    if (*error)
    {
        return 0;
    }

    der = evaluar(n->der, error);

    if (*error)
    {
        return 0;
    }

    switch (n->op)
    {
        case '+':
            return izq + der;

        case '-':
            return izq - der;

        case '*':
            return izq * der;

        case '/':
            if (der == 0)
            {
                *error = 1;
                return 0;
            }
            return izq / der;

        case '%':
            if (der == 0)
            {
                *error = 1;
                return 0;
            }
            return izq % der;

        default:
            *error = 2;
            return 0;
    }
}
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Expresiones complejas y división por 0

=== AUDITORIA SESION 07 - Evaluador recursivo con deteccion de errores ===

Caso                             esperado:              obtenido:            resultado

[ 1] (2+3)*4                     esperado: 20           obtenido: 20           OK

[ 2] 7/0 -> error 1              esperado: 1            obtenido: 1            OK

[ 3] 7%0 -> error 1              esperado: 1            obtenido: 1            OK

[ 4] arbol NULL -> error 2       esperado: 2            obtenido: 2            OK

[ 5] 10-2-3                      esperado: 5            obtenido: 5            OK

[ 6] (8/2)+1                     esperado: 5            obtenido: 5            OK

RESULTADO: 6/6 pruebas superadas
TODO EN VERDE. Documenta tu auditoria en auditorias/auditoria_sesion07.md

* [✓] Caso base y límites superados sin *Crash*.
* [✓] No existen accesos fuera de límites ni *Memory Leaks*.
* [✓] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> La propuesta generada por la IA resolvía correctamente las operaciones aritméticas principales, pero verificque cuidadosamente el manejo del caso donde el árbol recibido es NULL. Este caso se detectó gracias al harness de pruebas, que comprobaba explícitamente la asignación de error = 2. Después de revisar la implementación, confirmé que el error se propagaba correctamente y que la función cumplía con todos los casos de prueba.

---
**Firma digital:** [YACQUELINECANCHE]
