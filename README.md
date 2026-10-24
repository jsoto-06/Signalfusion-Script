# 📊 SignalFusion ScalpStation SR 1.0 (v2.1)

> Indicador y estrategia para **TradingView** (Pine Script v6) que combina scoring de señales, configuración EMA tipo ScalpStation y soportes/resistencias dinámicos.

![Pine Script](https://img.shields.io/badge/Pine%20Script-v6-blue)
![Estado](https://img.shields.io/badge/estado-en%20pruebas-orange)
![Plataforma](https://img.shields.io/badge/plataforma-TradingView-131722)
![Licencia](https://img.shields.io/badge/licencia-MIT-green)

---

## 📑 Índice

1. [Descripción](#-descripción)
2. [Características principales](#-características-principales)
3. [Lógica de señales](#-lógica-de-señales)
4. [Gestión de riesgo](#️-gestión-de-riesgo)
5. [Soportes y resistencias](#-soportes-y-resistencias)
6. [Parámetros](#️-parámetros)
7. [Instalación y uso](#-instalación-y-uso)
8. [Resultados de prueba](#-resultados-de-prueba)
9. [Changelog](#-changelog)
10. [Aviso legal](#️-aviso-legal)

---

## 🧭 Descripción

**SignalFusion ScalpStation SR 1.0** unifica tres componentes en un solo script:

- **SignalFusion**: sistema de puntuación (*score*) para señales largas y cortas, con pesos configurables por condición.
- **ScalpStation**: estructura de EMAs (9, 21, 50, 200) y filtros de tendencia para operar a corto plazo.
- **Soportes/Resistencias dinámicos**: niveles generados a partir de pivotes, con control de distancia mínima y número de toques.

---

## ✨ Características principales

| Función | Descripción |
|---|---|
| 🎚️ Modos de riesgo | `Conservative`, `Standard` y `Aggressive` ajustan umbrales y multiplicadores ATR |
| 🧮 Scoring ponderado | Cada condición suma peso; la señal se activa al superar el umbral |
| 📈 Filtro ADX | Solo opera cuando hay tendencia (ADX por encima del umbral) |
| 🔊 Filtros de volumen y volatilidad | Evita entradas en mercados planos o con volumen bajo |
| 🎯 SL/TP por ATR | Stops y objetivos separados para largos y cortos, con trailing stop |
| 🏷️ Marcadores visuales | Etiquetas y líneas de entrada, SL y TP en el gráfico |
| 🧱 Soportes y resistencias | Niveles dinámicos con borrado automático de niveles antiguos |
| 🔔 Alertas | Señales de entrada, SL alcanzado y TP alcanzado |
| 📋 Panel de información | Tabla con modo, puntuaciones, SL/TP, RSI/ATR, winrate y profit factor |

---

## 🧠 Lógica de señales

### Señal LONG (suma de pesos)

- RSI < 35 (peso alto) y RSI < 45 subiendo
- Cruce alcista EMA Fast / EMA Slow
- EMA Fast > EMA Slow y cierre por encima de EMA Fast
- Histograma MACD positivo y creciente
- Cierre por encima de EMA Mid, o rebote desde EMA Mid
- Cierre por debajo de la banda inferior de Bollinger
- Cierre por encima de la media de tendencia
- Filtros opcionales: volumen, VWAP y OBV

### Señal SHORT (suma de pesos)

- RSI > 65 (peso alto) y RSI > 60 bajando
- Cruce bajista EMA Fast / EMA Slow
- EMA Fast < EMA Slow y cierre por debajo de EMA Fast
- Histograma MACD negativo y decreciente
- Ruptura de la banda superior de Bollinger con cierre por debajo
- Cierre por debajo de EMA Mid y de la media de tendencia
- Filtros opcionales: volumen, VWAP y OBV

### Condiciones para disparar una señal

Una señal se activa solo si se cumplen **todas** estas condiciones:

- Score ≥ umbral del modo activo
- Dentro del horario de operación (si el filtro está activo)
- Volatilidad y volumen válidos (si los filtros están activos)
- ADX por encima del umbral

---

## 🛡️ Gestión de riesgo

Los niveles se calculan con el **ATR(14)** y varían según el timeframe y el modo:

| Parámetro | Long | Short |
|---|---|---|
| Multiplicador SL (base) | 1.3 | 1.3 |
| Multiplicador TP (base) | 4.0 | 3.0 |
| Trailing stop | 3.5 × ATR | 3.5 × ATR |

**Ajustes por modo:**

| Modo | Umbral Long | Umbral Short | SL | TP |
|---|---|---|---|---|
| Conservative | 4.0 | 4.5 | ×1.15 | ×0.95 |
| Standard | 2.0 | 2.0 | ×1.00 | ×1.00 |
| Aggressive | 2.0 | 2.5 | ×0.90 | ×1.25 |

**Ajustes por timeframe** (multiplicador sobre el ATR base): en 1m y 5m el SL y el TP se amplían; en 1h se usan los valores base con un SL algo más ajustado.

---

## 🧱 Soportes y resistencias

- Se detectan **pivotes** con `Pivot length` (por defecto 15).
- Un nuevo nivel no se dibuja si está a menos de `Distancia mínima` (en ticks) de otro existente.
- Se cuentan **toques** dentro de una ventana (`Lookback`) con una tolerancia en ticks.
- Se muestran como máximo N líneas por tipo y se eliminan las más antiguas que superen la edad máxima.

---

## ⚙️ Parámetros

Los parámetros principales se agrupan en el panel de configuración de TradingView:

| Grupo | Parámetros clave | Valor por defecto |
|---|---|---|
| Global | Modo, EMA/SMA, señales visuales | `Standard`, SMA |
| Indicadores | RSI, Bollinger, MACD, EMAs, ADX | 14 / 20 / 12-26-9 / 9-21-50-200 / 14 |
| Riesgo | ATR base SL / TP / trailing | 1.5 / 2.0 / 3.5 |
| Señales | Umbral de score, horario | 2.0, `0900-1600` |
| S/R | Pivot, distancia mínima, toques | 15, 50 ticks, 3 toques |

> 💡 **Recomendación:** empieza siempre con el modo `Conservative` y el timeframe de 1h para validar el comportamiento antes de probar otras configuraciones.

---

## 🚀 Instalación y uso

1. Abre **TradingView** y ve a **Pine Editor**.
2. Pega el contenido del archivo `signalfusion_scalpstation_sr_v2.1.pine`.
3. Pulsa **Añadir al gráfico**.
4. Revisa la pestaña **Strategy Tester** para ver resultados históricos.
5. Configura las alertas desde **Crear alerta** y elige una de las condiciones del script.

---

## 📊 Resultados de prueba

Pruebas realizadas con la configuración **Conservative**, horario `0000-2359` y filtros de volumen y ADX activos.

### Test 1h (desde enero hasta la fecha)

| Métrica | Valor |
|---|---|
| Periodo | Enero – octubre 2026 |
| Timeframe | 1h |
| Operaciones totales | ⏳ *completar* |
| Winrate | ⏳ *completar* |
| Profit Factor | ⏳ *completar* |
| Máximo drawdown | ⏳ *completar* |
| Beneficio neto | ⏳ *completar* |

**Muestra de las últimas operaciones registradas:**

| # | Tipo | Precio de entrada | Resultado aprox. |
|---|---|---|---|
| 1942 | Largo | 686.64 USDT | −1.43 USDT |
| 1941 | Corto | 687.44 USDT | −1.37 USDT |
| 1940 | Largo | 688.21 USDT | −1.34 USDT |
| 1939 | Corto | 689.03 USDT | −1.35 USDT |
| 1938 | Largo | 688.99 USDT | −1.34 USDT |
| 1937 | Corto | 690.25 USDT | −0.93 USDT |
| 1936 | Largo | 690.09 USDT | −1.22 USDT |

### Test 2h (desde enero de 2025)

| Métrica | Valor |
|---|---|
| Periodo | Enero 2025 – octubre 2026 |
| Timeframe | 2h |
| Operaciones totales | ⏳ *completar* |
| Winrate | ⏳ *completar* |
| Profit Factor | ⏳ *completar* |
| Máximo drawdown | ⏳ *completar* |
| Beneficio neto | ⏳ *completar* |

> ⚠️ Los resultados pasados no garantizan resultados futuros. Las pruebas no incluyen deslizamiento (*slippage*) real ni todas las condiciones de mercado.

---

## 📝 Changelog

**v2.1**
- Corrección de `max`/`min` por `math.max` / `math.min`.
- Sustitución de `ta.adx` por `ta.dmi` para calcular el ADX.
- Filtro ADX para señales en tendencia.
- Ajuste de pesos de scoring para mejorar el winrate.
- Aumento del multiplicador TP long para mejor relación riesgo/beneficio.

---

## ⚖️ Aviso legal

Este script es **material educativo y de investigación**. No constituye asesoramiento financiero. El trading con apalancamiento o en criptomonedas conlleva un alto riesgo de pérdida de capital. Úsalo bajo tu propia responsabilidad y prueba siempre en cuenta demo antes de operar con dinero real.

---

<p align="center">
  Hecho con ❤️ por <a href="https://github.com/jsoto-06">@jsoto-06</a>
</p>
