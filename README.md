# ☔ Прогноз опадів на основі Open-Meteo + Machine Learning

## Опис проєкту

Цей проєкт реалізує міні-сервіс прогнозування опадів на основі історичних метеоданих з **Open-Meteo API** та моделей машинного навчання.

Користувач може:

* завантажити історичні погодні дані для будь-якої локації;
* навчити ML-модель для класифікації опадів (так/ні);
* отримати прогноз опадів на кілька днів вперед;
* переглянути ймовірність опадів та візуалізацію результатів.

Проєкт реалізовано у вигляді **Streamlit-застосунку**.

---

## 🎯 Мета проєкту

Побудувати повний ML-пайплайн для задачі класифікації:

> **Чи будуть опади у конкретний день?**

Цільова змінна:

* `0` — опадів немає
* `1` — опади є

Формується на основі:

```python
precipitation_sum > 0
```

---

## ⚙️ Основний функціонал

### Завантаження даних (Open-Meteo API)

* Підтримка:

  * введення міста (через Geopy)
  * введення координат
* Дані отримуються через:

  * Historical API (архів)
  * Forecast API (прогноз)
* Використовуються **щоденні (daily) дані**

Використані змінні:
* "temperature_2m_max",
* "temperature_2m_min",
* "temperature_2m_mean",
* "apparent_temperature_max", 
* "apparent_temperature_min",
* "wind_speed_10m_max", 
* "wind_gusts_10m_max", 
* "wind_direction_10m_dominant",
* "shortwave_radiation_sum", 
* "sunshine_duration", 
* "daylight_duration",
* "et0_fao_evapotranspiration", 
* "precipitation_sum" 
---

### Підготовка даних

* Обробка пропусків:

  * інтерполяція + forward/back fill
* Feature Engineering:

  * `month`
  * `day_of_year`
* Видалення витоку даних:

  * `precipitation_sum` НЕ використовується як ознака

---

### ML-пайплайн

#### Розбиття даних

Використовується **Time Series підхід**:

* 80% — train
* 20% — test (без shuffle)
* TimeSeriesSplit для крос-валідації

#### Моделі

* Random Forest
* Logistic Regression

#### Pipeline включає:

* Imputation (заповнення пропусків)
* Scaling (тільки для Logistic Regression)
* Feature Selection (SelectFromModel)
* Класифікатор

#### ⚠️ Важливо:

Scaling не використовується для Random Forest (оскільки це дерево рішень)

---

### Оцінка моделей

Використовуються метрики:

* Accuracy
* Precision
* Recall
* F1-score (основна)
* ROC-AUC

Також реалізовано:

* Confusion Matrix (матриця помилок)
* Порівняння CV vs Test

---

### Відбір ознак (Feature Selection)

* Використовується SelectFromModel
* Аналіз важливості ознак:

  * для RF → feature_importances_
  * для Logistic → коефіцієнти

---

### Прогнозування

* Дані отримуються з **Forecast API**
* Прогноз робиться для кожного дня окремо

Результат:

* Клас:

  * 🌧️ ТАК
  * ☀️ НІ
* Ймовірність опадів (%)

---

### Візуалізація

Реалізовано:

* Таблиця прогнозу
* Графік:

  * ймовірність опадів (bar chart)
  * температура (line chart)

---

## Інтерфейс (Streamlit)

Додаток має 3 вкладки:

### 🔴 1. Завантаження даних

* вибір локації
* вибір періоду
* перегляд і скачування CSV

### 🔴 2. Навчання моделі

* запуск ML-пайплайну
* метрики моделей
* confusion matrix
* feature importance

### 🔴 3. Прогноз

* вибір кількості днів
* таблиця результатів
* графік прогнозу

---

## Як запустити?

### 1. Клонувати репозиторій

```bash
git clone https://github.com/your-username/weather-ml-app.git
cd weather-ml-app
```

### 2. Встановити залежності

```bash
pip install -r requirements.txt
```

### 3. Запустити Streamlit

```bash
streamlit run app.py
```

---

## 📦 Залежності

Основні бібліотеки:

* streamlit
* pandas
* numpy
* requests
* geopy
* scikit-learn
* plotly

---

## Приклад результату

Модель вміє:

* передбачати опади на кілька днів вперед
* оцінювати ймовірність
* враховувати погодні фактори

Типовий результат:

```
2026-05-03 → 🌧️ ТАК (57%)
2026-05-04 → ☀️ НІ (43%)
...
```

---
