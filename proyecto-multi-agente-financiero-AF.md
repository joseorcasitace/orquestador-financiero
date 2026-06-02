# Multi-Agente Financiero AF
## Documento de Proyecto — Estado al 02 de Junio 2026

**Proyecto:** Orquestador Docente — Multi-Agente de Análisis Financiero con IA  
**Docente:** José Orcasita Celedón · ADVANCE · Universidad del Rosario  
**Versión activa:** MVP v9  
**App:** https://joseorcasitace.github.io/orquestador-financiero/orquestador-mvp-v2.html  
**Landing:** https://joseorcasitace.github.io/orquestador-financiero/  
**Repositorio:** https://github.com/joseorcasitace/orquestador-financiero  

---

## 1. DESCRIPCIÓN DEL PROYECTO

Herramienta de IA para análisis financiero empresarial basada en la metodología de los Laboratorios 7 al 15 del curso *"Planeación Financiera y Valoración con IA"*. Procesa Balances de Prueba reales (ERP/SIIGO) y genera análisis ejecutivos del Estado de Resultados, Estado de Situación Financiera, Estado de Flujo de Efectivo, Proyección Financiera a 5 años y Valoración DCF, siguiendo el Plan de Cuentas PUC colombiano.

**Usuarios objetivo:** Docentes de posgrado, profesionales financieros, estudiantes avanzados, equipos de banca de inversión en formación.

**Diferenciador:** Sin conocimientos técnicos requeridos — sube el Balance de Prueba, selecciona el análisis, obtiene resultados certificados idénticos al Add-On Excel, con cadena metodológica heredada Labs 7→15.

---

## 2. ARQUITECTURA

### 3 Agentes (diseño Horizonte 1)
- **Agente Pedagógico:** finanzas-docente + diseno-academico-ia
- **Agente Financiero:** analisis-financiero-consultivo + benchmark
- **Agente Producción:** frontend-design + xlsx/docx/pptx

### Stack técnico
- Frontend: HTML/CSS/JS autocontenido (~135KB) — sin servidor
- IA: Claude API (`claude-sonnet-4-20250514` en claude.ai | `claude-sonnet-4-5-20250929` en GitHub Pages)
- Procesamiento Excel: SheetJS (XLSX) — sin límite de filas
- Exportación: docx.js + SheetJS + PptxGenJS — generación en navegador
- Hosting: GitHub Pages

### Horizontes de desarrollo
- **Horizonte 1 (activo):** Entorno docente — carga BP real, Labs 7-15
- **Horizonte 2 (próximo):** Uso interno consultivo — DCF + Sensibilidad para empresas reales
- **Horizonte 3 (futuro):** SaaS / Agent-as-a-Service

---

## 3. PIPELINE DE CERTIFICACIONES

| Lab | Módulo | Estado | Fecha |
|-----|--------|--------|-------|
| Lab 7 | ER Cálculos Base | ✅ CERTIFICADO [8/8] | Abr 2026 |
| Lab 8 | ER KPIs Rentabilidad | ✅ CERTIFICADO [5/5] | Abr 2026 |
| Lab 9 | ER Insights | ✅ CERTIFICADO [11/11] | Abr 2026 |
| ER Completo | Labs 7+8+9 | ✅ CERTIFICADO [38/38] | Abr 2026 |
| Lab 10 | ESF Cálculos Base | 🔜 En certificación | Jun 2026 |
| Lab 11 | ESF KPIs (11 ratios) | 🔜 En certificación | Jun 2026 |
| Lab 12 | ESF Insights | 🔜 En certificación | Jun 2026 |
| Lab 13 | EFE Indirecto + Directo | 🔜 MVP v9 · certificación en curso | Jul 2026 |
| Lab 14 | Proyección 5 años + VT | 🔜 MVP v9 · certificación en curso | Ago 2026 |
| Lab 15 | Valoración DCF | 🔜 MVP v9 · certificación en curso | Sep 2026 |

---

## 4. REGLAS BLINDADAS — NO MODIFICAR SIN REVISIÓN

### R1 — Columna base (extractAndValidateBP)
```javascript
// Detecta automáticamente:
if (columna "Nuevo Saldo Final" presente) → applyTransform = false → usar directo  [RUTA A]
else (solo "Saldo Final")                 → applyTransform = true  → extractor transforma  [RUTA B]
// El agente NUNCA re-transforma
// La decisión RUTA A/B se HEREDA INTACTA en Labs 13, 14 y 15.
```

### R2 — Cuenta 5121 (DEFINITIVA)
```
5121 "Deterioro de Cuentas por Cobrar" → G.Adm (SIEMPRE)
NO incluir en D&A
Validado: Add-On Claude Excel + Add-On ChatGPT Excel (abril 2026)
```

