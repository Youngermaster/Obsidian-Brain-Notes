# 1) PDA para la gramática $S \to aSbb \mid aab$

El profe hizo la **construcción estándar desde una CFG en forma de Greibach** (cada producción empieza por un terminal) a un **AP** que **acepta por pila vacía**. Te lo explico paso a paso.

---

## 1) Reescribir la gramática (formato Greibach)

Nombramos los terminales finales con variables para que cada regla quede “terminal seguido de variables”:

- $S \Rightarrow a\,SBB \mid a\,AB$
- $A \Rightarrow a$
- $B \Rightarrow b$

Ahora todas las reglas tienen la forma $X \to a\,\alpha$ o $X \to b\,\alpha$ con $\alpha\in V^*$. Eso es lo que pide la construcción.

---

## 2) Construir el autómata de pila $M$

Aceptaremos **por pila vacía**. Usamos **un solo estado** $q$.

- **Entrada**: $\Sigma=\{a,b\}$
- **Pila**: $\Gamma=\{S,A,B\}$
- **Estado inicial**: $q$
- **Símbolo inicial de pila**: $S$
- **Aceptación**: por pila vacía (no hace falta estado final distinto)

**Esquema de transición (Greibach):**  
Por cada producción $X \to a\,Y_1\cdots Y_k$ añadimos
$$\delta(q,a,X)\ni (q,Y_1\cdots Y_k).$$
Si $k=0$ (o sea, $X\to a$), entonces $\delta(q,a,X)\ni(q,\varepsilon)$. Análogo para $b$.

**Aplicado a nuestras reglas:**

- $S \to a\,SBB \quad\Rightarrow\quad \delta(q,a,S)\ni(q,SBB)$
- $S \to a\,AB \quad\Rightarrow\quad \delta(q,a,S)\ni(q,AB)$
- $A \to a \quad\Rightarrow\quad \delta(q,a,A)\ni(q,\varepsilon)$
- $B \to b \quad\Rightarrow\quad \delta(q,b,B)\ni(q,\varepsilon)$

**Diagrama ASCII (un estado; aceptación por pila vacía):**
```

            (a, S → SBB)
        ┌───────────────────┐
        │                   │

--> [ q ] --┴-- (a, S → AB) -- (a, A → ε) -- (b, B → ε)

```
Leyenda: (símbolo-entrada, tope-pila → reemplazo).

---

## 3) Por qué funciona (intuición)

- Cuando el tope es $S$ y leo una $a$, puedo:
  - **Recursivo**: usar $S\to aSBB$ (consumo la $a$ y apilo $SBB$).
  - **Base**: usar $S\to aAB$ (consumo la $a$ y apilo $AB$).
- Si el tope es $A$ o $B$, consumo el terminal correspondiente y **desapilo**:
  $A\xrightarrow{a}\varepsilon$, $B\xrightarrow{b}\varepsilon$.
- Si termino la entrada y la pila queda **vacía**, acepto.

Esto simula una **derivación por la izquierda** de la gramática.

---

## 4) Trazas de ejemplo

### a) Cadena base `aab`

Pila inicial: $S$

1. Leo `a`, uso $S\to aAB$: pila $\Rightarrow AB$
2. Leo `a`, uso $A\to a$: pila $\Rightarrow B$
3. Leo `b`, uso $B\to b$: pila $\Rightarrow \varepsilon$

Entrada consumida y pila vacía ⇒ **acepta**.

### b) Cadena recursiva `aaabbb` (una aplicación recursiva)

Pila: $S$

1. Leo `a`, $S\to aSBB$: pila $\Rightarrow SBB$
2. Leo `a`, ahora sobre el $S$ de arriba, $S\to aAB$: pila $\Rightarrow ABBB$
3. Leo `a`, $A\to a$: pila $\Rightarrow BBB$
4. Leo `b`, $B\to b$: pila $\Rightarrow BB$
5. Leo `b`, $B\to b$: pila $\Rightarrow B$
6. Leo `b`, $B\to b$: pila $\Rightarrow \varepsilon$

Entrada consumida y pila vacía ⇒ **acepta**.

---

## 5) Lenguaje generado (intuición)

Si aplico $S\to aSbb$ $t$ veces y luego $S\to aab$, obtengo:
- número de $a$: $2+t$
- número de $b$: $1+2t$

Es decir:
$$
L=\{\,a^n\,b^{\,2n-3}\mid n\ge 2\,\},
$$
que coincide con lo que acepta el AP.

---

## 6) Resumen operativo

- Reescribe la CFG para que cada producción sea $X\to a\,\alpha$ o $X\to b\,\alpha$ con $\alpha\in V^\*$.
- Construye un AP **de un solo estado**:
  - Pila inicial $S$.
  - Para cada $X\to a\,\alpha$, agrega $\delta(q,a,X)\ni(q,\alpha)$; análogo para $b$.
  - Acepta por **pila vacía**.

# 2) Gramática para el lenguaje $L = {,a^i b^j c^k d^l \mid i=j \land k=l,}$

Queremos diseñar una **gramática libre de contexto (GLC)** que genere exactamente las cadenas donde el número de $a$ coincide con el de $b$, y el número de $c$ coincide con el de $d$.

generar Gramática libre de contexto para el lenguaje
$L = \{x \in \{a,b\}^*, x= a^{n} b^{m} \land n\ge 3m > 0\}$

Sea el lenguaje $\{a,b\}$. Use el lema del bombeo para probar que el conjunto de cadenas $L \subset \Sigma^*$ definido por 
$L = \{a^{n} b^{m} \mid n, m \ge 1 \land 2n=3m\}$
no es un lenguaje libre de contexto

ten en cuenta el teorema del pumping lema que te adjunté en la imagen

---

## 1) Idea clave

- La condición **$i=j$** significa que hay la misma cantidad de $a$ y $b$.  
    Esto es el lenguaje clásico ${a^n b^n \mid n \ge 1}$, que es **libre de contexto**.
    
- La condición **$k=l$** significa que hay la misma cantidad de $c$ y $d$.  
    Esto también es ${c^n d^n \mid n \ge 1}$, igualmente **libre de contexto**.
    
- Como las dos condiciones son **independientes**, podemos generar la parte $a^i b^i$ con una variable, y la parte $c^k d^k$ con otra, y luego concatenarlas.
    

