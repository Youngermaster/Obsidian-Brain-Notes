## ✅ ¿Qué es una gramática LL(1)?

Una gramática es **LL(1)** si:

- **L**: lee de **izquierda a derecha** el input.
    
- **L**: produce derivaciones **más a la izquierda** (leftmost derivation).
    
- **1**: toma decisiones con **una mirada de anticipación** (un símbolo de lookahead).
    

Esto permite crear **parsers predictivos**, como **recursive-descent sin backtracking**.

---

## 🧠 Condiciones para que una gramática sea LL(1)

Dada una no terminal AA con dos producciones:

```
A → α | β
```

La gramática es LL(1) si se cumplen las siguientes condiciones:

1. **FIRST(α) ∩ FIRST(β) = ∅**  
    (No hay ambigüedad en el símbolo que puede venir primero)
    
2. **A lo sumo una de α o β puede derivar ε**
    
3. **Si alguna deriva ε, su FIRST debe ser disjunto con FOLLOW(A)**  
    (para que sepamos cuándo usar la producción que deriva ε)
    

---

## 🧩 Ejemplo:

### Gramática:

```
E  → T E'
E' → + T E' | ε
T  → F T'
T' → * F T' | ε
F  → ( E ) | id
```

### FIRST y FOLLOW:

|No Terminal|FIRST|FOLLOW|
|---|---|---|
|E|{(, id}|{), $}|
|E'|{+, ε}|{), $}|
|T|{(, id}|{+, ), $}|
|T'|{*, ε}|{+, ), $}|
|F|{(, id}|{*, +, ), $}|

---

### ✅ Verificación de LL(1):

Para cada conjunto de producciones múltiples de un no terminal:

- **E' → + T E' | ε**
    
    - FIRST(+TE′)={+}\text{FIRST}(+TE') = \{+\}, FIRST(ε)={ε}\text{FIRST}(ε) = \{ε\}
        
    - FIRST(+TE′)∩FIRST(ε)=∅\text{FIRST}(+TE') ∩ \text{FIRST}(ε) = ∅ ✅
        
    - FIRST(ε)={ε}\text{FIRST}(ε) = \{ε\} → Revisar FIRST(+TE′)∩FOLLOW(E′)=∅\text{FIRST}(+TE') ∩ \text{FOLLOW}(E') = ∅
        
    - {+}∩{),$}=∅\{+\} ∩ \{), \$\} = ∅ ✅
        

Se cumplen todas las condiciones → **Sí es LL(1)** ✅

---

## 📋 Tabla Predictiva

La tabla predictiva es una matriz **M[A, a]**, donde:

- **A**: no terminal
    
- **a**: terminal o símbolo $
    

### Algoritmo:

Para cada producción A→αA \rightarrow \alpha:

1. Para cada terminal a∈FIRST(α)a \in \text{FIRST}(\alpha), agrega A→αA \rightarrow \alpha a M[A,a]M[A, a]
    
2. Si ε∈FIRST(α)ε \in \text{FIRST}(\alpha), entonces para cada b∈FOLLOW(A)b \in \text{FOLLOW}(A), agrega A→αA \rightarrow \alpha a M[A,b]M[A, b]
    

---

### 🧠 Ejemplo para E':

Producciones:

- E′→+TE′E' → + T E'
    
- E′→εE' → ε
    
- FIRST(+TE') = {+}
    
- FIRST(ε) = {ε}
    
- FOLLOW(E') = {), $}
    

Tabla:

||+|)|$|
|---|---|---|---|
|**E'**|E′→+TE′E' → +TE'|E′→εE' → ε|E′→εE' → ε|

---

## 🧠 Transición sin ambigüedad

La idea es: **miramos el próximo símbolo**, y gracias a FIRST y FOLLOW, **sabemos exactamente** qué producción aplicar. No necesitamos backtracking.

---

## 📌 Resumen

|Concepto|¿Qué implica?|
|---|---|
|LL(1)|Predictivo, sin backtracking|
|FIRST|Terminales que pueden comenzar una derivación|
|FOLLOW|Terminales que pueden seguir un no terminal|
|Condiciones|FIRST disjuntos, máximo un ε, disjuntos con FOLLOW|
