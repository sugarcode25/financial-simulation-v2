# Changelog

Todos los cambios relevantes de este proyecto se registran aquí.
El formato sigue [Keep a Changelog](https://keepachangelog.com/es-ES/1.1.0/) y las versiones siguen [versionado semántico](https://semver.org/lang/es/).

## \[2.0.0] · 2026-11-15

Revisión completa del taller. Cada punto se rehízo desde fuentes primarias, con controles en el código y con las limitaciones escritas. La v1 se conserva sin cambios en `v1/` y en el repositorio archivado [`financial-simulation-and-portfolio-optimization`](https://github.com/sugarcode25/financial-simulation-and-portfolio-optimization), donde `ISSUES.md` lista sus errores.

### Agregado

* Notebook único `notebooks/taller\_simulacion\_v2.ipynb`, con una sección por punto y celdas que se pueden ejecutar desde un entorno limpio de Colab.
* Datos contables de **SEC EDGAR**: XBRL y estados financieros oficiales del año fiscal 2024, tal como se radicaron originalmente. Se guardan en `data/raw/` con su fecha de descarga.
* Tablas de resultados en `outputs/` (CSV y gráficos), una por cada cifra que cita el README.
* Controles dentro del código:

  * Cuadre A − P − E = 0 en todas las empresas.
  * Identidad β = ρ × σ\_acción / σ\_mercado, que debe dar la misma volatilidad de mercado para todas las acciones.
  * P/B calculado de dos formas que deben coincidir.
  * Beta del portafolio por suma ponderada y por regresión, que deben coincidir.
* **Punto 1:** indicadores antes y después (ROA, ROE, margen neto, rotación de activos y pasivos/patrimonio), el Escenario D de compra 100% con deuda (extensión propia) y una comparación entre empresas con ingresos netos de intereses.
* **Punto 2:** las métricas de riesgo calculadas con precios descargados, la correlación con el VIX (definida), los fundamentales de la SEC al corte del 27-mar-2025, el P/E y el ROE de HOOD ajustados por un beneficio tributario no recurrente (10-K, nota "Income Taxes"), el P/B y la nota sobre pasivos/patrimonio en bancos y fintech.
* **Punto 3:** la tasa libre de riesgo del T-bill a 3 meses (FRED, DTB3), una prueba de ruido de muestreo con 1,000 repeticiones por tamaño, la solución exacta de máximo Sharpe y de mínima varianza (extensión propia), la frontera eficiente exacta y las betas del portafolio.
* **Punto 4:**

  * Líneas base ingenuas: último valor, estacional y deriva.
  * Evaluación fuera de muestra de GLD y medida de la fuerza de su estacionalidad.
  * Intervalo de predicción del 90%.
  * Prueba ADF.
  * Separación entrenamiento / validación / prueba del LSTM con 3 semillas.
  * Verificación ex post de los pronósticos, con datos que no se usaron para elegir ningún modelo.
* `README.md` organizado por punto: "Qué pedía el curso", "Qué hice", "Resultados", "Lectura", "Qué corregí respecto a la v1" y "Limitaciones".

### Cambiado

* **Fuente de los datos:** de Yahoo Finance en vivo, sin copia, a SEC EDGAR para la contabilidad y a Yahoo Finance, Tiingo y FRED para los precios, con fecha de corte fija y fecha de descarga registrada. Los precios no se redistribuyen, por las licencias de los proveedores.
* **Benchmark:** SPY como aproximación al S\&P 500.
* **X (U.S. Steel)**, que dejó de cotizar en junio de 2025, se descarga de Tiingo. La clave de la API se guarda en los *Secrets* de Colab, no en el código.
* **Pesos aleatorios del Monte Carlo:** Dirichlet(1,1,1) en lugar de `random` normalizado.
* **GLD:** precio de cierre de mes, solo meses completos, en lugar de promedios mensuales con un mes incompleto.
* **Idioma:** README en español con resumen en inglés.

### Corregido

* **Punto 1:**

  * El "+31.7% de patrimonio" comparaba el patrimonio total proyectado contra el patrimonio **común** de Citigroup. El cambio real es +20%, y es un efecto mecánico del supuesto del curso.
  * Se comparaban niveles en lugar de indicadores.
  * Se mezclaban periodos: C anual y NU de un solo trimestre.
  * La fuente citada, Financial Modeling Prep, no había aportado datos ("Invalid API KEY").
* **Punto 2:**

  * Beta, volatilidades, correlación y "VIX" estaban escritas a mano. La relación con el VIX es negativa, no positiva.
  * El script publicado analizaba HIMS y el informe analizaba C.
  * Los P/E del texto y de la tabla no coincidían.
  * La deuda/patrimonio de C no tenía fuente.
* **Punto 3:**

  * **Error de indexación:** los pesos reportados no correspondían al portafolio de máximo Sharpe. Con ellos se cae también la recomendación de "reducir la concentración en IBM".
  * Se leía como tendencia lo que era ruido de muestreo.
  * Sharpe con rf = 0% sin declararlo, y sin semillas.
  * La mínima varianza "al 13–15%" nunca se calculó: es 17.97%.
  * La beta del portafolio usaba los pesos equivocados, y la "validación cruzada" de la beta era un error de fórmula (`ddof` mezclado).
* **Punto 4:**

  * Fuga de información: el LSTM se elegía con el conjunto de prueba.
  * No había línea base: con ella, ningún LSTM le gana a "mañana = hoy".
  * El *early stopping* vigilaba la pérdida de entrenamiento.
  * La proyección a 30 días salía de un modelo distinto al reportado y usaba días calendario.
  * La "credibilidad del 90%" venía de la autoevaluación del curso.
  * El SES con α = 0.2 se presentaba como línea base.
* **Reproducibilidad:**

  * El README describía archivos que no existían.
  * El `.py` no se podía ejecutar fuera de Colab.
  * Los resultados dependían del día de ejecución.

### Eliminado

* La columna "DCF Valuation", que era un número aleatorio, y la conclusión que se sacaba de ella.
* El "puntaje de recompensa" calculado con `fake\_rmse` y `fake\_mae`.
* La regresión `Return \~ Close` con Breusch-Pagan, Durbin-Watson y VIF. Se conserva la prueba ADF.
* Afirmaciones sin fuente ("los Transformers logran RMSE similares") y justificaciones escritas después de los resultados.
* El encuadre de "pipeline de extremo a extremo" para un taller académico.

## \[1.0.0] · 2025-03-28

Entrega original del taller de Simulación Financiera (Universidad de Manizales): informe en PDF y script de Colab. Se conserva sin cambios en `v1/`. Sus errores conocidos están en el `ISSUES.md` del repositorio archivado.

