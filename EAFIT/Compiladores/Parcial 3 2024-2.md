![[parcial3-1588.pdf]]
## 🧪 Punto 1 — Gramáticas Libres de Contexto (CFG)

### (a) Una gramática libre de contexto que genera un lenguaje de tamaño **finito**.

**Objetivo**: Una CFG que genere solo un número limitado de cadenas.

---

**Ejemplo:**

```plaintext
S → aA | b  
A → b
```

**Lenguaje generado:**  
L = { ab, b }

---

**Justificación**:

- No hay recursión en las producciones, por lo tanto **no puede generar infinitas cadenas**.
    
- Solo hay **dos cadenas posibles**:
    
    - S → aA → ab
        
    - S → b
        
- Por tanto, el lenguaje es **finito**.
    

---

### (b) Una gramática libre de contexto que **no genera ninguna cadena**.

**Objetivo**: Una CFG donde el símbolo inicial **no deriva** ninguna cadena terminal.

---

**Ejemplo:**

```plaintext
S → A  
A → B  
B → C
```

**Justificación**:

- No hay **producción que derive terminales**.
    
- Ningún símbolo no terminal tiene producción que lo lleve a una cadena terminal.
    
- El lenguaje generado es **el conjunto vacío**: L(G) = ∅
    

---

### (c) Una gramática libre de contexto que solo puede generar cadenas de **longitud mayor o igual que 10**.

**Objetivo**: Todas las cadenas generadas deben tener al menos 10 símbolos.

---

**Ejemplo:**

```plaintext
S → aSa | T  
T → aaaaaa
```

**Justificación**:

- La producción `T → aaaaaa` tiene longitud 6.
    
- Si usamos `S → aSa` una sola vez, obtenemos al menos 8 (por ejemplo: `aT a = a aaaaaa a = 8`).
    
- Necesitamos al menos **dos niveles de recursión** para superar los 10 caracteres.
    
- Por construcción, **nunca se puede generar una cadena menor que 10**.
    

---

### (d) Una gramática libre de contexto que genera un **lenguaje regular**.

**Objetivo**: Mostrar que una CFG también puede generar un lenguaje regular.

---

**Ejemplo:**

```plaintext
S → aS | bS | ε
```

**Lenguaje generado:**  
L = (a + b)*

---

**Justificación**:

- Esta gramática genera **todas las cadenas** de `a` y `b`, incluidas la vacía.
    
- Este lenguaje es regular y se puede representar con un autómata finito.
    
- Aunque es una CFG, el lenguaje pertenece al subconjunto **regular**.
    

---

## 🧪 Punto 3 — LL(1) y el lenguaje L(G)*

### 📌 Enunciado

Dada la gramática:

```plaintext
S → aSc | bB  
B → bB | ε
```

Sabemos que esta gramática **G es LL(1)**.  
La pregunta es:

> ¿También es LL(1) la gramática que genera el lenguaje L(G)*?  
> Justifique su respuesta claramente.

---

## ✅ Paso 1: Verificamos que G es LL(1)

Ya se nos da que **G es LL(1)**. Veamos por qué:

- **S → aSc | bB**
    
    - `FIRST(aSc) = {a}`
        
    - `FIRST(bB) = {b}`
        
    - Son disjuntos ⇒ ✔️ cumple condición LL(1)
        
- **B → bB | ε**
    
    - `FIRST(bB) = {b}`
        
    - `FIRST(ε) = {ε}`
        
    - `FIRST(bB) ∩ FOLLOW(B) = ∅` ⇒ también ✔️ cumple LL(1)
        

Entonces sí, G es LL(1).

---

## ✅ Paso 2: ¿Qué es L(G)*?

L(G)* es **la concatenación cero o más veces** de cadenas generadas por G.  
En otras palabras:

```plaintext
L(G)* = { w1 w2 w3 ... wn | cada wi ∈ L(G), n ≥ 0 }
```

Para construir una gramática que genere **L(G)***, se suele hacer algo así:

```plaintext
S' → S S' | ε
```

y se reutiliza la gramática original para S.

---

## ❌ Paso 3: ¿Sigue siendo LL(1)?

Veamos la nueva gramática extendida para L(G)*:

```plaintext
S' → S S' | ε
S → aSc | bB  
B → bB | ε
```

Analicemos la producción `S' → S S' | ε`

- `FIRST(S S') = FIRST(S) = {a, b}`
    
- `FIRST(ε) = {ε}`
    
