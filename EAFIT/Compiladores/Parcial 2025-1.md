## 1 Se definen las gramáticas $G_1$ y $G_2$ como

$S_{1} \rightarrow aS_{1}$ | $b$
y
$S_{2} \rightarrow aS_{2}b$ | $\varepsilon$
respectivamente. Note que $G_1$ y $G_2$ son $LL(1)$.
La gramática $G$ se define a partir de $G_1$ y $G_2$ como sigue:
- Todos los terminales, no terminales y las producciones de $G_1$ y $G_2$ hacen parte de $G$.
- Se define el símbolo inicial $S$ para $G$ tal que $S \neq S_1$ y $S \neq S_2$.
- Se agregan las producciones $S \rightarrow S_1$ | $S_2$ a $G$.
¿La gramática $G$ es $LL(1)$? Justifique la respuesta.

| No Terminal | FIRST    | FOLLOW |
| ----------- | -------- | ------ |
| S₁          | { a, b } | { $ }  |
| S₂          | { a, ε } | { $ }  |
| S           | { a, b } | { $ }  |


**Análisis de $FIRST(S)$** con $S \rightarrow S_1 \mid S_2$:

- $FIRST(S_1) = {a, b}$

- $FIRST(S_2) = {a, \varepsilon}$  
	🔥 **Intersección no vacía: ambos tienen "a"** 


 Con la intersección entre FIRST(S_1) y FIRST(S_2)** podemos concluir que **no es LL(1)**.

## 2 Si la gramática $G$ del punto 1 es $LL(1)$
2 Si la gramática $G$ del punto 1 es $LL(1)$, enuncie la propiedad que se está ejemplificando y justifique su propuesta. En caso contrario, ¿es posible que la gramática sea $LL(1)$ si se imponen condiciones adicionales sobre $G_1$ y $G_2$? Indique cuáles y justifique

### ¿Se puede hacer que $G$ sea LL(1)?

Sí, **imponiendo condiciones adicionales**:

#### Condición posible:

Que $FIRST(S_1)$ y $FIRST(S_2)$ sean **disjuntos**, es decir:

- Cambiar las producciones de $G_1$ y/o $G_2$ para que **no comiencen con el mismo símbolo terminal**.
    
- Por ejemplo, si:
    
    - $G_1$: empieza con `a`
        
    - $G_2$: empieza con otro terminal como `c`, o que **$G_2$ no derive ε**
        

Cambiar $G_2$ para evitar que derive `ε`, o para que empiece con un terminal que **no esté en $FIRST(S_1)$**.  
Esto haría que $FIRST(S_1) \cap FIRST(S_2) = \emptyset$ ✅ y permitiría que la gramática combinada sea LL(1).

## 3 Demostrar o refutar

Sea $G$ una gramática libre de contexto, $x$ un símbolo de $G$ y $K$ y $J$ conjuntos de ítems formados a partir de las producciones $G$. Si GoTo($K$, $x$) = GoTo($J$, $x$) entonces $J = K$
## 4 Considere la gramática libre de contexto G

```
S -> SaAbBC | ε
A -> AcA | c
B -> BB | ε
C -> a | b | c
```


Indique si las siguientes afirmaciones son verdaderas o falsas:
### (a)
$\$ \in Follow(C)$

### (b)
$First(B) - \{\varepsilon\} \subseteq Follow(B)$

### (c)
$First(A) - \{\varepsilon\} \subseteq Follow(Ac)$

| No Terminal | FIRST                    | FOLLOW    |
| ----------- | ------------------------ | --------- |
| S           | {a, b, c, $\varepsilon$} | {$}       |
| A           | {c}                      | {a, b, c} |
| B           | {$\varepsilon$}          | {a, b, c} |
| C           | {a, b, c}                | {$}       |
