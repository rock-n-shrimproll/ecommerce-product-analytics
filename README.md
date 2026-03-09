# 🛒 E-commerce Product Analytics & A/B Testing with CUPED

> **Продуктовый анализ поведения пользователей интернет-магазина и оценка влияния новой версии checkout на конверсию через A/B тестирование с применением CUPED**

---

## 📌 Business Problem

В e-commerce значительная часть пользователей добавляет товары в корзину, но не завершает покупку. Этот проект исследует поведение пользователей крупного онлайн-магазина на датасете 9М+ событий, выявляет ключевые точки потери в воронке и оценивает эффективность упрощённой версии checkout через строго спроектированный A/B эксперимент.

**Ключевые вопросы:**
- На каком шаге воронки теряется наибольшая доля пользователей?
- Как отличается retention между когортами пользователей?
- Даёт ли упрощённый checkout статистически значимый рост конверсии?
- Насколько метод CUPED эффективнее классического t-теста для данной задачи?

---

## Основные выводы

> Раздел обновляется по мере завершения анализа

---

## Notebooks

Все ноутбуки разработаны в Google Colab. Данные хранятся на Google Drive и не включены в репозиторий.

| # | Ноутбук | Описание | Colab |
|---|---|---|---|
| 1 | `01_eda.ipynb` | Разведочный анализ 9М+ событий | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/YOUR_USERNAME/ecommerce-product-analytics/blob/main/notebooks/01_eda.ipynb) |
| 2 | `02_funnel_analysis.ipynb` | Воронка + продуктовые метрики | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/YOUR_USERNAME/ecommerce-product-analytics/blob/main/notebooks/02_funnel_analysis.ipynb) |
| 3 | `03_cohort_analysis.ipynb` | Когорты + retention D7/D30 | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/YOUR_USERNAME/ecommerce-product-analytics/blob/main/notebooks/03_cohort_analysis.ipynb) |
| 4 | `04_ab_test_design.ipynb` | Дизайн эксперимента + power analysis | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/YOUR_USERNAME/ecommerce-product-analytics/blob/main/notebooks/04_ab_test_design.ipynb) |
| 5 | `05_ab_test_cuped.ipynb` | CUPED + линеаризация + результаты | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/YOUR_USERNAME/ecommerce-product-analytics/blob/main/notebooks/05_ab_test_cuped.ipynb) |
| 6 | `06_spark_pipeline.ipynb` | PySpark pipeline обработки данных | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/YOUR_USERNAME/ecommerce-product-analytics/blob/main/notebooks/06_spark_pipeline.ipynb) |

---

## Структура проекта

```
ecommerce-product-analytics/          # GitHub репозиторий
│
├── README.md                          # документация проекта
├── .gitignore
│
├── notebooks/
│   ├── 01_eda.ipynb                   # разведочный анализ данных
│   ├── 02_funnel_analysis.ipynb       # воронка + продуктовые метрики
│   ├── 03_cohort_analysis.ipynb       # когортный анализ + retention
│   ├── 04_ab_test_design.ipynb        # дизайн эксперимента + power analysis
│   ├── 05_ab_test_cuped.ipynb         # CUPED + линеаризация + результаты
│   └── 06_spark_pipeline.ipynb        # PySpark pipeline
│
├── reports/
│   ├── figures/                       # графики, экспортированные из ноутбуков
│   │   ├── funnel_dropoff.png
│   │   ├── cohort_retention_heatmap.png
│   │   ├── ab_test_results.png
│   │   └── cuped_variance_comparison.png
│   └── final_report.md                # итоговый аналитический отчёт
│
└── dashboard/
    └── app.py                         # Plotly Dash дашборд (деплой на Render)


Google Drive/                          # данные хранятся здесь, не в GitHub
└── ecommerce-analytics/
    ├── data/
    │   ├── raw/
    │   │   └── 2019-Oct.csv           # исходный датасет с Kaggle
    │   └── processed/
    │       ├── df_clean.parquet       # очищенные данные
    │       ├── funnel_metrics.csv     # метрики воронки
    │       └── cohort_matrix.csv      # матрица retention
    └── notebooks/                     # рабочие копии ноутбуков в Colab
```

---

## Методология

### 1. Исследвоательский анализ данных
Анализ 9М+ событий: распределение типов событий, временные паттерны, топ категорий, выявление аномалий и пропусков.

### 2. Анализ воронки
Построение воронки `view → cart → purchase` с оконными SQL-функциями (LEAD/LAG для реконструкции сессий). Drill-down по категориям товаров и временным периодам. Chi-square тест для сравнения конверсии между сегментами.

```
product_view  →  add_to_cart  →  purchase
```

### 3. Когортный анализ и анализ удержания пользователей
Когорты по месяцу первого события. Метрики: retention D7/D30, repeat purchase rate. Выявление «опасных» недель жизненного цикла пользователя.

### 4. Дизайн A/B теста
- Гипотеза: упрощение UX checkout снижает cart abandonment rate
- Метрика успеха: конверсия `cart → purchase`
- Guardrail-метрики: AOV, session depth
- Стратифицированная рандомизация по исторической активности
- Power analysis: α = 0.05, power = 0.80, MDE = 5%

### 5. CUPED
Variance reduction через ковариату — покупательская активность пользователя за 14 дней до эксперимента. Линеаризация ratio-метрик. Сравнение мощности: CUPED vs классический t-test vs bootstrap.

$$Y_{cuped} = Y - \theta \cdot (X - \mathbb{E}[X])$$

где $\theta = \frac{Cov(Y, X)}{Var(X)}$

### 6. PySpark Pipeline
Обработка полного датасета в распределённой среде: ingestion, feature engineering, агрегация метрик воронки.

---

## Результаты

> Раздел обновляется по мере реализации проекта

---

## Дашборды

> Раздел обновляется по мере реализации проекта

---

## Выводы

> Раздел обновляется по мере реализации проекта

---