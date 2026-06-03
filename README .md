# Multi-Agente FP&A+V — Análisis Financiero con IA
**ADVANCE · Universidad del Rosario · Docente: José Orcasita Celedón**

[![v10](https://img.shields.io/badge/version-v10-C8102E)](https://joseorcasitace.github.io/orquestador-financiero/multi-agente-fpav.html)
[![Labs 7-15](https://img.shields.io/badge/Labs-7--15-1a4f8a)](https://joseorcasitace.github.io/orquestador-financiero/)
[![Suma Cero](https://img.shields.io/badge/Motor-Suma%20Cero%20Algebraico-1a6b3c)](./auditoria-ER-ESF-EFE-completo.txt)

---

## ¿Qué es?

Herramienta de IA para análisis financiero empresarial basada en la metodología de los **Laboratorios 7 al 15** del curso *"Planeación Financiera y Valoración con IA"*. Procesa Balances de Prueba reales (cualquier ERP/SIIGO colombiano) y genera análisis ejecutivos del Estado de Resultados, Estado de Situación Financiera, Estado de Flujo de Efectivo, Proyección Financiera a 5 años y Valoración DCF, siguiendo el **Plan de Cuentas PUC colombiano**.

**App:** https://joseorcasitace.github.io/orquestador-financiero/multi-agente-fpav.html  
**Landing:** https://joseorcasitace.github.io/orquestador-financiero/

---

## 🆕 Novedades v10 — Junio 2026

### Rediseño estructural del motor de lectura de Balance de Prueba

La versión anterior (v9) decidía cómo leer el BP basándose en el **nombre de la columna** ("Nuevo Saldo Final" vs "Saldo Final"). Eso hacía al agente dependiente de la convención de nombres de un ERP específico — un agente cerrado, calibrado a un corpus de archivos, no abierto a cualquier BP colombiano.

La v10 reemplaza esa lógica con un **motor inferencial algebraico** que funciona igual con cualquier BP, independientemente del ERP, el nombre de columna, o la versión del archivo:

| # | Capacidad | Descripción |
|---|-----------|-------------|
| **C1** | **Test algebraico de suma cero** | Lee los saldos crudos y suma algebraicamente clases 1–7. Si `│suma│ < 0.5% de │∑clase1│` → RUTA A (transformar). Si no → RUTA B (usar directo). Decisión matemática, no lingüística. |
| **C2** | **Verificación de coherencia post-ruta** | Después de aplicar la decisión, todos los rubros (activos, pasivos, patrimonio, ingresos, gastos) deben ser positivos. Si alguna clase suma negativo → alerta antes de calcular. |
| **C3** | **Identidad contable preliminar** | Verifica `∑Activos ≈ ∑Pasivos + ∑Patrimonio` (V.ESF) y `∑Ingresos − ∑Egresos ≈ Resultado` (V.ER) antes del primer KPI. También detecta TEI > 100% (anomalía fiscal). |
| **C4** | **Panel de auditoría visible al usuario** | El modal muestra la tabla de diagnóstico completa por hoja: suma algebraica, decisión de ruta con justificación numérica, saldos por clase, coherencia de signos e identidad contable. El usuario valida antes de que el agente calcule. |

### Renombre del producto
El archivo y la app pasan de `orquestador-mvp-v2` a **`multi-agente-fpav`** (Multi-Agente FP&A+V).

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
- **Frontend:** HTML/CSS/JS autocontenido (~157KB) — sin servidor
- **IA:** Claude API (`claude-sonnet-4-20250514` en claude.ai | `claude-sonnet-4-5-20250929` en GitHub Pages)
- **Excel:** SheetJS (XLSX) — sin límite de filas, motor inferencial C1-C4
- **Exportación:** docx.js + SheetJS + PptxGenJS — generación en navegador
- **Hosting:** GitHub Pages

---

## Reglas del motor (R1–R8)

| Regla | Descripción | Impacto si se viola |
|-------|-------------|---------------------|
| **R1** | **RUTA decidida por test algebraico (C1)**, no por nombre de columna. El agente NUNCA re-transforma. | ER erróneo con cualquier BP no estándar |
| **R2** | `5121 Deterioro CxC` → G.Adm siempre. NUNCA en D&A. | D&A inflada; EBITDA, EBIT incorrectos |
| **R3** | D&A = SOLO cuentas con nombre `DEPRECIACIONES` o `AMORTIZACIONES`. Búsqueda semántica en todos los sub-prefijos 51x. | D&A incorrecto; cascada a EFE y DCF |
| **R4** | Prefijo 55 → Otros Gastos No Operacionales (NO a Impuestos). | TEI distorsionada; Margen Neto incorrecto |
| **R5** | Deuda Neta = OblFin (prefijo 21 puro) − Efectivo. NO incluir 22, 23, 24, 25. | DN/EBITDA, DSCR, Equity Value incorrectos |
| **R6** | FCF (Lab 13) = FNO − CAPEX. FCFF (Lab 15) = EBIT(1−T)+D&A−CAPEX−ΔCT. No intercambiables. | Valoración DCF incorrecta |
| **R7** | CAPEX = Δ PPE bruto ÚNICAMENTE. D&A NO se suma. | Doble conteo; FCF, V4 incorrectos |
| **R8** | Si cuenta 36 = 0 en BP → BP pre-cierre → incorporar UN al patrimonio antes del EFE. | V1 y V3 fallan |

---

## Cómo funciona el test de suma cero (C1)

Un BP **sin transformar** (forma cruda del ERP) tiene las clases acreedoras (2, 3, 4) con signo negativo y las deudoras (1, 5, 6, 7) con signo positivo. La suma algebraica de todos los saldos tiende a cero porque se cancelan entre sí.

Un BP **ya transformado** (por SIIGO u otro ERP) tiene todas las clases con signo positivo. La suma algebraica es grande y positiva — no tiende a cero.

```
Si │∑(saldos crudos clases 1-7)│ < 0.5% × │∑clase1│  →  RUTA A (transformar)
Si │∑(saldos crudos clases 1-7)│ ≥ 0.5% × │∑clase1│  →  RUTA B (usar directo)
```

Esto funciona con **cualquier BP colombiano**, sin importar el ERP, el nombre de la columna, ni la versión del archivo.

---

## Datasets de referencia certificados

| Dataset | Sector | Período | EBITDA | Estado |
|---------|--------|---------|--------|--------|
| `BPWOCONSOL_PE` | Lácteo | 2022–2023 | $4,241M (12.82%) | ✅ **Benchmark** — converge en 3 ambientes |
| `BPERPCONSOL_COSD` | Salud | 2024–2025 | $1,532M (14.20%) | ✅ Certificado (D&A por excepción 5118/5130) |
| `BPSIIGOCONSOL_ADW` | Retail | 2024–2025 | $1,106M (4.17%) | ⚠️ Pendiente BP 2024 completo + confirmar REA |

---

## Uso rápido

1. Abrir https://joseorcasitace.github.io/orquestador-financiero/multi-agente-fpav.html
2. Si está en GitHub Pages: ingresar API Key de Anthropic (`sk-ant-...`)
3. Clic en **📋 Caso** → cargar archivo Excel del Balance de Prueba (cualquier ERP)
4. **Leer el panel de auditoría C1-C4** que aparece automáticamente y validar la decisión de ruta
5. Seleccionar el análisis (Lab 7 → Lab 15)
6. Exportar en Word, Excel o PowerPoint

> **Nota:** En GitHub Pages se requiere API Key propia de Anthropic (gratuita en console.anthropic.com).  
> En claude.ai no se requiere key — usa el modelo nativo.

---

## Archivos del repositorio

| Archivo | Descripción | Tamaño |
|---------|-------------|--------|
| `multi-agente-fpav.html` | App principal v10 (Labs 7-15) · motor inferencial C1-C4 | ~157KB |
| `index.html` | Landing page | ~22KB |
| `README.md` | Este documento | ~6KB |
| `auditoria-ER-ESF-EFE-completo.txt` | Log de auditoría Labs 7-15 · hallazgos v10 | ~8KB |
| `proyecto-multi-agente-financiero-AF.md` | Documento técnico del proyecto | ~15KB |
| `SKILL-analisis-financiero-consultivo-v8.md` | Skill maestra serie Labs 7-15 | ~14KB |

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

*ADVANCE · Universidad del Rosario · Junio 2026 · v10 · Motor inferencial algebraico*
