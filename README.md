# Multi-Agente FP&A+V — Análisis Financiero con IA
**ADVANCE · Universidad del Rosario · Docente: José Orcasita Celedón**

[![v12](https://img.shields.io/badge/version-v12-C8102E)](https://joseorcasitace.github.io/orquestador-financiero/multi-agente-fpav.html)
[![Labs 7-15](https://img.shields.io/badge/Labs-7--15-1a4f8a)](https://joseorcasitace.github.io/orquestador-financiero/)
[![Nivel 2 PUC](https://img.shields.io/badge/Motor-Hoja%20PUC%20%2B%20Suma%20Cero-1a6b3c)](./auditoria-ER-ESF-EFE-completo.txt)

---

## ¿Qué es?

Herramienta de IA para análisis financiero empresarial que procesa Balances de Prueba reales de **cualquier ERP colombiano** y genera análisis ejecutivos del Estado de Resultados, ESF, EFE, Proyección a 5 años y Valoración DCF, siguiendo el **Plan de Cuentas PUC colombiano**.

**App:** https://joseorcasitace.github.io/orquestador-financiero/multi-agente-fpav.html  
**Landing:** https://joseorcasitace.github.io/orquestador-financiero/

---

## 🆕 Novedades v12 — Junio 2026

### C5 · Filtro de hojas jerárquicas — El problema del doble conteo

Un BP exportado desde cualquier ERP colombiano contiene simultáneamente **cuentas padre** (resúmenes acumulados) y **cuentas hija** (detalle). Sumar todas produce el doble, el triple, o el cuádruple del valor real.

**Evidencia confirmada:** IO v10 = $99,431M ≈ **3×** el certificado $33,082M. El ratio era constante en todas las clases, lo que sólo ocurre con doble conteo simétrico de un árbol PUC con 3 niveles expandidos.

| Clase | v10 (con doble conteo) | Certificado | Ratio |
|-------|----------------------|-------------|-------|
| Ingresos (4) | $99,431M | $33,082M | 3.01× |
| Gastos Op (5) | $32,767M | $6,543M | 5.01× |
| Costos (6+7) | $99,431M | ~$29,000M | 3.43× |

**Solución v12:** el extractor identifica y excluye todas las cuentas padre antes de sumar. Un código X es padre si existe algún código Y en el mismo archivo tal que `Y.startsWith(X) && Y.length > X.length`. Solo se suman las **cuentas hoja** — las que no tienen hijos.

### Motor completo C1-C5

| Cap. | Nombre | Descripción |
|------|--------|-------------|
| **C5** | Filtro de hojas jerárquicas | Elimina cuentas padre para evitar doble conteo. Reporta cuántas se eliminaron. |
| **C1** | Test algebraico suma cero | Decide RUTA A/B por propiedad matemática del BP, nunca por nombre de columna. |
| **C2** | Coherencia de signos | Verifica que todos los rubros sean positivos post-ruta. |
| **C3** | Identidad contable | V.ESF + V.ER + V.FISCAL antes del primer KPI. |
| **C4** | Panel de auditoría | Diagnóstico completo visible al usuario en el modal antes de calcular. |

---

## Arquitectura

```
📊 Análisis Financiero (Labs 7-15)
  ├── 📈 Estado de Resultados (Labs 7-9)        ✅ CERTIFICADO Abr 2026
  ├── 🏦 Estado de Situación Financiera (10-12) 🔜 Certificación Jun 2026
  ├── 💧 EFE Indirecto + Directo [Lab 13]       🔜 Certificación Jul 2026
  ├── 📊 Proyección 5 años + VT  [Lab 14]       🔜 Certificación Ago 2026
  ├── 💎 Valoración DCF          [Lab 15]       🔜 Certificación Sep 2026
  └── 🎯 Análisis Integral (Labs 7-15)
```

### Stack técnico
- **Frontend:** HTML/CSS/JS autocontenido (~161KB) — sin servidor
- **IA:** Claude API (`claude-sonnet-4-20250514` claude.ai | `claude-sonnet-4-5-20250929` GitHub Pages)
- **Excel:** SheetJS (XLSX) — motor inferencial C1-C5
- **Exportación:** docx.js + SheetJS + PptxGenJS

---

## Reglas del motor (R1–R8 + C1-C5)

| Regla | Descripción | Impacto si se viola |
|-------|-------------|---------------------|
| **C5** | Usar solo cuentas hoja del árbol PUC. Excluir padres. | Doble/triple conteo → valores 3-5× inflados |
| **C1** | RUTA decidida por test algebraico suma cero, no por nombre de columna. | ER erróneo con BPs no estándar |
| **R2** | `5121 Deterioro CxC` → G.Adm siempre. NUNCA en D&A. | D&A inflada; EBITDA, EBIT incorrectos |
| **R3** | D&A = SOLO cuentas con nombre `DEPRECIACIONES`/`AMORTIZACIONES` en sub-prefijos 51x. | D&A incorrecto |
| **R4** | Prefijo 55 → Otros Gastos No Operacionales. NO a Impuestos. | TEI distorsionada |
| **R5** | Deuda Neta = OblFin (prefijo 21 puro) − Efectivo. | DN/EBITDA, DSCR incorrectos |
| **R6** | FCF (Lab 13) = FNO − CAPEX. FCFF (Lab 15) = EBIT(1−T)+D&A−CAPEX−ΔCT. | DCF incorrecto |
| **R7** | CAPEX = Δ PPE bruto únicamente. D&A NO se suma. | Doble conteo en EFE |
| **R8** | Si cuenta 36 = 0 → BP pre-cierre → incorporar UN antes del EFE. | V1 y V3 fallan |

---

## Cómo funciona C5 (filtro de hojas)

```
Archivo BP (6,000+ filas con padres e hijos):
  4135   COMERCIO AL POR MAYOR    $33,082M  ← PADRE (será excluido)
  413506 PRODUCTOS LÁCTEOS        $33,082M  ← HOJA (se usa)

Algoritmo:
  1. Construir set de todos los códigos del archivo
  2. Para cada código X: si existe Y tal que Y.startsWith(X) && len(Y)>len(X) → X es padre
  3. Excluir todos los padres
  4. Sumar solo las hojas

Resultado:
  Sin C5: $99,431M (3× inflado)
  Con C5: $33,082M ✅ (valor correcto)
```

---

## Datasets de referencia certificados

| Dataset | Sector | EBITDA | Estado |
|---------|--------|--------|--------|
| `BPWOCONSOL_PE` | Lácteo | $4,241M (12.82%) | ✅ Benchmark |
| `BPERPCONSOL_COSD` | Salud | $1,532M (14.20%) | ✅ Certificado |
| `BPSIIGOCONSOL_ADW` | Retail | $1,106M (4.17%) | ⚠️ Pendiente |

---

## Uso rápido

1. Abrir https://joseorcasitace.github.io/orquestador-financiero/multi-agente-fpav.html
2. Clic en **📋 Caso** → cargar Excel del Balance de Prueba (cualquier ERP)
3. Leer el **panel C5-C1-C2-C3** en el modal — verificar cuántas cuentas padre se eliminaron
4. Seleccionar el análisis (Lab 7 → Lab 15)
5. Exportar en Word, Excel o PowerPoint

---

## Archivos del repositorio

| Archivo | Descripción | Tamaño |
|---------|-------------|--------|
| `multi-agente-fpav.html` | App principal v12 · motor C1-C5 | ~161KB |
| `index.html` | Landing page | ~23KB |
| `README.md` | Este documento | ~6KB |
| `auditoria-ER-ESF-EFE-completo.txt` | Log de auditoría · v12 | ~9KB |
| `proyecto-multi-agente-financiero-AF.md` | Documento técnico | ~16KB |
| `SKILL-analisis-financiero-consultivo-v8.md` | Skill maestra Labs 7-15 | ~14KB |

---

*ADVANCE · Universidad del Rosario · Junio 2026 · v12 · Motor C1-C5 · ERP-agnóstico · Sin doble conteo*
