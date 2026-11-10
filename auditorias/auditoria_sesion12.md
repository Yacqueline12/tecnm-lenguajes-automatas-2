# Bitácora de Requerimientos y Auditoría IA — Sesión 12
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Atributos de Símbolos y Memoria  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347]  
**Track:** [ C ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Estructura de registro de símbolos  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> Al momento de realizar mi código manualmente, tuve dificultad para calcular la posición correcta de cada variable en memoria. También me costó comprender cómo respetar la alineación de los tipos de datos para evitar asignar espacios incorrectos.
---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Cálculo de desplazamientos y alineación |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contexto: estoy realizando una práctica de Lenguajes y Autómatas II en la que necesito calcular el tamaño de diferentes tipos de datos y organizar su posición en memoria respetando la alineación.

Rol: quiero que actúes como un programador con experiencia en lenguaje C y que me ayudes a desarrollar una solución sencilla y fácil de comprender.

Especificación: necesito implementar las funciones ancho_de_tipo() y asignar_offset(). Los tipos que deben reconocerse son char, short, int, float, double y puntero. También necesito validar registros nulos, cursores negativos y tipos desconocidos. El offset debe quedar alineado según el tamaño del tipo y la función debe devolver la posición para la siguiente variable.

Output: quiero recibir el código completo en C, respetando las funciones declaradas en api_sesion12.h, sin incluir main(), con comentarios que expliquen cada parte y que pueda compilarse con GCC sin advertencias.                               ]
```

---

## 3. Código Devuelto por el Modelo
```c
%%writefile ia_sesion12_c.c
/* ia_sesion12_c.c - Fase 2: El Cientifico
 *
 * Pega aqui el codigo que te devolvio la IA, con la MISMA firma
 * que declara api_sesion12.h. No puede traer main().
 */

#include "api_sesion12.h"
#include <string.h>
#include <limits.h>

/* Devuelve los bytes que necesita cada tipo */
int ancho_de_tipo(const char *tipo) {
    if (tipo == NULL) {
        return -1;
    }

    if (strcmp(tipo, "char") == 0) return 1;
    if (strcmp(tipo, "short") == 0) return 2;
    if (strcmp(tipo, "int") == 0) return 4;
    if (strcmp(tipo, "float") == 0) return 4;
    if (strcmp(tipo, "double") == 0) return 8;
    if (strcmp(tipo, "puntero") == 0) return 8;

    return -1;
}

/* Calcula la posición alineada y el siguiente espacio disponible */
int asignar_offset(Simbolo *simbolo, int cursor) {
    int tamano;
    long long inicio;
    long long fin;

    if (simbolo == NULL || cursor < 0) {
        return -1;
    }

    tamano = ancho_de_tipo(simbolo->tipo);

    if (tamano < 1) {
        return -1;
    }

    inicio = ((long long)cursor + tamano - 1) / tamano * tamano;
    fin = inicio + tamano;

    if (fin > INT_MAX) {
        return -1;
    }

    simbolo->offset = (int)inicio;

    return (int)fin;
}
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Alineación de tipos y tamaños en bytes

=== AUDITORIA SESION 12 - Registro de simbolo y offsets alineados ===

Caso                               esperado:       obtenido:              resultado

[ 1] ancho_de_tipo("int")             esperado: 4            obtenido: 4            OK

[ 2] ancho_de_tipo("double")          esperado: 8            obtenido: 8            OK

[ 3] int en cursor 0                  esperado: 4            obtenido: 4            OK

[ 4] char e int: offset de b          esperado: 4            obtenido: 4            OK

[ 5] double desde cursor 4            esperado: 8            obtenido: 8            OK

[ 6] registro NULL                    esperado: -1           obtenido: -1           OK

RESULTADO: 6/6 pruebas superadas
TODO EN VERDE. Documenta tu auditoria en auditorias/auditoria_sesion12.md


* [✓] Caso base y límites superados sin *Crash*.
* [ ] No existen accesos fuera de límites ni *Memory Leaks*.
* [✓] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> Revisando el código de la inteligencia artificial, comprobé que era necesario validar los registros nulos, los cursores negativos y los tipos desconocidos. También agregué una validación para evitar que el cálculo del espacio de memoria sobrepasara el límite permitido.

---
**Firma digital:** [Yacqueline12]
