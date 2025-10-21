	

```markdown
## 3) Construcción producto de dos AFDs y lenguajes de intersección / unión

### AFDs dados (tablas)

AFD₁ (inicial: 0; finales: {1,2})
```

δ₁ | a b  
------+-------  
-> 0 | 1 2  
1F | 2 0  
2F | 0 1

```

AFD₂ (inicial: 0; finales: {0,2})
```

δ₂ | a b  
------+-------  
-> 0F | 2 1  
1 | 0 2  
2F | 1 0



### Construcción producto

- Conjunto de estados: $Q = Q_1 \times Q_2 = \{0,1,2\}\times\{0,1,2\}$.
- Estado inicial: $(0,0)$.
- Transición: $\delta((i,j),x)=(\delta_1(i,x),\delta_2(j,x))$.
- Alcanzables desde $(0,0)$:
  - $(0,0) \xrightarrow{a} (1,2)$, $(0,0) \xrightarrow{b} (2,1)$
  - $(1,2) \xrightarrow{a} (2,1)$, $(1,2) \xrightarrow{b} (0,0)$
  - $(2,1) \xrightarrow{a} (0,0)$, $(2,1) \xrightarrow{b} (1,2)$

Solo **tres** estados son alcanzables: $\{(0,0),(1,2),(2,1)\}$, con el siguiente grafo:


```
       a                 b
  +----------+     +----------+
  v          |     v          |
```

(0,0) -------> (1,2) <------- (2,1)  
^ \ ^ \ ^  
| \ b | \ a | \ a  
| \ | \ |  
+----- (2,1) +----- (0,0) +----- (0,0)


---

## (a) Intersección: $L(AFD_1) \cap L(AFD_2)$

- Conjunto de aceptación del producto para **intersección**:
  $$
  F_{\cap}=\{(i,j)\mid i\in F_1\ \text{y}\ j\in F_2\}=\{(1,0),(1,2),(2,0),(2,2)\}.
  $$
- Entre los alcanzables, solo **$(1,2)$** pertenece a $F_{\cap}$.

**AFD resultante (intersección):**
- Estados: $\{(0,0),(1,2),(2,1)\}$.
- Inicial: $(0,0)$.
- Finales: $\{(1,2)\}$.
- Transiciones (resumidas):
  - $(0,0)\xrightarrow{a}(1,2)$, $(0,0)\xrightarrow{b}(2,1)$
  - $(1,2)\xrightarrow{a}(2,1)$, $(1,2)\xrightarrow{b}(0,0)$
  - $(2,1)\xrightarrow{a}(0,0)$, $(2,1)\xrightarrow{b}(1,2)$

Diagrama ASCII (marcando final con “*”):
```

(0,0) --a--> (1,2)* --a--> (2,1)  
| ^ |  
b |b b  
v | v  
(2,1) <-------- + ----------- (0,0)  
a

```

---

## (b) Unión: $L(AFD_1) \cup L(AFD_2)$

- Conjunto de aceptación del producto para **unión**:
  $$
  F_{\cup}=\{(i,j)\mid i\in F_1\ \text{o}\ j\in F_2\}.
  $$
- Los alcanzables $(0,0)$, $(1,2)$ y $(2,1)$ **todos** están en $F_{\cup}$ (porque $0\in F_2$, $2\in F_1$, etc.).

**AFD resultante (unión):**
- Estados: $\{(0,0),(1,2),(2,1)\}$.
- Inicial: $(0,0)$.
- Finales: **todos** los alcanzables.
- Transiciones: exactamente las mismas que arriba.

Diagrama ASCII:
```

(0,0)* --a--> (1,2)* --a--> (2,1)*  
| ^ |  
b |b b  
v | v  
(2,1)* <------- + ------------ (0,0)*  
a

```

**Observación:** En (b) el estado inicial es aceptante y todos los estados alcanzables también, por lo que el lenguaje aceptado por el AFD de unión es $\Sigma^\*$.




---

