# Bitácora de Requerimientos y Auditoría IA — Sesión 25
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Control de Flujo: Bucles while y for  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347]  
**Track:** [ C | Rust ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Estructura de ciclo en instrucciones TAC  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> Al realizar el código manualmente, se me complicó organizar correctamente las instrucciones de los ciclos while y for, principalmente al colocar las etiquetas de inicio y fin. También tuve que cuidar que cada línea no superara los 64 caracteres y que el arreglo tuviera espacio suficiente para guardar todas las instrucciones. Otro detalle fue actualizar correctamente el contador de líneas y las etiquetas para evitar que se repitieran.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Garantía de actualización de iterador |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contexto: estoy realizando la práctica de Lenguajes y Autómatas II sobre los ciclos while y for. Necesito desarrollar dos funciones en lenguaje C que permitan representar estos ciclos mediante instrucciones TAC. Primero realicé una versión manual y ahora quiero mejorarla utilizando inteligencia artificial.

Rol: quiero que actúes como un programador especializado en lenguaje C y compiladores, que me ayude a desarrollar una solución clara, ordenada y fácil de comprender.

Especificación: necesito implementar las funciones `emitir_while()` y `emitir_for()` respetando las firmas establecidas en `api_sesion25.h`.

La función while debe generar cuatro instrucciones: etiqueta de inicio, salto condicional con IFFALSE, salto GOTO para regresar al inicio y etiqueta de fin.

La función for debe generar la inicialización cuando no sea NULL, la etiqueta de inicio, la condición, la actualización cuando no sea NULL, el salto de regreso y la etiqueta final.

Cada ciclo debe utilizar dos etiquetas nuevas para evitar que se repitan. También se debe respetar el límite máximo del arreglo, utilizar un máximo de 64 caracteres por línea, validar los parámetros y actualizar correctamente los contadores.

El código debe compilar con `gcc -std=c11 -Wall -Wextra -Werror`, respetar las firmas originales y no incluir una función `main()`.

Output: quiero recibir el código completo para el archivo `ia_sesion25_c.c`, con comentarios que expliquen su funcionamiento. La implementación debe utilizar una estructura diferente a la versión manual y cumplir con todos los requisitos para superar las seis pruebas del harness.]
```

---

## 3. Código Devuelto por el Modelo
```c
%%writefile ia_sesion25_c.c
/* ia_sesion25_c.c - Fase 2: El Cientifico
 *
 * Pega aqui el codigo que te devolvio la IA, con la MISMA firma
 * que declara api_sesion25.h. No puede traer main().
 */
#include "api_sesion25.h"
#include <stdio.h>
#include <stdarg.h>
#include <string.h>

static int agregar_linea(char lineas[][64], int max, int *n,
                         const char *formato, ...) {

    char texto[64];
    int resultado;
    va_list argumentos;

    if (lineas == NULL || n == NULL || formato == NULL ||
        *n < 0 || *n >= max) {
        return 0;
    }

    va_start(argumentos, formato);
    resultado = vsnprintf(texto, sizeof(texto), formato, argumentos);
    va_end(argumentos);

    if (resultado < 0 || resultado >= 64) {
        return 0;
    }

    memcpy(lineas[*n], texto, (size_t)resultado + 1);
    (*n)++;

    return 1;
}

void emitir_while(char lineas[][64], int max, int *n,
                  const char *cond, int *etiquetas) {

    int inicio, fin;

    if (lineas == NULL || n == NULL || cond == NULL ||
        etiquetas == NULL || max < 0 || *n < 0 || *n > max) {
        return;
    }

    if (max - *n < 4) {
        return;
    }

    inicio = *etiquetas;
    fin = inicio + 1;

    if (!agregar_linea(lineas, max, n, "L%d:", inicio) ||
        !agregar_linea(lineas, max, n,
                       "IFFALSE %s GOTO L%d", cond, fin) ||
        !agregar_linea(lineas, max, n, "GOTO L%d", inicio) ||
        !agregar_linea(lineas, max, n, "L%d:", fin)) {
        return;
    }

    *etiquetas += 2;
}

void emitir_for(char lineas[][64], int max, int *n,
                const char *inicio, const char *cond,
                const char *post, int *etiquetas) {

    int primera, ultima;
    int necesarias = 4;

    if (lineas == NULL || n == NULL || cond == NULL ||
        etiquetas == NULL || max < 0 || *n < 0 || *n > max) {
        return;
    }

    if (inicio != NULL) {
        necesarias++;
    }

    if (post != NULL) {
        necesarias++;
    }

    if (max - *n < necesarias) {
        return;
    }

    primera = *etiquetas;
    ultima = primera + 1;

    if ((inicio != NULL &&
         !agregar_linea(lineas, max, n, "%s", inicio)) ||
        !agregar_linea(lineas, max, n, "L%d:", primera) ||
        !agregar_linea(lineas, max, n,
                       "IFFALSE %s GOTO L%d", cond, ultima) ||
        (post != NULL &&
         !agregar_linea(lineas, max, n, "%s", post)) ||
        !agregar_linea(lineas, max, n, "GOTO L%d", primera) ||
        !agregar_linea(lineas, max, n, "L%d:", ultima)) {
        return;
    }

    *etiquetas += 2;
}

```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Bucles anidados y condiciones de parada

                  === AUDITORIA SESION 25 - Esqueleto TAC de while y for ===
          Caso                        esperado:            obtenido:         resultado
    [ 1] while: marca de inicio       esperado: L0:        obtenido: L0:         OK
    [ 2] while: condicion
     esperado: IFFALSE a < b GOTO L1
     obtenido: IFFALSE a < b GOTO L1
     -> OK
     [ 3] dos ciclos sin colision      esperado: L2:        obtenido: L2:        OK
     [ 4] for: inicializacion          esperado: i = 0      obtenido: i = 0      OK
     [ 5] for: actualizacion           esperado: i = i + 1  obtenido: i = i + 1  OK
     [ 6] for sin post (NULL)          esperado: 5          obtenido: 5          OK

     RESULTADO: 6/6 pruebas superadas
     TODO EN VERDE. Documenta tu auditoria en auditorias/auditoria_sesion25.md


* [✓] Caso base y límites superados sin *Crash*.
* [✓] No existen accesos fuera de límites ni *Memory Leaks*.
* [✓] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> Al revisar el código generado por la IA, identifiqué que no se comprobaba directamente el caso en el que el argumento inicio del ciclo for fuera NULL. Este caso es importante porque la inicialización es opcional y no debe agregarse ninguna instrucción cuando no se proporciona. Para solucionarlo, se agregó una condición que verifica si inicio es diferente de NULL antes de generar esa línea. También se revisó el espacio disponible en el arreglo para evitar escribir fuera de sus límites.

---
**Firma digital:** [Yacqueline12]
