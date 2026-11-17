# Bitácora de Requerimientos y Auditoría IA — Sesión 16
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Firmas de Función y Parámetros  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347 
**Track:** [C]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Registro de prototipos y aridad  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> Al realizar esta parte de la práctica, al principio se me dificultó entender cómo debía recorrer el catálogo para encontrar el nombre de la función que estaba buscando. También tuve que tener cuidado al comparar la cantidad de argumentos, porque no era lo mismo que la función no existiera a que sí existiera pero tuviera una cantidad diferente de argumentos. Otra cosa que consideré fue revisar que los datos recibidos fueran válidos antes de comenzar la búsqueda, para evitar errores durante la ejecución.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Coincidencia estricta de parámetros |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contexto: estoy trabajando en una práctica de Lenguajes y Autómatas II, donde estamos aprendiendo cómo un compilador puede identificar las funciones que ya están registradas y comprobar si las estamos utilizando correctamente. En esta actividad tengo que trabajar con un catálogo que contiene los nombres de las funciones y la cantidad de parámetros que acepta cada una.

Rol: quiero que actúes como alguien que tiene conocimientos de programación en lenguaje C y que pueda ayudarme a resolver este ejercicio de una manera clara. Me interesa que el código sea sencillo, que se pueda entender y que no utilice instrucciones demasiado complicadas.

Especificación: necesito implementar una función llamada verificar_llamada() que revise si una función existe dentro de un catálogo y compruebe que la cantidad de argumentos que recibe sea igual a la cantidad que tiene registrada.
La función debe devolver 1 cuando todo esté correcto, 0 cuando encuentre la función pero tenga una cantidad diferente de argumentos y -1 cuando no encuentre la función o reciba datos incorrectos.
También quiero que consideres situaciones como recibir un catálogo NULL, un nombre NULL, una cantidad negativa de argumentos o una función que no tenga parámetros.
La función debe respetar exactamente la estructura y los parámetros que aparecen en api_sesion16.h. No debe incluir un main(), porque el programa principal lo proporciona el archivo del auditor.

Output: entrégame el código completo en C, listo para copiar en ia_sesion16_c.c. Explícame brevemente cómo funciona y utiliza una estructura diferente a una implementación sencilla basada en un ciclo for. Evita agregar código que no sea necesario y asegúrate de que compile sin advertencias utilizando -Wall -Wextra -Werror.
]
```

---

## 3. Código Devuelto por el Modelo
```c
%%writefile ia_sesion16_c.c
/* ia_sesion16_c.c - Fase 2: El Cientifico
 *
 * Pega aqui el codigo que te devolvio la IA, con la MISMA firma
 * que declara api_sesion16.h. No puede traer main().
 */
#include "api_sesion16.h"
#include <string.h>

int verificar_llamada(const Funcion *catalogo, int total, const char *nombre, int nargs) {

    int posicion = 0;
    int resultado = -1;

    /* Comprobamos que los datos recibidos sean validos */
    if (catalogo == NULL || nombre == NULL || nargs < 0 || total <= 0) {
        return resultado;
    }

    /* Recorremos el catalogo mientras queden funciones */
    while (posicion < total) {

        if (strcmp(nombre, catalogo[posicion].nombre) == 0) {

            /* Guardamos el resultado de comparar las aridades */
            resultado = (catalogo[posicion].aridad == nargs) ? 1 : 0;

            break;
        }

        posicion++;
    }

    return resultado;
}
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Llamadas con número incorrecto de args

              === AUDITORIA SESION 16 - Registro de prototipos y aridad ===
        Caso                          esperado:             obtenido:         resultado
        [ 1] suma con 3 argumentos         esperado: 1        obtenido: 1            OK
        [ 2] suma con 2 argumentos         esperado: 0        obtenido: 0            OK
        [ 3] suma con 4 argumentos         esperado: 0        obtenido: 0            OK
        [ 4] funcion no registrada         esperado: -1       obtenido: -1           OK
        [ 5] main sin argumentos           esperado: 1        obtenido: 1            OK
        [ 6] nombre NULL                   esperado: -1       obtenido: -1           OK

        RESULTADO: 6/6 pruebas superadas
        TODO EN VERDE. Documenta tu auditoria en auditorias/auditoria_sesion16.md

* [✓] Caso base y límites superados sin *Crash*.
* [ ] No existen accesos fuera de límites ni *Memory Leaks*.
* [✓] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> Cuando revise el código que me proporcionó la IA, observé que no se estaba comprobando si el nombre de cada función dentro del catálogo era NULL antes de utilizar strcmp() para compararlo. Esto podría ocasionar un error si alguna posición del catálogo no tuviera un nombre válido. Para identificarlo, revisé las condiciones que se utilizaban durante el recorrido y consideré que también era necesario validar ese dato antes de realizar la comparación. De esta manera comprendí que no basta con revisar los datos que recibe la función, sino que también se deben considerar los datos que contiene el catálogo.

---
**Firma digital:** [Yacqueline 12]
