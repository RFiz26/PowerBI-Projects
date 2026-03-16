# Wprowadzenie. 
Postanowiłam zapisać tu wszystkie kroki, które wykonałam podczas stworzenia tego projektu. 

## 1. Przegląd zbioru danych: TheLook eCommerce, czyli Eksploracyjna Analiza Danych (EDA)

Projekt opiera się na publicznym zbiorze danych `thelook_ecommerce` w Google BigQuery. Jest to syntetyczny zbiór danych zawierający informacje o operacjach fikcyjnego sklepu odzieżowego online.

Baza danych składa się z 7 kluczowych tabel, które pozwalają na pełną analizę cyklu życia klienta oraz łańcucha dostaw:

| Nazwa tabeli | Opis zawartości | Kluczowe zastosowanie |
| :--- | :--- | :--- |
| **`users`** | Dane demograficzne klientów (imię, e-mail, wiek, płeć, lokalizacja GPS, miasto, kraj). | Analiza demograficzna i segmentacja klientów. |
| **`products`** | Katalog produktów zawierający nazwy marek, kategorie (np. Jeans, Suits), cenę detaliczną (`retail_price`) oraz koszt zakupu (`cost`). | Analiza asortymentu i obliczanie marży. |
| **`orders`** | Główne informacje o zamówieniach: status (Shipped, Returned, Cancelled), data utworzenia i liczba przypisanych przedmiotów. | Monitorowanie statusów realizacji i trendów sprzedaży w czasie. |
| **`order_items`** | Najbardziej szczegółowa tabela łącząca produkty z zamówieniami i użytkownikami. Zawiera ceny sprzedaży i daty zwrotów. | Obliczanie całkowitego przychodu, analiza zwrotów (Returns). |
| **`inventory_items`** | Stan magazynowy: informacje o tym, kiedy produkt trafił na stan i z którego centrum dystrybucji pochodzi. | Zarządzanie zapasami i analiza logistyczna. |
| **`events`** | Logi z aktywności na stronie (wejścia, kliknięcia, przeglądane produkty, dodanie do koszyka). | Analiza ścieżki zakupowej i współczynnika konwersji (UX/Marketing). |
| **`distribution_centers`** | Lista centrów logistycznych (nazwa oraz lokalizacja geograficzna). | Optymalizacja logistyki i dostaw. |

### Relacje między danymi
Wszystkie tabele są ze sobą powiązane za pomocą unikalnych identyfikatorów (kluczy):
* `user_id` łączy klientów z ich zamówieniami i aktywnością na stronie.
* `product_id` pozwala przypisać szczegóły produktu do konkretnej pozycji w koszyku.
* `order_id` grupuje wiele produktów w jedno zamówienie.

## 2. Cele biznesowe i pytania analityczne

Zanim przystąpiłam do analizy technicznej w SQL, zdefiniowałam główne cele biznesowe, które pomogą ocenić kondycję sklepu TheLook eCommerce. Moja analiza skupia się na trzech kluczowych obszarach:

### I. Rentowność i Asortyment (Profitability)
* **Cel:** Zidentyfikowanie najbardziej zyskownych kategorii produktów.
* **Pytanie biznesowe:** Które kategorie produktów generują największą marżę (różnica między ceną sprzedaży a kosztem zakupu)?
* **Kluczowe metryki:** Total Profit, Profit Margin per Category.

### II. Efektywność Marketingowa (Customer Acquisition)
* **Cel:** Ocena wartości klientów w zależności od źródła ich pozyskania.
* **Pytanie biznesowe:** Które źródło ruchu (`traffic_source`) sprowadza klientów o najwyższej średniej wartości zamówienia (AOV)?
* **Kluczowe metryki:** Average Order Value (AOV), User Count per Source.

### III. Optymalizacja Operacyjna (Returns Analysis)
* **Cel:** Zmniejszenie strat wynikających ze zwrotów towarów.
* **Pytanie biznesowe:** Które marki lub kategorie produktów charakteryzują się najwyższym współczynnikiem zwrotów?
* **Kluczowe metryki:** Return Rate (%), Total Returns.

