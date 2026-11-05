# Bitácora de Requerimientos y Auditoría IA — Sesión 08
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Inferencia y Promoción de Tipos  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347]  
**Track:** [ C ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Reglas de casteo implícito  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> Al realizar la implementación de forma manual, lo que más trabajo me costó fue identificar correctamente cuál tipo tenía mayor prioridad dentro de la jerarquía de promoción. También tuve que analizar cuándo una conversión podía considerarse pérdida de información y cuándo no. Al principio dudé sobre cómo manejar el caso especial del tipo ERROR. La sintaxis no fue complicada, pero sí fue necesario revisar con cuidado las condiciones para que todos los casos funcionaran correctamente.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Promoción entera y ensanchamiento |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contexto: estoy realizando una práctica de Lenguajes y Autómatas II sobre inferencia y promoción de tipos. Necesito implementar funciones en C para determinar el tipo resultante de una operación entre dos tipos de datos y detectar cuándo una conversión puede provocar pérdida de información.

Rol: actúa como un programador con experiencia en lenguaje C y ayúdame a generar una solución clara, funcional y compatible con el contrato proporcionado.

Especificación: debes implementar las siguientes funciones:
Tipo promover(Tipo a, Tipo b);
int hay_perdida(Tipo destino, Tipo origen);
const char *nombre_tipo(Tipo t);
Considera las siguientes reglas:
La jerarquía de promoción es: CHAR < INT 
< FLOAT
Si alguno de los parámetros de promover es TIPO_ERROR, debe devolver TIPO_ERROR.
promover debe devolver el tipo de mayor jerarquía.
hay_perdida debe devolver 1 cuando el tipo destino sea más pequeño que el tipo origen y 0 en cualquier otro caso.
nombre_tipo debe devolver:
"char"
"int"
"float"
"error"
No agregues función main().

Output: devuelve únicamente el código completo del archivo ia_sesion08_c.c listo para compilar.]
```

---

## 3. Código Devuelto por el Modelo
```c
%%writefile ia_sesion08_c.c
/* ia_sesion08_c.c - Fase 2: El Cientifico
 *
 * Pega aqui el codigo que te devolvio la IA, con la MISMA firma
 * que declara api_sesion08.h. No puede traer main().
 */
#include "api_sesion08.h"

Tipo promover(Tipo a, Tipo b)
{
    if (a == TIPO_ERROR || b == TIPO_ERROR)
    {
        return TIPO_ERROR;
    }

    return (a > b) ? a : b;
}

int hay_perdida(Tipo destino, Tipo origen)
{
    return destino < origen;
}

const char *nombre_tipo(Tipo t)
{
    switch (t)
    {
        case TIPO_CHAR:
            return "char";

        case TIPO_INT:
            return "int";

        case TIPO_FLOAT:
            return "float";

        case TIPO_ERROR:
        default:
            return "error";
    }
}
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Mezcla de tipos enteros y flotantes
=== AUDITORIA SESION 08 - Reglas de promocion y perdida ===

Caso                             esperado:             obtenido:          resultado

[ 1]   promover  (CHAR,INT)          esperado: int         obtenido: int          OK

[ 2]   promover   (INT,FLOAT)         esperado: float       obtenido: float        OK

[ 3]   promover     (FLOAT,FLOAT)       esperado: float       obtenido: float       OK

[ 4]   promover     (ERROR,INT)         esperado: error       obtenido: error        OK

[ 5]   hay_perdida  (CHAR,INT)       esperado: 1           obtenido: 1            OK

[ 6] hay_perdida(INT,CHAR)       esperado: 0           obtenido: 0            OK


RESULTADO: 6/6 pruebas superadas
TODO EN VERDE. Documenta tu auditoria en auditorias/auditoria_sesion08.md


* [ ] Caso base y límites superados sin *Crash*.
* [ ] No existen accesos fuera de límites ni *Memory Leaks*.
* [ ] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> La propuesta generada por la IA resolvía correctamente los casos del harness, pero no explicaba de forma detallada porqué debía tratarse primero el tipo ERROR antes de aplicar la promoción entre tipos. Durante la revisión confirmé que este caso era importante porque, si se ignoraba, se podría devolver un tipo válido cuando en realidad existía una condición de error. También verifiqué que los nombres devueltos por nombre_tipo() coincidieran exactamente con los esperados por el harness para evitar fallos en las comparaciones de cadenas.

---
**Firma digital:** [YACQUELINE CANCHE]