---

## 2) Construcción de la gramática

El profe definió **tres variables**:

- $S$: símbolo inicial, combina ambas partes.
    
- $X$: genera cadenas del tipo $a^i b^i$.
    
- $Y$: genera cadenas del tipo $c^k d^k$.
    

Gramática:

- $S \to XY$
    
- $X \to a X b \mid ab$
    
- $Y \to c Y d \mid cd$
    

---

## 3) Cómo funciona $X$

- Caso base: $X \to ab$ genera exactamente una $a$ seguida de una $b$.
    
- Caso recursivo: $X \to a X b$ agrega un $a$ al inicio y un $b$ al final de lo ya generado.
    
- Así se asegura que siempre habrá la misma cantidad de $a$’s que de $b$’s.
    

Ejemplo:  
$X \Rightarrow a X b \Rightarrow a (a b) b = aabb$.

---

## 4) Cómo funciona $Y$

- Caso base: $Y \to cd$ genera un par $cd$.
- Caso recursivo: $Y \to c Y d$ agrega un $c$ al inicio y un $d$ al final.
- Así se asegura que siempre habrá la misma cantidad de $c$’s que de $d$’s.


Ejemplo:  
$Y \Rightarrow c Y d \Rightarrow c (cd) d = ccdd$.

---

## 5) Combinación en $S$

El símbolo inicial $S$ primero genera $X$ (parte de $a$ y $b$) y después $Y$ (parte de $c$ y $d$).  
De esta forma, cualquier cadena generada tiene la forma:

S⇒XY    ⇒    $aibickdk,i,k≥1S \Rightarrow XY \;\;\Rightarrow\;\; a^i b^i c^k d^k, \quad i,k \ge 1$

---

## 6) Lenguaje generado

La gramática genera exactamente el lenguaje pedido:

$L={ aibjckdl∣i=j∧k=l,  i,j,k,l>0 }$
$L = \{\,a^i b^j c^k d^l \mid i=j \land k=l, \; i,j,k,l > 0\,\}$

# 3) Cociente de lenguajes $A/B$

Se nos da la definición formal:

A/B:={ x∣(∃y∈B)  (xy∈A) }A/B := \{\,x \mid (\exists y \in B)\;(xy \in A)\,\}

Es decir: **$x$ pertenece a $A/B$ si al concatenarlo con algún $y$ de $B$, el resultado está en $A$.**

---

## 1) Elección de conjuntos

El profe escogió:

- $A = {a, b}$
    
- $B = {a}$
    

---

## 2) Aplicar la definición

Queremos calcular:

$A/B={ x∣∃y∈B  tal que    xy∈A }A/B = \{\,x \mid \exists y \in B \;\; \text{tal que}\;\; xy \in A\,\}$

Como $B = {a}$, sólo existe un candidato: $y = a$.

Entonces:

A/B={ x∣(xa∈A) }A/B = \{\,x \mid (x a \in A)\,\}

---

## 3) Probar valores de $x$

- Si $x = \varepsilon$ (cadena vacía):  
    $x y = \varepsilon a = a \in A$  
    ⇒ entonces $\varepsilon \in A/B$.
    
- Si $x = b$:  
    $x y = b a$  
    pero $ba \notin A$ (porque $A = {a,b}$)  
    ⇒ entonces $b \notin A/B$.
    
- Si $x = a$:  
    $x y = aa$  
    pero $aa \notin A$  
    ⇒ entonces $a \notin A/B$.
    

Ninguna otra cadena $x$ funciona, porque $A$ sólo contiene $a$ y $b$.

---

## 4) Resultado final

Por lo tanto:

A/B={ε}A/B = \{\varepsilon\}

---

## 5) Intuición

- El cociente $A/B$ “recorta” de $A$ las cadenas que pueden terminar con algo en $B$.
    
- Como $B = {a}$, buscamos en $A$ cadenas que **terminen en $a$**.
    
- En este caso, la única cadena en $A$ que termina en $a$ es precisamente $a$.
    
- Para que $xy = a$, necesitamos $x = \varepsilon$ y $y = a$.
    

De ahí que el resultado sea solamente la cadena vacía.

# 4) MT para $L=\{\,a^n b^m \mid n\ge 1 \land n\ne m\,\}$

## Lo que escribió el profe (transcripción)

1. **Verificar que la cadena es de la forma $a^+ b^\*$**. Rechazar si no tiene esta forma.  
   Escribir **⊣** en el primer espacio en blanco después de la última `b`.

2. **Moverse entre los símbolos ⊢ y ⊣ así:**
   2.1. **Desde ⊢ hacia ⊣**, borrar la primera `a` que se encuentre y luego la primera `b` que se encuentre.  
        Si **no** se encuentra `b` para borrar, **aceptar** ($n>m$).

   2.2. **Desde ⊣ hacia ⊢**, borrar la primera `b` que se encuentre y luego la primera `a` que se encuentre.  
        Si **no** se encuentra `a`, **aceptar** ($m>n$).

Si en los casos 2.1 y 2.2 **no** se encuentra un **primer símbolo** para eliminar, **rechazar** ($n=m$).

> Acepta por estados (halt/accept) y rechaza cuando corresponde.

---

## Explicación paso a paso

