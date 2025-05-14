
Construir una tabla de análisis sintáctico descendente para las siguiente gramáticas (verificar primero si son LL(1)):

### 1
S -> ABC
A -> dA | f
B -> $\epsilon$
C -> S | mA | $\epsilon$


|     | First                 | Follow       |
| --- | --------------------- | ------------ |
| S   | {d, f}                | {$}          |
| A   | {d, f}                | {d, f, m, $} |
| B   | {$\epsilon$}          | {d, f, m, $} |
| C   | {m, $\epsilon$, d, f} | {$}          |
#### ¿Es LL(1)?

Sí
#### La Tabla de Análisis Sintáctico Descendente

| **No terminal** | **d**     | **f**     | **m**  | **$** |
| --------------- | --------- | --------- | ------ | ----- |
| **S**           | S → A B C | S → A B C | error  | error |
| **A**           | A → dA    | A → f     | error  | error |
| **B**           | B → ε     | B → ε     | B → ε  | B → ε |
| **C**           | C → S     | C → S     | C → mA | C → ε |
Using [[Predictive Parsing Algorithm]]:

| Cadena | Pila  |
| ------ | ----- |
| dfdf$  | S$    |
| dfdf$  | ABC$  |
| dfdf$  | dABC$ |
| fdf$   | ABC$  |
| fdf$   | fBC$  |
| df$    | BC$   |
| df$    | C$    |
| df$    | S$    |
| df$    | ABC$  |
| df$    | dABC$ |
| f$     | ABC$  |
| f$     | fBC$  |
| $      | BC$   |
| $      | C$    |
| $      | $     |

### 2

S -> S + S | S * S | n
### 3

S -> AB | CD
A -> aAb | $\epsilon$
B -> cB | c
C -> nC | $\epsilon$
D -> m | $\epsilon$
