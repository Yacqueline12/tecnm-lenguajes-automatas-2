# Bitácora de Requerimientos y Auditoría IA — Sesión 17
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Comprobación Semántica de Retornos  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347]  
**Track:** [ C ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Validación de tipo de sentencia return  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> En la implementación de forma manual, la principal dificultad fue definir el orden correcto de las validaciones. Primero tuve que comprobar los datos inválidos, después la compatibilidad entre tipos y finalmente verificar si todas las rutas de ejecución tenían una instrucción return. También tuve que analizar cuándo una conversión era válida, por ejemplo que una función double puede aceptar un valor int, pero una función int no debe aceptar un valor double. Además, tuve cuidado de no agregar una función main() porque el harness proporciona la suya y de lo contrario produciría errores de compilación.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Rutas de ejecución con retorno garantizado |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contexto: necesito implementar en lenguaje C una función que revise si el tipo de retorno de una función es válido y además verificar que todas las rutas posibles de ejecución terminan con una instrucción return.

Rol: actúa como un programador con experiencia en C y análisis semántico de compiladores. Debes generar una solución clara, correcta y compatible con un harness de pruebas.

Especificación: crea la función:

int*diagnosticar_retorno(

const ch*r *tipo_funcion,

const char *t*po_expresion,

int rutas_totale*,

int rutas_con_return

);

Reglas:
*   Regresar 0 si todo es correcto.
*   Regresar 1 si existe incompatibilidad de tipos.
*   Regresar 2 si alguna ruta de ejecución no tiene return.
*   Regresar -1 si los parámetros son inválidos.
*   Una función void solo acepta expresión void.
*   double puede aceptar int.
*   int no puede aceptar double.
*   No agregar función main.
*   Usar la misma firma indicada.


Output: devuelve únicamente el código completo de la implementación en C listo para compilar con gcc -Wall -Wextra -Werror.]
```

---

## 3. Código Devuelto por el Modelo
```c
%%writefile ia_sesion17_c.c
/* ia_sesion17_c.c - Fase 2: El Cientifico
 *
 * Pega aqui el codigo que te devolvio la IA, con la MISMA firma
 * que declara api_sesion17.h. No puede traer main().
 */
#include "api_sesion17.h"
#include <string.h>

#include "api_sesion17.h"
#include <string.h>

static int nivel_tipo(const char *tipo)
{
    if (strcmp(tipo, "void") == 0)
        return 0;

    if (strcmp(tipo, "int") == 0)
        return 1;

    if (strcmp(tipo, "double") == 0)
        return 2;

    return -1;
}

int diagnosticar_retorno(const char *tipo_funcion,
                         const char *tipo_expresion,
                         int rutas_totales,
                         int rutas_con_return)
{
    int destino;
    int origen;

    if (tipo_funcion == NULL || tipo_expresion == NULL)
        return -1;

    if (rutas_totales <= 0 || rutas_con_return > rutas_totales)
        return -1;

    destino = nivel_tipo(tipo_funcion);
    origen = nivel_tipo(tipo_expresion);

    if (destino < 0 || origen < 0)
        return 1;

    if (destino == 0)
    {
        if (origen == 0)
            return 0;
        return 1;
    }

    if (origen > destino)
        return 1;

    if (rutas_con_return != rutas_totales)
        return 2;

    return 0;
}
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Funciones sin retorno o retornos mixtos



                === AUDITORIA SESION 17 - Validacion de retornos y rutas ===
                Caso                 esperado:            obtenido:         resultado
      [ 1] int con int y dos rutas   esperado: 0          obtenido: 0            OK
      [ 2] void devuelve valor       esperado: 1          obtenido: 1            OK
      [ 3] ruta sin retorno          esperado: 2          obtenido: 2            OK
      [ 4] double acepta int         esperado: 0          obtenido: 0            OK
      [ 5] int no acepta double      esperado: 1          obtenido: 1            OK
      [ 6] cero rutas de ejecucion   esperado: -1         obtenido: -1           OK

      RESULTADO: 6/6 pruebas superadas
      TODO EN VERDE. Documenta tu auditoria en auditorias/auditoria_sesion17.md

* [✓] Caso base y límites superados sin *Crash*.
* [✓] No existen accesos fuera de límites ni *Memory Leaks*.
* [✓] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> Durante la revisión observé que una posible implementación generada por IA podía enfocarse únicamente en validar la compatibilidad de tipos y la presencia de retornos, pero pasar por alto algunos casos inválidos de entrada. El caso borde más importante fue cuando rutas_totales era igual a cero, situación que debe devolver -1 según el contrato de la práctica. También revisé la validación donde rutas_con_return es mayor que rutas_totales, ya que representa una condición incoherente. Para verificarlo utilicé el harness proporcionado y comparé los resultados obtenidos con los valores esperados hasta lograr que las seis pruebas fueran superadas correctamente.

---
**Firma digital:** [Yacqueline12]