### Objetivo
Aceptar exactamente las cadenas con al menos una `a` ($n\ge1$), todas las `a` antes que las `b` (forma $a^+b^\*$), y con **cantidad distinta** de `a` y `b$ (es decir, $n\ne m$).

### 1) Chequeo de forma $a^+b^\*$
La MT primero escanea de izquierda a derecha:
- Verifica que hay **al menos una `a`**.  
- Mientras vea `a` sigue a la derecha; al ver la **primera** `b` (si existe), a partir de ahí debe ver solo `b`.  
- Si aparece un patrón inválido (por ejemplo `ba` o cualquier símbolo fuera de $\{a,b\}$) ⇒ **rechaza**.

Al terminar la lectura, escribe el **marcador de fin** ⊣ en la primera celda en blanco a la derecha de la última `b`. Se asume que a la izquierda hay un marcador ⊢ (o se posiciona al inicio para actuar como tal).

### 2) Emparejar por “borrado cruzado”
La estrategia alterna dos barridos:

- **Barrido izquierda→derecha (⊢→⊣)**  
  - Busca la **primera `a`** que quede, la borra (reemplaza por `□`).  
  - Continúa a la derecha hasta hallar la **primera `b`**, la borra.  
  - Si **no existe** una `b` que borrar tras quitar una `a`, significa que **sobran `a`** ⇒ $n>m$ ⇒ **acepta**.

- **Barrido derecha→izquierda (⊣→⊢)**  
  - Busca la **primera `b`** que quede, la borra.  
  - Continúa a la izquierda hasta hallar la **primera `a`**, la borra.  
  - Si **no existe** una `a` que borrar tras quitar una `b`, significa que **sobran `b`** ⇒ $m>n$ ⇒ **acepta**.

Cada “ida y vuelta” elimina **a lo sumo un par** `a`/`b`. Si en alguno de los dos sentidos **no hay primer símbolo que eliminar** (no hay `a` en el paso 2.1 ni `b` en el 2.2, o viceversa), significa que se emparejaron **exactamente** todas las letras: $n=m$ ⇒ **rechaza**.

### 3) Corrección (invariante)
- Entre ⊢ y ⊣ la cinta siempre mantiene la **forma $a^\* b^\*$** (aunque con celdas borradas `□`), y cada ciclo elimina a lo sumo **un** `a` y **un** `b`.  
- Si alguna vez, justo después de borrar una letra de un tipo, **no** se encuentra la del otro tipo, entonces ese tipo **sobra** ⇒ $n\ne m$ ⇒ **acepta**.  
- Si el proceso “se queda sin primeras letras” en **ambas** direcciones (no hay `a` a la izquierda ni `b` a la derecha para eliminar), quedaron **cero** de ambos ⇒ $n=m$ ⇒ **rechaza**.

### 4) Casos ilustrativos
- Entrada `aaab` ($n=3,m=1$): en el barrido ⊢→⊣ se borra `a` y `b`; en el regreso ⊣→⊢ se borra `b` pero ya **no** hay `a` que emparejar ⇒ **acepta** ($m<n$).  
- Entrada `abbb` ($n=1,m=3$): en el barrido ⊢→⊣ se borra `a` y `b`; en el regreso ⊣→⊢ se borra `b` y luego **no** hay `a` ⇒ **acepta** ($m>n$).  
- Entrada `aabb` ($n=2,m=2$): dos ciclos eliminan dos pares y después **no** hay primer símbolo que borrar en ninguno de los sentidos ⇒ **rechaza**.

Con esto la máquina acepta exactamente $L=\{a^n b^m\mid n\ge1,\ n\ne m\}$.

# 5) Demostrar que si $L$ es libre de contexto, entonces $L^k$ es libre de contexto para todo $k\ge 1$

## Lo que escribió el profe (transcripción)

**Por inducción sobre $k$:**

- **Caso base $k=1$**  
  $L^1 = L$  
  $L^0 = L^{k+1} = L$  
  Como $L$ es libre de contexto, $L^1$ es libre de contexto.

- **Caso inductivo**  
  Afirmamos que $L^k$ es libre de contexto (H.I.).  
  Como $L^{k+1} = L \cdot L^k$, y $L$ y $L^k$ son libres de contexto (caso base e H.I., respectivamente),  
  y los lenguajes libres de contexto son cerrados bajo concatenación, tenemos que $L^{k+1}$ es libre de contexto.

**Conclusión:**  
Luego, para todo $k \ge 1$, $L^k$ es libre de contexto.

---

## Explicación paso a paso

### 1) Objetivo
Queremos probar que si $L$ es un lenguaje libre de contexto (CFL), entonces **todas las potencias concatenadas de $L$**, es decir
$$
L^k = \underbrace{L \cdot L \cdot \dots \cdot L}_{k\ \text{veces}},
$$
también son lenguajes libres de contexto.

### 2) Método: inducción sobre $k$
El profe usa **inducción matemática** sobre $k$.

---

### Caso base ($k=1$)
- Por definición, $L^1 = L$.  
- Como $L$ es CFL por hipótesis, $L^1$ también lo es.  
Esto establece el punto de partida.

---

### Hipótesis inductiva (H.I.)
- Supongamos que $L^k$ es CFL para algún $k \ge 1$.

---

### Paso inductivo
- Queremos probar que $L^{k+1}$ es CFL.  
- Notamos que
$$
L^{k+1} = L \cdot L^k.
$$
- Por la hipótesis inductiva, $L^k$ es CFL.  
- Por el caso base, $L$ es CFL.  
- Y como los **CFL son cerrados bajo concatenación**, el producto $L \cdot L^k$ también es CFL.

Por lo tanto, $L^{k+1}$ es CFL.

---

### 3) Conclusión
Por el principio de inducción matemática, **para todo $k\ge 1$, $L^k$ es un lenguaje libre de contexto**.

---

## 4) Intuición
La clave es usar dos propiedades bien conocidas de los CFL:
1. $L$ es CFL por hipótesis.  
2. Los CFL son **cerrados bajo concatenación**.

Esto permite encadenar la prueba inductiva de forma inmediata.


# 6) El lenguaje del punto 4 es libre de contexto. Diseñar un AP que lo acepte

Queremos un autómata de pila (AP) que acepte el lenguaje:
$$
L = \{\,a^n b^m \mid n \ge 1 \;\wedge\; n \neq m\,\}.
$$

---

## 1) Lo que escribió el profe (transcripción del diagrama)

- Estados: tres (el inicial a la izquierda, uno intermedio y uno final de aceptación).
- Transiciones clave:

1. $a, \perp \;\to\; a\perp$  
   (si empieza con una `a`, apila una `a` sobre el fondo).

2. $a, a \;\to\; aa$  
   (leyendo una `a`, duplica el símbolo `a` en la pila).

3. $\varepsilon, a \;\to\; a$  
   (movimiento $\varepsilon$, “mantiene” la pila con una `a`).

4. $b, a \;\to\; \varepsilon$  
   (leyendo una `b`, desapila una `a`).

5. $b, \perp \;\to\; -$  
   (si llega una `b` con la pila en fondo, ignora).

6. $\varepsilon, a \;\to\; -$  
   (transición vacía que ignora la `a` de la pila).

El círculo doble a la derecha indica aceptación.

Nota: el símbolo “–” escrito en el diagrama significa “no importa qué se meta en la pila”.

---

## 2) Explicación paso a paso

### Idea general
El lenguaje pide aceptar **todas las cadenas de la forma $a^n b^m$ con $n \ne m$**.  
Es decir:
- **Si $n > m$**: sobran `a` en la pila al final.
- **Si $m > n$**: sobran `b` en la entrada cuando ya no hay `a` en la pila.

En ambos casos, la máquina debe **aceptar**.  
El único caso que se rechaza es cuando $n=m$, porque se consumirían todas las `a` con todas las `b` y la pila quedaría exactamente vacía.

---

### Funcionamiento del AP

1. **Primera fase (contar las `a`)**
   - Cada vez que se lee una `a`, se apila una `a`.  
   - Ejemplo: al leer $a^3$, la pila tendrá $aaa$ (sobre el símbolo inicial $\perp$).

2. **Transición al leer la primera `b`**
   - Cuando llega un `b`, la máquina empieza a desapilar `a`.  
   - Cada `b` borra un `a` de la pila.

3. **Casos posibles**
   - **$n>m$**: Al terminar los `b`, todavía quedan `a` en la pila.  
     Entonces hay una transición $\varepsilon$ que se encarga de moverse al estado de aceptación.  
     (Acepta porque quedaron `a` sin emparejar).

   - **$m>n$**: Cuando se terminan las `a` de la pila pero todavía hay `b$ en la entrada, la máquina pasa al estado de aceptación.
     (Acepta porque hay más `b` que `a`).

   - **$n=m$**: Cada `a` encuentra un `b` que la desapila, y al terminar la entrada la pila está exactamente vacía.  
     En este caso la máquina **no tiene transición a aceptación** ⇒ rechaza.

