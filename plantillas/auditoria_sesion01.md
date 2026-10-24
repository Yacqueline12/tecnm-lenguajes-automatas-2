# Bitácora de Requerimientos y Auditoría IA — Sesión 01
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Encuadre, Diagnóstico de Punteros y Metodología IA  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo] . 
**Matrícula:** [24531347]  
**Track:** [ C ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Inversión de buffer in-place (char*)  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> Al realizar este ejercicio, se me dificulto un poco porque todavía no se usar muy bien GitHub y me confundí un poco al momento de trabajar con los archivos y el código. También tuve dudas sobre cómo invertir la cadena sin crear otra. Poco a poco fui entendiendo cómo hacerlo y cómo comprobar que el programa funcionara correctamente.
---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Punteros, O(n) tiempo, O(1) memoria |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contexto: Sesión 01 de Lenguajes y Autómatas II. Invertir una cadena en C sin memoria adicional.

Rol: Actúa como experto en C y manejo de punteros.

Especificación: Implementa void invertir(char *s); según api_sesion01.h. Invierte la cadena en el mismo buffer, maneja NULL y cadenas vacías, no uses malloc ni arreglos auxiliares y compila sin advertencias. No incluyas main().

Output: Entrega el código completo de ia_sesion01_c.c listo para Google Colab con %%writefile y explica brevemente su funcionamiento.]
```

---

## 3. Código Devuelto por el Modelo
```c

%%writefile artesano_sesion01_c.c
#include "api_sesion01.h"
#include <string.h>

void invertir(char *s) {
    size_t izquierda = 0;
    size_t derecha;
    char temporal;

    if (s == NULL) {
        return;
    }

    derecha = strlen(s);

    if (derecha == 0) {
        return;
    }

    derecha--;

    while (izquierda < derecha) {
        temporal = s[izquierda];
        s[izquierda] = s[derecha];
        s[derecha] = temporal;

        izquierda++;
        derecha--;
    }
}
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Valores NULL, buffers vacios y longitudes impares
=== AUDITORIA SESION 01 - Inversion de buffer in-place ===
Caso                               esperado:              obtenido:          resultado
[ 1] invertir("HOLA")               esperado: ALOH         obtenido: ALOH         OK
[ 2] invertir("abc")                esperado: cba          obtenido: cba          OK
[ 3] invertir("")                   esperado:              obtenido:              OK
[ 4] invertir("AB")                 esperado: BA           obtenido: BA           OK
[ 5] invertir("a")                  esperado: a            obtenido: a            OK
[ 6] invertir(NULL)                 esperado: sin crash    obtenido: sin crash    OK

RESULTADO: 6/6 pruebas superadas
TODO EN VERDE. Documenta tu auditoria en auditorias/auditoria_sesion01.md

* [✓] Caso base y límites superados sin *Crash*.
* [✓] No existen accesos fuera de límites ni *Memory Leaks*.
* [✓] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> Al revisar el código de la IA, me fijé en que era importante comprobar qué pasaba cuando la cadena estaba vacía o tenía el valor NULL, porque podía causar errores si no se revisaban esos casos. Para evitarlo, se agregó una condición para que el programa terminara sin hacer cambios cuando la cadena estuviera vacía o fuera NULL. Después ejecuté las pruebas en Google Colab y todas pasaron correctamente, obteniendo un resultado de 6/6 pruebas superadas.

---
**Firma digital:** [YACQUELINECANCHE]