### R3 — D&A identificada por NOMBRE (EXTENDIDA v9)
```
D&A = SOLO cuentas con nombre DEPRECIACIONES o AMORTIZACIONES
Busqueda semantica en TODOS los sub-prefijos de clase 5: 511x, 512x, 513x, 516x
NO incluir: Deterioro, Provision, Reserva
RAZON de extension: COSD usa 5118 y 5130 en lugar de 5160/5165 estandar.
Antes de v9, D&A = $0 en primera pasada (error). Corregido en v9.
```

### R4 — Prefijo 55 → Otros Gastos No Operacionales
```
55 NO se suma a Impuestos.
55 → Otros Gastos No Operacionales (junto con 53).
Razon: tasa efectiva de impuesto tecnicamente correcta + Margen Neto coherente.
```

### R5 — Deuda Neta
```
Deuda Neta = Obligaciones Financieras (prefijo 21 PURO) − Efectivo (prefijo 11)
NO incluir pasivos operativos (22, 23, 24, 25).
```

### R6 — FCF vs FCFF (distincion estructural)
```
FCF (Lab 13)  = FNO − CAPEX                              (free cash flow, diagnostico)
FCFF (Lab 15) = EBIT × (1−T) + D&A − CAPEX − ΔCT        (free cash flow to firm, descuento)
```

### R7 — CAPEX CORRECTO (NUEVA · CRITICA v9)
```
CAPEX = Delta PPE BRUTO UNICAMENTE
NO sumar D&A al CAPEX.
D&A ya esta en FNO como ajuste no monetario. Sumarla en CAPEX = doble conteo.
FORMULA CORRECTA:   CAPEX = -(Delta PPE bruto)
FORMULA INCORRECTA: CAPEX = -(Delta PPE + D&A)  [error detectado en auditoria jun 2026]
```

### R8 — BP pre-cierre (NUEVA v9)
```
Si cuenta prefijo 36 = 0 en el BP:
→ BP sin asientos de cierre.
→ Incorporar UN del Lab 7 al patrimonio antes de calcular EFE.
→ Documentar brecha tecnica V2 como consecuencia.
→ Brecha esperada = UN del periodo anterior no formalizada en clase 3.
```

### R9 — Headers API
```javascript
'anthropic-version': '2023-06-01'
'anthropic-dangerous-direct-browser-access': 'true'
// Obligatorios para llamadas desde navegador
```

### R10 — Modelos
```javascript
model: S.apiKey ? 'claude-sonnet-4-5-20250929' : 'claude-sonnet-4-20250514'
```

### R11 — JSON.stringify protegido
```javascript
try { bodyStr = JSON.stringify(payload); }
catch(e) { throw new Error('Error serializando: ' + e.message); }
```

### R12 — normalizeMsg
```javascript
// Todo mensaje del historial llega a la API como array de bloques
// {role:'user', content:[{type:'text', text:'...'}]}
// NUNCA como string plano
```

### R13 — Parámetros de mercado DCF (Mayo 2026)
```
Rf  = 4.25%   (UST 10Y, Damodaran)
ERP = 4.23%   (S&P 500 Implied ERP, Damodaran)
CRP = 2.85%   (Country Risk Premium Colombia, Moody's Baa2)
β   = 0.85    (Beta apalancado Food Processing, Damodaran)
TIB = 11.25%  (Banrep 31-mar-2026, subió 100 pbs)
T   = 35%     (Tasa impuestos efectiva Colombia)
g   = 3.0%    (Crecimiento perpetuo Gordon, ≈ meta inflación Banrep)
```

### R14 — Supuestos macro Lab 14 (mayo 2026)
```
Año     │ PIB   │ IPC   │ TIB    │ DTF    │ TRM   │ Sector lácteo
2024    │ 1.7%  │ 5.2%  │ 9.50%  │ 9.75%  │ 4,070 │ 0.9%
2025    │ 2.6%  │ 5.1%  │ 9.25%  │ 9.50%  │ 4,200 │ 3.5%
2026    │ 2.4%  │ 6.4%  │ 10.25% │ 10.50% │ 4,150 │ 3.5%
2027    │ 2.5%  │ 4.0%  │ 8.50%  │ 8.75%  │ 4,000 │ 3.5%
2028    │ 2.8%  │ 3.2%  │ 7.00%  │ 7.25%  │ 3,950 │ 3.5%
Fuentes: Banrep, FMI Art. IV, DNP, Bancolombia, Asoleche, OCILAC.
```

---