- Necesitamos que `FIRST(S S') ∩ FOLLOW(S') = ∅`
    

Pero:

- `FOLLOW(S') = FIRST(S)` por la regla 3 del FOLLOW (porque `S'` aparece como `S S'`)
    
- Entonces:  
    `FIRST(S S') = {a, b}`  
    `FOLLOW(S') = {a, b}`  
    ⇒ **Hay intersección ⇒ NO es LL(1)** ❌
    

---

## 🧠 Conclusión

**Resultado**: ❌ **L(G)*** no es LL(1)

**Justificación**:  
Al extender la gramática con `S' → S S' | ε`, la producción viola la condición LL(1) porque:

```plaintext
FIRST(S S') ∩ FOLLOW(S') ≠ ∅
```

Esto genera **ambigüedad** al momento de decidir si aplicar `S S'` o `ε`, lo cual impide la construcción de una tabla LL(1).

---

## 🧩 Punto 4 — Corrección del autómata LR(0)

### 📌 Enunciado

Dada la gramática:

```plaintext
S → aSc | bB  
B → bB | ε
```

Se nos muestra un **autómata LR(0) incorrecto** para esta gramática.  
Nuestra tarea es:

> Indique los errores y corrija el autómata.

---

### 🛠 Paso 1: Entender la construcción correcta

#### 📚 Gramática extendida

Agregamos la producción de inicio:

```plaintext
S' → S
```

#### ✅ Ítem inicial

El ítem inicial será:

```plaintext
I₀ = { S' → •S, S → •aSc, S → •bB, B → •bB, B → •ε }
```

⚠️ El cierre (`CLOSURE`) de `S' → •S` incluye todas las producciones de `S`, y estas a su vez referencian `B`, así que también se incluyen las producciones de `B`.

---

### ❌ Paso 2: Identificar los errores en el autómata dado

#### ❌ Error 1: Falta de cierre completo en I₀

En el estado `I₀`, sólo aparecen:

```plaintext
S' → •S  
S → •aSc  
S → •bB
```

🚫 Pero deberían estar también:

```plaintext
B → •bB  
B → •ε
```

Porque `B` aparece justo después del punto en `S → •bB`.

---

#### ❌ Error 2: Loop incorrecto en `I₃`

En el estado `I₃`, se muestra un **loop en `b`**, es decir:

```plaintext
I₃ --b--> I₃
```

Pero el conjunto de ítems en `I₃` es:

```plaintext
S → b•B  
B → •bB  
B → •ε
```

➡️ Desde ahí, el símbolo que sigue al punto es `B`, **no `b`**, por lo que **no puede haber una transición en `b` desde `I₃` a sí mismo**. Esa transición debe salir de donde `•b` aparece al frente, como en `B → •bB`.

---

#### ❌ Error 3: Duplicidad de estados

El estado `I₆` es idéntico al `I₄`. Ambos contienen:

```plaintext
S → aS•c
```

Y `c` no genera nada más, así que la **transición `c` no debería crear un nuevo estado**, sino **llegar a un único estado** (sin ida y vuelta).

---

### ✅ Paso 3: Corrección propuesta

Te doy el esquema textual corregido del LR(0):

#### 📍 Estados:

- `I₀`:  
    `{ S' → •S, S → •aSc, S → •bB, B → •bB, B → •ε }`
    
- `I₁`:  
    `{ S' → S• }`
    
- `I₂`:  
    `{ S → a•Sc, S → •aSc, S → •bB, B → •bB, B → •ε }`
    
- `I₃`:  
    `{ S → b•B, B → •bB, B → •ε }`
    
- `I₄`:  
    `{ S → aS•c }`
    
- `I₅`:  
    `{ B → b•B, B → •bB, B → •ε }`
    
- `I₆`:  
    `{ B → ε• }` (opcional, puede ser reducido directamente)
    
- `I₇`:  
    `{ S → aSc• }`
    

---

### 🧠 Conclusión

**Errores detectados**:

1. ❌ Faltan ítems en `I₀` (no se cerró correctamente)
    
2. ❌ Transición errónea `I₃ --b--> I₃`
    
3. ❌ Estados duplicados (`I₄` ≡ `I₆`)
    
4. ❌ No se manejaron correctamente las transiciones con `ε`
    

**Resultado corregido**:

- Agregar ítems que faltan por `CLOSURE`.
    
- Arreglar transiciones mal etiquetadas.
    
- Evitar estados duplicados.
    
- Manejar correctamente transiciones con `ε`.
    

---
