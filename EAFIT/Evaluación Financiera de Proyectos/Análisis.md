# 1) Correcciones puntuales a tu hoja

1. **Celda “k_e seleccionado”**
    
    - En tu captura aparece **16,25%** (eso es el **k_d post-impuestos**).
        
    - **Corrígela a 30%** (si usas la TIO como costo de equity) **o** al **k_e (CAPM)** que te dé la sección CAPM (≈ **28,8%** con tus supuestos).
        
    - Con **k_e = 30%** y **k_d,post = 16,25%**, tu **WACC = 21,75%** (correcto y consistente con tu informe).
        
2. **Consistencia de unidades (nominal COP):**
    
    - Tus flujos están en **COP nominales** (crecen con IPC/IPP). Mantén **todas** las tasas (Rf, ERP, k_d, k_e) también en **nominal COP**.
        
3. **CAPM (cómo usarlo bien):**
    
    - Fórmula correcta: **ke=rf+βL⋅ERPk_e = r_f + \beta_L \cdot ERP** _(si el ERP ya es local en COP)_ **+ CRP** _(solo si tu ERP es global en USD y necesitas agregar riesgo país)_.
        
    - **No** uses “Rm = 8%” como si fuera ERP. El **ERP es (Rm − Rf)**. Si tomas Rm=8% y Rf=6%, **ERP=2%** → te deja un **k_e artificialmente bajo**.
        
4. **β (beta) y apalancamiento:**
    
    - Si partes de una **β sin apalancar (β_U)** del sector, **re-apaláncala**:
        
        βL=βU [1+(1−T) D/E]\beta_L=\beta_U\,[1+(1-T)\,D/E]
        
        Con **D/E ≈ 1,5** y **T=35%**, el factor es **1,975**.
        
    - Si usas **β = 1,3** como “típica de tech” debes **aclarar** si ya está apalancada. Si fuera **β_U=1,3**, entonces **β_L ≈ 2,57**; si ya fuese **β_L**, no la vuelvas a apalancar. En tu hoja actual usas **β_U=1,0 ⇒ β_L≈1,975**, que es razonable para un startup con D/E 60/40.
        

---

# 2) ¿Qué pasa con la tabla de tu compañero?

- **Rf = 6% (TES 10Y)**: puede ser un proxy **válido** si trabajas en **COP nominal**.
    
- **Rm = 8%** y **β = 1,3**: si toma **ERP = Rm − Rf = 2%**, el resultado es **k_e = 6% + 1,3×2% = 8,6%**.
    
- Ese **8,6% es muy bajo** para un startup (ignora riesgo país adicional y, sobre todo, un **apalancamiento 60/40**).
    
- Con **k_e = 8,6%** y **k_d,post = 16,25%**, el **WACC ≈ 13,2%**. Eso **subestima el riesgo** y **sobre-eleva el VPN**.
    

> Si en cambio aplicas un **ERP local razonable** (ej.: 6–8% en COP) o **ERP global + CRP**, con tu **β_L≈1,975**, el **k_e** queda **18–22%**, y el **WACC** entra en el rango **17–19%** (mucho más cercano a tu 21,75% por TIO=30%).

---

# 3) Escenarios comparables (mismos FCL del proyecto)

Flujos: −130.939.169;43.752.455;54.985.854;68.076.597;83.248.039;106.380.538-130.939.169; 43.752.455; 54.985.854; 68.076.597; 83.248.039; 106.380.538

|Escenario de tasa|Supuestos clave|**WACC**|**VPN (COP)**|
|---|---|--:|--:|
|**Base (tu informe)**|k_e = 30% (TIO), k_d,post = 16,25%|**21,75%**|**57.467.798**|
|CAPM “local”|Rf 6%, **ERP_col 8%**, β_L≈1,975|**18,5%**|73.796.860|
|CAPM “global + CRP”|Rf 6%, ERP_glob 5% + **CRP 3%**, β_L≈1,975|**17,3%**|80.379.513|
|**Compañero**|Rf 6%, **ERP=2%**, β=1,3 (sin re-apalancar)|**13,2%**|105.547.412|

**Lectura:** el **WACC 13%** hace ver el proyecto más valioso de lo que es bajo un riesgo realista. Con CAPM bien parametrizado o con TIO=30% obtienes tasas (17–22%) muy cercanas y prudentes.

---

## 4) Qué dejar finalmente en tu Excel (resumen operativo)

1. **Usa TIO=30% como k_e** para el caso base (es coherente con tu estudio y entorno startup).
    
2. En la sección CAPM:
    
    - Si tomas **Rf = TES 10Y (COP)**, entonces usa **ERP _Colombia en COP_** (no Rm) **o** usa **ERP global + CRP**.
        
    - Define si tu **β** es **unlevered** (aplícale la fórmula) o **levered** ya comparable.
        
3. Reporta **WACC Base = 21,75%** y añade una **nota CAPM** con el rango **17–19%** como sensibilidad.
    
4. Corrige la celda **“k_e seleccionado”** a **30%** (o al CAPM que escojas).
    
