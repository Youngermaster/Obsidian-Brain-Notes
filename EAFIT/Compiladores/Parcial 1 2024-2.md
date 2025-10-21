# 1) Lenguaje H⊆{0,1}∗H\subseteq\{0,1\}^* con todas las cadenas **distintas** de `11` y `111`

Definiciones:

- LL es **reflexivo** si $\varepsilon \in L$.
- LL es **transitivo** si $LL \subseteq L$ (i.e., si $x,y\in L \Rightarrow xy\in L$).

Sea

$H={0,1}∗∖{11,111}.H=\{0,1\}^*\setminus\{\texttt{11},\texttt{111}\}.$


---

## (a) HH es reflexivo ✅

**Demostración.** 
$ε∈{0,1}∗\varepsilon \in \{0,1\}^* y ε≠11\varepsilon\neq\texttt{11}, ε≠111\varepsilon\neq\texttt{111}$.  
Luego $ε∈H\varepsilon\in H. Por definición, H es reflexivo. \square$

---

## (b) HH es transitivo ❌

**Refutación por contraejemplo.**  
Tómese $x=1x=\texttt{1}$ y $y=1y=\texttt{1}$.  
Ambos pertenecen a HH porque HH solo excluye exactamente `11` y `111`.

Sin embargo, $xy=11=11∉Hxy=\texttt{1}\texttt{1}=\texttt{11}\notin H$.

Por lo tanto, existe $x,y∈Hx,y\in H$ tal que $xy∉Hxy\notin H.$ 
Concluimos que $HH\nsubseteq H; H$  **no** es transitivo. □\square


## 2. Considere el alfabeto Σ = {0,1}.

---

### (a) Diseñar un autómata finito determinístico para el lenguaje 
$(L \subseteq \Sigma^*)$ de cadenas que contienen **una cantidad par de ceros** o **exactamente dos unos**.  
Por ejemplo: `001 ∈ L`, `0011 ∈ L` pero `1000 ∉ L`.

**Idea:**
- Condición 1: Paridad de ceros ⇒ usar dos estados para el conteo (Par/Impar).
- Condición 2: Exactamente dos unos ⇒ usar tres estados para contar (0 unos, 1 uno, 2 unos, y un pozo para más de 2).
- La aceptación se da si:
  - Estamos en estado "par de ceros", o
  - Estamos en estado "exactamente dos unos".

El autómata es la combinación de estas dos condiciones (producto cartesiano de estados).

**Diagrama ASCII simplificado (cada estado es un par `(ceros_paridad, contador_unos)`):**

Estados:  
P0 = (Par, 0 unos)  
P1 = (Par, 1 uno)  
P2 = (Par, 2 unos)
P3 = (Par, >2 unos)  
I0 = (Impar, 0 unos)  
I1 = (Impar, 1 uno)  
I2 = (Impar, 2 unos)  
I3 = (Impar, >2 unos)

Aceptación:

- Todos los que tengan ceros_paridad = Par, o
- Contador_unos = 2.

Transiciones:  
(Par, n) --0--> (Impar, n)  
(Par, n) --1--> (Par, n+1 ó >2)  
(Impar, n) --0--> (Par, n)  
(Impar, n) --1--> (Impar, n+1 ó >2)


En total hay 8 estados. Se puede dibujar en papel como DFA producto.

---

### (b) Diseñar un autómata finito para el lenguaje  
$(L = \{ x \in \Sigma^* \ | \ (|x| \equiv 0 \mod 2) \ \land\ (|x| \equiv 0 \mod 3) \})$.
Por ejemplo: `111000 ∈ L` pero `0101 ∉ L`.

**Idea:**
- La condición es que la longitud de la cadena sea múltiplo de 2 y de 3.
- El Mínimo Común Múltiplo (mcm) de 2 y 3 es 6.
- Por lo tanto, el lenguaje es: todas las cadenas cuya longitud es múltiplo de 6.

