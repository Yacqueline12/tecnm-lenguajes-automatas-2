# Bitácora de Requerimientos y Auditoría IA — Sesión 23
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Etiquetas y Saltos Incondicionales  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347]  
**Track:** [ C | Rust ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Generador de etiquetas e instrucciones goto  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> Tuve dificultad para convertir el número del contador a texto y unirlo con la letra L para generar las etiquetas. También tuve que cuidar el espacio disponible en los arreglos y utilizar condiciones para diferenciar cuándo escribir GOTO y cuándo colocar la etiqueta con dos puntos.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Trazabilidad del flujo de control |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contexto: estoy trabajando en la materia de Lenguajes y Autómatas II y necesito realizar la sesión 23, que trata sobre etiquetas y saltos incondicionales en lenguaje C. En esta actividad estoy desarrollando dos funciones que permiten generar etiquetas y escribir instrucciones GOTO.

Rol: quiero que actúes como un programador con experiencia en lenguaje C y me ayudes a realizar una implementación que sea fácil de comprender, que esté bien organizada y que cumpla con lo que solicita la actividad.

Especificación: necesito implementar las funciones `nueva_etiqueta()` y `emitir_flujo()` respetando exactamente las firmas que aparecen en el archivo `api_sesion23.h`.
La primera función debe generar etiquetas consecutivas comenzando desde el número que tenga el contador, por ejemplo L0, L1, L2, y aumentar el contador después de generar cada etiqueta.
La segunda función debe escribir `GOTO` seguido del nombre de la etiqueta cuando el valor de `es_salto` sea diferente de cero. Cuando sea cero, debe escribir el nombre de la etiqueta seguido de dos puntos.
Quiero que se revise que las cadenas no sobrepasen el tamaño del espacio disponible y que una etiqueta NULL produzca una cadena vacía. El código no debe contener una función `main()` porque será ejecutado junto con el programa de pruebas.

Output: quiero recibir el código completo en lenguaje C, con las dos funciones implementadas, comentarios que expliquen su funcionamiento y que pueda compilarse utilizando las opciones `-Wall -Wextra -Werror` sin presentar advertencias.
]
```

---

## 3. Código Devuelto por el Modelo
```c
%%writefile ia_sesion23_c.c
/* ia_sesion23_c.c - Fase 2: El Cientifico
 *
 * Pega aqui el codigo que te devolvio la IA, con la MISMA firma
 * que declara api_sesion23.h. No puede traer main().
 */
#include "api_sesion23.h"
#include <stdio.h>

void nueva_etiqueta(char *dst, int cap, int *contador)
{
    if (dst == NULL || cap <= 0)
        return;

    dst[0] = '\0';

    if (contador == NULL)
        return;

    snprintf(dst, (size_t)cap, "L%d", *contador);

    ++(*contador);
}

void emitir_flujo(char *dst, int cap, const char *etiqueta, int es_salto)
{
    if (dst == NULL || cap <= 0)
        return;

    dst[0] = '\0';

    if (etiqueta == NULL)
        return;

    if (es_salto != 0)
    {
        snprintf(dst, (size_t)cap, "GOTO %s", etiqueta);
    }
    else
    {
        snprintf(dst, (size_t)cap, "%s:", etiqueta);
    }
}
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Saltos adelante y atrás estructurados
                                             
                  === AUDITORIA SESION 23 - Etiquetas y saltos incondicionales ===
          Caso                     esperado:            obtenido:       resultado
    [ 1] nueva_etiqueta(L0)        esperado: L0         obtenido: L0        OK
    [ 2] nueva_etiqueta(L5)        esperado: L5         obtenido: L5        OK
    [ 3] emitir_flujo(GOTO L2)     esperado: GOTO L2    obtenido: GOTO L2   OK
    [ 4] emitir_flujo(marca L2)    esperado: L2:        obtenido: L2:       OK
    [ 5] emitir_flujo(NULL)        esperado:            obtenido:           OK
    [ 6] nueva_etiqueta(L10)       esperado: L10        obtenido: L10       OK

    RESULTADO: 6/6 pruebas superadas
    TODO EN VERDE. Documenta tu auditoria en auditorias/auditoria_sesion23.md

* [✓] Caso base y límites superados sin *Crash*.
* [✓] No existen accesos fuera de límites ni *Memory Leaks*.
* [✓] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> Identifiqué que la IA inicialmente no contemplaba cuando el contador recibía un valor NULL, lo que podía provocar un error. Para solucionarlo agregué una condición que verifica que el contador sea válido antes de utilizarlo. También revisé que se respetara el tamaño de los arreglos y que las etiquetas NULL generaran una cadena vacía.

---
**Firma digital:** [Yacqueline12]
