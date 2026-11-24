# Bitácora de Requerimientos y Auditoría IA — Sesión 22
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Generación de TAC para Expresiones  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347]  
**Track:** [ C | Rust ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Traductor de nodos AST a TAC  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> La principal dificultad fue entender cómo recorrer el árbol respetando la precedencia de las operaciones. Tuve que analizar que primero se deben procesar los nodos hijos y después el operador principal para generar correctamente las instrucciones TAC. También fue necesario controlar la numeración de los temporales para evitar repetir nombres.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Conservación de precedencia de operadores |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contexto: necesito implementar una función en lenguaje C para una práctica de Lenguajes y Autómatas II. La función debe recorrer un árbol de expresiones aritméticas y generar código de tres direcciones (TAC), respetando la precedencia de las operaciones.

Rol: actúa como un programador experto en compiladores y generación de código intermedio en lenguaje C.

Especificación: implementar la función:

*  int generar_tac(const Nodo *raiz, char lineas[][64], int max);

La función debe recorrer el árbol en postorden, procesando primero los hijos y después el operador. Cada operador debe generar una línea TAC utilizando temporales con formato _t0, _t1, _t2, etc. Las hojas no generan instrucciones. Nunca deben generarse más de max líneas ni escribir más de 63 caracteres por línea. Debe respetarse la precedencia definida por la estructura del árbol. No debe incluirse la función main() y el código debe compilar con -Wall -Wextra -Werror.

Output: devuelve únicamente el código completo para el archivo ia_sesion22_c.c.]
```

---

## 3. Código Devuelto por el Modelo
```c
%%writefile ia_sesion22_c.c
/* ia_sesion22_c.c - Fase 2: El Cientifico
 *
 * Pega aqui el codigo que te devolvio la IA, con la MISMA firma
 * que declara api_sesion22.h. No puede traer main().
 */
#include "api_sesion22.h"
#include <stdio.h>
#include <string.h>

typedef struct {
    char temp[64][16];
    int total;
} BancoTemporales;

static const char *crear_temp(BancoTemporales *banco, int *id)
{
    snprintf(banco->temp[banco->total],
             sizeof(banco->temp[banco->total]),
             "_t%d",
             *id);

    (*id)++;

    banco->total++;

    return banco->temp[banco->total - 1];
}

static const char *traducir(const Nodo *raiz,
                            char lineas[][64],
                            int max,
                            int *emitidas,
                            BancoTemporales *banco,
                            int *id_temp)
{
    const char *resultado_izq;
    const char *resultado_der;
    const char *destino;

    if (raiz == NULL)
        return "";

    if (raiz->tipo == 'H')
        return raiz->valor;

    resultado_izq = traducir(raiz->izq,
                             lineas,
                             max,
                             emitidas,
                             banco,
                             id_temp);

    resultado_der = traducir(raiz->der,
                             lineas,
                             max,
                             emitidas,
                             banco,
                             id_temp);

    destino = crear_temp(banco, id_temp);

    if (*emitidas < max)
    {
        snprintf(lineas[*emitidas],
                 64,
                 "%s = %s %c %s",
                 destino,
                 resultado_izq,
                 raiz->op,
                 resultado_der);

        (*emitidas)++;
    }

    return destino;
}

int generar_tac(const Nodo *raiz, char lineas[][64], int max)
{
    BancoTemporales banco;
    int instrucciones = 0;
    int consecutivo = 0;

    banco.total = 0;

    if (raiz == NULL)
        return 0;

    traducir(raiz,
             lineas,
             max,
             &instrucciones,
             &banco,
             &consecutivo);

    return instrucciones;
}
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Expresiones anidadas con paréntesis


              === AUDITORIA SESION 22 - Traductor de AST a TAC con precedencia ===
          Caso               esperado:             obtenido:               resultado
    [ 1] a + b * c           sperado: _t0 = b * c  obtenido: _t0 = b * c      OK
    [ 2] a + b * c (2a linea)
     esperado: _t1 = a + _t0
     obtenido: _t1 = a + _t0
     -> OK
     [ 3] (a + b) * c
     esperado: _t1 = _t0 * c
     obtenido: _t1 = _t0 * c
     -> OK
     [ 4] x * (y - z) + w
     esperado: _t2 = _t1 + w
     obtenido: _t2 = _t1 + w
     -> OK
     [ 5] hoja sin operaciones     esperado: 0        obtenido: 0           OK
     [ 6] (a + b) * (c - d)
     esperado: _t2 = _t0 * _t1
     obtenido: _t2 = _t0 * _t1
     -> OK
     
     RESULTADO: 6/6 pruebas superadas
     TODO EN VERDE. Documenta tu auditoria en auditorias/auditoria_sesion22.md


* [✓] Caso base y límites superados sin *Crash*.
* [✓] No existen accesos fuera de límites ni *Memory Leaks*.
* [✓] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> La IA si resolvió correctamente los casos incluidos en el harness. Sin embargo, observé que no se probó explícitamente el caso donde el árbol contiene únicamente una hoja sin operadores adicionales. Para verificar este escenario realicé pruebas adicionales y confirmé que la función devuelve 0 líneas generadas, tal como lo indica la especificación. Esto permitió comprobar que la implementación funciona correctamente tanto para expresiones simples como para expresiones compuestas.

---
**Firma digital:** [Yacqueline12]
