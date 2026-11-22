# Bitácora de Requerimientos y Auditoría IA — Sesión 19
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Estructuras (struct) y Miembros  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347]  
**Track:** [C]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Offsets de miembros en estructuras  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:* 
> Al realizar la implementación manualmente, la principal dificultad fue comprender cómo se calculan los offsets de cada campo dentro de una estructura. Tuve que tomar en cuenta que algunos tipos de datos requieren alineación y que, en ciertos casos, es necesario agregar padding para que el siguiente campo quede en una posición válida de memoria. También tuve que verificar cómo obtener correctamente el tamaño total de la estructura considerando el relleno final.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Alineación de memoria por palabras |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contexto: necesito implementar dos funciones en lenguaje C para una práctica de Lenguajes y Autómatas II relacionada con estructuras (struct). Una función debe calcular el offset de un campo dentro de la estructura considerando alineación y padding. La otra debe calcular el tamaño total de la estructura incluyendo el padding final.

Rol: actúa como un ingeniero de software especializado en programación de sistemas y organización de memoria en lenguaje C.

Especificación: Se deben implementar las funciones offset_campo() y tamano_struct() respetando las firmas indicadas. offset_campo() debe calcular la posición de un campo dentro de una estructura considerando alineación y padding, mientras que tamano_struct() debe calcular el tamaño total de la estructura. Los tipos válidos son char, short, int, float y double. Si existe un tipo desconocido, una lista NULL o un campo inexistente, se debe devolver -1. Si la estructura no tiene campos, debe devolver 0. El tamaño final debe incluir el padding necesario y redondearse según la alineación más grande. No debe incluirse main() y el código debe compilar sin advertencias con -Wall -Wextra -Werror.

Output: devuelve únicamente el código completo para el archivo ia_sesion19_c.c.
]
```

---

## 3. Código Devuelto por el Modelo
```c
%%writefile ia_sesion19_c.c
/* ia_sesion19_c.c - Fase 2: El Cientifico
 *
 * Pega aqui el codigo que te devolvio la IA, con la MISMA firma
 * que declara api_sesion19.h. No puede traer main().
 */
#include "api_sesion19.h"
#include <string.h>

static int tam_tipo(const char *tipo)
{
    if (strcmp(tipo, "char") == 0) return 1;
    if (strcmp(tipo, "short") == 0) return 2;
    if (strcmp(tipo, "int") == 0) return 4;
    if (strcmp(tipo, "float") == 0) return 4;
    if (strcmp(tipo, "double") == 0) return 8;
    return -1;
}

static int alinear(int valor, int alineacion)
{
    if (valor % alineacion == 0)
        return valor;

    return valor + (alineacion - (valor % alineacion));
}

int offset_campo(const Campo *campos, int total, const char *nombre)
{
    int offset = 0;
    int i;

    if (campos == NULL || nombre == NULL)
        return -1;

    for (i = 0; i < total; i++)
    {
        int tam = tam_tipo(campos[i].tipo);

        if (tam < 0)
            return -1;

        offset = alinear(offset, tam);

        if (strcmp(campos[i].nombre, nombre) == 0)
            return offset;

        offset += tam;
    }

    return -1;
}

int tamano_struct(const Campo *campos, int total)
{
    int offset = 0;
    int mayor = 0;
    int i;

    if (campos == NULL)
        return -1;

    if (total == 0)
        return 0;

    for (i = 0; i < total; i++)
    {
        int tam = tam_tipo(campos[i].tipo);

        if (tam < 0)
            return -1;

        if (tam > mayor)
            mayor = tam;

        offset = alinear(offset, tam);
        offset += tam;
    }

    return alinear(offset, mayor);
}
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Acceso a miembros inexistentes

          === AUDITORIA SESION 19 - Offsets de miembros y tamano de struct ===
          Caso                       esperado:            obtenido:         resultado
      [ 1] struct char e int: b      esperado: 4          obtenido: 4           OK
      [ 2] struct de tres campos: c  esperado: 8          obtenido: 8           OK
      [ 3] tamano total con padding  esperado: 12         obtenido: 12          OK
      [ 4] double seguido de char    esperado: 16         obtenido: 16          OK
      [ 5] miembro inexistente       esperado: -1         obtenido: -1          OK
      [ 6] struct sin campos         esperado: 0          obtenido: 0           OK
      
      RESULTADO: 6/6 pruebas superadas
      TODO EN VERDE. Documenta tu auditoria en auditorias/auditoria_sesion19.md

* [✓] Caso base y límites superados sin *Crash*.
* [✓] No existen accesos fuera de límites ni *Memory Leaks*.
* [✓] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> La IA resolvió correctamente los casos incluidos en el harness. Sin embargo, noté que no se probó de forma explícita el caso de un tipo de dato desconocido dentro de la lista de campos. Para verificar este escenario realicé una prueba adicional utilizando un tipo no válido y comprobé que las funciones devolvieran -1 como lo indica la especificación. Gracias a esta verificación confirmé que la implementación también maneja adecuadamente entradas inválidas que no fueron evaluadas por las pruebas principales.

---
**Firma digital:** [Yacqueline12]