---

## 3) Intuición
El truco está en que el AP **acepta por estado final, no por pila vacía**.  
De esta forma:
- Si la pila se vacía exactamente con la última `b` (caso $n=m$), no se alcanza el estado final ⇒ se rechaza.
- Si sobra pila ($n>m$) o sobra entrada ($m>n$), el diseño permite alcanzar el estado de aceptación.

---

## 4) Resumen

- Se usa la pila para “contar” y comparar $n$ y $m$ de forma implícita.  
- La aceptación se da solo cuando $n \ne m$, gracias a las transiciones:
  - desapilar `a` con cada `b`,  
  - aceptar si sobran `a` en la pila,  
  - aceptar si sobran `b` en la entrada.  
- El único caso que no tiene forma de aceptar es $n=m$.


# GLC para $L=\{\,a^n b^m \mid n\ge 3m>0\,\}$

**Símbolos terminales:** $\{a,b\}$  
**No terminales:** $\{S,U\}$  
**Símbolo inicial:** $S$

**Producciones:**
- $S \to aS \mid U$
- $U \to aaab \mid aaa\,U\,b$

---

## Por qué funciona

- $U$ genera exactamente las cadenas con **tres $a$ por cada $b$** y **al menos un $b$**:
  - Base: $U \Rightarrow aaab$ produce $a^{3\cdot 1}b^1$.
  - Paso: $U \Rightarrow aaa\,U\,b$ añade $3$ $a$ y $1$ $b$ al resultado de $U$.
  - Por inducción, $U \Rightarrow a^{3m}b^m$ con $m\ge 1$.

- $S \to aS$ permite **anteponer $t\ge 0$ $a$ adicionales** antes de iniciar $U$; así obtenemos
  $$
  a^t \cdot a^{3m} b^m \;=\; a^{t+3m} b^m,
  $$
  donde $t\ge 0$ y $m\ge 1$, luego $n=t+3m \ge 3m > 0$.

- Todas las $a$ aparecen antes que las $b$ por construcción.

Por tanto, la gramática genera exactamente $L$.


# ¿Es $L=\{a^{n}b^{m}\mid n,m\ge 1 \land 2n=3m\}$ un CFL?

## Hecho clave
La restricción $2n=3m$ implica que existen $t\ge 1$ tal que
$$
n=3t,\qquad m=2t.
$$
Por tanto
$$
L=\{\,a^{3t}b^{2t}\mid t\ge 1\,\}.
$$

## Gramática libre de contexto para $L$
Símbolo inicial: $S$.  
Producciones:
$$
S \to aaa\,S\,bb \;\mid\; aaabb.
$$
- La regla base genera $a^{3\cdot 1}b^{2\cdot 1}=aaabb$.
- Cada aplicación de $S\to aaa\,S\,bb$ añade exactamente $3$ $a$’s y $2$ $b$’s.
Por inducción, la gramática genera exactamente $a^{3t}b^{2t}$ con $t\ge 1$.
Luego $L$ **sí** es CFL.

## Otra demostración (clausuras)
Sea
$$
K=\{\,a^t b^t \mid t\ge 1\,\},
$$
que es CFL. Define el homomorfismo $h$ por $h(a)=aaa$, $h(b)=bb$.  
Entonces
$$
h(K)=\{\,a^{3t}b^{2t}\mid t\ge 1\,\}=L.
$$
Los CFL son cerrados bajo homomorfismos, así que $L$ es CFL.

## Consecuencia sobre el lema de bombeo para CFLs
Como $L$ **es** libre de contexto, **no** se puede usar el lema de bombeo para CFLs para “probar” que $L$ no es CFL (sería una conclusión falsa). Si necesitas un ejemplo para aplicar el lema y demostrar **no**-CFL, puedes usar, por ejemplo,
$$
L'=\{\,a^n b^n c^n \mid n\ge 0\,\},
$$
y con gusto te muestro la prueba completa con el lema de bombeo para CFLs.

¿pero estás seguro? ¿no faltan el resto de pasos como pasar a Chomsky y empezar el bombeo? Estos son los pasos de las notas del profe de otro ejemplo:


- Let G be a grammar for A in Chomsky normal form.
    
- Take k = 2n+1, where n is the number of non-terminals of G.
    
- Suppose z ∈ A with |z| ≥ k.
    
- By the argument above, any parse tree in G for z must be of depth at least n + 1.
    
- Consider the longest path in the tree. 
    
- That path is of length at least n + 1, therefore must contain at least n + 1 occurrences of non-terminals. 
    
- Some non-terminal occurs more than once along the path.
    
