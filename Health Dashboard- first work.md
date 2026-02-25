# Health Dashboard

## Business Problem
Analiza wpływu czynników psychicznych (stress, mindfulness, smoking) na stan zdrowia.

## 🗂 Dane i model
Dane pochodzą z serwisu Kaggle (Holistic Health & Lifestyle Score Dataset). Tabela zawierała następujące kolumny:
-Physical_Activity	
-Nutrition_Score	
-Stress_Level	
-Mindfulness	
-Sleep_Hours	
-Hydration	
-BMI	
-Alcohol	
-Smoking
-Overakk_Health_Score
-Health_Status

Spośród wszystkich czynników wybrałam **BMI** i **Smoking** — ponieważ są to czynniki, które w opinii publicznej mają największy wpływ na zdrowie — oraz **Mindfulness**, który według analizy danych miał największy wpływ na stan zdrowia.

## Kluczowe metryki
- Average Health Score: 78,23  
- % Poor Health Cases: 4,18%

## Wizualizacje
- Trend poziomu stresu względem stanu zdrowia  
- Rozkład mindfulness i poziomu BMI według stanu zdrowia  
- Wpływ palenia na zdrowie (scatterplot + trendline)  
- Porównanie kluczowych różnic między dobrym a złym stanem zdrowia

## Wnioski
- Wyższe wyniki mental well-being korelują z lepszym stanem zdrowia we wszystkich kategoriach BMI  
- Osoby ze złym stanem zdrowia mają wyższy poziom stresu i niższy mindfulness

![Dashboard](images/Health-first%20dashboard.png)

