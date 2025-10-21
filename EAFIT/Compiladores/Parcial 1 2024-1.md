## 1) Sea $L=\{\,00\,0^n\mid n\ge 0\,\}\ \cup\ \{\,11\,1^m\mid m\ge 0\,\}$. 
Es decir, $L=\{\,0^k\mid k\ge 2\,\}\ \cup\ \{\,1^k\mid k\ge 2\,\}$.

---

### (a) Construir un autómata finito que acepte $L^\*$

**Caracterización de $L^\*$:** es el conjunto de todas las cadenas sobre $\{0,1\}$ cuya descomposición en corridas (bloques máximos) de símbolos iguales **no contiene bloques de tamaño 1**.  
Equivalente: en la cadena **no aparecen ceros o unos aislados**. La cadena vacía también pertenece a $L^\*$.

**DFA (con arte ASCII):**

- Estados y su intuición:
  - `S` : inicio (aceptante). Aún no he visto nada.
  - `Z1`: la cadena termina en **exactamente un 0** (bloque de 1 ⇒ todavía no válido).
  - `Z2`: la cadena termina en **al menos dos 0** (bloque validado).
  - `O1`: la cadena termina en **exactamente un 1**.
  - `O2`: la cadena termina en **al menos dos 1**.
  - `D` : pozo (se violó la regla: apareció un bloque de tamaño 1).

- Estados de aceptación: `S`, `Z2`, `O2`.  
- Alfabeto: $\{0,1\}$.

             0                 1
         +-------+         +-------+
         v       |         v       |
(acc) +----+ +----+ +----+ +----+  
S ------>| Z1 |---| Z2 |<--| O1 |---| O2 |  
^ 1 +----+0 +----+1 +----+0 +----+1  
| ^ |  
|_______________________**|**_____________|  
else -> D (pozo)

Transiciones detalladas:  
S --0--> Z1 S --1--> O1  
Z1 --0--> Z2 Z1 --1--> D  
Z2 --0--> Z2 Z2 --1--> O1  
O1 --1--> O2 O1 --0--> D  
O2 --1--> O2 O2 --0--> Z1  
D --0/1--> D

**Correctitud (esbozo):**
- Si se cambia de símbolo estando en `Z1` u `O1`, se formó un bloque de tamaño 1 ⇒ ir a `D`.  
- Si se permanece en el mismo símbolo desde `Z1`/`O1`, se pasa a `Z2`/`O2` (bloque de tamaño ≥2), desde donde ya es lícito alternar a un bloque nuevo (`Z2 --1--> O1`, `O2 --0--> Z1`) que empieza con longitud 1 y debe crecer a 2 para volver a aceptar.  
- `S` acepta $\varepsilon$ y actúa como preámbulo de la primera corrida.

Con esto, el autómata acepta exactamente $L^\*$.

---

### (b) Proponer una expresión regular que denote el lenguaje $\{0,1\}LL^\*$

Recordemos:
$$
L=(00\,0^\*)\ \cup\ (11\,1^\*),\qquad
L^\*=(\,(00\,0^\*)\ \cup\ (11\,1^\*)\,)^\*.
$$

Entonces
$$
\{0,1\}LL^\*=(0\mid 1)\,( (00\,0^\*)\mid(11\,1^\*) )\,( (00\,0^\*)\mid(11\,1^\*) )^\*.
$$

Si se permite el operador “uno o más” $(\cdot)^+$, se simplifica a:
$$
(0\mid 1)\,\big( (00\,0^\*)\mid(11\,1^\*) \big)^+.
$$

**Intuición:** la cadena comienza con un símbolo cualquiera, y a partir de ahí se concatenan **bloques de longitud al menos 2** todos iguales (ya sea de ceros o de unos). El primer bloque “especial” que exige el enunciado está en $L$ y el resto en $L^\*$.


## 2) Dado el siguiente autómata no determinístico \(N\)

Estados: \(p\) (inicial y aceptante), \(q\) (aceptante), \(r\) (inicial, no aceptante).  
Transiciones: \(q \xleftrightarrow[\ 0\ ]{\ 0\ } r\) y lazo \(r \xrightarrow{1} r\). No hay otras transiciones.

---

### (a) Determinar \(L(N)\)

