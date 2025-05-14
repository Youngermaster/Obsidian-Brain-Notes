## 🧠 ¿Qué es LR(0)?

Un **parser LR(0)** es un tipo de analizador **de abajo hacia arriba (bottom-up)** que:

- Lee la entrada de **izquierda a derecha (L)**.
    
- Construye derivaciones **más a la derecha (Rightmost) pero en orden inverso**.
    
- Usa **0 símbolos de lookahead**, o sea, toma decisiones _sin mirar hacia adelante_.
    

La sigla significa:

> **L**: lectura de izquierda a derecha  
> **R**: derivación más a la derecha  
> **0**: cero símbolos de anticipación (lookahead)

---

## 🎯 ¿Cuál es la idea clave?

- Tienes una **pila de símbolos** (o estados).
    
- Tomas decisiones de **"shift"** o **"reduce"** con base en los **estados del autómata LR(0)**.
    
- El autómata usa **items** con un punto (`·`) para saber en qué parte de una producción estás.
    

---

## 🔁 ¿Qué es un _item LR(0)_?

Dado: `A → XYZ`, se generan estos **items LR(0)**:

```
A → ·XYZ      # Nada reconocido aún
A → X·YZ      # Se ha reconocido X
A → XY·Z      # Se ha reconocido XY
A → XYZ·      # Se ha reconocido XYZ (¡listo para reducir!)
```

> El punto indica **"hasta aquí hemos leído"**.

---

## 🔍 ¿Qué es el autómata LR(0)?

Es un autómata finito determinista que:

- Usa **items LR(0)** como estados.
    
- Comienza con `S' → ·S`.
    
- Usa las funciones **CLOSURE** y **GOTO** para construir los estados y transiciones.
    

---

### ✳️ CLOSURE(I)

Dado un conjunto de items `I`, CLOSURE agrega todos los items posibles a partir de los no terminales a la derecha del punto.

Ejemplo:  
Si tienes `E → ·E + T`, como hay `E` a la derecha del punto, agregas todas las producciones de `E` con punto al inicio:

```
E → ·E + T
E → ·T
```

---

### ➡️ GOTO(I, X)

GOTO es: mover el punto sobre el símbolo `X` en todos los items donde ese `X` aparece a la derecha del punto, y luego cerrar.

Ejemplo:  
Si tienes `E → E ·+ T` en I y haces GOTO(I, +), obtienes:

```
E → E + ·T
y luego aplicas CLOSURE → agregas T → ·T * F, T → ·F, etc.
```

---

## 💡 Diferencia entre LL(1) y LR(0)

|Característica|LL(1)|LR(0)|
|---|---|---|
|Dirección|De arriba hacia abajo|De abajo hacia arriba|
|Derivación|Leftmost|Rightmost (inversa)|
|Necesita lookahead|Sí (1 símbolo)|No (usa 0 símbolos)|
|Backtracking|No|No|
|Manejo de ambigüedad|Menor (no permite conflictos)|Mayor poder (puede manejar más casos)|
|Tabla|Tabla predictiva (M[A,a])|Tabla de estados con shift/reduce|

---

## 🧪 Ejemplo completo: Gramática

```
S' → S
S → a S b | ε
```

### Items:

```
S' → ·S
S → ·a S b
S → ·ε
```

### Estado inicial:

```
I0 = CLOSURE({S' → ·S})
   = {
       S' → ·S
       S → ·a S b
       S → ·ε
     }
```

### Transiciones GOTO:

- GOTO(I0, a): Avanza el punto sobre `a`:
    
    ```
    S → a ·S b
    ⇒ CLOSURE → S → ·a S b, S → ·ε
    ```
    
- GOTO(I1, S): Mueve el punto sobre `S`:
    
    ```
    S' → S·  → listos para reducir S' → S
    ```
    

---

## 🧱 Tabla de parsing LR(0) (estructura base):

Utiliza dos estructuras:

- **ACTION**: indica shift/reduce/accept
    
- **GOTO**: indica transiciones sobre no terminales
    

|Estado|a|b|$|S|
|---|---|---|---|---|
|0|s2|||1|
|1|||acc||
|2|s2|r2||3|
|3||s4|||
|4||r1|r1||

(Esto es sólo una idea; puede variar según la gramática.)

---

## ✅ Conclusión clara

- **LR(0)** es más poderoso que **LL(1)**.
    
- Usa una máquina de estados que conoce las “situaciones” en las que está el parser, gracias a los items.
    
- No necesita mirar el siguiente símbolo (por eso "0").
    
- Conduce directamente a la creación de parsers **SLR, LALR y LR(1)** más complejos.
    