## 5. HALLAZGOS DE AUDITORÍA JUNIO 2026

Auditoría comparativa End-to-End sobre 3 datasets y 3 ambientes (ChatGPT Web / Claude Web / Orquestador App).

### Hallazgo H1 — D&A por excepción (COSD)
ChatGPT calculó D&A = $0 en la primera pasada porque el motor dependía de códigos estándar (5160/5165). Claude y el Orquestador-App detectaron correctamente por semántica de nombre.
**Corrección v9:** búsqueda extendida a todos los sub-prefijos 511x-517x.

### Hallazgo H2 — CAPEX con doble conteo (PE · ChatGPT)
ChatGPT usó `CAPEX = -(ΔPPE + D&A)`, sumando D&A dos veces. Claude usó `CAPEX = Δ PPE bruto`, que es el estándar certificado.
**Corrección v9:** R7 adoptada en Orquestador-App.

### Hallazgo H3 — Control V4 no bloquea (PE · ChatGPT versión inicial)
La hoja de control marcaba "Converge" con diferencia de $261.3M.
**Corrección v9:** bloqueo real si diferencia > 0.1% IO sin explicación.

### Conclusión estratégica
`BPWOCONSOL_PE` es el **benchmark de disciplina contable**. Cuando el BP llega bien preparado (D&A en códigos estándar, clases PUC completas, transformación limpia), los 3 ambientes convergen con GAP = $0.004M en el EFE. Las divergencias en COSD y ADW son atribuibles a la disciplina del emisor del BP, no al motor del Orquestador.

---

## 6. INFRAESTRUCTURA TÉCNICA

### extractAndValidateBP (función clave — v9)
- Lee el archivo Excel completo (sin límite de filas)
- Detecta columnas por nombre (Código, Nombre, Saldo)
- Prioridad: "Nuevo Saldo Final" > "Saldo Final"
- Detecta clases PUC presentes (1-7) y alerta faltantes
- Detecta BP pre-cierre (prefijo 36 = 0) → activa R8
- Para Labs 13-15: detecta DOS períodos y reporta
- **v9:** alerta si clases de un período difieren vs. el otro

### Flujo de datos (v9)
```
Archivo Excel (BP, idealmente con 2 períodos)
  ↓
extractAndValidateBP()
  → detecta NSF vs SF → aplica R1
  → detecta clases PUC presentes
  → detecta BP pre-cierre (R8)
  → alerta comparabilidad si clases difieren entre períodos
  ↓
S.activeBPData → persiste en sesión
  ↓
[Labs 7-12] autoFill() → callAPI()
[Labs 13-15] autoFill() + contexto heredado → callAPI()
  ↓
Agente recibe datos + reglas R1-R14 → suma directa por prefijo PUC
  ↓
Controles V1-V5 → bloqueo si V4 falla sin explicación
  ↓
Respuesta → addAgent() → exportar Word/Excel/PPT
```

---

## 7. ÁRBOL DE ANÁLISIS (MVP v9)

```
📊 Análisis Financiero (Labs 7-15)
  │
  ├── 📈 Estado de Resultados (Labs 7-9) ✅ CERTIFICADO
  │     ├── Cálculos Base          [Lab 7]
  │     ├── KPIs Rentabilidad      [Lab 8]
  │     ├── Insights del ER        [Lab 9]
  │     └── ER Completo (7-9)
  │
  ├── 🏦 Estado de Situación Financiera (Labs 10-12) 🔜
  │     ├── Cálculos Base          [Lab 10]
  │     ├── KPIs (11 ratios)       [Lab 11]
  │     ├── Insights del ESF       [Lab 12]
  │     └── ESF Completo (10-12)
  │
  ├── 💧 Flujo de Efectivo
  │     └── EFE Indirecto + Directo [Lab 13] 🔜 v9
  │
  ├── 📊 Proyección y Valoración
  │     ├── Proyección 5 años + VT  [Lab 14] 🔜 v9
  │     └── Valoración DCF           [Lab 15] 🔜 v9
  │
  └── 🎯 Análisis Integral (Labs 7-15)
```

---

## 8. HISTORIAL DE ERRORES RESUELTOS

