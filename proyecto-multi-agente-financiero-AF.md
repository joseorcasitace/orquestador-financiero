# Multi-Agente FP&A+V
## Documento de Proyecto — Estado al 02 de Junio 2026

**Versión activa:** v15  
**Docente:** José Orcasita Celedón · ADVANCE · Universidad del Rosario  
**App:** https://joseorcasitace.github.io/orquestador-financiero/multi-agente-fpav.html  
**Repositorio:** https://github.com/joseorcasitace/orquestador-financiero

---

## 1. DESCRIPCIÓN

Herramienta de IA para análisis financiero empresarial basada en los Laboratorios 7-15 del curso *"Planeación Financiera y Valoración con IA"*. Procesa Balances de Prueba reales de **cualquier ERP colombiano** y genera análisis ejecutivos ER + ESF + EFE + Proyección + DCF bajo el PUC colombiano.

**Diferenciador v15:** Motor C1-C5 que lee cualquier BP correctamente — sin depender del ERP, del nombre de columna, de la versión del archivo, ni de si el árbol PUC viene expandido o resumido.

---

## 2. MOTOR INFERENCIAL C1-C5

### C5 · Filtro de hojas jerárquicas (nuevo v15)

```
PROBLEMA (confirmado v10):
  BP de 6,797 filas incluye cuentas padre E hijas.
  IO v10 = $99,431M = 3.006× el certificado $33,082M.
  Ratio constante en todas las clases → doble conteo simétrico.

SOLUCIÓN:
  Conservar solo las cuentas HOJA del árbol PUC.
  Un código X es PADRE si existe Y tal que:
    normalizeCode(Y).startsWith(normalizeCode(X)) && Y.length > X.length

CÓDIGO CLAVE:
  function normalizeCode(c){
    return String(c).replace(/[\.\-\s]/g,'').replace(/^0+/,'') || c;
  }
  var allCodes = {};
  rawData.forEach(d => allCodes[normalizeCode(d.cod)] = true);

  var parentCodes = {};
  rawData.forEach(d => {
    var nc = normalizeCode(d.cod);
    for(var len=1; len < nc.length; len++){
      if(allCodes[nc.slice(0,len)]) parentCodes[nc.slice(0,len)] = true;
    }
  });

  var rawDataLeaf = rawData.filter(d => !parentCodes[normalizeCode(d.cod)]);
```

### C1 · Test algebraico suma cero
```
Aplicado sobre rawDataLeaf (hojas).
sumaAlg = ∑(hojas clases 1-7, sin transformar)
rutaA   = |sumaAlg| ≤ 0.5% × |∑clase1_hojas|
```

### C2 · Coherencia de signos post-ruta
```
postSums[cl] ≥ 0 para todas las clases. Si negativo → alerta.
```

### C3 · Identidad contable preliminar
```
V.ESF: |Activos − (Pasivos + Patrimonio)| / Activos < 0.01%
V.ER:  Resultado_calc ≈ cuenta 36 (si existe)
V.FISCAL: TEI entre 0% y 100%
```

### C4 · Panel de auditoría en el modal
```
Visible al usuario antes de calcular cualquier KPI.
Por hoja: "6,797 filas raw → 2,297 hojas PUC · RUTA B · Signos OK · ESF OK"
```

---

## 3. REGLAS BLINDADAS

### R1 (rediseñada v10) — Ruta por test algebraico
```
Sin dependencia del nombre de columna ni del ERP.
Ejecutada sobre hojas filtradas (post-C5).
```

### R2 — 5121 en G.Adm (desde v7)
```
5121 "Deterioro CxC" → G.Adm SIEMPRE. NO en D&A.
```

### R3 — D&A por nombre semántico (extendida v8)
```
DEPRECIACIONES o AMORTIZACIONES en sub-prefijos 511x-517x.
```

### R4 — Prefijo 55 → G.No Operacionales
### R5 — Deuda Neta = OblFin(21) − Efectivo(11)
### R6 — FCF (Lab13) ≠ FCFF (Lab15)
### R7 — CAPEX = Δ PPE bruto únicamente
### R8 — BP pre-cierre → R8 antes del EFE

### R13 — Parámetros DCF (Mayo 2026)
```
Rf=4.25% | ERP=4.23% | CRP=2.85% | β=0.85 | TIB=11.25% | T=35% | g=3.0%
```

### R14 — Macro Colombia 2024-2028
```
Año  │ PIB   │ IPC   │ TIB    │ Sector lácteo
2024 │ 1.7%  │ 5.2%  │ 9.50%  │ 0.9%
2025 │ 2.6%  │ 5.1%  │ 9.25%  │ 3.5%
2026 │ 2.4%  │ 6.4%  │ 10.25% │ 3.5%
2027 │ 2.5%  │ 4.0%  │ 8.50%  │ 3.5%
2028 │ 2.8%  │ 3.2%  │ 7.00%  │ 3.5%
```

