![[parcial3-2534.pdf]]
## 📌 **Gramática dada**


Alfabeto:  
Σ={=,∗,i}\Sigma = \{ =, *, i \}  
Gramática:

```
S → L = R | R  
L → *R | i  
R → L
```

---

## ✅ **(a) Identificar las producciones que satisfacen la condición del algoritmo de FOLLOW**

### 📜 Enunciado:

> El procedimiento para calcular los conjuntos Follow establece que:  
> Si A→αBA \rightarrow \alpha B o A→αBβA \rightarrow \alpha B \beta con ε∈FIRST(β)\varepsilon \in \text{FIRST}(\beta), entonces FOLLOW(A)⊆FOLLOW(B)\text{FOLLOW}(A) \subseteq \text{FOLLOW}(B).  
> Escriba todas las producciones que satisfacen esta condición y describa claramente qué son A,B,α,βA, B, \alpha, \beta en cada caso.

### ✍️ Resolución:

Veamos cada producción y si encaja con la forma A→αBβA \rightarrow \alpha B \beta.

1. **S → L = R**
    
    - Forma: A→αBβ
        
    - Aquí:
        
        - A=SA = S
            
        - B=LB = L
            
        - α=ε
            
        - β==R\beta = =R
            
    - Entonces, si ε∈FIRST(=R)\varepsilon \in \text{FIRST}(=R), se aplica FOLLOW(S)⊆FOLLOW(L)\text{FOLLOW}(S) \subseteq \text{FOLLOW}(L)
        
    - También:
        
        - A=SA = S, B=RB = R, α=L=\alpha = L=, β=ε\beta = \varepsilon
            
        - Se aplica directamente FOLLOW(S)⊆FOLLOW(R)\text{FOLLOW}(S) \subseteq \text{FOLLOW}(R)
            
2. *_L → _R__
    
    - A=LA = L, B=RB = R, α=∗\alpha = *, β=ε\beta = \varepsilon
        
    - Entonces: FOLLOW(L)⊆FOLLOW(R)\text{FOLLOW}(L) \subseteq \text{FOLLOW}(R)
        
3. **R → L**
    
    - A=RA = R, B=LB = L, α=ε\alpha = \varepsilon, β=ε\beta = \varepsilon
        
    - Entonces: FOLLOW(R)⊆FOLLOW(L)\text{FOLLOW}(R) \subseteq \text{FOLLOW}(L)
        

### ✅ Resultado del punto (a):

Las producciones que satisfacen la condición son:

|Producción|A|B|α|β|
|---|---|---|---|---|
|S → L = R|S|L|ε|=R|
|S → L = R|S|R|L=|ε|
|L → *R|L|R|*|ε|
|R → L|R|L|ε|ε|

---

## ✅ **(b) ¿Es correcto afirmar que FIRST(i) ⊆ FOLLOW(L)?**

### 📜 Enunciado:

> De acuerdo con el procedimiento para calcular los conjuntos Follow, ¿es correcto afirmar que FIRST(i)⊆FOLLOW(L)\text{FIRST}(i) \subseteq \text{FOLLOW}(L)? Justifique su respuesta.

### ✍️ Resolución:

Veamos en qué producción aparece **L seguido por algo**:

- Producción: **R → L**
    
    - L está al final → no le sigue nada → aplicar regla:
        
        - FOLLOW(R)⊆FOLLOW(L)\text{FOLLOW}(R) \subseteq \text{FOLLOW}(L)
            

También:

- Producción: **S → L = R**
    
    - L está seguido de "=" → FIRST(=)={=}\text{FIRST}(=) = \{ = \}
        

La **símbolo i** aparece como terminal en la producción:

- **L → i** pero eso no implica nada sobre FOLLOW, ya que no aparece **después de L** en ninguna producción.
    

### ✅ Resultado del punto (b):

**No**, **FIRST(i)** no está contenido en **FOLLOW(L)**, ya que **"i" no aparece después de L en ninguna producción**. No hay forma en que la gramática imponga eso.

---

## ✅ **(c) ¿Es correcto afirmar que FOLLOW(R) = FOLLOW(L)?**

