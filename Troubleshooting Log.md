(Note: Original documentation was created in Polish; screenshots reflect the Polish language version of Power BI/Power Query).
## [2026-01-22] Solving Decimal Separator Issue

**Problem:** A column containing decimal values was recognized as text because of the period (US format), which prevented calculations in Power BI (due to Polish regional settings expecting a comma).

**Solution:** Instead of a manual "find and replace" operation, I used the built-in Power Query feature: 
`Change Type` -> `Using Locale...` -> `English (United States)`.
<img width="1711" height="833" alt="image" src="https://github.com/user-attachments/assets/31521e2d-5bf5-4478-a97e-26f42296791c" />



**Kod M (Power Query):**
```powerquery
#"Zmieniono typ z ustawieniami regionalnymi" = Table.TransformColumnTypes(#"Changed Type", {{"Physical_Activity", type number}}, "en-US")
```
## [2026-01-30] Weryfikacja jakości danych (Data Profiling) w Power Query

**Problem:** Chciałam sprawdzić jakość danych (puste wartości błędy,duplikaty, rozkład danych, wartości ekstremalne)

**Rozwiązanie:** Zastosowanie narzędzi profilowania danych w Power Query (Zakładka **Widok**), aby szybko ocenić stan zbioru danych przed dalszą obróbką.

**Użyte funkcje:**
* **Jakość kolumn (Column Quality):** Pozwala szybko sprawdzić procent danych prawidłowych (Valid), błędów (Errors) oraz pustych (Empty).
* **Rozkład kolumn (Column Distribution):** Wyświetla wykres słupkowy pokazujący liczbę **unikatowych** (Unique) i **odrębnych** (Distinct)  wartości w kolumnie. (**Jeżeli Unique=Distinct <- nie ma dublikatów**)
* **Profil kolumn (Column Profile):** Wyświetla szczegółowe statystyki (Minimum, Maximum, Średnia, Odchylenie standardowe) oraz histogram dla zaznaczonej kolumny. <- tutaj można popatrzeć na **ekstrema** czy mają sens
### Kluczowe pojęcia (Data Profiling):
W sekcji "Rozkład kolumn" Power BI podaje dwie wartości, które łatwo pomylić:

| Pojęcie | Definicja | Przykład `[1, 1, 2, 3]` |
| :--- | :--- | :--- |
| **Odrębne (Distinct)** | Wszystkie różne wartości (każda liczona raz). | **3** (wartości: 1, 2, 3) |
| **Unikatowe (Unique)** | Wartości, które wystąpiły tylko jeden jedyny raz. | **2** (wartości: 2, 3) |

<img width="1491" height="987" alt="image" src="https://github.com/user-attachments/assets/9d41d2ba-27f3-4443-94b8-c10decf65f7b" />

## [2026-02-10] Załadowanie danych z Google BigQuery

**Problem** Podczas próby ściągnięcia danych z systemu Google BigQuery (Pobierz dane->Więcej...->Google BigQuery-> Połącz) pojawił się problem
<img width="975" height="609" alt="image" src="https://github.com/user-attachments/assets/91f7e60d-cd13-40e8-b74b-2d4f9cc4b0b2" />
**Próba 1** Zainstalowanie 64-bitowowego sterownika ODBC dla BigQuery.
    **Rezultat** Błąd dalej występował
**Próba 2** Wyłączenie Google BigQuery Storage API (Sugestia Google Gemini)
  Plik -> Opcje i ustawienia -> Opcje
<img width="1001" height="800" alt="image" src="https://github.com/user-attachments/assets/405c11a4-a1eb-42db-846d-02e76faed85e" />
<img width="998" height="793" alt="image" src="https://github.com/user-attachments/assets/45644cd1-fd58-40ef-a590-9df7fd121658" />

  **Rezultat** Problem zniknął, dane zostały załadowane

## [2026-03-16] Zwiększenie się liczby wartości odrębnych w tabeli po usunięciu dublikatów
**Problem**: Wzrost liczby wartości odrębnych (z 579 do 1000) po wykonaniu operacji usunięcia duplikatów w Power Query.

