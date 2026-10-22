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
[Necesito una función en lenguaje C que se llame invertir(char *s) y que invierta una cadena en el mismo espacio de memoria, sin crear otra cadena ni utilizar malloc. Debe funcionar con cadenas de diferentes longitudes y conservar la misma firma de la función. También debe revisar si el valor recibido es NULL o si la cadena está vacía, para evitar errores. Utiliza una variable temporal para intercambiar los caracteres. El tiempo de ejecución debe ser O(n) y la memoria adicional O(1). Entrega el código y una explicación breve de cómo funciona.]
```

---

## 3. Código Devuelto por el Modelo
```c
// #include "api_sesion01.h"
#include <string.h>

void invertir(char *s) {
    if (s == NULL) {
        return;
    }

    size_t n = strlen(s);

    if (n == 0) {
        return;
    }

    size_t i = 0;
    size_t j = n - 1;

    while (i < j) {
        char temporal = s[i];
        s[i] = s[j];
        s[j] = temporal;

        i++;
        j--;
    }
}
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Valores NULL, buffers vacios y longitudes impares

* [✓] Caso base y límites superados sin *Crash*.
* [✓] No existen accesos fuera de límites ni *Memory Leaks*.
* [✓] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> Al revisar el código de la IA, me fijé en que era importante comprobar qué pasaba cuando la cadena estaba vacía o tenía el valor NULL, porque podía causar errores si no se revisaban esos casos. Para evitarlo, se agregó una condición para que el programa terminara sin hacer cambios cuando la cadena estuviera vacía o fuera NULL. Después ejecuté las pruebas en Google Colab y todas pasaron correctamente, obteniendo un resultado de 6/6 pruebas superadas.

---
**Firma digital:** [YACQUELINECANCHE]
