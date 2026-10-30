# Bitácora de Requerimientos y Auditoría IA — Sesión 06
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Sistema de Tipos Primitivos  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347]  
**Track:** [ C ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Chequeo de compatibilidad de tipos  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> Al realizar el código manualmente, tuve un poco de dificultad para identificar qué tipos de datos podían ser compatibles entre sí, tuve que revisar cuidadosamente las condiciones if para evitar aceptar asignaciones incorrectas, también tuve que respetar los nombres y las funciones que ya estaban definidos en el archivo api_sesion06.h para evitar errores al compilar.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Matriz de compatibilidad estática |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contexto: estoy realizando una práctica de Lenguajes y Autómatas II en lenguaje C, necesito implementar una tabla de compatibilidad de tipos primitivos que permita identificar cuándo una asignación entre dos tipos de datos es correcta.

Rol: quiero que actúes como un programador que me ayude a resolver el ejercicio de manera clara, sencilla y fácil de comprender, utilizando conocimientos básicos de programación en C.

Especificación: Necesito implementar las funciones compatible(Tipo destino, Tipo origen) y nombre_tipo(Tipo t), respetando las declaraciones del archivo api_sesion06.h.

La jerarquía de los tipos es CHAR, INT y FLOAT. Se deben aceptar las asignaciones cuando el destino tenga el mismo rango o uno mayor que el origen. Cuando se intente asignar un tipo más grande a uno más pequeño, se debe rechazar. También se debe comprobar que TIPO_ERROR nunca sea compatible con otro tipo.

Quiero que esta segunda implementación sea diferente a la que realicé manualmente en la fase del Artesano, utilizando otra estructura de programación, pero que cumpla exactamente con las mismas condiciones. 

Output: quiero recibir el código completo en C, sin incluir la función main(), respetando las firmas originales y evitando advertencias al compilar con -Wall -Wextra -Werror. También necesito que las seis pruebas del harness sean correctas y que el resultado final sea de 6/6 pruebas superadas.]
```

---

## 3. Código Devuelto por el Modelo
```c
%%writefile ia_sesion06_c.c
#include "api_sesion06.h"

int compatible(Tipo destino, Tipo origen) {

    if (destino == TIPO_ERROR || origen == TIPO_ERROR) {
        return 0;
    }

    if (destino < TIPO_CHAR || destino > TIPO_FLOAT ||
        origen < TIPO_CHAR || origen > TIPO_FLOAT) {
        return 0;
    }

    return destino >= origen;
}

const char *nombre_tipo(Tipo t) {

    switch (t) {
        case TIPO_ERROR:
            return "error";

        case TIPO_CHAR:
            return "char";

        case TIPO_INT:
            return "int";

        case TIPO_FLOAT:
            return "float";

        default:
            return "desconocido";
    }
}
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Rechazo de asignaciones incompatibles

=== AUDITORIA SESION 06 - Tabla de compatibilidad de tipos ===

Caso                           esperado:              obtenido:          resultado

[ 1] compatible(INT,INT)       esperado: 1            obtenido: 1            OK

[ 2] compatible(FLOAT,INT)     esperado: 1            obtenido: 1            OK

[ 3] compatible(INT,FLOAT)     esperado: 0            obtenido: 0            OK

[ 4] compatible(INT,ERROR)     esperado: 0            obtenido: 0            OK

[ 5] nombre_tipo(INT)          esperado: int          obtenido: int          OK

[ 6] nombre_tipo(ERROR)        esperado: error        obtenido: error        OK


RESULTADO: 6/6 pruebas superadas
TODO EN VERDE. Documenta tu auditoria en auditorias/auditoria_sesion06.md

* [✓] Caso base y límites superados sin *Crash*.
* [✓] No existen accesos fuera de límites ni *Memory Leaks*.
* [✓] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> Un caso que revisé fue cuando se utilizan tipos de datos que no están definidos o cuando alguno de los valores es TIPO_ERROR. La IA podía pasar por alto la validación de estos casos. Para solucionarlo, revisé el código y agregué condiciones que comprueban que los tipos sean válidos antes de realizar la comparación. Así evité que se aceptaran asignaciones incorrectas y comprobé que el programa cumpliera con lo solicitado.

---
**Firma digital:** [Usuario de GitHub]