**DFA:**
- Estados \(q_0, q_1, q_2, q_3, q_4, q_5\), donde \(q_i\) representa "longitud ≡ i (mod 6)".
- Estado inicial: \(q_0\) (longitud 0).
- Estado de aceptación: solo \(q_0\).
- En cada símbolo (0 o 1), se avanza al siguiente estado en el ciclo mod 6.

**Diagrama ASCII:**

q0 --0/1--> q1  
q1 --0/1--> q2  
q2 --0/1--> q3  
q3 --0/1--> q4  
q4 --0/1--> q5  
q5 --0/1--> q0

Acepta: solo q0.

Este autómata es un ciclo de 6 estados, todos los símbolos llevan al siguiente estado en el ciclo.

## 3) Demostrar que $A - B = L(M_3)$

**Enunciado (reescrito).**  
Sean $A$ y $B$ lenguajes regulares y sean $M_1=(Q_1,\Sigma,\delta_1,s_1,F_1)$ y $M_2=(Q_2,\Sigma,\delta_2,s_2,F_2)$ autómatas finitos determinísticos tales que $A=L(M_1)$ y $B=L(M_2)$.  
Definimos $M_3=(Q_3,\Sigma,\delta_3,s_3,F_3)$ por:
- $Q_3 = Q_1 \times Q_2$.
- Para $q_3=(q_1,q_2)\in Q_3$ y $e\in\Sigma$:
  $$
  \delta_3(q_3,e)=\delta_3((q_1,q_2),e)=(\delta_1(q_1,e),\delta_2(q_2,e)).
  $$
- $s_3=(s_1,s_2)$.
- $F_3 = F_1 \times (Q_2 \setminus F_2)$.

Recordatorio: para conjuntos cualesquiera $A$ y $B$, $A-B = A \cap \sim B$.

---

### Prueba de que $L(M_3)=A-B$

**Notación.** Denotamos por $\delta_i^\*$ y $\delta_3^\*$ (para $i=1,2$) las funciones de transición extendidas a cadenas.

**Lema (compatibilidad del producto con la transición extendida).**  
Para toda cadena $x\in\Sigma^\*$:
$$
\delta_3^\*((s_1,s_2),x)=\big(\,\delta_1^\*(s_1,x),\ \delta_2^\*(s_2,x)\,\big).
$$

*Demostración del lema.*  
Por inducción en $|x|$.

- **Caso base** $x=\varepsilon$:  
  $\delta_3^\*((s_1,s_2),\varepsilon)=(s_1,s_2)$ y $\delta_i^\*(s_i,\varepsilon)=s_i$ para $i=1,2$. Entonces la igualdad vale.

- **Paso inductivo**: supongamos que vale para $x$ y sea $a\in\Sigma$. Entonces:
  $$
  \begin{aligned}
  \delta_3^\*((s_1,s_2),xa)
  &= \delta_3\!\left(\,\delta_3^\*((s_1,s_2),x),\,a\right) \\
  &= \delta_3\!\left(\,(\delta_1^\*(s_1,x),\delta_2^\*(s_2,x)),\,a\right) \\
  &= \big(\,\delta_1(\delta_1^\*(s_1,x),a),\ \delta_2(\delta_2^\*(s_2,x),a)\,\big) \\
  &= \big(\,\delta_1^\*(s_1,xa),\ \delta_2^\*(s_2,xa)\,\big).
  \end{aligned}
  $$
  Con esto queda probado el lema.

**Caracterización de aceptación en $M_3$.**  
Para $x\in\Sigma^\*$:
$$
\begin{aligned}
x\in L(M_3)
&\iff \delta_3^\*(s_3,x)\in F_3  \\
&\iff \big(\delta_1^\*(s_1,x),\delta_2^\*(s_2,x)\big)\in F_1 \times (Q_2\setminus F_2) \quad \text{(por el lema)}\\
&\iff \delta_1^\*(s_1,x)\in F_1 \ \text{y}\ \delta_2^\*(s_2,x)\notin F_2 \\
&\iff x\in L(M_1) \ \text{y}\ x\notin L(M_2) \\
&\iff x\in A \ \text{y}\ x\notin B \\
&\iff x\in A-B.
\end{aligned}
$$

