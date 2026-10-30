# Bitácora de Requerimientos y Auditoría IA — Sesión 03
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Google Colab, Mágicos y Flujo de Entrega  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347]  
**Track:** [ C ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Manipulación de archivos y llamadas OS  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> Al realizar el código manualmente, identifiqué que uno de los principales problemas era trabajar correctamente con los archivos en lenguaje C, ya que era necesario abrirlos, leer o escribir la información y cerrarlos al terminar. También tuve que tener cuidado con las llaves, los paréntesis y los puntos y comas, porque un pequeño error de sintaxis podía impedir que el programa compilara.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Salida estándar y códigos de retorno ABI |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contexto: Necesito implementar tres funciones en C para escribir archivos, contar líneas y ejecutar comandos externos.


Rol: Actúa como experto en C y explica el código de forma sencilla.

Especificación: Implementa escribir_texto(), contar_lineas() y ejecutar(), respetando las firmas de api_sesion03.h. Comprueba errores, cuenta correctamente las líneas aunque no haya salto final y usa WIFEXITED y WEXITSTATUS. No incluyas main() y asegúrate de que compile sin advertencias.

Output: Entrega el código completo para ia_sesion03_c.c, una explicación breve y las pruebas necesarias para verificar que las seis pruebas del harness funcionen correctamente.

]
```

---

## 3. Código Devuelto por el Modelo
```c

%%writefile ia_sesion03_c.c
#include "api_sesion03.h"
#include <stdio.h>
#include <stdlib.h>
#include <sys/wait.h>

int escribir_texto(const char *ruta, const char *texto) {
    if (ruta == NULL || texto == NULL) return -1;

    FILE *archivo = fopen(ruta, "w");
    if (archivo == NULL) return -1;

    int error = (fputs(texto, archivo) == EOF);
    if (fclose(archivo) == EOF) error = 1;

    return error ? -1 : 0;
}

int contar_lineas(const char *ruta) {
    if (ruta == NULL) return -1;

    FILE *archivo = fopen(ruta, "r");
    if (archivo == NULL) return -1;

    int c, lineas = 0, hay_texto = 0, ultimo = '\n';

    while ((c = fgetc(archivo)) != EOF) {
        hay_texto = 1;
        if (c == '\n') lineas++;
        ultimo = c;
    }

    if (ferror(archivo)) {
        fclose(archivo);
        return -1;
    }

    if (hay_texto && ultimo != '\n') lineas++;

    if (fclose(archivo) == EOF) return -1;

    return lineas;
}

int ejecutar(const char *comando) {
    if (comando == NULL) return -1;

    int estado = system(comando);
    if (estado == -1 || !WIFEXITED(estado)) return -1;

    return WEXITSTATUS(estado);
}
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Detección de errores de compilación GCC

* [✓] Caso base y límites superados sin *Crash*.
* [✓] No existen accesos fuera de límites ni *Memory Leaks*.
* [✓] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> Como apenas me estoy familiarizando nuevamente con GitHub y los códigos en lenguaje C, sabía que la IA podía generar código con errores o que no cumpliera completamente con lo que se pedía. Por eso, fui revisando constantemente el código que me proporcionaba y le preguntaba cómo lo había realizado y para qué servía cada parte. Cuando aparecían errores, revisaba las indicaciones y volvía a consultar con la IA para entender qué estaba pasando y cómo podía solucionarlo. 

---
**Firma digital:** [YACQUELINECANCHE]