## 1) AFD que acepte las cadenas que **empiezan con `b`** y tienen **número par de `a`**

**Lenguaje.**  
$L=\{\,w\in\{a,b\}^\* \mid \text{$w$ empieza con $b$ y $\#a(w)$ es par}\,\}$.

### Diseño del AFD

- Necesitamos controlar **paridad de `a`** (par / impar) **después** de leer el primer símbolo.
- Además, **la primera letra debe ser `b`**; si empieza por `a`, rechazamos.

**Estados (intuición).**
- `S` : inicio (aún no he leído nada).
- `Bpar` : ya leí el primer símbolo `b` y el número de `a` vistos es **par**.
- `Bimp` : ya leí el primer símbolo `b` y el número de `a` vistos es **impar**.
- `R` : pozo de rechazo (porque la cadena empezó por `a`).

**Inicio y aceptación.**
- Estado inicial: `S`.
- Estados de aceptación: solo `Bpar` (porque se exige paridad de `a` y que haya empezado por `b`).

**Transiciones.**
```

S --b--> Bpar  
S --a--> R

Bpar --a--> Bimp  
Bpar --b--> Bpar

Bimp --a--> Bpar  
Bimp --b--> Bimp

R --a--> R  
R --b--> R

```

### Tabla de transición

| Estado | a     | b     | ¿Acepta? |
|-------:|:-----:|:-----:|:--------:|
| S      | R     | Bpar  | —        |
| Bpar   | Bimp  | Bpar  | ✓        |
| Bimp   | Bpar  | Bimp  | —        |
| R      | R     | R     | —        |

### Verificación pedida

Usamos la función de transición multipaso $\hat\delta$.

1) Para `baba`:
$$
\hat\delta(S,b)=\text{Bpar},\ 
\hat\delta(\text{Bpar},a)=\text{Bimp},\ 
\hat\delta(\text{Bimp},b)=\text{Bimp},\ 
\hat\delta(\text{Bimp},a)=\text{Bpar}\in F.
$$
Por tanto, $\hat\delta(S,\texttt{baba})\in F$ y `baba` es **aceptada**.

2) Para `aba`:
$$
\hat\delta(S,a)=R,\quad
\hat\delta(R,ba)=R\notin F.
$$
Luego $\hat\delta(S,\texttt{aba})\notin F$ y `aba` es **rechazada**.

---

## 2) AFN de 4 estados (como en la figura)

A continuación **transcribo el AFN exactamente como se aprecia en el diagrama** (estados de izquierda a derecha: $s,t,u,v$; inicial $s$; **final** $v$):

- Transiciones con `b` (la cadena lineal):
  $$
  s \xrightarrow{b} t,\quad t \xrightarrow{b} u,\quad u \xrightarrow{b} v.
  $$
- Transiciones con `a` (arcos curvos):
  $$
  s \xrightarrow{a} s,\quad s \xrightarrow{a} u,\quad s \xrightarrow{a} v,\quad v \xrightarrow{a} s.
  $$
- No hay transiciones $\varepsilon$.

> Si tu dibujo tiene una ligera variación, el método que sigue (construcción por subconjuntos) es el mismo; solo ajusta las transiciones.

### 2a) Una cadena que **empieza con `a`** y **no** es aceptada

La palabra `ab` **no** es aceptada.

