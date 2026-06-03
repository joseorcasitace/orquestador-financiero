# Multi-Agente FP&A+V
## Documento de Proyecto — Estado al 02 de Junio 2026

**Proyecto:** Multi-Agente FP&A+V — Análisis Financiero, Planeación y Valoración con IA  
**Docente:** José Orcasita Celedón · ADVANCE · Universidad del Rosario  
**Versión activa:** v10  
**App:** https://joseorcasitace.github.io/orquestador-financiero/multi-agente-fpav.html  
**Landing:** https://joseorcasitace.github.io/orquestador-financiero/  
**Repositorio:** https://github.com/joseorcasitace/orquestador-financiero  

---

## 1. DESCRIPCIÓN DEL PROYECTO

Herramienta de IA para análisis financiero empresarial basada en la metodología de los Laboratorios 7 al 15 del curso *"Planeación Financiera y Valoración con IA"*. Procesa Balances de Prueba reales (cualquier ERP colombiano) y genera análisis ejecutivos del Estado de Resultados, Estado de Situación Financiera, Estado de Flujo de Efectivo, Proyección Financiera a 5 años y Valoración DCF, siguiendo el Plan de Cuentas PUC colombiano.

**Usuarios objetivo:** Docentes de posgrado, profesionales financieros, estudiantes avanzados, equipos de banca de inversión en formación.

**Diferenciador v10:** Motor inferencial algebraico que lee cualquier BP colombiano correctamente, sin depender del ERP, del nombre de columna ni de archivos pre-certificados. El agente expone su diagnóstico al usuario antes de calcular, convirtiendo el proceso en una experiencia de auditoría activa.

---

## 2. ARQUITECTURA

### 3 Agentes (diseño Horizonte 1)
- **Agente Pedagógico:** finanzas-docente + diseno-academico-ia
- **Agente Financiero:** analisis-financiero-consultivo + benchmark
- **Agente Producción:** frontend-design + xlsx/docx/pptx

### Stack técnico
- Frontend: HTML/CSS/JS autocontenido (~157KB) — sin servidor
- IA: Claude API (`claude-sonnet-4-20250514` en claude.ai | `claude-sonnet-4-5-20250929` en GitHub Pages)
- Procesamiento Excel: SheetJS (XLSX) — sin límite de filas
- Motor de diagnóstico: `extractAndValidateBP()` v10 con C1-C4
- Exportación: docx.js + SheetJS + PptxGenJS — generación en navegador
- Hosting: GitHub Pages

### Horizontes de desarrollo
- **Horizonte 1 (activo):** Entorno docente — carga BP real, Labs 7-15, motor inferencial
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
| Labs 7-9 con motor C1 | Re-validación con test suma cero | 🔜 Hito jun 2026 | — |
| Lab 10 | ESF Cálculos Base | 🔜 En certificación | Jun 2026 |
| Lab 11 | ESF KPIs (11 ratios) | 🔜 En certificación | Jun 2026 |
| Lab 12 | ESF Insights | 🔜 En certificación | Jun 2026 |
| Lab 13 | EFE Indirecto + Directo | 🔜 v10 · certificación en curso | Jul 2026 |
| Lab 14 | Proyección 5 años + VT | 🔜 v10 · certificación en curso | Ago 2026 |
| Lab 15 | Valoración DCF | 🔜 v10 · certificación en curso | Sep 2026 |

---

## 4. REGLAS BLINDADAS — NO MODIFICAR SIN REVISIÓN

### R1 — Decisión de ruta (REDISEÑADA v10)
```
ANTES (v9): if columna "Nuevo Saldo Final" existe → RUTA A, si no → RUTA B
AHORA (v10): test algebraico de suma cero

sumaAlg = ∑(saldos raw clases 1-7 sin transformar)
umbral   = |∑clase1| × 0.005   // 0.5% de activos
rutaA    = |sumaAlg| ≤ umbral   // BP sin transformar → aplicar ×(−1) a clases 2,3,4
rutaB    = |sumaAlg| > umbral   // BP ya transformado → usar directo

Caso borde: si clase1 = 0 → usar signo de clase4 (ingresos negativos = rutaA)
El agente NUNCA re-transforma. La decisión viene del extractor.
```

### R2 — Cuenta 5121 (DEFINITIVA)
```
5121 "Deterioro de Cuentas por Cobrar" → G.Adm (SIEMPRE)
NO incluir en D&A
```

