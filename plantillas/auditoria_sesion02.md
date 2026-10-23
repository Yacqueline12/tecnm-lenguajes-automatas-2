# Bitácora de Requerimientos y Auditoría IA — Sesión 02
**Materia:** Lenguajes y Autómatas II (SCD-1016)  
**Tema:** Gestión en GitHub Web y Nodos Dinámicos  
**Docente Titular:** MCC. Iván Márquez Larios (`ijmarquezl`)  
**Estudiante:** [Jimena Yacqueline Canche Pablo]  
**Matrícula:** [24531347]  
**Track:** [ C ]  

---

## 1. Fase 1: El Artesano (Dificultad Manual)
*Reto:* Lista enlazada de tokens en Heap  
*Describe qué problema o restricción de memoria/sintaxis identificaste al codificar a mano:*
> Al realizar esta práctica, se me dificultó un poco entender cómo reservar memoria y conectar los nodos con punteros, se me dificulta un poco porque estoy aprendiendo a utilizar nuevamente GitHub, ya que hace tiempo que no lo uso e intento familiarizarme más con sus herramientas.

---

## 2. Fase 2: El Científico (Ingeniería de Requerimientos C-R-E-O)

### 2.1 Matriz de Especificación Formal
| Elemento C-R-E-O | Definición Técnica |
| :--- | :--- |
| **Contexto (C)** | C99 / GCC o Rust 2021 sin bibliotecas no estándar |
| **Rol (R)** | Ingeniero de software de sistemas y compiladores |
| **Especificación (E)** | Gestión de memoria dinámica C99/Rust |
| **Output (O)** | Solo código modular y análisis de complejidad |

### 2.2 Prompt Estructurado
```text
[Contexto: Estoy trabajando en una práctica de C sobre listas enlazadas y memoria dinámica de la asignarura de Lenguajes y Automatas II.

Rol: Quiero que actúes como un programador que me ayude a resolver el ejercicio.

Especificación: Necesito las funciones crear_nodo(), agregar_token(), contar_tokens() y liberar_lista(), respetando el archivo api_sesion02.h. El código debe utilizar malloc() y free(), revisar los punteros nulos y evitar errores al copiar los lexemas.

Output: Dame el código completo para ia_sesion02_c.c, sin incluir main(), y que compile correctamente.]
```

---

## 3. Código Devuelto por el Modelo
```c
// %%writefile ia_sesion02_c.c
#include "api_sesion02.h"
#include <stdlib.h>
#include <string.h>

NodoToken *crear_nodo(const char *lexema) {
    if (lexema == NULL) {
        return NULL;
    }

    NodoToken *nuevo = malloc(sizeof(NodoToken));

    if (nuevo == NULL) {
        return NULL;
    }

    size_t longitud = strlen(lexema);

    if (longitud >= sizeof(nuevo->lexema)) {
        longitud = sizeof(nuevo->lexema) - 1;
    }

    memcpy(nuevo->lexema, lexema, longitud);
    nuevo->lexema[longitud] = '\0';
    nuevo->siguiente = NULL;

    return nuevo;
}

void agregar_token(NodoToken **cabeza, const char *lexema) {
    if (cabeza == NULL || lexema == NULL) {
        return;
    }

    NodoToken *nuevo = crear_nodo(lexema);

    if (nuevo == NULL) {
        return;
    }

    if (*cabeza == NULL) {
        *cabeza = nuevo;
        return;
    }

    NodoToken *actual = *cabeza;

    while (actual->siguiente != NULL) {
        actual = actual->siguiente;
    }

    actual->siguiente = nuevo;
}

int contar_tokens(NodoToken *cabeza) {
    int contador = 0;

    while (cabeza != NULL) {
        contador++;
        cabeza = cabeza->siguiente;
    }

    return contador;
}

void liberar_lista(NodoToken *cabeza) {
    while (cabeza != NULL) {
        NodoToken *temporal = cabeza;
        cabeza = cabeza->siguiente;
        free(temporal);
    }
}
```

---

## 4. Fase 3: El Auditor (Resultados del Test Harness)
*Pruebas ejecutadas:* Prevención de Use-After-Free y cadenas nulas
=== AUDITORIA SESION 02 - Lista enlazada de tokens en heap ===
Caso                               esperado:              obtenido:          resultado
[ 1] crear_nodo("if")->lexema      esperado: if           obtenido: if           OK
[ 2] contar_tokens(3 tokens)       esperado: 3            obtenido: 3            OK
[ 3] orden: segundo token          esperado: dos          obtenido: dos          OK
[ 4] ultimo->siguiente == NULL     esperado: 1            obtenido: 1            OK
[ 5] contar_tokens(NULL)           esperado: 0            obtenido: 0            OK
[ 6] liberar_lista(NULL)           esperado: sin crash    obtenido: sin crash    OK

=== AUDITORIA SESION 02 - Lista enlazada de tokens en heap ===
Caso                               esperado:              obtenido:         resultado
[ 1] crear_nodo("if")->lexema      esperado: if           obtenido: if           OK
[ 2] contar_tokens(3 tokens)       esperado: 3            obtenido: 3            OK
[ 3] orden: segundo token          esperado: dos          obtenido: dos          OK
[ 4] ultimo->siguiente == NULL     esperado: 1            obtenido: 1            OK
[ 5] contar_tokens(NULL)           esperado: 0            obtenido: 0            OK
[ 6] liberar_lista(NULL)           esperado: sin crash    obtenido: sin crash    OK

RESULTADO: 6/6 pruebas superadas
TODO EN VERDE. Documenta tu auditoria en auditorias/auditoria_sesion02.md
RESULTADO: 6/6 pruebas superadas
TODO EN VERDE. Documenta tu auditoria en auditorias/auditoria_sesion02.md

* [x] Caso base y límites superados sin *Crash*.
* [x] No existen accesos fuera de límites ni *Memory Leaks*.
* [x] La complejidad teórica coincide con la implementada.

### Reporte Crítico
*¿Qué caso borde o problema omitió la IA y cómo lo solucionaste?*
> Al revisar el código, tomé en cuenta los punteros nulos, los errores al reservar memoria y los textos muy largos. Las seis pruebas salieron bien, aunque todavía harían falta otras pruebas para comprobar que no haya salido algo mal.

---
**Firma digital:** [YACQUELINECANCHE].
