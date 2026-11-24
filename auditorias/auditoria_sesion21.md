# Bitácora de Requerimientos y Auditoría IA — Sesión 21
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Fundamentos de Código de 3 Direcciones  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347]  
**Track:** [ C | Rust ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Representación en Cuádruplos (op,a,b,r)  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> Al realizar la implementación manualmente, la principal dificultad fue comprender cómo generar temporales sin repetir nombres y actualizar correctamente el contador después de cada uso. También tuve que revisar la forma correcta de construir el texto de un cuádruplo respetando el formato solicitado y considerando los casos donde algunos argumentos pueden venir como NULL y deben mostrarse con un guion bajo.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Generación de temporales únicos (_t0..) |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contexto: necesito implementar dos funciones en lenguaje C para una práctica de Lenguajes y Autómatas II relacionada con la generación de código de tres direcciones. Una función debe generar nombres de temporales únicos y la otra debe construir cuádruplos utilizando los operandos recibidos.

Rol: actúa como un programador especializado en compiladores y generación de código intermedio en lenguaje C.

Especificación: se deben implementar las funciones nuevo_temporal() y emitir_cuadruplo() respetando exactamente las firmas proporcionadas. nuevo_temporal() debe generar nombres con el formato _t0, _t1, _t2, etc., utilizando el valor actual del contador y después incrementándolo. emitir_cuadruplo() debe escribir el cuádruplo con el formato (op, a1, a2, res).
Si alguno de los operandos o el resultado es NULL, se debe escribir el carácter _ en su lugar. Las funciones deben escribir siempre en el buffer recibido sin reservar memoria dinámica ni utilizar variables globales. No debe incluirse la función main() y el código debe compilar correctamente con -Wall -Wextra -Werror.

Output: devuelve únicamente el código completo para el archivo ia_sesion21_c.c.]
```

---

## 3. Código Devuelto por el Modelo
```c
%%writefile ia_sesion21_c.c
/* ia_sesion21_c.c - Fase 2: El Cientifico
 *
 * Pega aqui el codigo que te devolvio la IA, con la MISMA firma
 * que declara api_sesion21.h. No puede traer main().
 */
#include "api_sesion21.h"
#include <stdio.h>

void nuevo_temporal(char *dst, int cap, int *contador)
{
    if (dst == NULL || contador == NULL)
        return;

    snprintf(dst, (size_t)cap, "_t%d", *contador);
    *contador = *contador + 1;
}

void emitir_cuadruplo(char *dst,
                      int cap,
                      const char *op,
                      const char *a1,
                      const char *a2,
                      const char *res)
{
    snprintf(dst,
             (size_t)cap,
             "(%s, %s, %s, %s)",
             op ? op : "_",
             a1 ? a1 : "_",
             a2 ? a2 : "_",
             res ? res : "_");
}
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Expresiones aritméticas compuestas
                               
                === AUDITORIA SESION 21 - Cuadruplos y temporales unicos ===
        Caso                        esperado:          obtenido:        resultado
        [ 1] nuevo_temporal(_t0)    esperado: _t0      obtenido: _t0        OK
        [ 2] nuevo_temporal(_t7)    esperado: _t7      obtenido: _t7        OK
        [ 3] cuadruplo de a + b
     esperado: (+, a, b, _t0)
     obtenido: (+, a, b, _t0)
     -> OK
     [ 4] cuadruplo de _t0 * c
     esperado: (*, _t0, c, _t1)
     obtenido: (*, _t0, c, _t1)
     -> OK
     [ 5] asignacion simple         esperado: (=, a, _, x)   obtenido: (=, a, _, x) OK
     [ 6] goto sin operandos
     esperado: (goto, L1, _, _)
     obtenido: (goto, L1, _, _)
     -> OK
     
     RESULTADO: 6/6 pruebas superadas
     TODO EN VERDE. Documenta tu auditoria en auditorias/auditoria_sesion21.md

* [✓] Caso base y límites superados sin *Crash*.
* [✓] No existen accesos fuera de límites ni *Memory Leaks*.
* [✓] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> Al revisar observé que no se probó explícitamente el caso en el que el primer operando también fuera NULL. Para verificar este comportamiento realicé pruebas adicionales y confirmé que la función reemplaza correctamente cualquier argumento faltante por el carácter "_" manteniendo el formato del cuádruplo. De esta manera comprobé que la implementación funciona correctamente incluso en escenarios no evaluados por el harness principal.

---
**Firma digital:** [Yacqueline12]