- Take the first pair of occurrences of the same non-terminal along the path, reading from bottom to top.
- Say X is the non-terminal with two occurrences. 
    
- Break z up into substrings uvwxy such that w is the string of terminals generated by the lower occurrence of X and vwx is the string generated by the upper occurrence of X.
    
- Let T be the subtree rooted at the upper occurrence of X and let t be the subtree rooted at the lower occurrence of X.
    
- By removing t from the original tree and replacing it with a copy of T, we get a valid parse tree of uv2wx2y
    
- We can repeat this cutting out of t and replacing it with a copy of T as many times as we like to get a valid parse tree for uviwxiy for any i ≥ 1.

- We can even cut T out of the original tree and replace it with t to get a parse tree for uv0wx0y = uwy.
    


# Sobre el uso del lema de bombeo para $L=\{a^{n}b^{m}\mid n,m\ge 1,\ 2n=3m\}$

## 1) Punto clave
Primero: este lenguaje **sí es CFL**. De hecho
$$
2n=3m \ \Longleftrightarrow\  n=3t,\ m=2t \ \ (t\ge 1),
$$
y por tanto
$$
L=\{\,a^{3t}b^{2t}\mid t\ge 1\,\}.
$$

Una GLC que genera $L$ es
$$
S\to aaa\,S\,bb\ \mid\ aaabb.
$$
Cada paso recursivo añade exactamente $3$ $a$’s y $2$ $b$’s.

---

## 2) ¿Dónde encajan los pasos “de Chomsky y bombeo”?
Los bullets que copiaste son **la demostración del lema de bombeo para CFLs** (no un procedimiento para mostrar que un lenguaje *no* es CFL).  
Ese lema dice: *todo CFL* bombea. Como $L$ es CFL, **cumple** el lema. Por eso no tiene sentido “usar el lema para probar que $L$ no es CFL”: sería contradictorio.

Aun así, si tu profe te pide “aplicar” el lema a $L$, puedes **exhibir una descomposición que bombea** (consistente con la prueba). No es necesario convertir a CNF para aplicar el lema en un ejercicio; basta con razonar sobre un árbol de derivación con una variable repetida en un camino.

---

## 3) Descomposición que bombea para $L$ (estilo del lema)

Tomemos la gramática
$$
S\to aaa\,S\,bb\ \mid\ aaabb.
$$

Elige una cadena larga de $L$, por ejemplo
$$
z=a^{3t}b^{2t} \quad(t\text{ grande}).
$$

La derivación (por la regla recursiva aplicada $t-1$ veces y luego la base) tiene una **cadena de $t$ ocurrencias de $S$** en un camino del árbol:
$$
S \Rightarrow aaa\,S\,bb \Rightarrow \cdots \Rightarrow
\underbrace{(aaa)^{t-1}}_{\text{prefijo}}\, S \,\underbrace{(bb)^{t-1}}_{\text{sufijo}}
\Rightarrow (aaa)^{t-1}\,aaabb\,(bb)^{t-1}.
$$

Considera las dos **primeras** ocurrencias iguales de la variable $S$ a lo largo de ese camino (la “superior” que expande $aaa\,S\,bb$ y la “inferior” que vuelve a ser un $S$).  
Con la notación del lema, definimos:
Me gusta que hiciste esto de acá:
- $v=aaa$ (lo que se genera **a la izquierda** del $S$ inferior dentro del $S$ superior),
- $w$ es lo que genera el $S$ inferior (algún $a^{3r}b^{2r}$, en la última expansión puede ser $aaabb$),
- $x=bb$ (lo que se genera **a la derecha** del $S$ inferior dentro del $S$ superior),
- $u$ es el prefijo anterior a ese $S$ superior (algunas copias previas de `aaa`),  
- $y$ es el sufijo posterior a ese $S$ superior (algunas copias de `bb`).

Entonces la cadena queda como
$$
z=uvwxy \ =\ u\ \underbrace{aaa}_{v}\ \underbrace{a^{3r}b^{2r}}_{w}\ \underbrace{bb}_{x}\ y.
$$

Al “bombear” de acuerdo con el lema, obtenemos para todo $i\ge 0$:
$$
uv^i w x^i y \;=\; u\ (aaa)^i\ a^{3r}b^{2r}\ (bb)^i\ y.
$$

pero te faltan los dos pasos adjuntados en las imagenes, la idea es demostrar de manera inversa con el bombeo demostrar que no es un CLF

Bueno hagamos el siguiente:
Describa el lenguaje A generado por la siguiente gramática
$S \to aaSb \mid b,$
y construya un autómata de pila no determinista M, tal que $A=L(M)$

Pero esto **sigue en $L$**, porque añadir una copia de $v=aaa$ y $x=bb$ simultáneamente
incrementa $3$ $a$’s y $2$ $b$’s a la vez, preservando la razón $3:2$:
$$
uv^i w x^i y \in \{\,a^{3(t+i)} b^{2(t+i)} \mid t\ge 1\,\} = L.
$$

Con esto se ilustra exactamente el fenómeno que garantiza el lema: hay un par de segmentos ($v$ y $x$) que pueden repetirse con el mismo exponente y la cadena **permanece** en $L$.

---

## 4) (Opcional) Una CNF equivalente

Si te exigen CNF, una conversión estándar para la gramática anterior es:

- $A\to a,\quad B\to b$
- $X\to AA,\quad X_3\to X A$   (de modo que $X_3 \Rightarrow AAA$)
- $Y\to BB$
- $Z\to S Y$
- $S\to X_3\,Z\ \mid\ X_3\,Y$
  
Todas las reglas son de la forma variable→dos variables o variable→terminal. Esta CNF genera el mismo lenguaje $L$ y te permite invocar literalmente el enunciado del lema.

---

## 5) Conclusión

- $L=\{a^{3t}b^{2t}\mid t\ge 1\}$ es **CFL** (por gramática o por homomorfismo).  
- Por ser CFL, **satisface** el lema de bombeo para CFLs.  
- Si necesitas “aplicarlo”, puedes usar la descomposición $v=aaa$, $x=bb$ alrededor de una repetición de $S$ en el árbol, y mostrar que $uv^i w x^i y$ permanece en $L$.


# Aplicando **todo** el lema de bombeo (incluyendo los dos pasos finales) a $L=\{a^{n}b^{m}\mid n,m\ge 1,\ 2n=3m\}$

