# Multi-Agente Financiero AF
## Documento de Proyecto — Estado al 27 de Mayo 2026

**Proyecto:** Orquestador Docente — Multi-Agente de Análisis Financiero con IA
**Docente:** José Orcasita Celedón · ADVANCE · Universidad del Rosario
**Versión activa:** MVP v8
**App:** https://joseorcasitace.github.io/orquestador-financiero/orquestador-mvp-v2.html
**Landing:** https://joseorcasitace.github.io/orquestador-financiero/
**Repositorio:** https://github.com/joseorcasitace/orquestador-financiero

---

## 1. DESCRIPCIÓN DEL PROYECTO

Herramienta de IA para análisis financiero empresarial basada en la metodología de los Laboratorios 7 al 15 del curso "Planeación Financiera y Valoración con IA". Procesa Balances de Prueba reales (ERP/SIIGO) y genera análisis ejecutivos del Estado de Resultados, Estado de Situación Financiera, Estado de Flujo de Efectivo, Proyección Financiera a 5 años y Valoración por Flujo de Caja Descontado (DCF), siguiendo el Plan de Cuentas PUC colombiano.

**Usuarios objetivo:** Docentes de posgrado, profesionales financieros, estudiantes avanzados, equipos de banca de inversión en formación.
**Diferenciador:** Sin conocimientos técnicos requeridos — sube el Balance de Prueba, selecciona el análisis, obtiene resultados certificados idénticos al Add-On Excel, con cadena metodológica heredada Labs 7→8→9→10→11→12→13→14→15.

---

## 2. ARQUITECTURA

### 3 Agentes (diseño Horizonte 1)
- **Agente Pedagógico:** finanzas-docente + diseno-academico-ia
- **Agente Financiero:** analisis-financiero-consultivo + benchmark
- **Agente Producción:** frontend-design + xlsx/docx/pptx

### Stack técnico
- Frontend: HTML/CSS/JS autocontenido (~130KB con Labs 13-15) — sin servidor
- IA: Claude API (claude-sonnet-4-20250514 en claude.ai | claude-sonnet-4-5-20250929 en GitHub Pages)
- Procesamiento Excel: SheetJS (XLSX) — sin límite de filas
- Exportación: docx.js + SheetJS + PptxGenJS — generación en navegador
- Hosting: GitHub Pages — gratuito, sin servidor

### Horizontes de desarrollo
- **Horizonte 1 (activo):** Entorno docente — carga BP real, Labs 7-15
- **Horizonte 2 (próximo):** Uso interno consultivo — DCF + Sensibilidad para empresas reales
- **Horizonte 3 (futuro):** SaaS / Agent-as-a-Service

---

## 3. PIPELINE DE CERTIFICACIONES

| Lab | Módulo | Checks | Estado | Fecha |
|-----|--------|--------|--------|-------|
| Lab 7 | ER Cálculos Base | 8/8 | ✅ CERTIFICADO | Abr 2026 |
| Lab 8 | ER KPIs Rentabilidad | 5/5 | ✅ CERTIFICADO | Abr 2026 |
| Lab 9 | ER Insights | 11/11 | ✅ CERTIFICADO | Abr 2026 |
| ER Completo | Labs 7+8+9 | 38/38 | ✅ CERTIFICADO | Abr 2026 |
| Lab 10 | ESF Cálculos Base | — | 🔜 PENDIENTE | — |
| Lab 11 | ESF KPIs (11 ratios) | — | 🔜 PENDIENTE | — |
| Lab 12 | ESF Insights | — | 🔜 PENDIENTE | — |
| Lab 13 | EFE Indirecto + Directo | — | 🔜 IMPLEMENTADO MVP v8 · pendiente certificación | — |
| Lab 14 | Proyección 5 años + VT | — | 🔜 IMPLEMENTADO MVP v8 · pendiente certificación | — |
| Lab 15 | Valoración DCF | — | 🔜 IMPLEMENTADO MVP v8 · pendiente certificación | — |

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

### R3 — D&A identificada por NOMBRE
```
D&A = SOLO cuentas con nombre DEPRECIACIONES o AMORTIZACIONES
NO incluir: Deterioro, Provisión, Reserva (aunque prefijo 51/52)
Esta D&A se usa en: EBIT (Lab 8), EFE Indirecto (Lab 13), CAPEX = ΔPPE+D&A (Labs 13-14),
FCFF (Lab 15).
```