Justificación (búsqueda de alguna rama que termine en $v$):
- Desde $s$ con `a` se puede ir a $\{s,u,v\}$.
- Si tomamos la rama que cae en $v$, ya no podemos leer `b` (no hay transición con `b$ desde $v$); esa rama muere.
- Las otras ramas: $s\xrightarrow{a}s$ y $s\xrightarrow{a}u$.  
  Con el `b` siguiente, $s\xrightarrow{b}t$ y $u\xrightarrow{b}v$.  
  La única rama que llega a $v$ necesita dos símbolos (`\dots bb`), pero aquí se acaba la entrada.  
  Resultado: ninguna rama termina en $v$ al consumir toda la entrada; `ab` **no** se acepta.

### 2b) AFD equivalente por **método de los subconjuntos**

- Conjunto base: $\{s,t,u,v\}$.
- Estado inicial del AFD: $\{s\}$.
- Un estado del AFD es **aceptante** si contiene $v$.
- No hay $\varepsilon$-cierres que calcular.

Calculemos $\Delta(C,x)=\bigcup_{q\in C}\Delta(q,x)$:

1. Desde $\{s\}$:
   $$
   \Delta(\{s\},a)=\{s,u,v\},\qquad \Delta(\{s\},b)=\{t\}.
   $$
   Denotemos $X=\{s,u,v\}$ y $T=\{t\}$.

2. Desde $X=\{s,u,v\}$:
   $$
   \Delta(X,a)=\{s,u,v\}\ (\text{porque } s{\xrightarrow a}\{s,u,v\},\ v{\xrightarrow a}\{s\}),\quad
   \Delta(X,b)=\{t,v\}.
   $$
   Denotemos $Y=\{t,v\}$.

3. Desde $T=\{t\}$:
   $$
   \Delta(T,a)=\varnothing,\qquad \Delta(T,b)=\{u\}.
   $$
   Denotemos $U=\{u\}$.

4. Desde $Y=\{t,v\}$:
   $$
   \Delta(Y,a)=\{s\},\qquad \Delta(Y,b)=\{u\}.
   $$

5. Desde $U=\{u\}$:
   $$
   \Delta(U,a)=\varnothing,\qquad \Delta(U,b)=\{v\}.
   $$

6. Desde $\{v\}$:
   $$
   \Delta(\{v\},a)=\{s\},\qquad \Delta(\{v\},b)=\varnothing.
   $$

7. Desde $\varnothing$:
   $$
   \Delta(\varnothing,a)=\varnothing,\qquad \Delta(\varnothing,b)=\varnothing.
   $$

**Estados alcanzables del AFD:**  
$\{s\},\ X=\{s,u,v\},\ T=\{t\},\ Y=\{t,v\},\ U=\{u\},\ \{v\},\ \varnothing$.

**Estados aceptantes:** los que contienen $v$  
$\Rightarrow \{s,u,v\},\ \{t,v\},\ \{v\}$.

### Tabla del AFD resultante

| Estado          | a           | b        | ¿Acepta? |
|----------------:|:-----------:|:--------:|:--------:|
| $\{s\}$         | $\{s,u,v\}$ | $\{t\}$  | —        |
| $\{s,u,v\}$     | $\{s,u,v\}$ | $\{t,v\}$| ✓        |
| $\{t\}$         | $\varnothing$ | $\{u\}$| —        |
| $\{t,v\}$       | $\{s\}$     | $\{u\}$  | ✓        |
| $\{u\}$         | $\varnothing$ | $\{v\}$| —        |
| $\{v\}$         | $\{s\}$     | $\varnothing$ | ✓   |
| $\varnothing$   | $\varnothing$ | $\varnothing$ | — |

(Estados inaccesibles: ninguno adicional; todos los de la tabla son alcanzables desde $\{s\}$.)

### Diagrama ASCII del AFD (marco general)

```

```
                a                      b
```

[s] -------> [s,u,v]* ---------------> [t,v]*  
| ^ a | b  
| b | v  
v | [u]  
[t] --a--> [∅] | | b  
| b ____________________/  
v  
[u] --b--> [v]* --a--> [s] (∅ --a/b--> ∅)

```

Corchetes indican subconjuntos; “*” marca aceptación (contiene $v$).

> Si e diagrama original difiere levemente (por ejemplo, si faltara alguna transición con `a` desde $s$), repite exactamente el mismo procedimiento de arriba: enumera $\Delta(\{s\},a)$ y $\Delta(\{s\},b)$, sigue expandiendo los subconjuntos alcanzables, y marca como aceptantes los que contengan el/los estados finales del AFN.
`