> Spoiler importante: este lenguaje **sí es CFL**. Por eso, al completar *todos* los pasos del lema (los de tus láminas incluidas), **no** aparece contradicción: las cadenas “bombeadas” siguen en $L$. Justo eso es lo que el lema garantiza para lenguajes CFL.

---

## 1) Gramática y árbol con dos ocurrencias del mismo no terminal
$S \to aa\,S\,b \mid\ b.$
Tomemos la GLC equivalente:
$$
S \to aaa\,S\,bb \ \mid\ aaabb.
$$

Sea $z=a^{3t}b^{2t}$, con $t$ grande (por encima del umbral $k$ del lema).  
En el árbol de derivación, por aplicar $t-1$ veces la regla recursiva, hay un camino con varias ocurrencias de $S$. Elige la **primera pareja repetida** de $S$ a lo largo del camino (de abajo hacia arriba), como en las notas.

- El $S$ **superior** (raíz de $T$) expande a $aaa\; S\; bb$.
- El $S$ **inferior** (raíz de $t$) expande a alguna subcadena $a^{3r}b^{2r}$, y en la última expansión puede ser $aaabb$.

---

## 2) Descomposición $z=uvwxy$ en el formato del lema

Siguiendo exactamente las láminas:

- $v=aaa$ (lo que queda **a la izquierda** del $S$ inferior dentro del $S$ superior),
- $w$ = cadena generada por el $S$ inferior (algún $a^{3r}b^{2r}$),
- $x=bb$ (lo que queda **a la derecha** del $S$ inferior dentro del $S$ superior),
- $u$ = prefijo que hay **antes** del $S$ superior (varias copias previas de `aaa`),
- $y$ = sufijo que hay **después** del $S$ superior (varias copias de `bb`).

Queda:
$$
z \;=\; u\ \underbrace{aaa}_{v}\ \underbrace{a^{3r}b^{2r}}_{w}\ \underbrace{bb}_{x}\ y.
$$

Además se cumple la condición del lema: $|vwx|\le k$ y $vx\ne\varepsilon$.

---

## 3) Los dos pasos *exactos* de las láminas

### 3.1 “Cortar $t$ y reemplazar por una copia de $T$” (bombear **hacia arriba**)

Esto crea un árbol para $uv^2wx^2y$ (y en general para $uv^i w x^i y$, $i\ge 1$).  
En nuestra descomposición:
$$
uv^i w x^i y \;=\; u\ (aaa)^i\ a^{3r}b^{2r}\ (bb)^i\ y.
$$

**Conteo de símbolos.** Sea $u=a^{3p}$ y $y=b^{2q}$ (porque el prefijo/sufijo provienen de iteraciones previas de la producción recursiva). Entonces:
- número de $a$ en $uv^i w x^i y$: $3p + 3i + 3r = 3(p+i+r)$;
- número de $b$: $2q + 2i + 2r = 2(q+i+r)$.

Se mantiene la razón $3:2$, luego
$$
uv^i w x^i y \in \{\,a^{3t'}b^{2t'}\mid t'\ge 1\,\}=L
\quad\text{para todo } i\ge 1.
$$

### 3.2 “Cortar $T$ y reemplazarlo por $t$” (bombear **hacia abajo**)

Esto crea un árbol para $uv^0wx^0y = uwy$:
$$
uwy \;=\; u\ a^{3r}b^{2r}\ y \;=\; a^{3(p+r)}\, b^{2(q+r)}.
$$

Nuevamente la razón $3:2$ se preserva, así que
$$
uwy \in L.
$$

---

## 4) Conclusión respecto al lema

- **Para todo $i\ge 0$** se obtiene $uv^i w x^i y \in L$.  
- Por tanto, $L$ **satisface** el lema de bombeo para CFLs (como debe ocurrir, porque $L$ es CFL).  
- No se puede llegar a contradicción “bombeando al revés” porque aquí **sí existe** una descomposición que preserva el lenguaje para todos los $i$.

> Moral: el lema de bombeo para CFLs sirve para **refutar** que un lenguaje es CFL **solo si** *toda* posible descomposición falla al bombear. Aquí encontramos una que funciona (la inducida por las dos ocurrencias de $S$), así que no hay refutación: $L$ es CFL.

---

## 5) Si necesitas un ejemplo donde **sí** se prueba “no-CFL” con el lema

Un clásico es
$$
L'=\{\,a^n b^n c^n \mid n\ge 0\,\}.
$$
Ahí, para cualquier descomposición $uvwxy$ con $|vwx|\le k$ y $vx\ne\varepsilon$, bombeando ($i=0$ o $i\ge 2$) rompes alguna de las tres igualdades de conteo y obtienes una cadena fuera de $L'$, contradiciendo el lema. Con gusto te lo escribo completo si lo quieres en tus notas.

# Lenguaje generado por la gramática $S \to aaSb \mid b$ y APND equivalente

## 1) Descripción del lenguaje $A$

Derivaciones:
- Caso base: $S \Rightarrow b$ genera $b$.
- Paso inductivo: $S \Rightarrow aaSb \Rightarrow aa\,(a^{2n}b^{n+1})\,b = a^{2(n+1)}\,b^{(n+1)+1}$.

Por inducción, las únicas cadenas generadas son
$$
A \;=\; \{\,a^{2n}\,b^{\,n+1}\mid n\ge 0\,\}
\;=\; \{\,a^{2k}\,b^{\,k+1}\mid k\ge 0\,\}.
$$
Equivalente: si la cadena tiene $m\ge 1$ símbolos $b$, entonces tiene exactamente $2(m-1)$ símbolos $a$, y todos los $a$ van antes que los $b$.

---

## 2) Autómata de pila no determinista $M$ tal que $A=L(M)$

Usamos la construcción estándar “CFG → AP por pila vacía” que simula derivaciones por la izquierda:
- Apilamos el símbolo inicial $S$.
- Si en la cima hay una **variable**, la reemplazamos (vía transición $\varepsilon$) por el **reverso** de la parte derecha de una producción.
- Si en la cima hay un **terminal** que coincide con la entrada, lo desapilamos leyendo ese símbolo.

### Definición formal
- Conjunto de estados: $Q=\{q\}$ (un solo estado).
- Alfabeto de entrada: $\Sigma=\{a,b\}$.
- Alfabeto de pila: $\Gamma=\{S,a,b,Z\}$ (con $Z$ fondo de pila).
- Estado inicial: $q$.
- Símbolo inicial de pila: $Z$.
- Aceptación: **por pila vacía** ($F=\varnothing$).

Transiciones $\delta$ (especificadas como “lectura, cima ⟶ reemplazo” en la pila):

1) Inicialización (empujar $S$ sobre el fondo):
- $\varepsilon, Z \;\to\; SZ$.