Desde \(p\):
- No hay transiciones; como \(p\) es aceptante, se acepta \(\varepsilon\).

Desde \(r\):
- Se puede consumir cualquier cantidad de \(1\)s quedando en \(r\).
- Con un \(0\) se pasa a \(q\). Si la entrada termina allí, se acepta.
- Si se continúa desde \(q\), **obligatoriamente** debe venir un \(0\) (no hay transición con \(1\)), con lo cual se vuelve a \(r\). Desde \(r\) se pueden leer de nuevo \(1^\*\) y otro \(0\) para volver a \(q\), y así sucesivamente.

Por tanto, las cadenas aceptadas desde \(r\) tienen la forma:
$$
1^\*\,0\,(0\,1^\*\,0)^\*,
$$
y además se acepta \(\varepsilon\) por el estado \(p\).

Conclusión:
$$
L(N)=\{\varepsilon\}\ \cup\ \{\,1^\*0(0\,1^\*0)^\*\,\}.
$$

(Equivalente: cadenas con **al menos un 0**, que **terminan en 0** y en las que cada \(0\) salvo el último está seguido inmediatamente por otro \(0\); entre pares de ceros pueden intercalarse corridas de \(1\)s. El número de ceros es impar.)

---

### (b) AFD que acepta el mismo lenguaje

Aplicamos construcción por subconjuntos.  
Conjunto de estados del AFD: \(\mathcal{P}(\{p,q,r\})\).  
Estado inicial: \(\{p,r\}\) (ambos eran iniciales).  
Estados aceptantes: los subconjuntos que contengan \(p\) o \(q\).

Transiciones del AFD (por símbolo \(a\), se une la imagen de cada estado NDFA en el subconjunto):

- $\{p,r\} \xrightarrow{0} \{q\}\), \(\{p,r\} \xrightarrow{1} \{r\}$.
- $\{q\} \xrightarrow{0} \{r\}\), \(\{q\} \xrightarrow{1} \varnothing$.
- $\{r\} \xrightarrow{0} \{q\}\), \(\{r\} \xrightarrow{1} \{r\}$.
- $\varnothing \xrightarrow{0} \varnothing), (\varnothing \xrightarrow{1} \varnothing)$.

Estados relevantes (los alcanzables) y aceptación:
- $(A=\{p,r\}) — **aceptante** (contiene (p))$.
- $(B=\{q\})$ — **aceptante**.
- \(C=\{r\}\) — no aceptante.
- $\(D=\varnothing\)$ — no aceptante (pozo).

**Diagrama ASCII del AFD:**
0            1
(acc) A --------> B ----1----> D  
| |0  
|1 v  
v C  
C --0--> B ^1  
^ |  
|____________|  
1  
(‘D’ es pozo: D --0/1--> D)

Leyenda:  
A = {p,r} (inicial, aceptante)  
B = {q} (aceptante)  
C = {r} (no aceptante)  
D = ∅ (pozo)


Este AFD acepta exactamente $L(N)=\{\varepsilon\}\cup 1^\*0(0\,1^\*0)^\*$.


## 3) Demostrar que $L$ es regular usando propiedades de clausura

**Enunciado.**  
Sea $\Sigma=\{0,1\}$ y $L=L_1 L_2 L_3$, donde:

- $L_1$: lenguaje de cadenas que contienen solamente el símbolo $1$ y tienen longitud al menos tres.  
- $L_2$: lenguaje que únicamente contiene la cadena $0$.  
- $L_3$: estrella de Kleene del lenguaje $\{111,0\}$.

---

### Paso 1: Demostrar que $L_1$ es regular

$L_1$ contiene solo $1$'s y longitud $\ge 3$:
$$
L_1 = \{1^n \mid n \ge 3\}.
$$
Esto se describe con la expresión regular:
$$
L_1 = 111\,1^\*.
$$
Las expresiones regulares definen lenguajes regulares, por lo que $L_1$ es regular.

---

### Paso 2: Demostrar que $L_2$ es regular

$L_2$ es:
$$
L_2 = \{0\}.
$$
Un lenguaje finito es regular (cualquier conjunto finito es regular).

---

### Paso 3: Demostrar que $L_3$ es regular

