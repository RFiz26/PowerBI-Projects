# Wprowadzenie. 
Postanowiłam zapisać tu wszystkie kroki, które wykonałam podczas stworzenia tego projektu. 

## 1. Przegląd zbioru danych: TheLook eCommerce

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
