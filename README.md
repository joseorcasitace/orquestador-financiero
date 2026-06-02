# Orquestador Financiero — Multi-Agente de Análisis Financiero con IA
**ADVANCE · Universidad del Rosario · Docente: José Orcasita Celedón**

[![MVP v9](https://img.shields.io/badge/MVP-v9-C8102E)](https://joseorcasitace.github.io/orquestador-financiero/orquestador-mvp-v2.html)
[![Labs 7-15](https://img.shields.io/badge/Labs-7--15-1a4f8a)](https://joseorcasitace.github.io/orquestador-financiero/)
[![Auditoría Junio 2026](https://img.shields.io/badge/Auditoría-Junio%202026-1a6b3c)](./auditoria-ER-ESF-EFE-completo.txt)

---

## ¿Qué es?

Herramienta de IA para análisis financiero empresarial basada en la metodología de los **Laboratorios 7 al 15** del curso *"Planeación Financiera y Valoración con IA"*. Procesa Balances de Prueba reales (ERP/SIIGO) y genera análisis ejecutivos del Estado de Resultados, Estado de Situación Financiera, Estado de Flujo de Efectivo, Proyección Financiera a 5 años y Valoración DCF, siguiendo el **Plan de Cuentas PUC colombiano**.

**App:** https://joseorcasitace.github.io/orquestador-financiero/orquestador-mvp-v2.html  
**Landing:** https://joseorcasitace.github.io/orquestador-financiero/

---

## 🆕 Novedades MVP v9 — Junio 2026

Basadas en auditoría comparativa End-to-End (3 ambientes × 3 datasets):

| # | Ajuste | Impacto |
|---|--------|---------|
| A1 | **CAPEX = Δ PPE bruto ÚNICAMENTE** — se eliminó el doble conteo `CAPEX = -(ΔPPE + D&A)` | FCF, DSCR, Lab 14 correctos |
| A2 | **Detección semántica D&A extendida** — ahora cubre prefijos 511x, 512x, 513x además de 5160/5165 | COSD y BPs con D&A fuera de estándar |
| A3 | **Bloqueo EFE V4** — si `Δ efectivo calculado ≠ Δ efectivo real ESF` y la diferencia no está explicada, el agente bloquea la interpretación CFO | Integridad del pipeline |
| M1 | Detector automático de BP pre-cierre (cuenta 36 = 0) → aplica R8 y documenta la brecha técnica | Elimina falsos errores V2 |
| M2 | Alerta de comparabilidad: si el BP de un período tiene clases PUC faltantes vs. el otro, emite advertencia antes de calcular variaciones | ADW y BPs incompletos |
| M3 | Detector de anomalía fiscal: si TEI > 100% o < 0%, emite alerta de revisión con revisor fiscal | ADW tasa 377% |
| M4 | Clasificación automática de Resultados Ejercicios Anteriores (REA) en EFE como *pendiente de confirmación contable* cuando coincide con la diferencia no conciliada | ADW diferencia $572M |

---

## Arquitectura

```
📊 Análisis Financiero (Labs 7-15)
  ├── 📈 Estado de Resultados (Labs 7-9)        ✅ CERTIFICADO Abr 2026
  │     ├── Cálculos Base          [Lab 7]
  │     ├── KPIs Rentabilidad      [Lab 8]
  │     ├── Insights del ER        [Lab 9]
  │     └── ER Completo (7-9)
  ├── 🏦 Estado de Situación Financiera (Labs 10-12)  🔜 Certificación Jun 2026
  │     ├── Cálculos Base          [Lab 10]
  │     ├── KPIs (11 ratios)       [Lab 11]
  │     ├── Insights del ESF       [Lab 12]
  │     └── ESF Completo (10-12)
  ├── 💧 Flujo de Efectivo
  │     └── EFE Indirecto + Directo [Lab 13]    🔜 Certificación Jul 2026
  ├── 📊 Proyección y Valoración
  │     ├── Proyección 5 años + VT  [Lab 14]    🔜 Certificación Ago 2026
  │     └── Valoración DCF          [Lab 15]    🔜 Certificación Sep 2026
  └── 🎯 Análisis Integral (Labs 7-15)
```

### Stack técnico
- **Frontend:** HTML/CSS/JS autocontenido (~135KB) — sin servidor
- **IA:** Claude API (`claude-sonnet-4-20250514` en claude.ai | `claude-sonnet-4-5-20250929` en GitHub Pages)
- **Excel:** SheetJS (XLSX) — sin límite de filas
- **Exportación:** docx.js + SheetJS + PptxGenJS — generación en navegador
- **Hosting:** GitHub Pages

---

## Reglas críticas del motor (R1–R8)

| Regla | Descripción | Impacto si se viola |
|-------|-------------|---------------------|
| **R1** | RUTA A (`Nuevo Saldo Final`) → usar directo. RUTA B (`Saldo Final`) → extractor aplica ×(−1) a clases 2,3,4. **El agente NUNCA re-transforma.** | ER completamente erróneo |
| **R2** | `5121 Deterioro CxC` → **G.Adm siempre**. NUNCA en D&A. | D&A inflada; EBITDA, EBIT incorrectos |
| **R3** | D&A = **SOLO** cuentas con nombre `DEPRECIACIONES` o `AMORTIZACIONES` en clases 5, 6, 7. Búsqueda semántica en **todos** los sub-prefijos 51x. | D&A incorrecto; cascada a EFE y DCF |
| **R4** | Prefijo 55 → **Otros Gastos No Operacionales** (NO a Impuestos). | TEI distorsionada; Margen Neto incorrecto |
| **R5** | Deuda Neta = OblFin (prefijo 21 **puro**) − Efectivo. NO incluir 22, 23, 24, 25. | DN/EBITDA, DSCR, Equity Value incorrectos |
| **R6** | FCF (Lab 13) = FNO − CAPEX. FCFF (Lab 15) = EBIT(1−T)+D&A−CAPEX−ΔCT. No intercambiables. | Valoración DCF incorrecta |
| **R7** | **CAPEX = Δ PPE bruto ÚNICAMENTE.** D&A NO se suma. | Doble conteo; FCF, V4 incorrectos |
| **R8** | Si cuenta 36 = 0 en BP, incorporar UN del Lab 7 al patrimonio antes de calcular EFE. | V1 y V3 fallan |

---

## Datasets de referencia certificados

| Dataset | Sector | Período | EBITDA | Estado |
|---------|--------|---------|--------|--------|
| `BPWOCONSOL_PE` | Lácteo | 2022–2023 | $4,241M (12.82%) | ✅ **Benchmark** — converge en 3 ambientes |
| `BPERPCONSOL_COSD` | Salud | 2024–2025 | $1,532M (14.20%) | ✅ Certificado (D&A por excepción) |
| `BPSIIGOCONSOL_ADW` | Retail | 2024–2025 | $1,106M (4.17%) | ⚠️ Pendiente BP 2024 completo |

### Valores certificados ER — COSD (empresa salud)
| KPI | 2024 | 2025 |
|-----|------|------|
| Ingresos Operacionales | $8,459,873,286 | $10,789,026,227 |
| EBITDA | $1,080,051,173 (12.77%) | $1,531,766,521 (14.20%) |
| D&A (5118+5130 por excepción) | $233,843,423 | $202,221,262 |
| EBIT | $846,207,750 (10.00%) | $1,329,545,259 (12.32%) |
| Utilidad Neta | $1,026,406,523 | $1,399,377,177 |

---

## Uso rápido

1. Abrir https://joseorcasitace.github.io/orquestador-financiero/orquestador-mvp-v2.html
2. Si está en GitHub Pages: ingresar API Key de Anthropic (`sk-ant-...`)
3. Clic en **📋 Caso** → cargar archivo Excel del Balance de Prueba
4. Seleccionar el análisis (Lab 7 → Lab 15)
5. Exportar en Word, Excel o PowerPoint

> **Nota:** En GitHub Pages se requiere API Key propia de Anthropic (gratuita en console.anthropic.com).  
> En claude.ai no se requiere key — usa el modelo nativo.

---

## Archivos del repositorio

| Archivo | Descripción |
|---------|-------------|
| `orquestador-mvp-v2.html` | App principal MVP v9 (Labs 7-15) ~135KB |
| `index.html` | Landing page |
| `README.md` | Este documento |
| `auditoria-ER-ESF-EFE-completo.txt` | Log de auditoría Labs 7-15 certificado Jun 2026 |
| `proyecto-multi-agente-financiero-AF.md` | Documento técnico del proyecto |
| `SKILL-analisis-financiero-consultivo-v8.md` | Skill maestra serie Labs 7-15 |

---

## Parámetros de mercado DCF (Mayo 2026)

| Parámetro | Valor | Fuente |
|-----------|-------|--------|
| Rf (UST 10Y) | 4.25% | Damodaran |
| ERP (S&P 500 Implied) | 4.23% | Damodaran |
| CRP Colombia | 2.85% | Moody's Baa2 |
| β sector lácteo | 0.85 | Damodaran Food Processing |
| TIB (Banrep mar-2026) | 11.25% | Banrep |
| T impuesto efectivo | 35% | Reforma tributaria 2022 |
| g crecimiento perpetuo | 3.0% | Meta inflación Banrep |

---

*ADVANCE · Universidad del Rosario · Junio 2026 · MVP v9*