### R3 — D&A identificada por NOMBRE (extendida v9)
```
D&A = SOLO cuentas con nombre DEPRECIACIONES o AMORTIZACIONES
Búsqueda semántica en 511x, 512x, 513x, 516x, 517x
NO incluir: Deterioro, Provisión, Reserva
```

### R4 — Prefijo 55 → Otros Gastos No Operacionales
```
55 NO se suma a Impuestos. 55 → Otros Gastos No Operacionales.
```

### R5 — Deuda Neta
```
Deuda Neta = OblFin (prefijo 21 PURO) − Efectivo (prefijo 11)
NO incluir 22, 23, 24, 25
```

### R6 — FCF vs FCFF
```
FCF (Lab 13)  = FNO − CAPEX
FCFF (Lab 15) = EBIT × (1−T) + D&A − CAPEX − ΔCT
```

### R7 — CAPEX correcto
```
CAPEX = Δ PPE BRUTO ÚNICAMENTE
NO sumar D&A al CAPEX
```

### R8 — BP pre-cierre
```
Si cuenta prefijo 36 = 0 → BP sin asientos de cierre
→ Incorporar UN del Lab 7 al patrimonio antes del EFE
→ Documentar brecha técnica V2
```

### R9-R12 — Headers API, modelos, serialización, normalización de mensajes
```javascript
'anthropic-version': '2023-06-01'
'anthropic-dangerous-direct-browser-access': 'true'
model: S.apiKey ? 'claude-sonnet-4-5-20250929' : 'claude-sonnet-4-20250514'
try { bodyStr = JSON.stringify(payload); } catch(e) { ... }
// content siempre como array de bloques, nunca string plano
```

### R13 — Parámetros de mercado DCF (Mayo 2026)
```
Rf = 4.25% | ERP = 4.23% | CRP = 2.85% | β = 0.85 | TIB = 11.25% | T = 35% | g = 3.0%
```

### R14 — Supuestos macro Lab 14 (mayo 2026)
```
Año  │ PIB   │ IPC   │ TIB    │ DTF    │ TRM   │ Sector lácteo
2024 │ 1.7%  │ 5.2%  │ 9.50%  │ 9.75%  │ 4,070 │ 0.9%
2025 │ 2.6%  │ 5.1%  │ 9.25%  │ 9.50%  │ 4,200 │ 3.5%
2026 │ 2.4%  │ 6.4%  │ 10.25% │ 10.50% │ 4,150 │ 3.5%
2027 │ 2.5%  │ 4.0%  │ 8.50%  │ 8.75%  │ 4,000 │ 3.5%
2028 │ 2.8%  │ 3.2%  │ 7.00%  │ 7.25%  │ 3,950 │ 3.5%
```

---

## 5. MOTOR INFERENCIAL v10 — extractAndValidateBP()

### Flujo de la función

```
Archivo Excel cargado
  ↓
Por cada hoja (hasta 4):
  1. Detectar fila de cabecera (busca 'cod' o 'saldo')
  2. Localizar columnas: código, nombre, saldo numérico (cualquier nombre)
  3. Leer saldos RAW sin transformar → rawData[]
  ↓
  C1 · TEST DE SUMA CERO:
    sumaAlg = ∑(raw clases 1-7)
    umbral  = |∑clase1| × 0.005
    rutaA   = |sumaAlg| ≤ umbral
  ↓
  Aplicar (o no) transformación × (−1) a clases 2,3,4 según rutaA
  → sheetRows[] con saldos económicos
  ↓
  C2 · COHERENCIA: postSums por clase, alertas si negativo
  ↓
  C3 · IDENTIDAD: V.ESF, V.ER, V.FISCAL
  ↓
  C4 · PANEL: panelLines[] con diagnóstico completo
  ↓
Concatenar todas las hojas → allRows[]
Detectar clases PUC presentes / faltantes
Construir header con panel de auditoría
Retornar { content, validation }
```

### Objeto validation retornado
```javascript
{
  ready:           boolean,   // todas las clases 1-7 presentes
  classesFound:    string[],  // clases detectadas
  classesMissing:  string[],  // clases faltantes
  totalRows:       number,
  truncated:       boolean,
  sheets:          string[],
  isPreClose:      boolean,   // cuenta 36 = 0 en alguna hoja
  hasAlerts:       boolean,   // C2 o C3 generaron alertas
  coherenceAlerts: string[],  // alertas de signos negativos (C2)
  identityAlerts:  string[],  // alertas de identidad contable (C3)
  routesSummary:   string[],  // ["BP2023:RUTAA", "BP2022:RUTAB"]
  sheetDiagnosis:  object[]   // diagnóstico completo por hoja
}
```

