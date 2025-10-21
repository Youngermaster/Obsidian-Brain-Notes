### ¿Qué es Recursive-Descent Parsing?

Es un método de **análisis sintáctico descendente** (top-down parsing), donde:

- Cada **no terminal** del lenguaje tiene una función (procedimiento) asociada.
- El análisis empieza con el **símbolo inicial**, y cada función intenta hacer coincidir el input con las reglas (producciones) de la gramática.
- Si encuentra coincidencias, avanza; si no, puede retroceder (**backtracking**).

---
### ¿Cuándo se usa backtracking?

Cuando una producción **no coincide con la entrada**, el parser:

- **regresa al punto de decisión anterior**, e intenta otra producción.
- Esto es lo que se vio en el ejemplo del libro con la regla A→ab ∣ aA → ab \,|\, a.

---
### Ejemplo del libro (Gramática):

```
S → cAd
A → ab | a
```

Y queremos analizar la cadena `cad`.

---

### Paso a paso:

1. **Inicio con SS**:
    - S→cAd
    - Espero encontrar una `c`, luego algo generado por `A`, y finalmente `d`.
2. **Input = `cad`**, posición actual: **1**, símbolo: `c`
    - `c` coincide con la producción → avanzamos.
    - Llamamos a la función `A()`.
3. **Input = `cad`**, posición actual: **2**, símbolo: `a`

#### Intento 1: A→abA → ab

- Leo `a` → coincide.
- Avanzo a la posición 3, símbolo: `d`
- Espero `b`, pero **tengo `d` → error**

👉 **retrocedo** al inicio de `A()` (posición 2), intento otra alternativa.

#### Intento 2: A→aA \rightarrow a

- Leo `a` → ✅ coincide.
- Avanzo a la posición 3, símbolo: `d`
- Regreso al análisis de S→cAd
- Espero `d` → ✅ coincide.

¡Listo! Cadena aceptada.

---

### Código tipo pseudocódigo (basado en Fig 4.13):

```c
void A() {
  // Intento la primera producción
  if (current_symbol == 'a') {
    advance(); // avanza si es 'a'
    if (current_symbol == 'b') {
      advance(); // espera 'b'
      return;
    }
    // fallo → backtrack
    reset_input();
  }
  // Intento la segunda producción
  if (current_symbol == 'a') {
    advance(); // solo consume 'a'
    return;
  }
  error();
}
```

---

### 🧨 Problema con **gramáticas recursivas a la izquierda**

Por ejemplo:

```
A → Aα | β
```

Esto hace que al llamar recursivamente `A()` desde `A()`, **nunca se consuma input** → **bucle infinito**.

✅ **Solución**: Eliminar la recursividad izquierda antes de aplicar Recursive-Descent Parsing.

---

### 🧠 Resumen

|Concepto|¿Qué hace?|
|---|---|
|Función por no terminal|Llama a otras funciones o compara terminales|
|Coincidencia directa|Avanza en el input|
|No coincide|Hace **backtracking**|
|Left-recursion|Produce bucles infinitos, hay que evitarla|