---

## 4. PIPELINE DE CERTIFICACIONES

| Lab | Módulo | Estado | Fecha |
|-----|--------|--------|-------|
| Lab 7 | ER Cálculos Base | ✅ CERTIFICADO [8/8] | Abr 2026 |
| Lab 8 | ER KPIs | ✅ CERTIFICADO [5/5] | Abr 2026 |
| Lab 9 | ER Insights | ✅ CERTIFICADO [11/11] | Abr 2026 |
| Labs 7-9 con C5 | Re-validación sobre BPs jerárquicos | 🔜 Jun 2026 | — |
| Lab 10 | ESF Cálculos | 🔜 Jun 2026 | — |
| Lab 11 | ESF KPIs (11 ratios) | 🔜 Jun 2026 | — |
| Lab 12 | ESF Insights | 🔜 Jun 2026 | — |
| Lab 13 | EFE Indirecto + Directo | 🔜 Jul 2026 | — |
| Lab 14 | Proyección 5 años + VT | 🔜 Ago 2026 | — |
| Lab 15 | Valoración DCF | 🔜 Sep 2026 | — |

---

## 5. FLUJO COMPLETO extractAndValidateBP() v15

```
Archivo Excel (cualquier ERP, cualquier jerarquía)
  ↓
Detectar cabecera + localizar columnas
  ↓
Leer rawData[] (saldos sin tocar)
  ↓
C5: Construir allCodes + parentCodes → rawDataLeaf[]
    Reportar removedParents y c5Note
  ↓
C1: Test suma cero sobre rawDataLeaf → RUTA A o B
  ↓
Aplicar transformación (si RUTA A) → sheetRows[]
  ↓
C2: postSums por clase → coherenceAlerts[]
  ↓
C3: V.ESF + V.ER + V.FISCAL → identityAlerts[]
  ↓
Detectar isPreClose (cuenta 36 = 0)
  ↓
C4: Construir panelLines[] con C5+C1+C2+C3
  ↓
Retornar { content, validation }
  validation = { ready, classesFound, classesMissing, totalRows,
                 truncated, sheets, isPreClose, hasAlerts,
                 coherenceAlerts, identityAlerts, routesSummary,
                 sheetDiagnosis[{sheet, ruta, rawRows, leafRows,
                                 removedParents, c5Note, ...}] }
```

---

## 6. HISTORIAL DE ERRORES RESUELTOS

| Error | Versión | Causa | Solución |
|-------|---------|-------|----------|
| 5121 en D&A | v5-v6 | Regla incorrecta | R2: 5121 → G.Adm |
| D&A = $0 en COSD | v8 | Solo buscaba 5160/5165 | R3: búsqueda semántica 511x |
| CAPEX doble conteo | v8 | CAPEX = -(ΔPPE+D&A) | R7: solo ΔPPE bruto |
| V4 "Converge" con $261M | v9 | Control evaluaba etiqueta | Bloqueo por valor numérico |
| IO incorrecto con BP "Saldo Final" | v9 | Motor dependía del nombre de columna | C1: test suma cero |
| **IO 3× inflado con BP jerárquico** | **v10** | **Sin filtro de jerarquía PUC** | **C5: filtro de hojas** |

---

## 7. ARCHIVOS DEL REPOSITORIO

| Archivo | Tamaño |
|---------|--------|
| `multi-agente-fpav.html` — App v15 · C1-C5 | ~161KB |
| `index.html` — Landing page | ~23KB |
| `README.md` | ~6KB |
| `auditoria-ER-ESF-EFE-completo.txt` | ~9KB |
| `proyecto-multi-agente-financiero-AF.md` | ~16KB |
| `SKILL-analisis-financiero-consultivo-v8.md` | ~14KB |

---

## 8. VALORES DE REFERENCIA CERTIFICADOS

### ER — COSD sector salud (Labs 7-9)
| KPI | 2024 | 2025 |
|-----|------|------|
| Ingresos Op. | $8,459,873,286 | $10,789,026,227 |
| EBITDA | $1,080,051,173 (12.77%) | $1,531,766,521 (14.20%) |
| D&A (5118+5130) | $233,843,423 | $202,221,262 |
| EBIT | $846,207,750 | $1,329,545,259 |
| Margen Neto | $1,026,406,523 | $1,399,377,177 |

### EFE — PE (benchmark)
| Concepto | 2023 |
|----------|------|
| IO | $33,081,618,980 |
| EBITDA | $4,241,392,150 (12.82%) |
| FNO | $2,603,260,000 |
| FCF | $2,490,570,000 |
| GAP V4 | $0.004M = 0.000% ✅ |

---

*v15 · Motor C1-C5 · ERP-agnóstico · Sin doble conteo · ADVANCE · Universidad del Rosario · Jun 2026*