| Error | Causa | Solución |
|-------|-------|----------|
| "string did not match expected pattern" | Faltaban headers `anthropic-version` | Agregados a callAPI |
| "Datos Insuficientes" en BP | Límite 120 filas cortaba clases 4,5,6,7 | extractAndValidateBP sin límite |
| Valores incorrectos vs Add-On Excel | Agente re-transformaba "Nuevo Saldo Final" | applyTransform logic en extractor |
| Lab 8 truncado en D&A | max_tokens=1000 insuficiente | Aumentado a 8192 |
| 5121 mal clasificada en D&A | Regla incorrecta v5-v6 | Revertida en v7: 5121 → G.Adm |
| D&A = $0 en COSD (ChatGPT) | Motor solo buscaba 5160/5165 | Búsqueda semántica extendida 511x-517x (v9) |
| CAPEX con doble conteo | CAPEX = -(ΔPPE + D&A) incorrecto | R7: CAPEX = Δ PPE bruto solo (v9) |
| Control V4 "Converge" con $261M diferencia | Control evaluaba etiqueta, no número | Bloqueo real por valor numérico (v9) |
| Brecha V2 en BP pre-cierre | Cuenta 36 = 0, UN no en patrimonio | R8 + alerta automática (v9) |
| TEI 377% sin alerta | Sin detector de anomalía fiscal | Alerta A3 TEI > 100% (v9) |
| Lab 14 truncado en matrices | max_tokens=8192 insuficiente | Aumentado a 16384 en v8 |

---

## 9. ARCHIVOS DEL PROYECTO

| Archivo | Descripción | Tamaño |
|---------|-------------|--------|
| `orquestador-mvp-v2.html` | App principal MVP v9 (Labs 7-15) | ~135KB |
| `index.html` | Landing page GitHub Pages | ~30KB |
| `README.md` | Documentación del repositorio | ~5KB |
| `auditoria-ER-ESF-EFE-completo.txt` | Log auditoría Labs 7-15 + hallazgos jun 2026 | ~7KB |
| `SKILL-analisis-financiero-consultivo-v8.md` | Skill maestra serie Labs 7-15 | ~14KB |
| `proyecto-multi-agente-financiero-AF.md` | Este documento | ~14KB |

---

## 10. VALORES DE REFERENCIA CERTIFICADOS

### 10.1 ER — COSD sector salud (Labs 7-9) — Validados contra Add-On Claude y ChatGPT Excel

| KPI | 2024 | % | 2025 | % | YoY |
|-----|------|---|------|---|-----|
| Ingresos Op. | $8,459,873,286 | 100% | $10,789,026,227 | 100% | +27.5% |
| Costos Venta | $4,917,166,236 | 58.1% | $6,127,085,679 | 56.8% | +24.6% |
| Margen Bruto | $3,542,707,050 | 41.9% | $4,661,940,548 | 43.2% | +31.6% |
| G.Adm (c/5121) | $2,460,460,277 | 29.1% | $3,111,307,866 | 28.8% | +26.5% |
| G.Ventas | $2,195,600 | 0.0% | $18,866,161 | 0.2% | +759% |
| EBITDA | $1,080,051,173 | 12.8% | $1,531,766,521 | 14.2% | +41.8% |
| D&A (5118+5130) | $233,843,423 | 2.8% | $202,221,262 | 1.9% | -13.5% |
| EBIT | $846,207,750 | 10.0% | $1,329,545,259 | 12.3% | +57.1% |
| Margen Neto | $1,026,406,523 | 12.1% | $1,399,377,177 | 13.0% | +36.3% |

### 10.2 EFE — BPWOCONSOL_PE (benchmark disciplina contable)

| Concepto | Valor 2023 |
|----------|------------|
| FNO | +$2,603.26M |
| CAPEX (Δ PPE bruto) | $112.69M |
| FNI | -$621.32M |
| FNF | -$1,785.21M |
| Δ Efectivo calculado | +$196.72M |
| Δ Efectivo real ESF | +$196.72M |
| GAP V4 | $0.004M = 0.000% IO ✅ |
| FCF = FNO − CAPEX | +$2,490.57M |
| DSCR | 0.52x ⚠️ (< 1.2x mínimo) |

### 10.3 Parámetros DCF (Mayo 2026)

| Parámetro | Valor | Aplicación |
|-----------|-------|------------|
| Ke preliminar (β=0.85) | 10.70% | Costo del capital propio referencial |
| WACC Pesimista típico | 15-17% | Estructura D/V=70%, spread 400 pbs |
| WACC Base típico | 13-15% | Estructura D/V=55%, spread 300 pbs |
| WACC Optimista típico | 11-13% | Estructura D/V=40%, spread 200 pbs |
| g terminal | 3.0% | Meta inflación Banrep |
| EV/EBITDA sector lácteo | 4x-9x | Triangulación obligatoria |

---

*Documento actualizado: 02 de junio de 2026 · Multi-Agente Financiero AF · ADVANCE · Universidad del Rosario*  
*MVP v9 — Serie completa Labs 7-15 · Auditoría jun 2026 incorporada · R7 y R8 blindadas*