$L_3$ es la estrella de Kleene de $\{111,0\}$:
$$
L_3 = \{111,0\}^\*.
$$
- El lenguaje base $\{111,0\}$ es finito ⇒ regular.
- Los lenguajes regulares son cerrados bajo estrella de Kleene ⇒ $L_3$ es regular.

---

### Paso 4: Demostrar que $L$ es regular

Por definición:
$$
L = L_1 L_2 L_3.
$$
- La concatenación de lenguajes regulares produce un lenguaje regular (clausura por concatenación).
- Como $L_1$, $L_2$ y $L_3$ son regulares, $L$ también lo es.

---

**Conclusión:**
$$
L \ \text{es regular}.
$$

## 4) Para un AFND $N=(Q,\Sigma,\Delta,S,F)$ y $A,B\subseteq Q$, $x\in\Sigma^\*$, probar:
$$
\widehat{\Delta}(A\cup B,x)\;=\;\widehat{\Delta}(A,x)\ \cup\ \widehat{\Delta}(B,x).
$$

### Definiciones que usaremos
- Para estado $q\in Q$ y símbolo $a\in\Sigma$: $\Delta(q,a)\subseteq Q$.
- Para conjunto de estados $C\subseteq Q$ y símbolo $a\in\Sigma$:
  $$
  \Delta(C,a)\;=\;\bigcup_{q\in C}\Delta(q,a).
  $$
- Extensión a cadenas:
  $$
  \widehat{\Delta}(C,\varepsilon)=C,\qquad
  \widehat{\Delta}(C,xa)=\Delta\!\big(\widehat{\Delta}(C,x),a\big)\quad (x\in\Sigma^\*,\,a\in\Sigma).
  $$

### Lema auxiliar (distributividad en un símbolo)
Para todo $C,D\subseteq Q$ y $a\in\Sigma$:
$$
\Delta(C\cup D,a)=\Delta(C,a)\cup\Delta(D,a).
$$
**Prueba del lema.**  
Por definición:
$$
\Delta(C\cup D,a)=\bigcup_{q\in C\cup D}\Delta(q,a)
=\left(\bigcup_{q\in C}\Delta(q,a)\right)\ \cup\ \left(\bigcup_{q\in D}\Delta(q,a)\right)
=\Delta(C,a)\cup\Delta(D,a).
$$
$\square$

### Prueba principal por inducción sobre $|x|$

**Base ($x=\varepsilon$).**
$$
\widehat{\Delta}(A\cup B,\varepsilon)=A\cup B
=\widehat{\Delta}(A,\varepsilon)\cup\widehat{\Delta}(B,\varepsilon).
$$

**Paso inductivo.**  
Supongamos cierto para $x\in\Sigma^\*$ y probemos para $xa$ con $a\in\Sigma$:
$$
\begin{aligned}
\widehat{\Delta}(A\cup B,xa)
&=\Delta\!\big(\widehat{\Delta}(A\cup B,x),a\big) \\
&=\Delta\!\big(\widehat{\Delta}(A,x)\cup\widehat{\Delta}(B,x),a\big) \qquad\text{(H.I.)}\\
&=\Delta\!\big(\widehat{\Delta}(A,x),a\big)\ \cup\ \Delta\!\big(\widehat{\Delta}(B,x),a\big)\qquad\text{(lema)}\\
&=\widehat{\Delta}(A,xa)\ \cup\ \widehat{\Delta}(B,xa).
\end{aligned}
$$

Con esto, por inducción se tiene para toda $x\in\Sigma^\*$:
$$
\widehat{\Delta}(A\cup B,x)=\widehat{\Delta}(A,x)\cup\widehat{\Delta}(B,x).
$$























hagamos lo mismo con el siguiente punto 3
3. Considere el siguiente par de AFDs
q | a b
-> 0 1 2
1F 2 0
2F 0 1

q | a b
-> 0F 2 1
1 0 2
2F 1 0

Mediante la construcción producto determine un AFD que acepte:
a. La intersección de los conjuntos aceptados por autómatas.
b. La unión de los conjuntos aceptados por los autómatas.
En cada caso, dibuje el diagrama del autómata resultante indicando claramente los estados de inicio y aceptación.

Ayúdame a realizar el punto 1 y el 2 de la imagen para el autómata finito no determinista (AFN), acuérdate lo que hablamos del $STUFF$ y $$STUFF$$