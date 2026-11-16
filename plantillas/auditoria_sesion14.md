# Bitácora de Requerimientos y Auditoría IA — Sesión 14
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Declaración vs Uso de Variables  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347]  
**Track:** [ C ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Detección de variables no declaradas  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> Al realizar el código manualmente, se me dificultó un poco recorrer la tabla y comprobar si el nombre que quería agregar ya existía. También tuve que tener cuidado con el límite de espacios disponibles y evitar que se guardaran nombres repetidos. Para solucionarlo utilicé un ciclo while y la función strcmp() para comparar los nombres.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Invariante de definición previa |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contexto: estoy trabajando en mi práctica de Lenguajes y Autómatas II, en la sesión 14, donde estoy aprendiendo cómo funciona la declaración y el uso de variables. Necesito completar un programa en C que permita guardar nombres de variables y comprobar si ya fueron declarados.

Rol: quiero que actúes como un programador que me ayude a resolver este ejercicio en lenguaje C, explicándome el funcionamiento del código de una manera sencilla.

Especificación: necesito implementar las funciones declarar() y usar(). La primera debe revisar si el nombre está vacío, si ya existe en la tabla y, si no está repetido, agregarlo. La segunda debe comprobar si el nombre que quiero utilizar ya fue declarado. También necesito que se respeten los nombres completos, por ejemplo, que x y xy sean diferentes. Debes respetar las firmas del archivo api_sesion14.h y no agregar una función main().

Output: quiero que me entregues el código completo en C para copiarlo en ia_sesion14_c.c. También quiero que utilices una forma de resolverlo diferente a la que utilicé manualmente en el Artesano, para poder comparar ambas implementaciones. Explícame brevemente cómo funciona y asegúrate de que compile sin advertencias con -Wall -Wextra -Werror.]
```

---

## 3. Código Devuelto por el Modelo
```c
%%writefile ia_sesion14_c.c
/* ia_sesion14_c.c - Fase 2: El Cientifico
 *
 * Pega aqui el codigo que te devolvio la IA, con la MISMA firma
 * que declara api_sesion14.h. No puede traer main().
 */
#include "api_sesion14.h"

#include <string.h>

/* Busca la posicion de un nombre dentro de la tabla */
static int encontrar_nombre(const Tabla *tabla, const char *nombre) {

    for (int posicion = 0; posicion < tabla->total; posicion++) {

        if (strcmp(nombre, tabla->nombres[posicion]) == 0) {
            return posicion;
        }
    }

    return -1;
}

/* Registra un nombre nuevo en la tabla */
int declarar(Tabla *tabla, const char *nombre) {

    if (tabla == NULL || nombre == NULL || nombre[0] == '\0') {
        return -1;
    }

    if (encontrar_nombre(tabla, nombre) != -1) {
        return 0;
    }

    if (tabla->total == TABLA_MAX) {
        return -1;
    }

    tabla->nombres[tabla->total++] = nombre;

    return 1;
}

/* Comprueba si un nombre ya se encuentra registrado */
int usar(const Tabla *tabla, const char *nombre) {

    if (tabla == NULL || nombre == NULL || nombre[0] == '\0') {
        return 0;
    }

    return encontrar_nombre(tabla, nombre) >= 0;
}

```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Uso antes de declarar y redeclaración

=== AUDITORIA SESION 14 - Declaracion previa y redeclaracion ===

Caso                               esperado:              obtenido:              resultado

[ 1] usar sin declarar                  esperado: 0            obtenido: 0            OK

[ 2] usar despues de declarar           esperado: 1            obtenido: 1            OK

[ 3] redeclarar el mismo nombre         esperado: 0            obtenido: 0            OK

[ 4] total tras redeclarar              esperado: 1            obtenido: 1            OK

[ 5] prefijo no es el mismo nombre      esperado: 0            obtenido: 0            OK

[ 6] declarar nombre vacio              esperado: -1           obtenido: -1           OK


RESULTADO: 6/6 pruebas superadas

TODO EN VERDE. Documenta tu auditoria en auditorias/auditoria_sesion14.md

* [✓] Caso base y límites superados sin *Crash*.
* [✓] No existen accesos fuera de límites ni *Memory Leaks*.
* [ ] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> Al revisar el código de la IA, observé que no comprobaba si el total de elementos de la tabla estaba dentro de los límites permitidos antes de realizar la búsqueda. Para solucionar este problema, consideré necesario validar que el total no fuera menor que cero ni mayor que TABLA_MAX antes de recorrer los nombres. Esto ayuda a evitar accesos fuera de los límites de la tabla.

---
**Firma digital:** [Yacqueline12]