### 📜 Enunciado:

> ¿Es correcto afirmar que FOLLOW(R)=FOLLOW(L)\text{FOLLOW}(R) = \text{FOLLOW}(L)? Justifique su respuesta.

### ✍️ Resolución:

Veamos lo que sabemos:

- De la producción **S → L = R**, R está al final →  
    ⇒ FOLLOW(S)⊆FOLLOW(R)\text{FOLLOW}(S) \subseteq \text{FOLLOW}(R)
    
- De la producción *_L → _R__, R está al final →  
    ⇒ FOLLOW(L)⊆FOLLOW(R)\text{FOLLOW}(L) \subseteq \text{FOLLOW}(R)
    
- De la producción **R → L**, L está al final →  
    ⇒ FOLLOW(R)⊆FOLLOW(L)\text{FOLLOW}(R) \subseteq \text{FOLLOW}(L)
    

→ Entonces tenemos:

- FOLLOW(L)⊆FOLLOW(R)\text{FOLLOW}(L) \subseteq \text{FOLLOW}(R)
    
- FOLLOW(R)⊆FOLLOW(L)\text{FOLLOW}(R) \subseteq \text{FOLLOW}(L)
    

→ Por lo tanto:

### ✅ Resultado del punto (c):

**Sí**, es correcto afirmar que FOLLOW(R)=FOLLOW(L)\text{FOLLOW}(R) = \text{FOLLOW}(L),  
porque hay **inclusión mutua** entre sus conjuntos FOLLOW debido a las producciones R → L y L → *R.

---

Vamos a resolver detalladamente el punto 2 del parcial (apartados a, b y c):

---

## 📌 Gramática dada

Σ={=,∗,i}\Sigma = \{ =, *, i \} S→L=R ∣ RL→∗R ∣ iR→L\begin{aligned} S &\to L = R \ |\ R \\ L &\to *R \ |\ i \\ R &\to L \end{aligned}

---

## ✅ (a) ¿La gramática G es LL(1)?

### 📜 Enunciado:

> ¿La gramática G es LL(1)? Justificar clara y completamente cada respuesta.

### ✍️ Resolución:

Una gramática es **LL(1)** si para cada no terminal con múltiples producciones:

- Sus conjuntos **FIRST** son disjuntos,
    
- A lo sumo una de las producciones puede derivar a ε (epsilon), y
    
- Si una producción deriva a ε, entonces su **FOLLOW** no interseca con el **FIRST** de las otras.
    

#### Revisamos las producciones múltiples:

1. **S → L = R** y **S → R**
    

- Ambas producciones **comienzan con L y R**, y **R → L**, por lo que existe ambigüedad entre L y R (circularidad).
    
- ¿Qué deriva **FIRST(L)**?
    
    - L → *R ⇒ comienza en `*`
        
    - L → i ⇒ también puede comenzar en `i`
        
- ¿Qué deriva **FIRST(R)**?
    
    - R → L ⇒ mismo FIRST que L ⇒ `*`, `i`
        
- Entonces:
    
    - **FIRST(L = R)** comienza en FIRST(L) = {*, i}
        
    - **FIRST(R)** también = {*, i}
        

👉 Esto significa que **FIRST(L=R)** ∩ **FIRST(R)** ≠ ∅  
⛔ **Violación directa de LL(1)**.

### ✅ Resultado del punto (a):

**No**, la gramática **no es LL(1)**, porque las producciones **S → L = R** y **S → R** tienen **FIRST sets que se intersecan** ({*, i}), lo que genera **ambigüedad en el análisis predictivo**.

---

## ✅ (b) Si se elimina la producción R → L, ¿la gramática G sería LL(1)?

### 📜 Enunciado:

> Si se elimina la producción **R → L**, ¿la gramática G sería LL(1)?

### ✍️ Resolución:

La nueva gramática sería:

```
S → L = R | R  
L → *R | i  
(R eliminado como no terminal)
```

Ahora **R no tiene producciones**, lo que no es válido. Pero si asumimos que se _redefine_ para que **S → L = R** sea válida, **y eliminamos la alternativa S → R**, la gramática queda:

