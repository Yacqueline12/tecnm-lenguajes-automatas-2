# Bitácora de Requerimientos y Auditoría IA — Sesión 09
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Reporte Formal de Errores Semánticos  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347]  
**Track:** [ C ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Diagnóstico con línea y columna  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> Al realizar la implementación de forma manual, la principal dificultad fue asegurar que el mensaje se generara exactamente con el formato solicitado y sin exceder el tamaño del buffer, también tuve que verificar que la cadena terminara correctamente con '\0' para evitar problemas de memoria y que la función funcionara correctamente incluso cuando la capacidad del buffer fuera pequeña.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Estructura estándar de errores GCC |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contenido: estoy realizando una práctica de Lenguajes y Autómatas II, tengo que crear una función en C que genere mensajes de error similares a los que muestran compiladores como GCC, la función recibe el nombre de un archivo, una línea, una columna y un mensaje de error, y debe construir una cadena con un formato específico dentro de un buffer proporcionado por el usuario.

Rol: actúa como un programador con experiencia en lenguaje C y buenas prácticas de manejo seguro de cadenas.

Especificación: necesito implementar una función en C con la firma void reportar(const char *archivo, int linea, int columna, const char *mensaje, char *salida, int cap); que genere un mensaje con el formato archivo:linea:columna: error: mensaje, por ejemplo main.c:14:5: error: tipo incompatible. La implementación debe conservar exactamente la firma indicada, no incluir la función main(), utilizar funciones seguras como snprintf para evitar desbordamientos, respetar el límite de caracteres definido por cap, garantizar que la cadena resultante termine siempre en '\0' y compilar correctamente sin advertencias utilizando las opciones -Wall -Wextra -Werror.

Output: devuelve únicamente el contenido completo de un archivo .c listo para compilar, sin explicaciones adicionales.]
```

---

## 3. Código Devuelto por el Modelo
```c
%%writefile ia_sesion09_c.c
/* ia_sesion09_c.c - Fase 2: El Cientifico
 *
 * Pega aqui el codigo que te devolvio la IA, con la MISMA firma
 * que declara api_sesion09.h. No puede traer main().
 */

#include "api_sesion09.h"
#include <stdio.h>

void reportar(const char *archivo,
              int linea,
              int columna,
              const char *mensaje,
              char *salida,
              int cap)
{
    if (cap > 0 && salida != NULL)
    {
        int escritos;

        escritos = snprintf(
            salida,
            (size_t)cap,
            "%s:%d:%d: error: %s",
            archivo,
            linea,
            columna,
            mensaje
        );

        if (escritos < 0)
        {
            salida[0] = '\0';
        }
    }
}

```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Múltiples errores sin abortar parser

=== AUDITORIA SESION 09 - Diagnostico con linea y columna ===

Caso                               esperado:              obtenido:   
resultado

[ 1] main.c:14:5
     esperado: main.c:14:5: error: tipo incompatible
     obtenido: main.c:14:5: error: tipo incompatible
     -> OK
     
[ 2] a.c:1:1
     esperado: a.c:1:1: error: x
     obtenido: a.c:1:1: error: x
     -> OK
     
[ 3] linea 0
     esperado: x.c:0:0: error: y
     obtenido: x.c:0:0: error: y
     -> OK
     
[ 4] cap=10 trunca                      esperado: main.c:14    obtenido: main.c:14    OK

RESULTADO: 4/4 pruebas superadas

TODO EN VERDE. Documenta tu auditoria en auditorias/auditoria_sesion09.md

* [ ] Caso base y límites superados sin *Crash*.
* [ ] No existen accesos fuera de límites ni *Memory Leaks*.
* [ ] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> La IA generó el código, pero fue necesario que lo revisara 

---
**Firma digital:** [Usuario de GitHub]