### R4 — Prefijo 55 → Otros Gastos No Operacionales (Lab 7 v3) [NUEVA]
```
55 NO se suma a Impuestos.
55 → Otros Gastos No Operacionales (junto con 53).
Razón: tasa efectiva de impuesto técnicamente correcta + Margen Neto coherente.
UN del Lab 7 P8 YA incorpora esta reclasificación.
Heredada intacta por Labs 13, 14, 15.
```

### R5 — Deuda Neta (Lab 11, usada Labs 13-15) [NUEVA]
```
Deuda Neta = Obligaciones Financieras (prefijo 21 PURO) − Efectivo (prefijo 11)
NO incluir pasivos operativos (22, 23, 24, 25).
Aplica en: DN/EBITDA, DN/Patrimonio (Lab 11), Cobertura servicio deuda (Lab 13 P8),
Equity Value = EV − Deuda Neta (Lab 15 P7).
```

### R6 — FCF vs FCFF [NUEVA]
```
FCF (Lab 13)  = FNO − CAPEX                              (free cash flow, diagnóstico)
FCFF (Lab 15) = EBIT × (1−T) + D&A − CAPEX − Δ Capital Trabajo
                                                          (free cash flow to firm, descuento)
```

### R7 — max_tokens
```
max_tokens: 8192 para Labs 7-12
max_tokens: 16384 para Labs 13-15 (tablas de 5 años × 3 escenarios + sensibilidades)
Razón: el Lab 14 P5+P6+P7 y el Lab 15 P8 (matrices 6x6) requieren respuestas extensas.
```