## 3.I. Transformacja i Agregacja Danych (SQL)

### Notatnik techniczny: Analiza Rentowności
**Zadanie:** Obliczenie zysku i marży procentowej dla każdej kategorii i marki.
**Logika:** - Połączyłam tabele `products` i `order_items`.
* Wykluczyłam zamówienia anulowane i zwrócone, aby uzyskać realny obraz finansowy.
* Obliczyłam marżę jako `(Suma Cena - Suma Koszt) / Suma Cena *100%`.
* Wszystkie sumy są zaokrąglone do 2 miejsc po przecinku ROUND(...,2), ponieważ cena nie może być inna
* Sortujemy od najwyższego zysku, aby zobaczyć, jakiego rodzaju produkty przynoszą największą marże

```
SELECT 
  p.category AS kategoria,
  p.brand AS marka,
  ROUND(SUM(p.retail_price), 2) AS przychod_razem,
  ROUND(SUM(p.cost), 2) AS koszt_razem,
  ROUND(SUM(p.retail_price - p.cost), 2) AS zysk_razem,
  ROUND(SAFE_DIVIDE(SUM(p.retail_price - p.cost), SUM(p.retail_price)) * 100, 2) AS procent_marzy
FROM `bigquery-public-data.thelook_ecommerce.products` AS p
JOIN `bigquery-public-data.thelook_ecommerce.order_items` AS oi 
  ON p.id = oi.product_id
WHERE oi.status NOT IN ('Cancelled', 'Returned') -- Liczymy tylko faktyczną sprzedaż
GROUP BY 1, 2 -- (lub GROUP BY p.category, p.brand)
ORDER BY zysk_razem DESC
```
  
  **Wynik został zapisany jako widok (Save View), który można odczytać w Power Query**

Zamiast przesyłać miliony wierszy surowych danych do Power BI, wykonałam agregację po stronie BigQuery. Przygotowałam dedykowany widok SQL, który przelicza marżę na poziomie kategorii i marki, co optymalizuje czas odświeżania raportu.

## 4.I. Prezentacja danych w Power Bi
### Załadowanie danych do Power Query
Po połączeniu bazy BigQuery z Power BI Desktop zostały ściągnięte dane do Power Query. Następnie zostały sprawdzone dane za pomocą funkcji znajdujących się w zakładce Widok, co zostało przedstawione poniżej.  
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/0c9daf49-d34f-4d48-bb32-d82b7ab29c19" />
Na postawie tych danych można zauważyć pare rzeczy:
  *Nie zaobserwowano żadnych błędów, ani pustych kolumn.
  *W kategorii `przychod_razem`, `koszt_razem` i `zysk_razem` wartości minumalna i maksymalna jest większa od zera, co jako wartość pieniężna jest istotna.
  *W kolumnie `procent_marzy` wartości zawierają się w przedziale 36.9-65.7, co wygląda na prawidłowe dane.
  * `kategoria` zawiera w sobie 25 kategorii, które się powtarzają(25 wartości odrębnych, 0 unikatowych. Do zoptymaliwania analizy należy przenieść te dane do osobnej tablicy ( tablica wymiarów)
  *`marka` występuje znacznie więcej wartości odrębnych niż kolumna kategoria, do tego większa część tych danych występuje tylko raz ( 431 wartości unikatowych). Jednak z powodu tego, że jest to tekst(Power BI i Power Query używają silnika kolumnowego (VertiPaq). Ten silnik uwielbia liczby, a nie przepada za długimi tekstami.) należy przenieść te dane do tablicy wymiaru. Przez dużą różnice między danymi w kolumnie `kategoria` i `marka` zostaną stworzone dwie tabele wymiaru

Po stworzeniu tabel dla kategorii i marki, oraz odpowiednich id w tych tabelach i w tabeli faktu 
<img width="995" height="722" alt="image" src="https://github.com/user-attachments/assets/6003e363-83c6-464f-a14d-208aece233d2" />

