# Bitácora de Requerimientos y Auditoría IA — Sesión 24
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Control de Flujo: Sentencia if-else  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347]  
**Track:** [ C | Rust ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Saltos condicionales y evaluación booleana  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> Tuve dificultad para identificar cuándo debía utilizar IFFALSE y cuándo IFTRUE. También tuve que controlar la cantidad de instrucciones generadas y considerar el caso en el que el segundo operando fuera NULL.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Cortocircuito lógico en expresiones |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contexto: estoy realizando la Sesión 24 de Lenguajes y Autómatas II. Necesito implementar una función en C que genere saltos condicionales para expresiones AND y OR.

Rol: actúa como un programador experto en lenguaje C y compiladores.

Especificación: implementa una función llamada emitir_condicion() que genere instrucciones de saltos condicionales. Cuando el operador sea &, deberá generar instrucciones IFFALSE, y cuando sea |, deberá generar instrucciones IFTRUE. Todos los saltos deberán dirigirse a la etiqueta recibida. Si el segundo operando es NULL, deberá generar únicamente un salto. La función deberá respetar el límite máximo de instrucciones indicado por el parámetro max, evitar escribir fuera de los 64 caracteres disponibles en cada línea y validar los parámetros para prevenir errores. Además, deberá devolver la cantidad de instrucciones generadas, no incluir la función main() y compilar correctamente utilizando los parámetros gcc -std=c11 -Wall -Wextra -Werror.

Output: proporciona el código completo de ia_sesion24_c.c, explica su funcionamiento y menciona las mejoras respecto a una implementación manual.]
```

---

## 3. Código Devuelto por el Modelo
```c
%%writefile ia_sesion24_c.c
/* ia_sesion24_c.c - Fase 2: El Cientifico
 *
 * Pega aqui el codigo que te devolvio la IA, con la MISMA firma
 * que declara api_sesion24.h. No puede traer main().
 */

#include "api_sesion24.h"
#include <stdio.h>

int emitir_condicion(char lineas[][64], int max,
                     const char *a, const char *b,
                     char op, const char *etiqueta) {

    const char *instruccion;
    const char *operandos[2];
    int total;
    int cantidad = 0;

    if (lineas == NULL || max <= 0 ||
        a == NULL || etiqueta == NULL) {
        return 0;
    }

    if (op == '&') {
        instruccion = "IFFALSE";
    } 
    else if (op == '|') {
        instruccion = "IFTRUE";
    } 
    else {
        return 0;
    }

    operandos[0] = a;
    operandos[1] = b;

    total = (b == NULL) ? 1 : 2;

    for (int i = 0; i < total && i < max; i++) {

        int escritos = snprintf(
            lineas[i], 64,
            "%s %s GOTO %s",
            instruccion, operandos[i], etiqueta
        );

        if (escritos < 0 || escritos >= 64) {
            lineas[i][0] = '\0';
            break;
        }

        cantidad++;
    }

    return cantidad;
}
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Condiciones compuestas con AND y OR


                                 




* [ ] Caso base y límites superados sin *Crash*.
* [ ] No existen accesos fuera de límites ni *Memory Leaks*.
* [ ] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> 

---
**Firma digital:** [Usuario de GitHub]