---

## 6. INFRAESTRUCTURA TÉCNICA

### Flujo completo del sistema (v10)

```
Archivo Excel (cualquier ERP colombiano)
  ↓
extractAndValidateBP() v10
  → C1: test suma cero → RUTA A o B (sin depender del nombre de columna)
  → C2: coherencia de signos post-ruta
  → C3: identidad contable preliminar (V.ESF + V.ER + V.FISCAL)
  → C4: panel de auditoría visible en modal
  ↓
handleModalFile(): renderiza panel C4 en UI → usuario valida
  ↓
S.activeBPData → persiste en sesión con routesSummary y hasAlerts
  ↓
buildSys(): inyecta rutas y alertas en el prompt de sistema
  ↓
agente recibe datos + panel + reglas R1-R14 → suma directa por prefijo PUC
  ↓
controles V1-V5 → bloqueo si V4 falla sin explicación
  ↓
respuesta → addAgent() → exportar Word/Excel/PPT
```

---

## 7. ÁRBOL DE ANÁLISIS (v10)

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
  │     └── EFE Indirecto + Directo [Lab 13] 🔜 v10
  │
  ├── 📊 Proyección y Valoración
  │     ├── Proyección 5 años + VT  [Lab 14] 🔜 v10
  │     └── Valoración DCF          [Lab 15] 🔜 v10
  │
  └── 🎯 Análisis Integral (Labs 7-15)
```

---

## 8. HISTORIAL DE ERRORES RESUELTOS

| Error | Causa | Solución | Versión |
|-------|-------|----------|---------|
| Valores incorrectos vs Add-On Excel | Agente re-transformaba "Nuevo Saldo Final" | applyTransform logic en extractor | v7 |
| 5121 mal clasificada en D&A | Regla incorrecta v5-v6 | 5121 → G.Adm | v7 |
| D&A = $0 en COSD | Motor solo buscaba 5160/5165 | Búsqueda semántica 511x-517x | v9 |
| CAPEX con doble conteo | CAPEX = -(ΔPPE + D&A) | R7: CAPEX = Δ PPE bruto solo | v9 |
| Control V4 "Converge" con $261M diferencia | Control evaluaba etiqueta | Bloqueo real por valor numérico | v9 |
| **IO incorrecto con cualquier BP no estándar** | **Motor dependía del nombre de columna** | **Test algebraico suma cero C1** | **v10** |
| Sin visibilidad del diagnóstico de ruta | Sin panel de auditoría | Panel C4 visible en modal | v10 |
| Sin verificación de coherencia post-ruta | Sin C2 | Alerta si clase suma negativa | v10 |
| Sin identidad contable antes de calcular | Sin C3 | V.ESF, V.ER, V.FISCAL pre-cálculo | v10 |
| TEI 377% sin alerta | Sin detector fiscal | Alerta A3 TEI > 100% en C3 | v10 |

---

## 9. ARCHIVOS DEL REPOSITORIO

| Archivo | Descripción | Tamaño |
|---------|-------------|--------|
| `multi-agente-fpav.html` | App principal v10 · motor inferencial C1-C4 | ~157KB |
| `index.html` | Landing page | ~22KB |
| `README.md` | Documentación del repositorio | ~6KB |
| `auditoria-ER-ESF-EFE-completo.txt` | Log auditoría · hallazgos v10 | ~8KB |
| `proyecto-multi-agente-financiero-AF.md` | Este documento | ~15KB |
| `SKILL-analisis-financiero-consultivo-v8.md` | Skill maestra serie Labs 7-15 | ~14KB |

---

## 10. VALORES DE REFERENCIA CERTIFICADOS

### ER — COSD sector salud (Labs 7-9)

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

### EFE — BPWOCONSOL_PE (benchmark disciplina)

| Concepto | Valor 2023 |
|----------|-----------|
| FNO | +$2,603.26M |
| CAPEX (Δ PPE bruto, R7) | $112.69M |
| FNI | -$621.32M |
| FNF | -$1,785.21M |
| GAP V4 | $0.004M = 0.000% ✅ |
| FCF = FNO − CAPEX | +$2,490.57M |
| DSCR | 0.52x ⚠️ |

---

*Documento actualizado: 02 de junio de 2026 · Multi-Agente FP&A+V · ADVANCE · Universidad del Rosario*  
*v10 — Motor inferencial algebraico C1-C4 · ERP-agnóstico · Diagnóstico abierto al usuario*
