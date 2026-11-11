# Predicción de demanda eléctrica horaria (PJM East)

Predicción a 24 horas del consumo eléctrico de la región PJM East (PJME) comparando
un baseline, Prophet y LightGBM sobre datos horarios de 2002 a 2018.

## Metodología

- **Limpieza:** orden temporal, eliminación de duplicados e interpolación de las horas que faltan.
- **Features:** calendario (hora, día de la semana, mes, festivos federales de EE. UU.),
  lags de 24, 48 y 168 horas y media/desviación móvil de 24 h (desplazadas para evitar fuga de datos).
- **Split temporal:** entrenamiento hasta 2017-01-01 y test posterior.
- **Modelos:**
  - Baseline: consumo de la misma hora una semana antes.
  - Prophet con los lags de 24 y 168 h como regresores.
  - LightGBM, validado con `TimeSeriesSplit` (5 folds).

## Resultados (MAE en el conjunto de test)

| Modelo | MAE (MW) |
|---|---|
| Baseline (lag 168 h) | 3511 |
| Prophet | 1975 |
| LightGBM | 1871 |

La validación cruzada temporal de LightGBM da un MAE medio de 1600 ± 40 MW.

## Cómo ejecutarlo

```bash
git clone https://github.com/llorenccobos/pjm-energy-forecast.git
cd pjm-energy-forecast
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

Descarga el CSV como se indica en [`data/README.md`](data/README.md) y abre
`pjm_hourly_forecast.ipynb`.

## Posibles mejoras

- Añadir variables meteorológicas (temperatura), el principal motor de la demanda.
- Ajuste de hiperparámetros y early stopping.
- Métricas adicionales (RMSE, MAPE) y análisis del error por hora y estación.
