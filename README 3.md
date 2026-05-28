# Orquestador Financiero — Multi-Agente de Análisis Financiero con IA

[![MVP v8](https://img.shields.io/badge/MVP-v8-C8102E)](https://joseorcasitace.github.io/orquestador-financiero/orquestador-mvp-v2.html)
[![Labs 7–15](https://img.shields.io/badge/Labs-7--15-1a4f8a)](#metodología-laboratorios-7-15)
[![ADVANCE](https://img.shields.io/badge/ADVANCE-Univ.%20del%20Rosario-c4922a)](https://www.urosario.edu.co/)

Herramienta web de IA para análisis financiero empresarial bajo Plan de Cuentas (PUC) colombiano. Procesa Balances de Prueba reales (ERP/SIIGO) y genera análisis ejecutivos certificados del Estado de Resultados, Estado de Situación Financiera, Estado de Flujo de Efectivo, Proyección Financiera a 5 años y Valoración por Flujo de Caja Descontado (DCF), siguiendo la metodología verbatim de los Laboratorios 7 al 15 del programa **Análisis Financiero Consultivo con IA · ADVANCE · Universidad del Rosario**.

**Docente:** José Orcasita Celedón
**App publicada:** https://joseorcasitace.github.io/orquestador-financiero/
**Aplicación principal:** [`orquestador-mvp-v2.html`](./orquestador-mvp-v2.html)

---

## Tabla de contenido

1. [Qué es esto](#qué-es-esto)
2. [Para quién está hecho](#para-quién-está-hecho)
3. [Cómo usarlo](#cómo-usarlo)
4. [Metodología Laboratorios 7-15](#metodología-laboratorios-7-15)
5. [Reglas blindadas](#reglas-blindadas)
6. [Arquitectura técnica](#arquitectura-técnica)
7. [Despliegue local](#despliegue-local)
8. [Estructura del repositorio](#estructura-del-repositorio)
9. [Roadmap](#roadmap)
10. [Créditos y licencia](#créditos-y-licencia)

---

## Qué es esto

Una aplicación web autocontenida (HTML + JS + Claude API) que automatiza la cadena de análisis financiero consultivo profesional. El usuario sube un Balance de Prueba en Excel, selecciona el módulo de análisis y obtiene resultados ejecutivos idénticos a los producidos por los Add-Ons de Claude y ChatGPT en Excel MS, certificados contra los laboratorios pedagógicos del programa.

**Diferenciador clave:** sin conocimientos técnicos requeridos. El usuario sube el archivo, selecciona qué quiere analizar, y la app aplica automáticamente la metodología certificada — transformación de signos PUC, exclusiones de D&A, cálculo de KPIs, validaciones contables, escenarios proyectados y modelo DCF — con las reglas profesionales blindadas en el system prompt.

## Para quién está hecho

- **Docentes de posgrado** que enseñan análisis financiero, valoración o planeación financiera y necesitan ejecutar laboratorios estandarizados sobre datos reales.
- **Estudiantes avanzados** (MBA, especialización en finanzas) que construyen modelos consultivos siguiendo la cadena Lab 7 → Lab 15.
- **Profesionales financieros** (CFOs, analistas de banca de inversión en formación) que requieren un acelerador para análisis ejecutivos con metodología auditable.
- **Equipos consultores** que necesitan triangular sus análisis Excel con un benchmark IA independiente.

## Cómo usarlo

1. **Abra la app:** https://joseorcasitace.github.io/orquestador-financiero/orquestador-mvp-v2.html
2. **Ingrese su API key de Anthropic** (debe comenzar con `sk-ant`). La key permanece en memoria — nunca se persiste.
3. **Cargue el Balance de Prueba** (Excel ERP/SIIGO). Para los Labs 13-15 son obligatorios **dos períodos** (t y t-1).
4. **Seleccione el módulo** desde el árbol lateral o el modal:
   - ER (Lab 7), KPIs (Lab 8), Insights ER (Lab 9), ER Completo
   - ESF (Lab 10), KPIs ESF (Lab 11), Insights ESF (Lab 12), ESF Completo
   - EFE Indirecto + Directo (Lab 13)
   - Proyección 5 años + VT (Lab 14)
   - Valoración DCF (Lab 15)
   - Análisis Integral (Labs 7-15)
5. **Reciba el análisis ejecutivo** con tablas, validaciones y memo C-Level.
6. **Exporte** a Word, Excel o PowerPoint con un clic.

---

## Metodología Laboratorios 7-15

La aplicación implementa la cadena metodológica completa del programa ADVANCE. Cada Lab hereda los insumos certificados del anterior — no se recalculan ni se reabren decisiones.

### Bloque A · Estado de Resultados (Labs 7-9) ✅ Certificado

| Lab | Módulo | Salida |
|-----|--------|--------|
| Lab 7 | ER Cálculos Base | 8 rubros a 4 dígitos con signos PUC |
| Lab 8 | ER KPIs Rentabilidad | Margen Bruto, EBITDA, EBIT, EBT, Margen Neto |
| Lab 9 | ER Insights | Top 10 por rubro a 6 dígitos + resumen ejecutivo |

### Bloque B · Estado de Situación Financiera (Labs 10-12)

| Lab | Módulo | Salida |
|-----|--------|--------|
| Lab 10 | ESF Cálculos Base | 21 rubros a 6 dígitos + identidad contable |
| Lab 11 | ESF KPIs | 11 ratios (liquidez, endeudamiento, ROA, ROE, rotación, Deuda Neta) |
| Lab 12 | ESF Insights | Top 10 por 5 rubros a 6 dígitos + comparativo YoY |

### Bloque C · Flujo de Efectivo (Lab 13)

| Lab | Módulo | Salida |
|-----|--------|--------|
| Lab 13 | EFE Indirecto + Directo | Doble método, 5 validaciones (V1-V5), FCF = FNO − CAPEX, calidad del FNO |

### Bloque D · Proyección Financiera (Lab 14)

| Lab | Módulo | Salida |
|-----|--------|--------|
| Lab 14 | Proyección 5 años + VT | Driver-Based Hybrid, 7 palancas, 3 escenarios, Valor Terminal Gordon Growth |

**Supuestos macro Colombia 2024-2028** (oficiales, fuentes: Banrep, FMI, DNP, Bancolombia, Asoleche):

| Variable | 2024 | 2025 | 2026 | 2027 | 2028 |
|----------|------|------|------|------|------|
| PIB | 1.7% | 2.6% | 2.4% | 2.5% | 2.8% |
| IPC | 5.2% | 5.1% | 6.4% | 4.0% | 3.2% |
| TIB | 9.50% | 9.25% | 10.25% | 8.50% | 7.00% |
| Sector lácteo | 0.9% | 3.5% | 3.5% | 3.5% | 3.5% |

### Bloque E · Valoración DCF (Lab 15)

| Lab | Módulo | Salida |
|-----|--------|--------|
| Lab 15 | Valoración DCF | CAPM ajustado riesgo país, WACC por escenario, EV/Equity Value, 3 matrices de sensibilidad bidimensional |

**Ecuación maestra:**
```
Enterprise Value = Σ [FCFF_t / (1 + WACC)^t] + [VT / (1 + WACC)^n]
Equity Value     = EV − Deuda Neta
```

**Parámetros de mercado (Mayo 2026):** Rf = 4.25% · ERP = 4.23% · CRP Colombia = 2.85% · β sector lácteo = 0.85 · g = 3.0%

---

## Reglas blindadas

El system prompt del agente contiene 14 reglas no negociables que garantizan resultados idénticos al Add-On Claude/ChatGPT en Excel:

| # | Regla | Aplicación |
|---|-------|-----------|
| R1 | RUTA A/B columna base | "Nuevo Saldo Final" → directo; "Saldo Final" → extractor transforma; agente nunca re-transforma |
| R2 | Cuenta 5121 → G.Adm | Deterioro CxC nunca va en D&A |
| R3 | D&A por NOMBRE | Solo cuentas con nombre DEPRECIACIONES o AMORTIZACIONES en clases 5,6,7 |
| R4 | Prefijo 55 → Otros Gtos No Op | NO se suma a Impuestos (54). Heredada de Lab 7 v3 |
| R5 | Deuda Neta | Obl Fin (21 puro) − Efectivo. Usada en Labs 11, 13 y 15 |
| R6 | FCF vs FCFF | FCF = FNO − CAPEX (Lab 13) · FCFF = EBIT(1−T) + D&A − CAPEX − ΔCT (Lab 15) |
| R7 | max_tokens | 16384 (Labs 13-15 requieren tablas extensas) |
| R8 | Lab 9 exclusiones | G.Adm excluir 5160, 5165; G.Vtas excluir 5260, 5265 |
| R9 | Headers API | `anthropic-version: 2023-06-01` + `anthropic-dangerous-direct-browser-access: true` |
| R10 | Modelos | `claude-sonnet-4-5-20250929` con API Key; `claude-sonnet-4-20250514` en claude.ai |
| R11 | JSON.stringify protegido | try/catch propio para serialización |
| R12 | normalizeMsg | Mensajes como array de bloques, nunca string plano |
| R13 | Parámetros DCF | Fijos en MASTER_SYS para evitar improvisación del agente |
| R14 | Supuestos macro | Tabla 2024-2028 fija en MASTER_SYS |

Detalle completo en [`proyecto-multi-agente-financiero-AF.md`](./proyecto-multi-agente-financiero-AF.md).

---

## Arquitectura técnica

### Stack

- **Frontend:** HTML + CSS + JavaScript autocontenido (~141 KB) — sin servidor, sin build step.
- **IA:** Claude API directa desde navegador.
- **Procesamiento Excel:** SheetJS (XLSX) — sin límite de filas.
- **Exportación:** docx.js + SheetJS + PptxGenJS — generación en navegador.
- **Hosting:** GitHub Pages — gratuito, sin servidor.

### Tres agentes (Horizonte 1)

- **Agente Pedagógico:** `finanzas-docente` + `diseno-academico-ia`
- **Agente Financiero:** `analisis-financiero-consultivo` + `benchmark`
- **Agente Producción:** `frontend-design` + `xlsx/docx/pptx`

### Modal de API Key (GitHub Pages)

Cuando la app se sirve desde un dominio externo (no claude.ai, no localhost), aparece automáticamente un modal de pantalla completa solicitando la API key de Anthropic. La key se almacena únicamente en memoria (`S.apiKey`) y nunca se persiste a `localStorage` ni se transmite a ningún servidor distinto de `api.anthropic.com`.

### Diseño responsive

| Pantalla | Layout |
|----------|--------|
| Desktop (>768px) | Sidebar fijo + main |
| Tablet iPad (≤768px) | Drawer ☰ + main fullscreen |
| iPhone (≤480px) | Drawer ☰ + 1 columna + bottom sheet |

Touch targets mínimo 44 px (Apple HIG), scroll horizontal en toolbar, modal como bottom sheet en móvil.

---

## Despliegue local

Si desea ejecutar la app en su máquina (útil para pruebas o desarrollo):

```bash
# 1. Clonar el repositorio
git clone https://github.com/joseorcasitace/orquestador-financiero.git
cd orquestador-financiero

# 2. Servir con cualquier servidor HTTP estático
python3 -m http.server 8000
# o
npx serve .

# 3. Abrir en navegador
# http://localhost:8000/orquestador-mvp-v2.html
```

**Nota:** servir desde `file://` directo no funciona porque el navegador bloquea las llamadas a la API por CORS. Use siempre un servidor HTTP local.

---

## Estructura del repositorio

```
orquestador-financiero/
├── orquestador-mvp-v2.html             # Aplicación principal (MVP v8)
├── index.html                          # Landing page GitHub Pages
├── README.md                           # Este archivo
├── proyecto-multi-agente-financiero-AF.md  # Documento de proyecto · estado actual
├── auditoria-ER-completo.txt           # Log de auditoría de certificaciones
└── SKILL-analisis-financiero-consultivo-v8.md  # Skill maestra serie Labs 7-15
```

---

## Roadmap

### Pipeline de certificaciones

| Lab | Estado | Próximo hito |
|-----|--------|--------------|
| Lab 7 | ✅ Certificado (abr 2026) | — |
| Lab 8 | ✅ Certificado (abr 2026) | — |
| Lab 9 | ✅ Certificado (abr 2026) | — |
| Lab 10 | 🔜 Implementado | Validar identidad contable junio 2026 |
| Lab 11 | 🔜 Implementado | Validar 11 ratios verbatim junio 2026 |
| Lab 12 | 🔜 Implementado | Validar Top 10 + YoY junio 2026 |
| Lab 13 | 🔜 Implementado (MVP v8) | Validar convergencia V3 julio 2026 |
| Lab 14 | 🔜 Implementado (MVP v8) | Validar 5 escenarios x 5 años agosto 2026 |
| Lab 15 | 🔜 Implementado (MVP v8) | Validar matrices sensibilidad septiembre 2026 |

### Horizontes de desarrollo

- **Horizonte 1 (activo):** Entorno docente con Labs 7-15
- **Horizonte 2 (próximo):** Uso interno consultivo para empresas reales
- **Horizonte 3 (futuro):** SaaS / Agent-as-a-Service

### Funcionalidades futuras

- **Lab 16:** Simulación Monte Carlo del DCF con distribuciones de probabilidad
- **Repositorio de empresas:** 5 empresas PUC colombiano precargadas
- **Histórico de análisis:** persistencia opcional de análisis previos
- **Modo consultivo:** workflow para análisis de empresas no docentes (Horizonte 2)

---

## Créditos y licencia

**Programa:** Posgrado en Finanzas · ADVANCE · Universidad del Rosario
**Curso:** Análisis Financiero Consultivo con IA
**Docente y arquitecto del proyecto:** José Orcasita Celedón

**Validaciones cruzadas:**
- Add-On Claude para Excel (Anthropic)
- Add-On ChatGPT para Excel (OpenAI)

**Tecnologías de terceros:**
- [Claude API](https://docs.anthropic.com/) — Anthropic
- [SheetJS](https://sheetjs.com/) — procesamiento Excel en navegador
- [docx.js](https://docx.js.org/) — generación Word
- [PptxGenJS](https://gitbrent.github.io/PptxGenJS/) — generación PowerPoint
- [Mammoth](https://github.com/mwilliamson/mammoth.js) — lectura .docx

**Licencia de uso:** este proyecto es material académico del programa ADVANCE de la Universidad del Rosario. El código está disponible bajo licencia MIT para uso educativo y consultivo. La metodología de los Laboratorios 7-15 es propiedad intelectual del programa académico.

---

*MVP v8 · Mayo 2026 · Serie completa Labs 7-15 implementada*
*ADVANCE · Universidad del Rosario*