### R8 — Lab 9 exclusiones D&A (renumerada)
```
G.Adm (prefijo 51) excluir: 5160, 5165
G.Vtas (prefijo 52) excluir: 5260, 5265
D&A rubro independiente: prefijos 5160, 5165, 5260, 5265 únicamente
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
// claude.ai: usa el modelo nativo del artifact
// GitHub Pages + API Key: usa sonnet-4-5 (calidad equivalente)
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

### R13 — Parámetros de mercado DCF (Mayo 2026) [NUEVA]
```
Rf  = 4.25%   (UST 10Y, Damodaran ene 2026)
ERP = 4.23%   (S&P 500 Implied ERP, Damodaran ene 2026)
CRP = 2.85%   (Country Risk Premium Colombia, Moody's Baa2)
β   = 0.85    (Beta apalancado Food Processing, Damodaran)
TIB = 11.25%  (Banrep 31-mar-2026, subió 100 pbs)
T   = 35%     (Tasa impuestos efectiva Colombia)
g   = 3.0%    (Crecimiento perpetuo Gordon, ≈ meta inflación Banrep)
```

### R14 — Supuestos macro Lab 14 (mayo 2026) [NUEVA]
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

## 5. INFRAESTRUCTURA TÉCNICA

### extractAndValidateBP (función clave)
- Lee el archivo Excel completo (sin límite de filas)
- Detecta columnas por nombre (Código, Nombre, Saldo)
- Prioridad: "Nuevo Saldo Final" > "Saldo Final"
- Detecta clases PUC presentes (1-7)
- Reporta clases faltantes antes de ejecutar
- Para Labs 13-15: detecta presencia de **DOS períodos** (t y t−1) y reporta
- Formato de salida: `[Hoja] Codigo|Nombre|Valor`
- Separador pipe (|) para evitar confusión con comas decimales

### Flujo de datos (extendido para Labs 13-15)
```
Archivo Excel (BP, idealmente con 2 períodos)
  ↓
extractAndValidateBP() → detección NSF vs SF → detección de períodos → transformación si necesario
  ↓
S.activeBPData → persiste en sesión completa
  ↓
[Labs 7-12] autoFill() → sanitize + normalizeMsg → callAPI()
[Labs 13-15] autoFill() + herencia contexto Labs previos → callAPI()
  ↓
Agente recibe datos ya transformados + reglas R1-R14 → suma directa por prefijo PUC
  ↓
Respuesta → addAgent() → exportar Word/Excel/PPT
```

### Modal de API Key (GitHub Pages)
- Se activa automáticamente en dominios externos (no claude.ai, no localhost)
- Modal pantalla completa z-index:9999
- Key almacenada en `S.apiKey` (memoria únicamente, nunca localStorage)
- Validación: debe comenzar con `sk-ant`

---

## 6. DISEÑO RESPONSIVE (MVP v6+)

### Breakpoints
| Pantalla | Layout | Sidebar |
|---|---|---|
| Desktop (>768px) | Sidebar fijo + main | Visible siempre |
| Tablet iPad (≤768px) | Main fullscreen | Drawer ☰ deslizable |
| iPhone (≤480px) | Main fullscreen | Drawer ☰ + 1 columna |

### Características mobile
- Touch targets mínimo 44px (Apple HIG)
- `-webkit-tap-highlight-color: transparent`
- `-webkit-overflow-scrolling: touch`
- `safe-area-inset-bottom` para iPhone con notch
- Modal como bottom sheet (sube desde abajo)
- Toolbar con scroll horizontal (no overflow)
- Sidebar auto-cierra al seleccionar un módulo

---

## 7. ÁRBOL DE ANÁLISIS (MVP v8 — versión actualizada)

```
📊 Análisis Financiero (Labs 7-15)
  │
  ├── 📈 Estado de Resultados (Labs 7-9)
  │     ├── Cálculos Base          [Lab 7]  ✅
  │     ├── KPIs Rentabilidad      [Lab 8]  ✅
  │     ├── Insights del ER        [Lab 9]  ✅
  │     └── ER Completo (7-9)      [Full]   ✅
  │
  ├── 🏦 Estado de Situación Financiera (Labs 10-12)
  │     ├── Cálculos Base          [Lab 10] 🔜
  │     ├── KPIs (11 ratios)       [Lab 11] 🔜
  │     ├── Insights del ESF       [Lab 12] 🔜
  │     └── ESF Completo (10-12)   [Full]   🔜
  │
  ├── 💧 Flujo de Efectivo
  │     └── EFE Indirecto + Directo [Lab 13] 🔜 v8
  │
  ├── 📊 Proyección y Valoración
  │     ├── Proyección 5 años + VT  [Lab 14] 🔜 v8
  │     └── Valoración DCF           [Lab 15] 🔜 v8
  │
  └── 🎯 Análisis Integral (Labs 7-15) [Pipeline completo]
```

---

## 8. HISTORIAL DE ERRORES RESUELTOS

| Error | Causa | Solución |
|-------|-------|----------|
| "string did not match expected pattern" | Faltaban headers `anthropic-version` y `dangerous-direct-browser-access` | Agregados a callAPI |
| "Datos Insuficientes" en BP | Límite 120 filas cortaba clases 4,5,6,7 | extractAndValidateBP sin límite |
| Valores incorrectos vs Add-On Excel | Agente re-transformaba "Nuevo Saldo Final" | applyTransform logic en extractor |
| Lab 8 truncado en D&A | max_tokens=1000 insuficiente | Aumentado a 8192 |
| 404 GitHub Pages | Archivo subido como "index .html" (espacio) | Renombrado a index.html |
| "x-api-key header required" | API pública requiere key explícita | Modal API Key + header dinámico |
| "model: claude-sonnet-4-20250514" error | Modelo no disponible en API pública | S.apiKey → claude-sonnet-4-5-20250929 |
| 5121 mal clasificada en D&A | Regla incorrecta implementada en v5-v6 | Revertida en v7: 5121 → G.Adm |
| Botones inactivos (Script error) | Newline literal en template literal JS | Reemplazado por string concat |
| "Error de red: pattern" | JSON.stringify fallaba con chars especiales | try/catch propio para serialización |
| Banner API Key no aparecía | window.load timing / display none | Cambiado a modal pantalla completa z-index:9999 |
| Lab 14 truncado en matrices | max_tokens=8192 insuficiente para 5 años × 3 escenarios | Aumentado a 16384 en v8 |
| EFE no converge Indirecto vs Directo | Δ Reservas mal incluido en Financiación | Regla en MASTER_SYS Lab 13: solo Δ Capital + Δ Obl Fin |

---

## 9. ARCHIVOS DEL PROYECTO

| Archivo | Descripción | Tamaño |
|---------|-------------|--------|
| `orquestador-mvp-v2.html` | Aplicación principal MVP v8 (Labs 7-15) | ~130KB |
| `index.html` | Landing page GitHub Pages | ~28KB |
| `README.md` | Documentación técnica del repositorio | ~6KB |
| `auditoria-ER-completo.txt` | Log de auditoría Labs 7-9 + nuevos módulos | ~5KB |
| `SKILL-analisis-financiero-consultivo-v8.md` | Skill maestra serie Labs 7-15 | ~14KB |
| `proyecto-multi-agente-financiero-AF.md` | Este documento | ~13KB |

---

## 10. PRÓXIMOS PASOS

### Inmediatos (consolidación MVP v8)
- [ ] Subir MVP v8 a GitHub Pages con Labs 13-15
- [ ] Validar Labs 10-12 con archivo real (BP de 2 períodos)
- [ ] Probar pipeline Lab 13 con identidad contable y convergencia V3
- [ ] Validar matrices de sensibilidad Lab 15 con WACC × g

### Lab 13 — EFE (Indirecto + Directo)
- 9 prompts encadenados, 5 validaciones (V1-V5)
- Doble método con convergencia obligatoria
- Entregable: FCF para alimentar Lab 14

### Lab 14 — Proyección 5 años + Valor Terminal
- Driver-Based Hybrid con 7 palancas y 3 escenarios
- Supuestos macro Colombia 2024-2028 fijos en MASTER_SYS
- Valor Terminal Gordon Growth (g = 3.0%)
- Entregable: tabla FCF + VT para alimentar Lab 15

### Lab 15 — Valoración DCF
- CAPM ajustado por riesgo país (Rf + β × ERP + CRP)
- WACC distinto por escenario
- 3 matrices de sensibilidad bidimensional
- Entregable: EV, Equity Value, múltiplos implícitos, memo C-Level

### Post-Labs 7-15
- Reactivar Repositorio de Empresas (5 empresas PUC colombiano)
- Lab 16 (futuro): Simulación Monte Carlo del DCF
- Preparar versión para uso consultivo (Horizonte 2)
- Documentar pricing y modelo SaaS (Horizonte 3)

---

## 11. VALORES DE REFERENCIA CERTIFICADOS

### 11.1 ER — Empresa del sector salud Colombia (Labs 7-9)
Validados contra Add-On Claude Excel y Add-On ChatGPT Excel.

| KPI | 2024 | % | 2025 | % | YoY |
|-----|------|---|------|---|-----|
| Ingresos Op. | $8,459,873,286 | 100% | $10,789,026,227 | 100% | +27.5% |
| Costos Venta | $4,917,166,236 | 58.1% | $6,127,085,679 | 56.8% | +24.6% |
| Margen Bruto | $3,542,707,050 | 41.9% | $4,661,940,548 | 43.2% | +31.6% |
| G.Adm (c/5121) | $2,460,460,277 | 29.1% | $3,111,307,866 | 28.8% | +26.5% |
| G.Ventas | $2,195,600 | 0.0% | $18,866,161 | 0.2% | +759% |
| EBITDA | $1,080,051,173 | 12.8% | $1,531,766,521 | 14.2% | +41.8% |
| D&A (sin 5121) | $233,843,423 | 2.8% | $202,221,262 | 1.9% | -13.5% |
| EBIT | $846,207,750 | 10.0% | $1,329,545,259 | 12.3% | +57.1% |
| Ing.No Op. | $238,539,099 | 2.8% | $352,037,426 | 3.3% | +47.6% |
| Gtos.No Op. | $58,340,326 | 0.7% | $41,371,858 | 0.4% | -29.1% |
| EBT | $1,026,406,523 | 12.1% | $1,640,210,827 | 15.2% | +59.8% |
| Impuestos | $0 | 0% | $240,833,650 | 2.2% | n/a |
| Margen Neto | $1,026,406,523 | 12.1% | $1,399,377,177 | 13.0% | +36.3% |

### 11.2 Parámetros DCF de referencia (Mayo 2026)

| Parámetro | Valor | Aplicación |
|-----------|-------|-----------|
| Ke preliminar (β=0.85) | 10.70% | Costo del capital propio referencial |
| WACC Pesimista típico | 15-17% | Estructura D/V=70%, spread 400 pbs |
| WACC Base típico | 13-15% | Estructura D/V=55%, spread 300 pbs |
| WACC Optimista típico | 11-13% | Estructura D/V=40%, spread 200 pbs |
| g terminal | 3.0% | Cercano meta inflación Banrep |
| EV/EBITDA sector lácteo | 4x-9x | Banda de triangulación obligatoria |

---

*Documento generado: 27 de mayo de 2026 · Multi-Agente Financiero AF · ADVANCE · Universidad del Rosario*
*MVP v8 — Serie completa Labs 7-15 implementada (certificación en curso para Labs 10-15)*