Por lo tanto, $L(M_3)=A-B$. En particular, la construcción de producto con $F_3=F_1\times(Q_2\setminus F_2)$ implementa la operación $A\cap \sim B$, que coincide con $A-B$.


## 4) Truncar cadenas y lenguajes. Si $A$ es regular y $\varepsilon\notin A$, entonces $A^T$ es regular.

**Definiciones (reescritas).**

1. **Truncar una cadena.** Sea un alfabeto finito $\Sigma$. Definimos $(\cdot)^T:\Sigma^+\to\Sigma^\*$ por
   $$
   a^T=\varepsilon\quad(\text{para }a\in\Sigma),\qquad (xa)^T=x\quad(\text{para }x\in\Sigma^\*,\,a\in\Sigma).
   $$
   Es decir, $w^T$ elimina **el último símbolo** de $w$.  
   Ejemplo: si $w=\texttt{abbab}$, entonces $w^T=\texttt{abba}$.

2. **Truncar un conjunto.** Para $A\subseteq\Sigma^\*$ definimos
   $$
   A^T=\{\,w^T\mid w\in A\,\}.
   $$

**Objetivo.** Probar: si $A$ es regular y $\varepsilon\notin A$, entonces $A^T$ es regular.

---

### Idea clave

Como $\varepsilon\notin A$, toda $w\in A$ tiene la forma $w=xa$ con $x\in\Sigma^\*$ y $a\in\Sigma$, y entonces $w^T=x$. Por lo tanto,
$$
A^T=\{\,x\in\Sigma^\*\mid \exists a\in\Sigma:\ xa\in A\,\}=A/\Sigma,
$$
es decir, el **cociente derecho** de $A$ por $\Sigma$ (un caso particular de cierre por cociente con un lenguaje finito).

---

### Construcción de un AFD para $A^T$

Sea $M=(Q,\Sigma,\delta,s,F)$ un AFD tal que $L(M)=A$ (existe porque $A$ es regular).  
Definimos el conjunto de **pre-aceptación**
$$
P=\{\,q\in Q\mid \exists a\in\Sigma:\ \delta(q,a)\in F\,\}.
$$
Construimos
$$
M_T=(Q,\Sigma,\delta,s,P).
$$
Es decir, usamos **los mismos estados y transiciones** de $M$, mismo estado inicial $s$, pero hacemos de aceptación exactamente a los estados que en **un paso** pueden ir a un estado aceptante de $M$.

---

### Corrección de la construcción

Denotemos por $\delta^\*$ la extensión de $\delta$ a cadenas. Para todo $x\in\Sigma^\*$:

1. **($\Rightarrow$)** Si $x\in L(M_T)$, entonces $\delta^\*(s,x)\in P$. Por definición de $P$, existe $a\in\Sigma$ tal que $\delta(\delta^\*(s,x),a)\in F$, luego
   $$
   \delta^\*(s,xa)\in F\quad\Rightarrow\quad xa\in A.
   $$
   Por la caracterización anterior, $x\in A^T$.

2. **($\Leftarrow$)** Si $x\in A^T$, existe $a\in\Sigma$ tal que $xa\in A$, luego $\delta^\*(s,xa)\in F$, es decir
   $$
   \delta(\delta^\*(s,x),a)\in F.
   $$
   Por tanto $\delta^\*(s,x)\in P$ y $x\in L(M_T)$.

Hemos probado $L(M_T)=A^T$. Como $M_T$ es un AFD finito, $A^T$ es regular.

---

### Observación

La hipótesis $\varepsilon\notin A$ garantiza que $w^T$ está bien definido para todo $w\in A$ (la operación $(\cdot)^T$ sólo se definió sobre $\Sigma^+$). La construcción anterior además muestra que $A^T=A/\Sigma=\bigcup_{a\in\Sigma}\{x\mid xa\in A\}$, de donde también se deduce el resultado por cierre de los lenguajes regulares bajo **cociente derecho** con un lenguaje finito.