```
S → L = R  
L → *R | i  
```

Ahora analizamos:

- L tiene dos producciones:
    
    - L → *R ⇒ FIRST = { * }
        
    - L → i ⇒ FIRST = { i }
        

👉 Disjuntos ⇒ ✅

- S tiene una sola producción ⇒ ✅
    

### ✅ Resultado del punto (b):

**Sí**, si se elimina la producción **R → L**, la gramática se **convierte en LL(1)**, porque:

- No hay intersección en los FIRST sets de L,
    
- S tiene una sola producción,
    
- Y no hay posibilidad de derivar ε.
    

---

## ✅ (c) ¿Es posible construir la tabla de análisis descendente (Top-Down) para la gramática G?

### 📜 Enunciado:

> ¿Es posible construir la tabla de análisis descendente (Top-Down) para la gramática G? Explique claramente.

### ✍️ Resolución:

La tabla **Top-Down** (predictiva) **sólo puede construirse si la gramática es LL(1)**.

Y como vimos en (a):

- G no es LL(1) porque hay ambigüedad entre **S → L=R** y **S → R**
    
- Específicamente, el FIRST de ambas se interseca en {*, i}
    
- Eso causa *_conflicto en la tabla M[S, _] y M[S, i]__
    

👉 Se genera conflicto de entrada en la tabla, por tanto no es construible sin ambigüedad.

### ✅ Resultado del punto (c):

**No**, no es posible construir la tabla **Top-Down** para la gramática G original, porque **no es LL(1)** y tiene **conflictos de selección** en el análisis descendente.

---

## 🧠 Ejercicio 3 — Parcial de Compiladores

### 📘 Gramática dada

Producciones:

```
S → SA | A  
A → a
```

Gramática extendida con símbolo inicial nuevo:

```
S' → S
```

---

### ✅ (a) Completar el autómata LR(0)

#### 📜 Enunciado

> Complete las etiquetas faltantes en el siguiente autómata LR(0) asociado a G. Justificar sus elecciones.

#### 🔍 Paso a paso

Los conjuntos de ítems son:

```
I₀ = {S' → ·S, S → ·SA, S → ·A, A → ·a}
I₁ = {S' → S·, S → S·A, A → ·a}
I₂ = {S → A·}
I₃ = {A → a·}
I₄ = {S → SA·}
```

Ahora determinamos las transiciones desde **I₀** según el símbolo **a**, **A**, y **S**:

1. **I₀ —a→ I₃**
    
    Desde A → ·a  
    ⇒ al ver `a`, pasamos a A → a· ⇒ estado **I₃**
    
2. **I₀ —A→ I₂**
    
    Desde S → ·A ⇒ al ver `A`, pasamos a S → A· ⇒ **I₂**
    
3. **I₀ —S→ I₁**
    
    Desde S' → ·S ⇒ al ver `S`, pasamos a S' → S· y S → S·A ⇒ **I₁**
    
4. **I₁ —A→ I₄**
    
    Desde S → S·A y A → ·a ⇒ al ver A ⇒ S → SA· ⇒ **I₄**
    

---

#### ✅ Resultado

```plaintext
Etiquetas faltantes:

I₀ —a→ I₃  
I₀ —A→ I₂  
I₀ —S→ I₁  
I₁ —A→ I₄
```

Justificación: cada transición responde al **símbolo a la derecha del punto (·)** en el conjunto actual.

---

### ✅ (b) Acciones en la tabla SLR

#### 📜 Enunciado

> Suponga que M es la tabla de análisis SLR para la gramática G. Describa una acción de reducción y una acción de desplazamiento (shift) que pueden encontrarse en M.

#### 🔍 Paso a paso

**Reducción (reduce):**

- En **I₃** está el ítem: `A → a·`
    
- Como el punto está al final ⇒ acción de **reducción** por `A → a`
    
- Esta reducción ocurre en las entradas `M[3, =]`, `M[3, a]`, etc. según el FOLLOW(A)
    

🔁 Necesitamos calcular **FOLLOW(A)**

