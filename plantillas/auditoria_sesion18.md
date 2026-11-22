# Bitácora de Requerimientos y Auditoría IA — Sesión 18
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Arreglos y Verificación de Límites  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347]  
**Track:** [ C ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Cálculo de tamaño de arreglo en memoria  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> Al realizar la implementación manualmente, la principal dificultad fue identificar cuánto espacio ocupa cada tipo de dato y cómo calcular correctamente el tamaño total del arreglo multiplicando el número de elementos por el tamaño del tipo correspondiente. También tuve que cuidar que los tipos no válidos y las cantidades negativas de elementos devolvieran -1, además de verificar correctamente los límites para evitar acceder a posiciones fuera del rango permitido del arreglo.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Indexación entera no negativa |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contexto: necesito implementar dos funciones en lenguaje C para una práctica de Lenguajes y Autómatas II. La primera calcula los bytes necesarios para un arreglo según su tipo y cantidad de elementos. La segunda verifica si un índice está dentro de los límites válidos de un arreglo.

Rol: actúa como un programador experto en lenguaje C que sigue estrictamente las especificaciones proporcionadas.

Especificación: implementar:
long bytes_arreglo(const char *tipo, int elementos);
int indice_en_rango(int indice, int elementos);

Tipos válidos:

*   char
*   short
*   int
*   float
*   double
*  Si elementos es negativo devolver -1.
*  Si el tipo no existe devolver -1.
indice_en_rango debe devolver:
* 1 si 0 <= indice < elementos  
* 0 en cualquier otro caso. 
* No incluir función main(). 
* Debe compilar con: -Wall -Wextra -Werror

Output: devuelve únicamente el código completo del archivo ia_sesion18_c.c.
]
```

---

## 3. Código Devuelto por el Modelo
```c
%%writefile ia_sesion18_c.c
/* ia_sesion18_c.c - Fase 2: El Cientifico
 *
 * Pega aqui el codigo que te devolvio la IA, con la MISMA firma
 * que declara api_sesion18.h. No puede traer main().
 */
#include "api_sesion18.h"
#include <string.h>

long bytes_arreglo(const char *tipo, int elementos)
{
    long tamano = -1;

    if (elementos < 0)
        return -1;

    if (strcmp(tipo, "char") == 0)
        tamano = sizeof(char);
    else if (strcmp(tipo, "short") == 0)
        tamano = sizeof(short);
    else if (strcmp(tipo, "int") == 0)
        tamano = sizeof(int);
    else if (strcmp(tipo, "float") == 0)
        tamano = sizeof(float);
    else if (strcmp(tipo, "double") == 0)
        tamano = sizeof(double);
    else if (strcmp(tipo, "puntero") == 0)
        tamano = sizeof(void *);
    else
        return -1;

    return tamano * elementos;
}

int indice_en_rango(int indice, int elementos)
{
    return (indice >= 0 && indice < elementos);
}
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Límites estáticos y dimensiones inválidas

            === AUDITORIA SESION 18 - Tamano de arreglo y limites ===
          Caso                   esperado:            obtenido:          resultado
    [ 1] int[10]                 esperado: 40         obtenido: 40           OK
    [ 2] double[3]               esperado: 24         obtenido: 24           OK
    [ 3] int[-4]                 esperado: -1         obtenido: -1           OK
    [ 4] indice 0 de 5           esperado: 1          obtenido: 1            OK
    [ 5] indice 5 de 5           esperado: 0          obtenido: 0            OK
    [ 6] indice negativo         esperado: 0          obtenido: 0            OK
    
    RESULTADO: 6/6 pruebas superadas
    TODO EN VERDE. Documenta tu auditoria en auditorias/auditoria_sesion18.md
                    
* [✓] Caso base y límites superados sin *Crash*.
* [✓] No existen accesos fuera de límites ni *Memory Leaks*.
* [✓] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
>La IA resolvió correctamente todos los casos incluidos en el harness; sin embargo, observé que no se probó explícitamente el caso de un arreglo con cero elementos. Verifiqué de forma adicional que cuando elementos es igual a 0, la función bytes_arreglo() devuelve 0, ya que un arreglo vacío no ocupa espacio reservado para elementos. También comprobé que indice_en_rango() rechaza cualquier índice cuando el número de elementos es cero. Esto me permitió confirmar que la implementación se comporta correctamente en condiciones de borde que no fueron evaluadas por el harness principal.

---
**Firma digital:** [Yacqueline12]