**Przyczyna**: Wynika to z domyślnego sposobu działania Edytora Power Query, który profiluje dane jedynie na podstawie pierwszych 1000 wierszy. Po usunięciu powtarzających się rekordów, struktura próbki uległa zmianie, co wpłynęło na wyświetlane statystyki.

**Rozwiązanie**: Aby uzyskać rzetelne informacje o całym zbiorze danych, należy zmienić tryb profilowania:

   1.Spójrz na pasek stanu w lewym dolnym rogu okna Power Query.

   2.Kliknij komunikat: `Profilowanie kolumn na podstawie 1000 pierwszych wierszy.`

   3.Zmień opcję na: `Profilowanie kolumn na podstawie całego zestawu danych.`

## [2026-03-16] Problem "płaskiej linii" na wykresach (Błędy relacji i miar)
**Problem**: Po stworzeniu miar DAX (np. `Total Profit`), wykresy z podziałem na kategorie wyświetlały tę samą wartość dla każdego punktu (linia prosta), mimo że dane źródłowe były zróżnicowane.

**Próba 1**: Weryfikacja relacji między tabelami
W widoku modelu sprawdzono istnienie relacji między tabelami wymiarów (Kategoria, Marka) a tabelą faktów oraz poprawność kierunku filtrowania.

   **Rozwiązanie:** Usunięcie i stworzenie nowych relacji, gdzie został ustawiony kierunek filtrowania na Pojedynczy (od wymiaru "1" do faktów "*") lub Oba. 

   **Rezultat** Problem nadal występował – wykresy nie reagowały na filtrowanie.

**Próba 2**: Weryfikacja kontekstu miar DAX
Analiza formuły wykazała błąd w odwołaniu do źródła danych.

**Błąd**: Miara `Total Profit = SUM('Oryginalna_Tabela'[zysk_razem])` odwoływała się do tabeli technicznej (źródłowej), która nie posiadała relacji z nowymi tabelami wymiarów.

**Rozwiązanie**: Aktualizacja kodu DAX, aby sumował kolumny bezpośrednio z nowej tabeli faktów. Poprawa: `Total Profit = SUM('Fakt_table'[zysk_razem])`


**Wnioski na przyszłość**:

   * **Kierunek filtrowania**: Zawsze sprawdzaj kierunek strzałek w Widoku Modelu – w schemacie gwiazdy (Star Schema) powinny one wskazywać stronę tabeli faktów.

   * **Zarządzanie tabelami**: Po zakończeniu transformacji w Power Query (tworzenie duplikatów/referencji), należy wyłączyć ładowanie tabel źródłowych lub je ukryć. Zapobiega to tworzeniu miar w błędnym kontekście i poprawia czytelność modelu.
   
   <img width="399" height="460" alt="image" src="https://github.com/user-attachments/assets/ef86575c-a211-4d4e-932b-d8b427cfab06" />

## [2026-03-30] Nie działająca relacje

**Problem**: Wykresy z podziałem na marki wyświetlały tę samą wartość dla każdego punktu (linia prosta), mimo że dane źródłowe były zróżnicowane, ale przy zastosowaniu innej tabeli połączonej relacją wartości się zmieniał.

**Próba 1**: Stworzenie od nowa relacji 
   **Rozwiązanie:** Usunięcie i stworzenie nowych relacji, gdzie został ustawiony kierunek filtrowania na Pojedynczy (od wymiaru "1" do faktów "*") lub Oba. 

   **Rezultat** Problem nadal występował – wykresy nie reagowały na filtrowanie.

**Próba 2**: Aktywacja relacji
   **Rozwiązanie:** Zaobserwowałam, że nie jest zaznaczona opcja "Aktywuj tę relację", więc została włączona
   <img width="529" height="854" alt="image" src="https://github.com/user-attachments/assets/a8665702-c513-4ee0-b1ec-f4ac9591590e" />


   **Rezultat** Problem przestał występować
