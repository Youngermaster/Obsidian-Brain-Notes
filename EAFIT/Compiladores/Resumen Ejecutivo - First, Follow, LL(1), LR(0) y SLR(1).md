## 1. FIRST y FOLLOW

### ● FIRST(X)

- El conjunto FIRST(X) contiene los terminales que pueden aparecer al inicio de alguna cadena derivada de X.
    
- Si X puede derivar ε (la cadena vacía), entonces ε también pertenece a FIRST(X).
    

**Ejemplo:**  
Si tenemos:

```
A → aB | ε
B → b
```

Entonces:

- FIRST(A) = {a, ε}
    
- FIRST(B) = {b}
    

### ● FOLLOW(X)

- FOLLOW(X) contiene los terminales que pueden aparecer justo después del no terminal X en alguna derivación.
    
- Si X puede estar al final de una producción, se incluye el símbolo $ (fin de cadena) en FOLLOW(X).
    

**Ejemplo:**  
Para:

```
S → AB
A → a | ε
B → b
```

- FOLLOW(S) = {$}
    
- FOLLOW(A) = {b}
    
- FOLLOW(B) = {$}
    

---

## 2. LL(1)

### ● Qué es:

- Análisis **top-down**, sin backtracking.
    
- Usa **una unidad de lookahead** para elegir la producción.
    
- Requiere que los conjuntos FIRST y FOLLOW no tengan intersección para decidir sin ambigüedades.
    

### ● Reglas para ser LL(1):

Para dos producciones A → α | β:

1. FIRST(α) ∩ FIRST(β) = ∅
    
2. A lo sumo una de α o β puede derivar ε.
    
3. Si β ⇒* ε, entonces FIRST(α) ∩ FOLLOW(A) = ∅
    

### ● Ejemplo de gramática LL(1):

```
S → a A
A → b | ε
```

- FIRST(A) = {b, ε}
    
- FOLLOW(A) = {$}
    

Tabla predictiva:

|a|b|$|
|---|---|---|---|
|S|S → aA|||
|A||A → b|A → ε|

---

## 3. LR(0)

### ● Qué es:

- Análisis **bottom-up**.
    
- Usa estados construidos a partir de **items LR(0)**.
    
- No tiene lookahead, solo se basa en los items y el autómata.
    

### ● Item LR(0):

Un item es una producción con un punto indicando qué se ha procesado.

Para `A → XYZ`, los items LR(0) son:

```
A → ·XYZ
A → X·YZ
A → XY·Z
A → XYZ·
```

### ● Construcción del autómata LR(0):

1. **CLOSURE(I)**: agrega items con el punto antes de un no terminal.
    
2. **GOTO(I, X)**: mueve el punto sobre X y aplica CLOSURE.
    

---

## 4. SLR(1)

### ● Qué es:

- Versión simplificada de LR(1).
    
- Usa el autómata LR(0), pero agrega 1 símbolo de lookahead **usando FOLLOW**.
    
- Más potente que LL(1).
    

### ● Tabla SLR(1):

Tiene dos componentes:

- **ACTION[state, terminal]**: shift, reduce, accept o error.
    
- **GOTO[state, no-terminal]**: transición en autómata.
    

Una reducción A → β se aplica si:

- El item `A → β·` está en el estado
    
- El símbolo actual está en FOLLOW(A)
    

### ● Ejemplo:

```
S' → S
S → aS | ε
```

- FIRST(S) = {a, ε}
    
- FOLLOW(S) = {$, a}
    

Tabla:

|Estado|a|$|S|
|---|---|---|---|
|0|s1|r2|2|
|1|s1|r2|2|
|2||acc||

---

## 5. Comparación

|LL(1)|LR(0)|SLR(1)|
|---|---|---|---|
|Tipo|Top-down|Bottom-up|Bottom-up|
|Lookahead|1 terminal|0|1 (FOLLOW)|
|Ambigüedad|No permitida|Puede fallar|Más robusta|
|Tabla|Predictiva|Items + trans|ACTION + GOTO|
|Derivación|Leftmost|Rightmost inv|Rightmost inv|

---
