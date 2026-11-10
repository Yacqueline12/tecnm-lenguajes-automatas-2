# Bitácora de Requerimientos y Auditoría IA — Sesión 10
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Hito 1: Mini-Evaluador de Expresiones  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347]  
**Track:** [ C ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Integración AST y Tipado Estático  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> Al desarrollar la solución de forma manual, la principal dificultad fue lograr que el evaluador respetara correctamente la precedencia de los operadores, tambien tuve que analizar cómo hacer que las multiplicaciones y divisiones se resolvieran antes que las sumas y restas, además de manejar correctamente los paréntesis, por ultimo también fue importante identificar cuándo una expresión tenía un error de sintaxis y cuándo se trataba específicamente de una división entre cero.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Contrato formal de Frontend Semántico |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contexto: estoy realizando una práctica de Lenguajes y Autómatas II donde necesito crear un mini evaluador de expresiones matemáticas. El programa debe leer una expresión escrita como texto y calcular su resultado respetando la precedencia de operadores y el uso de paréntesis.

Rol: actúa como un programador con experiencia en lenguaje C y en la construcción de analizadores sintácticos sencillos.

Especificación: necesito implementar la función:long evaluar_expresion(const char *fuente, int *error); La solución debe aceptar números enteros, los operadores +, -, * y /, además de paréntesis y espacios en blanco, tambien debe respetar la precedencia de operadores, evaluando primero multiplicaciones y divisiones antes que sumas y restas. Los códigos de error son:

error = 1 para división entre cero. error = 2 para expresiones con sintaxis inválida.

No se deben utilizar variables dentro de la expresión ni implementar menos unario. La firma original debe conservarse exactamente igual, no debe incluirse una función main() y el código debe compilar correctamente usando: -Wall -Wextra -Werror

Output: devuelve únicamente el contenido completo de un archivo .c listo para compilar, sin explicaciones adicionales.]
```

---

## 3. Código Devuelto por el Modelo
```c
%%writefile ia_sesion10_c.c
/* ia_sesion10_c.c - Fase 2: El Cientifico
 *
 * Pega aqui el codigo que te devolvio la IA, con la MISMA firma
 * que declara api_sesion10.h. No puede traer main().
 */
#include "api_sesion10.h"
#include <ctype.h>
#include <stddef.h>

typedef struct { const char *s; int error; } Parser;

static void saltar(Parser *p)
{
    while (*p->s == ' ')
        p->s++;
}

static long leer_numero(Parser *p)
{
    long n = 0;

    while (isdigit((unsigned char)*p->s))
    {
        n = n * 10 + (*p->s - '0');
        p->s++;
    }

    return n;
}

long expr(Parser *p);

long primaria(Parser *p)
{
    long valor;

    saltar(p);

    if (isdigit((unsigned char)*p->s))
        return leer_numero(p);

    if (*p->s == '(')
    {
        p->s++;

        valor = expr(p);

        saltar(p);

        if (*p->s != ')')
        {
            p->error = 2;
            return 0;
        }

        p->s++;
        return valor;
    }

    p->error = 2;
    return 0;
}

long termino(Parser *p)
{
    long resultado = primaria(p);

    while (p->error == 0)
    {
        long rhs;

        saltar(p);

        if (*p->s == '*')
        {
            p->s++;
            rhs = primaria(p);
            resultado *= rhs;
        }
        else if (*p->s == '/')
        {
            p->s++;
            rhs = primaria(p);

            if (rhs == 0)
            {
                p->error = 1;
                return 0;
            }

            resultado /= rhs;
        }
        else
        {
            break;
        }
    }

    return resultado;
}

long expr(Parser *p)
{
    long resultado = termino(p);

    while (p->error == 0)
    {
        long rhs;

        saltar(p);

        if (*p->s == '+')
        {
            p->s++;
            rhs = termino(p);
            resultado += rhs;
        }
        else if (*p->s == '-')
        {
            p->s++;
            rhs = termino(p);
            resultado -= rhs;
        }
        else
        {
            break;
        }
    }

    return resultado;
}

long evaluar_expresion(const char *fuente, int *error)
{
    static int sin_error;

    if (error == NULL)
        error = &sin_error;

    *error = 0;

    if (fuente == NULL)
    {
        *error = 2;
        return 0;
    }

    Parser p = { fuente, 0 };

    long valor = expr(&p);

    saltar(&p);

    if (p.error == 0 && *p.s != '\0')
        p.error = 2;

    *error = p.error;

    if (p.error != 0)
        return 0;

    return valor;
}
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Batería de 10 expresiones mixtas complejas

=== AUDITORIA SESION 10 - Mini-evaluador de expresiones (Hito 1) ===

Caso                               esperado:              obtenido:  
resultado


[ 1] "2+3*4"                            esperado: 14           obtenido: 14
OK


[ 2] "(2+3)*4"                          esperado: 20           obtenido: 20           OK


[ 3] "10/0" -> error 1                  esperado: 1            obtenido: 1            OK


[ 4] "2+" -> error 2                    esperado: 2            obtenido: 2            OK


[ 5] "  7 "                             esperado: 7            obtenido: 7            OK


[ 6] "5-10"                             esperado: -5           obtenido: -5           OK

RESULTADO: 6/6 pruebas superadas
TODO EN VERDE. Documenta tu auditoria en auditorias/auditoria_sesion10.md

* [ ] Caso base y límites superados sin *Crash*.
* [ ] No existen accesos fuera de límites ni *Memory Leaks*.
* [ ] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> La IA me proporcionó una solución que en general funcionaba bien, pero al momento de compilar encontré un pequeño problema porque faltaba una biblioteca necesaria para reconocer NULL, revisé el código, agregué la cabecera que hacía falta y volví a ejecutar las pruebas, después de corregir ese detalle, comprobé que el programa funcionaba correctamente y que identificaba tanto los errores de sintaxis como las divisiones entre cero de la manera esperada.

---
**Firma digital:** [Yacqueline12]