2) Reglas de producción (reemplazo de variables con $\varepsilon$-movimientos):
- Para $S \to aaSb$:  
  $\varepsilon, S \;\to\; b\,S\,a\,a$  (empujamos en **orden inverso** para que el primer símbolo a consumir quede arriba).
- Para $S \to b$:  
  $\varepsilon, S \;\to\; b$.

3) Consumo de terminales:
- $a, a \;\to\; \varepsilon$  (si la cima es $a$ y la entrada es $a$, desapilar).
- $b, b \;\to\; \varepsilon$.

4) Aceptación por pila vacía al terminar la entrada:
- $\varepsilon, Z \;\to\; \varepsilon$.

### Intuición de funcionamiento
- Cada uso de $S\to aaSb$ agrega **dos** $a$ a la izquierda y **un** $b$ a la derecha (vía la pila).  
- El caso base $S\to b$ fuerza que **al menos** haya un $b$.  
- Como todo terminal de la pila debe coincidir con la entrada para desapilarse, la máquina acepta **exactamente** las cadenas $a^{2n}b^{n+1}$.

### Traza breve de ejemplo
Cadena $a^4 b^3$ (corresponde a $n=2$):
1. Empujar $S$; aplicar $S\to aaSb$; aplicar de nuevo $S\to aaSb$; finalmente $S\to b$ en la última $S$.  
   La pila (de arriba hacia abajo) queda: $a\,a\,a\,a\,b\,b\,b\,Z$.
2. Consumir $a,a,a,a$ con las cuatro reglas $a,a\to\varepsilon$.  
3. Consumir $b,b,b$ con $b,b\to\varepsilon$.  
4. Pila en $Z$ y entrada vacía ⇒ $\varepsilon,Z\to\varepsilon$ ⇒ **acepta**.

Con esto $L(M)=A$.

# PDA para la gramática $S \to aSbb \mid aab$ (con demostración del lenguaje)

---

## 1) Lenguaje generado por la gramática

**Base.** $S \Rightarrow aab \;=\; a^{2}b^{1}$.

**Paso.** Si $S \Rightarrow a^{n}b^{m}$, entonces
$$
S \Rightarrow aSbb \Rightarrow a\,(a^{n}b^{m})\,bb \;=\; a^{n+1}b^{m+2}.
$$

Partiendo de la base $a^{2}b^{1}$ y aplicando el paso $t\ge 0$ veces:
$$
S \Rightarrow a^{\,2+t} \, b^{\,1+2t}.
$$
Si ponemos $n=2+t$, queda $m=2(2+t)-3=2n-3$ y $n\ge 2$.

**Conclusión.**
$$
L(G)=\{\,a^{n}b^{\,2n-3}\mid n\ge 2\,\}.
$$

---

## 2) Reescritura “tipo Greibach” (terminal primero)

Nombramos los terminales finales:
- $A\to a$,  $B\to b$.

Reescribimos $S$:
- $S \to a\,S\,B\,B \mid a\,A\,B$.

Ahora cada producción empieza por un terminal, lo que simplifica la construcción del AP.

---

## 3) AP no determinista que **acepta por pila vacía**

Usamos la construcción estándar “CFG en forma de Greibach → AP (pila vacía)”:

- **Estados:** $Q=\{q\}$.
- **Entrada:** $\Sigma=\{a,b\}$.
- **Pila:** $\Gamma=\{S,A,B\}$.
- **Estado inicial:** $q$.
- **Símbolo inicial de pila:** $S$.
- **Aceptación:** por **pila vacía**.

**Transiciones** (formato “símbolo-entrada, tope-pila → reemplazo-de-pila”):

1) De $S$ leyendo `a`:
   - $a, S \to S B B$  (por $S\to a\,S\,B\,B$)
   - $a, S \to A B$    (por $S\to a\,A\,B$)

2) Consumir terminales en la cima (sin cambiar de estado):
   - $a, A \to \varepsilon$  (por $A\to a$)
   - $b, B \to \varepsilon$  (por $B\to b$)

**Diagrama ASCII (un solo estado, aceptación por pila vacía):**


## 2.a  Diseñar un APND que acepte el lenguaje generado por la gramática de 1.a  
Gramática dada:  $S \to aSbb \mid aab$.

---

### Paso 1. Reescritura de las producciones en la forma $A \to c\,B_1B_2\cdots B_k$  
(primera letra terminal $c\in\Sigma$, luego sólo variables)

Introducimos variables para los terminales:
$$
A \to a, \qquad B \to b.
$$

Reescribimos $S$:
$$
\tilde P:\qquad
S \to a\,S\,B\,B \ \mid\ a\,A\,B,\qquad
A \to a,\qquad
B \to b.
$$

Obsérvese que cada regla de $S$ comienza con un terminal y continúa con sólo variables.  
Claramente $L(\tilde G)=L(G)$, donde $\tilde G=(\{S,A,B\},\{a,b\},\tilde P,S)$.

El lenguaje generado es (por la inducción usual):
$$
L(G) \;=\; \{\,a^n b^{\,2n-3}\mid n\ge 2\,\}.
$$

---

### Paso 2. Construcción estándar de un **APND** $M$ que acepta **por pila vacía**

**Idea.** Para cada regla $X\to c\,B_1\cdots B_k$ agregamos una transición que,
leyendo $c$ con $X$ en la cima de la pila, reemplaza $X$ por $B_1\cdots B_k$.
Para reglas terminales $A\to a$ y $B\to b$, consumimos el símbolo de entrada
y desapilamos.

**Definición formal.**
- $Q=\{q\}$;  $\Sigma=\{a,b\}$;  $\Gamma=\{S,A,B\}$
- estado inicial $q$, símbolo inicial de pila $S$
- aceptación por **pila vacía** (sin estados finales)

