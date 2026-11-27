# Bitácora de Requerimientos y Auditoría IA — Sesión 26
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** TAC para Llamadas a Funciones  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347]  
**Track:** [ C | Rust ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Instrucciones param, call y return  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> Al realizar mi código manualmente, tuve un poco de dificultad para construir las instrucciones PARAM y CALL, porque tenía que cuidar que los argumentos se guardaran en el orden correcto y que el número de parámetros coincidiera con los que realmente se enviaban. También tuve que tener cuidado con el espacio disponible en los arreglos de caracteres, ya que cada instrucción tenía un límite de 64 caracteres. Para evitar errores, utilicé funciones auxiliares que me ayudaron a copiar los textos y construir las instrucciones poco a poco. También tuve que revisar que las llamadas anidadas conservaran sus temporales y que la instrucción RETURN pudiera generarse con o sin un valor.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Convención de paso de argumentos TAC |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contexto: estoy trabajando en Lenguajes y Autómatas II, utilizando el lenguaje C99 con GCC. Necesito desarrollar funciones que permitan representar llamadas a funciones y retornos mediante código de tres direcciones (TAC).

Rol: actúa como un ingeniero de software especializado en compiladores, programación en C y generación de código intermedio.

Especificación: desarrolla las funciones `emitir_llamada()` y `emitir_return()` respetando exactamente las firmas declaradas en `api_sesion26.h`.

La función `emitir_llamada()` debe:
- Generar una instrucción PARAM por cada argumento diferente de NULL.
- Mantener el orden de los argumentos.
- Generar CALL indicando la cantidad real de parámetros.
- Permitir llamadas sin argumentos.
- Permitir llamadas sin variable de resultado.
- Conservar correctamente el contador de instrucciones para soportar llamadas anidadas.
- Validar los límites del arreglo antes de escribir.

La función `emitir_return()` debe:
- Generar RETURN acompañado de un valor cuando exista.
- Generar únicamente RETURN cuando el valor sea NULL.
- Evitar escribir fuera de la capacidad del destino.

Output: entrega código modular en C99, sin función `main()`, compatible con `-Wall -Wextra -Werror`. Incluye una explicación de la lógica y un análisis de complejidad temporal y espacial.]
```

---

## 3. Código Devuelto por el Modelo
```c
%%writefile ia_sesion26_c.c
/* ia_sesion26_c.c - Fase 2: El Cientifico
 *
 * Pega aqui el codigo que te devolvio la IA, con la MISMA firma
 * que declara api_sesion26.h. No puede traer main().
 */
#include "api_sesion26.h"
#include <stdio.h>
#include <string.h>

int emitir_llamada(char lineas[][64], int max, int *n,
                   const char *funcion, const char *a1,
                   const char *a2, const char *resultado) {

    char instrucciones[3][64] = {{0}};
    int parametros = 0;
    int total = 0;
    int longitud;

    if (lineas == NULL || n == NULL || funcion == NULL ||
        max <= 0 || *n < 0 || *n > max) {
        return 0;
    }

    /* Se generan los parametros en el orden recibido */
    if (a1 != NULL) {
        longitud = snprintf(instrucciones[total], 64,
                            "PARAM %s", a1);

        if (longitud < 0 || longitud >= 64) {
            return 0;
        }

        total++;
        parametros++;
    }

    if (a2 != NULL) {
        longitud = snprintf(instrucciones[total], 64,
                            "PARAM %s", a2);

        if (longitud < 0 || longitud >= 64) {
            return 0;
        }

        total++;
        parametros++;
    }

    /* Se construye la instruccion de llamada */
    if (resultado != NULL) {
        longitud = snprintf(instrucciones[total], 64,
                            "%s = CALL %s, %d",
                            resultado, funcion, parametros);
    } else {
        longitud = snprintf(instrucciones[total], 64,
                            "CALL %s, %d",
                            funcion, parametros);
    }

    if (longitud < 0 || longitud >= 64) {
        return 0;
    }

    total++;

    /* Se comprueba la capacidad disponible */
    if (total > max - *n) {
        return 0;
    }

    /* Se transfieren las instrucciones al arreglo final */
    memcpy(&lineas[*n], instrucciones,
           (size_t)total * sizeof instrucciones[0]);

    *n += total;

    return total;
}

void emitir_return(char *dst, int cap, const char *valor) {

    int longitud;

    if (dst == NULL || cap <= 0) {
        return;
    }

    if (valor != NULL) {
        longitud = snprintf(dst, (size_t)cap,
                            "RETURN %s", valor);
    } else {
        longitud = snprintf(dst, (size_t)cap,
                            "RETURN");
    }

    if (longitud < 0 || longitud >= cap) {
        dst[0] = '\0';
    }
}
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Llamadas anidadas f(g(x))
                              
                  === AUDITORIA SESION 26 - Llamadas, parametros y retorno ===
              Caso           esperado:              obtenido:              resultado
      [ 1] PARAM x           esperado: PARAM x      obtenido: PARAM x         OK
      [ 2] PARAM y           esperado: PARAM y      obtenido: PARAM y         OK
      [ 3] CALL con dos argumentos
     esperado: _t0 = CALL f, 2
     obtenido: _t0 = CALL f, 2
     -> OK
     [ 4] llamada anidada f(g(x))
     esperado: _t1 = CALL f, 1
     obtenido: _t1 = CALL f, 1
     -> OK
     [ 5] CALL sin argumentos
     esperado: _t0 = CALL p, 0
     obtenido: _t0 = CALL p, 0
     -> OK
     [ 6] RETURN con valor    esperado: RETURN _t0    obtenido: RETURN _t0    OK


     RESULTADO: 6/6 pruebas superadas
     TODO EN VERDE. Documenta tu auditoria en auditorias/auditoria_sesion26.md


* [ ] Caso base y límites superados sin *Crash*.
* [✓] No existen accesos fuera de límites ni *Memory Leaks*.
* [✓] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> Al revisar el código que me proporcionó la inteligencia artificial, identifiqué que no se comprobaban directamente todos los casos relacionados con los límites de memoria. Por ejemplo, cuando el arreglo no tiene suficiente espacio para guardar los parámetros y la instrucción CALL, o cuando se intenta almacenar un texto demasiado largo. Para solucionar este problema revisé las condiciones antes de copiar las instrucciones y agregué validaciones para comprobar que existiera espacio suficiente. También consideré el caso en el que el valor de retorno fuera NULL, para que solamente se generara la instrucción RETURN. Con esto comprendí que no basta con que la inteligencia artificial genere el código, sino que también es necesario revisarlo y comprobar que funcione correctamente en diferentes situaciones.

---
**Firma digital:** [Yacqueline12]
