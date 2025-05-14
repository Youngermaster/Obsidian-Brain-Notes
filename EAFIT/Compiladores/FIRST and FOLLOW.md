## 🔹 ¿Qué es FIRST?

### 🔸 Definición:

El conjunto **FIRST(α)** para una cadena α (que puede ser un símbolo o una secuencia de símbolos) es el conjunto de **símbolos terminales** que pueden **aparecer primero** en alguna derivación desde α.  
También incluye **ε** si α puede derivar la cadena vacía.

---

### 📌 Reglas para calcular FIRST(X):

1. **Si X es un terminal**  
    → FIRST(X)={X}
    
2. **Si X es un no terminal y hay una producción X→Y1Y2…Yk**
    
    - Entonces se agrega a **FIRST(X)** todo símbolo en **FIRST(Y₁)**
        
    - Si **FIRST(Y₁)** contiene ε, también agregas **FIRST(Y₂)**, y así sucesivamente.
        
    - Si todos los **Yᵢ** pueden derivar ε, entonces ε∈FIRST(X)ε \in \text{FIRST}(X)
        
3. **Si hay una producción X→ε**  
    → entonces ε∈FIRST(X)
    

---

### ✅ Ejemplo:

Dada la gramática:

```
S → AB
A → aA | ε
B → b
```

Cálculo:

- **FIRST(a) = {a}**
    
- **FIRST(A):**
    
    - A → aA → **{a}**
        
    - A → ε → **{ε}**
        
    - ⇒ **FIRST(A) = {a, ε}**
        
- **FIRST(B) = {b}**
    
- **FIRST(AB):**
    
    - Como **A** tiene ε, entonces:
        
        - **FIRST(AB) = FIRST(A) - {ε} ∪ FIRST(B)**
            
        - ⇒ **FIRST(AB) = {a} ∪ {b} = {a, b}**
            

---

## 🔹 ¿Qué es FOLLOW?

### 🔸 Definición:

**FOLLOW(A)** es el conjunto de **símbolos terminales** que **pueden aparecer inmediatamente después** del no terminal **A** en alguna derivación.

---

### 📌 Reglas para FOLLOW:

1. Si **A** es el símbolo inicial ⇒ **$ ∈ FOLLOW(A)**  
    (donde **$** es el marcador de fin de entrada)
    
2. Si hay una producción **B → αAβ**:
    
    - Entonces todos los símbolos de **FIRST(β)** menos ε están en **FOLLOW(A)**.
        
3. Si **β** puede derivar ε (es decir, **ε ∈ FIRST(β)**), o si **A** está al final:
    
    - Entonces **FOLLOW(B) ⊆ FOLLOW(A)**
        

---

### ✅ Ejemplo (siguiendo la misma gramática):

```
S → AB
A → aA | ε
B → b
```

- **FOLLOW(S) = {$}** (es el símbolo inicial)
    
- **FOLLOW(A)**:
    
    - En la producción **S → AB**, **A** va seguido de **B** → **FOLLOW(A) += FIRST(B) = {b}**
        
- **FOLLOW(B)**:
    
    - B está al final de la producción, así que **FOLLOW(B) += FOLLOW(S) = {$}**
        

**Resultado:**

|No Terminal|FIRST|FOLLOW|
|---|---|---|
|S|{a, b}|{$}|
|A|{a, ε}|{b}|
|B|{b}|{$}|

---

## 🧠 ¿Para qué se usan?

- En análisis **predictivo** (como **LL(1)**), para decidir **cuál producción aplicar** mirando el **siguiente símbolo de entrada**.
    
- También se usa **FOLLOW** como **token de sincronización** para recuperación de errores.
    

---

## ✅ Otro ejemplo: Expr aritméticas

```
E  → T E'
E' → + T E' | ε
T  → F T'
T' → * F T' | ε
F  → ( E ) | id
```

**FIRST y FOLLOW (resumen):**

|No Terminal|FIRST|FOLLOW|
|---|---|---|
|E|{(, id}|{), $}|
|E'|{+, ε}|{), $}|
|T|{(, id}|{+, ), $}|
|T'|{*, ε}|{+, ), $}|
|F|{(, id}|{*, +, ), $}|

---

## 🧠 Truco para calcularlos correctamente:

- **FIRST:** “¿Qué puede venir de primero?”
    
- **FOLLOW:** “¿Qué puede venir después si ya usé este no terminal?”
    
