# Bitácora de Requerimientos y Auditoría IA — Sesión 20
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Hito 2: Analizador Semántico y Scopes  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347]  
**Track:** [ C | Rust ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Integración de Tabla de Símbolos y AST  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> Al realizar la implementación manualmente, la principal dificultad fue comprender cómo recorrer la cadena de ámbitos utilizando los punteros al ámbito padre. También tuve que considerar el caso de shadowing, donde un mismo nombre puede aparecer en varios niveles, pero siempre debe tomarse el más cercano. Para la función de visibilidad fue necesario evitar contar nombres repetidos para obtener el resultado correcto.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Contrato de Validación Semántica Integral |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contexto: necesito implementar dos funciones en lenguaje C para una práctica de Lenguajes y Autómatas II. La primera debe localizar un símbolo dentro de una cadena de ámbitos y devolver a qué profundidad se encuentra. La segunda debe contar los símbolos visibles desde el ámbito actual considerando shadowing.

Rol: actúa como un ingeniero especializado en compiladores, análisis semántico y tablas de símbolos en lenguaje C.

Especificación: se deben implementar las funciones profundidad_de() y contar_visibles() respetando exactamente las firmas proporcionadas. La función profundidad_de() debe buscar un nombre desde el ámbito actual hacia los ámbitos padre y devolver la profundidad en la que se encuentra. El ámbito más cercano siempre tiene prioridad. Debe devolver 0 si el nombre está en el ámbito actual, 1, 2 o más según la profundidad, -1 si el nombre no existe y -2 si alguno de los argumentos es NULL.
La función contar_visibles() debe contar todos los nombres visibles desde el ámbito actual sin repetirlos. Si un mismo nombre aparece en varios ámbitos debido al shadowing, solo debe contarse una vez, tomando en cuenta la declaración más cercana.
La implementación no debe incluir la función main(), ya que será compilada junto con el harness de pruebas. Además, el código debe compilar correctamente sin advertencias utilizando las opciones -Wall -Wextra -Werror.

Output: devuelve únicamente el código completo para el archivo ia_sesion20_c.c.]
```

---

## 3. Código Devuelto por el Modelo
```c
%%writefile ia_sesion20_c.c
/* ia_sesion20_c.c - Fase 2: El Cientifico
 *
 * Pega aqui el codigo que te devolvio la IA, con la MISMA firma
 * que declara api_sesion20.h. No puede traer main().
 */
#include "api_sesion20.h"
#include <string.h>

int profundidad_de(const Ambito *actual, const char *nombre)
{
    int profundidad = 0;

    if (actual == NULL || nombre == NULL)
        return -2;

    while (actual)
    {
        int i;

        for (i = 0; i < actual->total; i++)
        {
            if (strcmp(actual->simbolos[i], nombre) == 0)
                return profundidad;
        }

        actual = actual->padre;
        profundidad++;
    }

    return -1;
}

int contar_visibles(const Ambito *actual)
{
    const char *nombres[64];
    int total = 0;

    if (actual == NULL)
        return -2;

    while (actual)
    {
        int i;

        for (i = 0; i < actual->total; i++)
        {
            int existe = 0;
            int j;

            for (j = 0; j < total; j++)
            {
                if (strcmp(nombres[j], actual->simbolos[i]) == 0)
                {
                    existe = 1;
                    break;
                }
            }

            if (!existe)
            {
                nombres[total++] = actual->simbolos[i];
            }
        }

        actual = actual->padre;
    }

    return total;
}

```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Casos complejos de shadowing y ámbito

              === AUDITORIA SESION 20 - Resolucion de nombres y visibilidad ===
         Caso                          esperado:         obtenido:       resultado
    [ 1] nombre en el ambito actual    esperado: 0       obtenido: 0         OK
    [ 2] nombre dos niveles arriba     esperado: 2       obtenido: 2         OK
    [ 3] shadowing gana el interno     esperado: 0       obtenido: 0         OK
    [ 4] nombre no visible             esperado: -1      obtenido: -1        OK
    [ 5] visibles sin repetir          esperado: 3       obtenido: 3         OK
    [ 6] ambito NULL                   esperado: -2      obtenido: -2        OK
    
    RESULTADO: 6/6 pruebas superadas
    TODO EN VERDE. Documenta tu auditoria en auditorias/auditoria_sesion20.md

* [✓] Caso base y límites superados sin *Crash*.
* [✓] No existen accesos fuera de límites ni *Memory Leaks*.
* [✓] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> La IA resolvió correctamente los casos del harness. Sin embargo, observé que no se probó explícitamente el caso donde el parámetro nombre es NULL en la función profundidad_de(). Para verificar este escenario añadí una prueba adicional y comprobé que la función devolviera -2, tal como indica la especificación. De esta forma confirmé que la implementación maneja adecuadamente entradas inválidas además de los casos evaluados por el harness.

---
**Firma digital:** [Yacqueline12]