Transiciones $\delta$ (usamos el formato “entrada, cima ⟶ nuevo-tope(s)”):

1) Reglas de $S$:
- $a, S \;\to\; SBB$ (corresponde a $S\to a\,S\,B\,B$)
- $a, S \;\to\; AB$  (corresponde a $S\to a\,A\,B$)

2) Consumo de terminales:
- $a, A \;\to\; \varepsilon$ ($A\to a$)
- $b, B \;\to\; \varepsilon$ ($B\to b$)

> Observación: apilamos $B_1\cdots B_k$ **en el mismo orden** para que el símbolo a reconocer
> quede arriba en el instante correcto (primero $A$ para la última `a` del bloque inicial, luego los $B$ para las `b`).

**Diagrama ASCII (un estado; aceptación por pila vacía):**
```


            (a, S → SBB)
        ┌───────────────────┐
        │                   │

--> [ q ] --┴-- (a, S → AB) -- (a, A → ε) -- (b, B → ε)

```

---

### Correctitud (esbozo)

- **Sonido.** Si $M$ acepta una cadena, necesariamente fue producida
  por usar $t≥0$ veces la transición $a,S\to SBB$ y terminar con $a,S\to AB$.
  Eso fuerza a leer $a^{t+2}$ y luego exactamente $2t+1$ $b$’s, es decir
  $a^n b^{2n-3}$ con $n=t+2\ge 2$. Luego la cadena pertenece a $L(G)$.

- **Completitud.** Dada $w=a^n b^{2n-3}$ con $n\ge 2$, elige $t=n-2$.  
  Aplica $t$ veces $a,S\to SBB$ y luego una $a,S\to AB$ consumiendo los $a$.
  La pila queda con un $A$ y luego $2t+1$ símbolos $B$; se consumen con
  $a,A\to\varepsilon$ y $b,B\to\varepsilon$ justo al finalizar la entrada,
  dejando la pila vacía. Por tanto $M$ acepta $w$.

Concluimos que $A=L(M)=L(G)=\{a^n b^{2n-3}\mid n\ge 2\}$.

# Gramática original y APND equivalente

La **gramática original** es
$$
G:\qquad S \to aa\,S\,b \;\mid\; b.
$$

---

## 1) Lenguaje generado por $G$

**Base.** $S \Rightarrow b = a^{0}b^{1}$.

**Paso.** Si $S \Rightarrow a^{2t}b^{t+1}$, entonces
$$
S \Rightarrow aa\,S\,b \;\Rightarrow\; aa\,(a^{2t}b^{t+1})\,b
= a^{2(t+1)}\,b^{(t+1)+1}.
$$

Por inducción en el número de aplicaciones de la regla recursiva, las cadenas generadas son
$$
L(G)=\{\,a^{2t}b^{\,t+1}\mid t\ge 0\,\}.
$$
Equivalente: si la cadena tiene $m\ge1$ símbolos `b`, entonces tiene exactamente $2(m-1)$ símbolos `a`, y todos los `a` van antes que los `b`.

---

## 2) Reescritura “terminal-primero” (para construir el APND)

Queremos reglas de la forma **“primer símbolo terminal, luego sólo variables”**.  
Introducimos una variable para el `b` final y factorizamos:

$$
\tilde P:\qquad
S \to a\,X \;\mid\; b,\qquad
X \to a\,S\,B,\qquad
B \to b.
$$

Se tiene $L(\tilde G)=L(G)$ con $\tilde G=(\{S,X,B\},\{a,b\},\tilde P,S)$.

---

## 3) AP no determinista que acepta **por pila vacía**

Usamos la construcción “terminal-primero”:  
si $A\to c\,B_1\cdots B_k$, añadimos una transición que **lee** $c$ y reemplaza $A$ por $B_1\cdots B_k$ en la pila.

- **Estados:** $Q=\{q\}$
- **Entrada:** $\Sigma=\{a,b\}$
- **Pila:** $\Gamma=\{S,X,B\}$
- **Estado inicial:** $q$
- **Símbolo inicial de pila:** $S$
- **Aceptación:** por **pila vacía** (sin estados finales)

**Transiciones** (formato “entrada, cima ⟶ reemplazo”):

1) Reglas de $S$  
   - $a, S \;\to\; X$ (corresponde a $S\to aX$)  
   - $b, S \;\to\; \varepsilon$ (corresponde a $S\to b$)

2) Regla de $X$  
   - $a, X \;\to\; S B$ (corresponde a $X\to aSB$)

3) Regla de $B$  
   - $b, B \;\to\; \varepsilon$ (corresponde a $B\to b$)

**Diagrama ASCII (un estado; aceptación por pila vacía):**
```

         (a, S → X)
     ┌────────────────┐
     │                │

--> [q] -┴- (b, S → ε) -- (a, X → S B) -- (b, B → ε)

```

---

## 4) Correctitud: $L(M)=L(G)$

### (⊆) Toda cadena aceptada por $M$ está en $L(G)$
- La única manera de expandir desde $S$ es:
  - leer una `a` y reemplazar $S$ por $X$, luego leer otra `a` y reemplazar $X$ por $S B$
    (esto “añade” **dos `a`** y un **`b` pendiente** en la pila), o
  - leer una `b` y eliminar $S$ (caso base).
- Cada $B$ en la pila obliga a consumir exactamente una `b`.  
  Si se aplican $t$ veces las transiciones con `a` (recursión) y se cierra con `b` desde $S$,
  la entrada forzada es $a^{2t}b^{t+1}$.  
  Aceptación por pila vacía exige consumir exactamente esos símbolos ⇒ la cadena está en $L(G)$.

### (⊇) Toda cadena de $L(G)$ es aceptada por $M$
- Sea $w=a^{2t}b^{t+1}$ con $t\ge 0$.  
  Lee las primeras dos `a` aplicando sucesivamente $a,S\to X$ y $a,X\to SB$; repite esto $t$ veces.  
  Luego lee la `b` del caso base $b,S\to\varepsilon$.  
  Finalmente consume los $t$ símbolos $B$ con $b,B\to\varepsilon$ en las $t$ `b` restantes.  
  La pila queda vacía al terminar la entrada ⇒ $M$ acepta $w$.

**Conclusión.** $A=L(M)=L(G)=\{a^{2t}b^{t+1}\mid t\ge 0\}$.
