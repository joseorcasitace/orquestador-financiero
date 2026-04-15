# 🎓 Orquestador Docente — Análisis Financiero con IA

**Multi-Agente de Análisis Financiero para Posgrado · ADVANCE · Universidad del Rosario**

[![Labs 7-9 Certificados](https://img.shields.io/badge/Labs%207--9-Certificados-green)](https://joseorcasitace.github.io/orquestador-financiero/)
[![Checks](https://img.shields.io/badge/Auditor%C3%ADa-38%2F38%20OK-brightgreen)](./auditoria-ER-completo.txt)
[![PUC Colombia](https://img.shields.io/badge/Plan%20de%20Cuentas-PUC%20Colombia-blue)](https://joseorcasitace.github.io/orquestador-financiero/)

---

## 🚀 Acceso Directo

**Landing Page:** https://joseorcasitace.github.io/orquestador-financiero/

**Orquestador MVP:** https://joseorcasitace.github.io/orquestador-financiero/orquestador-mvp-v2.html

---

## ¿Qué es el Orquestador Docente?

Herramienta de IA para análisis financiero empresarial basada en la metodología de los Laboratorios 7 al 12 del curso **"Planeación Financiera y Valoración con IA"**. Procesa Balances de Prueba reales (ERP/SIIGO) y genera análisis ejecutivos del Estado de Resultados y Estado de Situación Financiera siguiendo el Plan de Cuentas PUC colombiano.

---

## ✅ Módulos Certificados (MVP v2)

| Lab | Módulo | Estado | Checks |
|-----|--------|--------|--------|
| **Lab 7** | ER — Cálculos Base | ✅ Certificado | 8/8 |
| **Lab 8** | ER — KPIs Rentabilidad | ✅ Certificado | 5/5 |
| **Lab 9** | ER — Insights | ✅ Certificado | 11/11 |
| **ER Completo** | Labs 7 + 8 + 9 | ✅ Certificado | 38/38 total |
| Lab 10 | ESF — Cálculos Base | 🔜 En desarrollo | — |
| Lab 11 | ESF — KPIs (11 ratios) | 🔜 En desarrollo | — |
| Lab 12 | ESF — Insights | 🔜 En desarrollo | — |

---

## 📁 Estructura del Repositorio

```
/
├── index.html                    ← Landing page (GitHub Pages)
├── orquestador-mvp-v2.html       ← Aplicación principal
├── auditoria-ER-completo.txt     ← Log de auditoría acumulado
└── README.md
```

---

## 🔧 Cómo Usar

1. Abra el [Orquestador MVP](https://joseorcasitace.github.io/orquestador-financiero/orquestador-mvp-v2.html)
2. Haga clic en **📋 Caso**
3. Cargue su archivo Excel (Balance de Prueba ERP o SIIGO — con o sin transformar)
4. Seleccione el análisis: Lab 7, 8, 9 o ER Completo
5. El agente ejecuta el análisis con metodología PUC certificada
6. Exporte en **Word**, **Excel** o **PowerPoint**

---

## 📐 Metodología

### Reglas fundamentales

- **Columna base**: "Nuevo Saldo Final" — si el archivo ya la tiene, se usa directamente. Si no, el extractor aplica la transformación PUC automáticamente.
- **Transformación PUC**: códigos 2, 3, 4 × (−1) | códigos 1, 5, 6, 7 × (+1)
- **El agente NUNCA re-transforma** — los datos llegan listos para operar

### Lab 7 — Cálculos Base ER

| Rubro | Prefijos PUC | Nivel | Exclusiones |
|-------|-------------|-------|-------------|
| Ingresos Operacionales | 41 | 4 dígitos | — |
| Ingresos No Operacionales | 42 | 4 dígitos | — |
| Costos de Venta | 61, 62, 71, 72, 73, 74 | 4 dígitos | DEPRECIACIONES, AMORTIZACIONES |
| Gastos Op. Administración | 51 | 4 dígitos | DEPRECIACIONES, AMORTIZACIONES |
| Gastos Op. Ventas | 52 | 4 dígitos | DEPRECIACIONES, AMORTIZACIONES |
| Gastos D&A | 51, 52, 71, 72, 73 | 4 dígitos | SOLO DEPRECIACIONES y AMORTIZACIONES |
| Gastos No Operacionales | 53 | 4 dígitos | — |
| Gastos Impuestos Renta | 54 | 4 dígitos | — |

### Lab 8 — KPIs Rentabilidad

```
Margen Bruto  = Ingresos Op − Costos Venta
EBITDA        = IO − Costos − G.Adm − G.Ventas
EBIT          = EBITDA − D&A
EBT           = EBIT + Ing.No Op − Gtos.No Op
Margen Neto   = EBT − Impuestos Renta
% KPI         = KPI / Ingresos Operacionales × 100 (2 decimales)
```

### Lab 9 — Insights

- Top 10 cuentas a **6 dígitos** por cada uno de los 8 rubros
- Exclusiones D&A: 5160, 5165 de prefijo 51 | 5260, 5265 de prefijo 52
- D&A como rubro independiente: prefijos 5160, 5165, 5260, 5265
- Resumen Ejecutivo al cierre: foto ER + KPIs + insights + acciones priorizadas

---

## 🏗️ Stack Técnico

- **Frontend**: HTML/CSS/JS autocontenido (~106KB) — sin dependencias de servidor
- **IA**: Claude Sonnet 4 via Anthropic API (claude-sonnet-4-20250514)
- **Procesamiento Excel**: SheetJS (XLSX) — extracción completa sin límite de filas
- **Exportación**: docx.js + SheetJS + PptxGenJS — generación en navegador
- **Hosting**: GitHub Pages — gratuito, sin servidor

---

## 👤 Autor

**José Orcasita Celedón**  
Docente ADVANCE · Universidad del Rosario  
[LinkedIn](https://www.linkedin.com/in/jose-orcasita-celedon/) · [GitHub](https://github.com/joseorcasitace)

---

## 📄 Auditoría

El archivo [`auditoria-ER-completo.txt`](./auditoria-ER-completo.txt) contiene el registro completo de los 38 checks de auditoría aprobados para los Labs 7, 8, 9 y ER Completo.

---

*ADVANCE · Universidad del Rosario · 2026*