- S → SA → A está al final ⇒ **FOLLOW(S) ⊆ FOLLOW(A)**
    
- S es símbolo inicial ⇒ $ ∈ FOLLOW(S) ⇒ $ ∈ FOLLOW(A)
    

Entonces:  
📌 **Reducción en M[3, $] = "reduce A → a"**

---

**Desplazamiento (shift):**

- En **I₀** hay `A → ·a`
    
- Si el siguiente símbolo es `a`, pasamos a `A → a·` ⇒ **I₃**
    

📌 Entonces:  
**Desplazamiento en M[0, a] = "shift 3"**

---

#### ✅ Resultado

```markdown
- Acción de desplazamiento:  
  - M[0, a] = shift 3  
  - Justificación: en I₀, `A → ·a`, al ver `a` nos movemos a `A → a·` (estado I₃)

- Acción de reducción:  
  - M[3, $] = reduce A → a  
  - Justificación: en I₃, `A → a·` y $ ∈ FOLLOW(A)
```

---

## 🧠 Ejercicio 4 — Máquina de Turing para L = { y c y | y ∈ {a, b}⁺ }

### 📜 Enunciado

> Considere el alfabeto Σ = {a, b, c}. Proponer en palabras una máquina de Turing que acepte el lenguaje:
> 
> L = { y c y | y ∈ {a, b}⁺ }
> 
> Describir claramente cada etapa de su propuesta.

---

### 🔍 Análisis del lenguaje

La máquina debe aceptar palabras que tengan la forma **ycy**, es decir, una palabra formada por una cadena **y** (compuesta solo de `a` y `b`, y no vacía), seguida de una `c`, y luego **la misma cadena y** repetida exactamente.

Ejemplos válidos:

- `aca`
    
- `abcab`
    
- `aabbc aabb`
    

Ejemplos **inválidos**:

- `caa` → empieza con `c`
    
- `aabcaabx` → contiene símbolo inválido
    
- `abcba` → la parte después de `c` no coincide con la anterior
    

---

### ✅ Propuesta de Máquina de Turing (en palabras)

Vamos a diseñar una MT que verifique que:

1. Hay una `c` en el medio.
    
2. Los símbolos a la izquierda y derecha de `c` son idénticos y en el mismo orden.
    
3. No hay más símbolos antes o después.
    

#### 🎯 Etapas de la Máquina

```markdown
1. **Buscar el primer símbolo (a o b) y marcarlo** con un símbolo especial, por ejemplo:  
   - a → à  
   - b → b̀

2. **Buscar hacia la derecha la `c`** y luego avanzar hasta encontrar el primer símbolo no marcado del otro lado. Comprobar que coincide:  
   - Si vimos `a` al inicio, debe haber `a` después de `c`.  
   - Si vimos `b`, debe haber `b`.  
   - Si no coincide → RECHAZAR.

3. **Marcar también ese símbolo en la segunda mitad** para no usarlo dos veces. Por ejemplo:  
   - a → á  
   - b → b́

4. **Regresar al inicio** (símbolo `⊢` o al primer blanco) y repetir el proceso hasta marcar todos los símbolos **antes y después** de la `c`.

5. Finalmente, cuando ya no quede nada por marcar, verificar que todos los símbolos están en pares `à...á`, `b̀...b́` y que no hay caracteres sin marcar.

6. Si se cumple todo → **ACEPTAR**, si no → **RECHAZAR**.
```

---

### 🧪 Simulación rápida (ejemplo)

Entrada: `abbcabb`

- Paso 1: marcar primer `a` como `à`
    
- Paso 2: ir al otro lado de `c`, buscar `a`, marcar `á`
    
- Repetir con `b`, `b` → `b̀` y `b́`, y así sucesivamente
    

Final: todo queda marcado correctamente, **ACEPTADO**.

---

### ✅ Resultado

```markdown
La Máquina de Turing marca uno a uno los símbolos de ambos lados de `c` y los compara.  
- Acepta solo si ambos lados son **idénticos**, en el mismo orden, y completamente marcados.  
- Rechaza si no hay `c`, si hay más símbolos, o si no hay coincidencia exacta.
```

---
