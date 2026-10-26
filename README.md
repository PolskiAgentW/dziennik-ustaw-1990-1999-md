# Dziennik Ustaw 1990–1999 w Markdown

Teksty aktów z **Dziennika Ustaw** z lat 1990–1999, które API ELI Sejmu podaje tylko jako PDF, w Markdown i jako
drzewo jednostek w JSON, z metadanymi z API ELI. Tekst odczytał OCR (tesseract) ze skanów.
*Texts of Polish Journal of Laws acts of 1990–1999 that the Sejm ELI API serves only as PDF (scans), read by OCR
and converted to Markdown and a JSON tree of units (art./§/ust./pkt/lit.).*

> **Nieoficjalne.** Teksty powstają przez automatyczny OCR skanów, więc zawierają błędy odczytu.
> Wiążący jest PDF w Dzienniku Ustaw (link `source_pdf` w każdym pliku).

<!-- zbiory:start -->
**Wszystkie zbiory** (ten sam format plików, konwerter [eli2md](https://github.com/PolskiAgentW/eli2md)). Akty, które API ELI
podaje w HTML (np. większość Dziennika Ustaw 2012–2024), nie są tu powielane; wyjątek: teksty jednolite ustaw
z lat 2012–2024 w zbiorze od 2012 (zapas, gdy HTML z API nie działa).

| lata | Dziennik Ustaw | Monitor Polski |
|---|---|---|
| od 2012 | [GitHub](https://github.com/PolskiAgentW/dziennik-ustaw-md) · [HF](https://huggingface.co/datasets/PolskiAgentW/dziennik-ustaw-md): od 2025 r. wszystkie, wcześniej 98 aktów bez HTML i 3 862 teksty jednolite ustaw; codziennie | [GitHub](https://github.com/PolskiAgentW/monitor-polski-md) · [HF](https://huggingface.co/datasets/PolskiAgentW/monitor-polski-md): wszystkie z PDF (API nie ma HTML); codziennie |
| 2000–2011 | [GitHub](https://github.com/PolskiAgentW/dziennik-ustaw-2000-2011-md) · [HF](https://huggingface.co/datasets/PolskiAgentW/dziennik-ustaw-2000-2011-md): akty bez HTML w API | [GitHub](https://github.com/PolskiAgentW/monitor-polski-2000-2011-md) · [HF](https://huggingface.co/datasets/PolskiAgentW/monitor-polski-2000-2011-md): wszystkie z PDF |
| 1990–1999 | [GitHub](https://github.com/PolskiAgentW/dziennik-ustaw-1990-1999-md) · [HF](https://huggingface.co/datasets/PolskiAgentW/dziennik-ustaw-1990-1999-md): akty bez HTML w API (OCR skanów) | brak |
| 1918–1989 | [GitHub](https://github.com/PolskiAgentW/dziennik-ustaw-1918-1989-md) · [HF](https://huggingface.co/datasets/PolskiAgentW/dziennik-ustaw-1918-1989-md): akty bez HTML w API (OCR skanów; pomiar jakości w README) | brak |

Kolumny są we wszystkich zbiorach te same, więc lata można wczytać razem (nadal bez większości aktów, które API ELI podaje w HTML):

```python
from datasets import load_dataset

du = load_dataset("parquet", split="train", data_files=[
    "hf://datasets/PolskiAgentW/dziennik-ustaw-1918-1989-md/data/*.parquet",
    "hf://datasets/PolskiAgentW/dziennik-ustaw-1990-1999-md/data/*.parquet",
    "hf://datasets/PolskiAgentW/dziennik-ustaw-2000-2011-md/data/*.parquet",
    "hf://datasets/PolskiAgentW/dziennik-ustaw-md/data/*.parquet",
])  # 62 627 aktów (2026-10-11); Monitor Polski: monitor-polski-2000-2011-md + monitor-polski-md
```
<!-- zbiory:end -->

## Dlaczego

W latach 1990–1999 Dziennik Ustaw ma 8 441 aktów. API ELI Sejmu (`api.sejm.gov.pl/eli`) podaje tekst HTML dla
1 324 z nich. Pozostałe 7 117 są tylko w PDF (stan list API z 2026-10-05). Tutaj jest ich tekst. Nowsze lata:
[dziennik-ustaw-2000-2011-md](https://github.com/PolskiAgentW/dziennik-ustaw-2000-2011-md),
[dziennik-ustaw-md](https://github.com/PolskiAgentW/dziennik-ustaw-md) (od 2012 r., aktualizowany codziennie).

PDF-y z tych lat to zeskanowane strony całych zeszytów: dwa łamy, kilka aktów na jednej stronie. Większość ma
niewidoczną warstwę tekstu z OCR programu Adobe Acrobat (czcionka „HiddenHorzOCR”; w latach 1990–1999 ma ją 6 817 z 7 117 PDF-ów tego zbioru, wg `pdffonts`), część nie ma żadnej. Warstwa
Acrobata przestawia wyrazy między wierszami i ma własne błędy, więc konwerter
[eli2md](https://github.com/PolskiAgentW/eli2md) (od wersji 0.6.26) jej nie używa: czyta każdą stronę tesseractem,
układa wiersze w kolejności łamów, wycina akt spośród sąsiednich na tych samych stronach i rozpoznaje jednostki
(`§`, art.), załączniki i podpisy.

Na 60 losowych aktach z lat 1990–1999, które mają też oficjalny HTML (próba testowa, niewidziana przy pisaniu
konwertera), odsetek słów oficjalnego tekstu odczytanych we właściwej kolejności wynosi: z warstwy Acrobata
(eli2md 0.6.25) 0,664, z OCR eli2md 0.6.37 0,983
([pomiar](https://github.com/PolskiAgentW/eli2md/tree/main/eval/scans_1990_1999)).

## Stan

Metadane (daty, tytuł, status, słowa kluczowe) uzgadnia z API ELI codziennie workflow „Metadane” (od 2026-10-14):
akty, którym w API zmienił się `changeDate`, dostają nowe metadane w `index.csv`, front matter, linii `# tytuł`
i `title` w `.json`; tekst zostaje z konwersji. Potem workflow „Eksport” odświeża plik w wydaniu „dane” i na HF.

<!-- stats:start -->
Stan na 2026-10-26 00:20 UTC (liczone z `index.csv`, aktualizowane automatycznie).

| rok | aktów w indeksie | przekonwertowanych | błędów |
|---|---:|---:|---:|
| 1990 | 433 | 433 | 0 |
| 1991 | 421 | 421 | 0 |
| 1992 | 461 | 461 | 0 |
| 1993 | 572 | 572 | 0 |
| 1994 | 701 | 701 | 0 |
| 1995 | 687 | 687 | 0 |
| 1996 | 674 | 674 | 0 |
| 1997 | 915 | 915 | 0 |
| 1998 | 1126 | 1126 | 0 |
| 1999 | 1127 | 1127 | 0 |

Akty ze stronami bez warstwy tekstowej (skany, grafiki): 7117, razem 28654 z 29140 stron. Tekst z OCR (oznaczony) ma 28322 z nich w 7117 aktach; treści pozostałych brak.
Akty ze stronami z dużymi obrazami (wzory, rysunki; ich treści brak): 46.

Rodzaje aktów: Rozporządzenie 6303, Oświadczenie rządowe 362, Umowa międzynarodowa 182, Uchwała 91, Konwencja 87, Protokół 24, Traktat 20, Porozumienie 20, Zarządzenie 15, Układ 8, Statut 3, Ustawa 1, Postanowienie 1.
Wersje konwertera: eli2md 0.6.37 (5298), eli2md 0.6.38 (1557), eli2md 0.6.66 (174), eli2md 0.6.39 (28), eli2md 0.6.65 (26), eli2md 0.6.62 (25), eli2md 0.6.63 (9).
<!-- stats:end -->

## Akty obowiązujące

Według API ELI (pole `status`, odczyt z 2026-10-05) status „obowiązujący” ma 692 z 7 117 aktów tego zbioru. Status
jest w front matter każdego pliku (`status_pl`) i w kolumnie `legal_status` w Parquet na Hugging Face (`index.csv`
go nie ma). Wybór w klonie repozytorium: `grep -l '^status_pl: "obowiązujący"' DU/*/*.md`. Status nie jest
aktualizowany po konwersji. Teksty są w brzmieniu ogłoszonym w Dzienniku Ustaw, bez późniejszych zmian (tekst
jednolity, jeśli go ogłoszono, jest osobną pozycją Dziennika Ustaw).

## Zmiana 2026-10-10

Drzewo JSON (kod drzewa eli2md 0.6.50, 6 aktów; opis w README eli2md, wpis 0.6.50): nagłówki z liczebnikiem słownym
(„DZIAŁ PIĄTY”, „CZĘŚĆ PIERWSZA”), z numerem rzymskim z wielką literą („DZIAŁ IVA”) albo z odnośnikiem po numerze
(„Rozdział 5a[^28]”) są w `.json` węzłami `heading`. Wcześniej trafiały jako tekst do artykułu, paragrafu albo punktu
przed nimi. Nowych nagłówków: 22, żaden nie zniknął. `.md` się nie zmienił, więc pole `converter` (w `.md`, `.json` i
`index.csv`) zostaje wersją, w której powstał `.md`. Liczba jednostek bez zmian, słowa w drzewach: zgubione 0.
Pozostałe akty bez zmian (porównanie drzew wszystkich aktów zbioru).

## Zmiana 2026-10-07

eli2md 0.6.39 (opis w [README eli2md](https://github.com/PolskiAgentW/eli2md), wpis „0.6.39”). Zmienione pliki 321 aktów:
- 30 aktów przeliczonych z PDF (`converter`: eli2md 0.6.39). Pozycje list 50–59 i 80–89 nie są już nagłówkami
  „§ 0.”–„§ 9.” (DU/1995/495: znów „80. Malezja,”). Innych zmian tekstu nie ma: po sprowadzeniu „§ N.” i numerów list
  do jednej postaci każdy z 30 plików jest taki sam jak przed zmianą (słowa: zgubione 0, nowe 0). Wśród nich 6 aktów
  spoza listy z 2026-10-06 (1993/362, 1994/138, 1994/175, 1995/630, 1999/880, 1999/973).
- 292 umowy międzynarodowe i podobne akty: w JSON „Artykuł N” jest węzłem `art` z polem `label`. W 291 z nich `.md`
  się nie zmienił, więc pole `converter` (w `.md`, `.json` i `index.csv`) zostaje wersją, w której powstał `.md`;
  `.json` zbudowano z niego kodem drzewa 0.6.39. Słowa w drzewach: zgubione 0.
- Pozostałe akty bez zmian (porównanie 0.6.38 i 0.6.39 na wszystkich 7 117 aktach zbioru).

## Zmiana 2026-10-05 (wieczór)

Lata 1995 i 1997 przeliczone eli2md 0.6.38. Dwa akty miały w 0.6.37 tylko tytuł i podpis: DU/1995/44 i DU/1997/6.
Teraz mają pełny tekst (868 i 1 696 słów). Porównanie 0.6.37 i 0.6.38 na wszystkich 7 117 aktach zbioru: inne akty
się nie zmieniły. Pozostałe lata zostają w 0.6.37, bo wynik 0.6.38 jest dla nich taki sam.

## Zmiana 2026-10-05

Dodane lata 1998 (1 126 aktów) i 1999 (1 127). Lata 1990–1997 przeliczone eli2md 0.6.37 (było 0.6.34). Porównanie
słów każdego aktu z poprzednią wersją: w 37 aktach doszedł tekst (razem 3 334 słowa; usuniętych słów najwyżej
1/10 dodanych), m.in. prawa kolumna ostatniego aktu zeszytu, którą 0.6.34 obcinało razem z kolofonem (DU/1996/84:
sentencja uchwały Trybunału Konstytucyjnego). W 16 aktach 25 nagłówków „Załącznik…” jest teraz zwykłym akapitem, tekst bez zmian:
załączniki umów międzynarodowych ogłaszanych w akcie, załącznik cytowany w akcie zmieniającym, załącznik innego aktu
z tych samych stron oraz DU/1995/229 (zob. „Znane usterki”). Innych zmian słów nie ma.

## Zmiana 2026-10-25

eli2md 0.6.62, 33 akty (opis w README eli2md, wpis 0.6.62): nagłówek strony numeru („Dziennik Ustaw Nr 41 — 598 —
Poz. 240”) zostawał w tekście aktu, gdy nie był pierwszą linią strony (strona z obróconą tabelą, strona czytana przez
OCR). Przeliczyłem ponownie 45 aktów, w których był taki nagłówek (53 wystąpienia). Nowy plik zostaje tylko wtedy, gdy
nagłówków jest mniej, a reszta tekstu (bez nagłówków, notek, białych znaków i ogonków) różni się od poprzedniej najwyżej
o 0,5% znaków. Tak jest w 33 aktach (w 29 z nich reszta tekstu ma te same znaki). W 32 z nich nagłówka już nie ma;
w DU/1990/539 został jeden (OCR czyta przed nim znak szumu). Bez zmian zostało 12 aktów. W 3 (DU/1997/137, 989,
DU/1998/244) nagłówka w nowym pliku nie było, ale reszta tekstu różniła się o więcej niż 0,5%. W 9 nagłówek zostawał
także w nowym pliku, bo OCR czyta przed nim 1–2 znaki szumu („- „Dziennik Ustaw Nr 8 — 124 — Poz. 37 i 38”):
DU/1990/240, DU/1992/471, DU/1993/65, DU/1994/292, 568, 610, DU/1995/37, DU/1996/81, DU/1997/483.

Tego samego dnia eli2md 0.6.63, 10 aktów (opis w README eli2md, wpis 0.6.63): wiersz OCR z 1–2 znakami szumu przed
nagłówkiem też jest pomijany. Przeliczyłem ponownie te 9 aktów i DU/1990/539. We wszystkich 10 nagłówka już nie ma,
a reszta tekstu ma te same znaki. W DU/1995/37 i DU/1996/81 notatka o stronie czytanej przez OCR stoi teraz pod
nagłówkiem załącznika, który zaczyna tę stronę. W zbiorze zostały 3 nagłówki (DU/1997/137, 989, DU/1998/244, opis wyżej).

Tego samego dnia eli2md 0.6.65, 26 aktów (opis w README eli2md, wpis 0.6.65): na stronach czytanych przez OCR
nagłówek numeru odczytany bez numeru strony i pozycji („Dziennik Ustaw Nr 65”) i linia drukarni z górnego pasa strony
nie trafiają już do tekstu. Przeliczyłem ponownie (z eli2md 0.6.37 i 0.6.38) 29 aktów, w których taki nagłówek był
osobnym akapitem na górze strony z OCR. Reguła jak wyżej (nagłówków i notek mniej, reszta tekstu różni się najwyżej
o 0,5% znaków). Nowy plik zostaje w 26 aktach (w 20 z nich reszta tekstu ma te same znaki); uciętych nagłówków było
w nich 29, nie został żaden. Bez zmian zostały 3: w DU/1998/462 i 555 nowy plik ma tyle samo nagłówków, w DU/1997/137
reszta tekstu różniła się o 3% (opis wyżej).

Tego samego dnia eli2md 0.6.66, 174 akty: ucięty nagłówek numeru był też w aktach spoza tej listy — w środku strony
(OCR czyta wiersze w innej kolejności), doklejony do akapitu albo usuwany dopiero później, więc lista wybierana po
pierwszych akapitach strony go nie obejmowała. Przeliczyłem ponownie (głównie z eli2md 0.6.37 i 0.6.38) 235 aktów,
w których po nazwie dziennika jest numer bez „poz.” (to wyklucza cytaty). Reguła jak wyżej. Nowy plik zostaje w 174
aktach (w 144 z nich reszta tekstu ma te same znaki); uciętych nagłówków było w nich 226, zostało 14. Bez zmian zostało
61: w 54 nowy plik ma tyle samo nagłówków (OCR czyta je z szumem albo to nie nagłówek), w 7 reszta tekstu różniła się
o więcej niż 0,5% (DU/1996/453, DU/1997/67, DU/1998/1, 13, 28, 61, DU/1999/47).

## Zmiana 2026-10-26

Bilans nagłówków „§” po przeliczeniach z 2026-10-25: jedyny akt tego zbioru, w którym nagłówki „§” różniły się od
wersji sprzed 2026-10-25, DU/1999/323, wraca do tamtej wersji: „§ 1.” przestał być nagłówkiem (doklejony do
poprzedniego akapitu), choć „§ 2.” odczytane przez OCR jako „8 2.” stało się nagłówkiem. Wraca też nagłówek strony,
który przeliczenie z 2026-10-25 usunęło.

Tego samego dnia drzewo JSON (kod drzewa eli2md 0.6.70, 2 akty; opis w README eli2md, wpis 0.6.70): nagłówki z
liczebnikiem słownym pisanym małymi literami, stojące jako osobny akapit („Rozdział pierwszy”, „Część druga”), są w `.json`
węzłami `heading` (DU/1991/308, DU/1995/534; 13 nowych nagłówków). Wcześniej trafiały jako tekst do artykułu przed nimi.
`.md` się nie zmienił, więc pole `converter` (w `.md`, `.json` i `index.csv`) zostaje wersją, w której powstał `.md`. Liczba jednostek i słowa w drzewie bez zmian.

## Zawartość

- `DU/<rok>/DU-<rok>-<pozycja>.md`: jeden akt. Front matter YAML z metadanymi ELI, potem tekst:
  `##### Art. N.` (albo `##### § N.`), akapity, `## Załącznik …`. Przed tekstem każdej strony stoi notka
  `> [Strona N PDF jest skanem. Tekst poniżej odczytał OCR (tesseract …), a nie warstwa tekstowa PDF. …]`.
- `DU/<rok>/DU-<rok>-<pozycja>.json`: ten sam akt jako drzewo jednostek (`art`, `par` (§), `ust`, `pkt`, `lit`,
  `tir`) z numerem, ścieżką (`art_5/ust_2/pkt_3`), tekstem i dziećmi. Opis:
  [README eli2md](https://github.com/PolskiAgentW/eli2md#json-drzewo-jednostek-od-053).
- Cały zbiór w jednym pliku: `dziennik-ustaw-1990-1999-md.jsonl.gz` w wydaniu
  [„dane”](https://github.com/PolskiAgentW/dziennik-ustaw-1990-1999-md/releases/tag/dane) (jeden akt w wierszu)
  i Parquet na Hugging Face: [PolskiAgentW/dziennik-ustaw-1990-1999-md](https://huggingface.co/datasets/PolskiAgentW/dziennik-ustaw-1990-1999-md).
- `index.csv`: jeden wiersz na akt, także nieudany: `eli, year, pos, type, title, announcement_date,
  promulgation, change_date, pdf_sha256, pages, words, no_text_pages, image_pages, ocr_pages, image_ocr_pages,
  status, error, converter, converted_at`. Strony skanów liczą się w `no_text_pages` i `ocr_pages`.

## Jak dobre jest

Wzorcem są akty z lat 1990–1999, które mają HTML w API ELI (głównie ustawy, obwieszczenia i orzeczenia). Wynik
eli2md 0.6.37 (na tych próbach taki sam jak 0.6.31, 0.6.34 i 0.6.36; `eval/evaluate.py --ocr`, słowa bez wielkości liter i interpunkcji):

| próba | treść R | treść P | aktów z R < 0,90 | załączniki R |
|---|---:|---:|---:|---:|
| test, 60 aktów (niewidziana) | 0,983 | 0,974 | 6 | 0,960 (6 aktów) |
| dev, 40 aktów (na niej strojone) | 0,986 | 0,986 | 2 | 0,953 (6 aktów) |

R = odsetek słów oficjalnego tekstu odczytanych we właściwej kolejności, P = odsetek słów wyniku obecnych
w oficjalnym tekście. Dla porównania warstwa Acrobata (0.6.25) na tych samych aktach: R 0,664 i 0,670, P 0,568
i 0,616. Akty w tym zbiorze (bez HTML, w większości rozporządzenia) nie mają wzorca; zakładam, że wynik jest podobny,
ale tego nie zmierzyłem. Liczby i pliki: [README eli2md](https://github.com/PolskiAgentW/eli2md) (wpisy „0.6.26”–„0.6.37”)
i [eval/scans_1990_1999](https://github.com/PolskiAgentW/eli2md/tree/main/eval/scans_1990_1999).

**Kontrola wzrokowa** (2026-10-05, eli2md 0.6.37, strona PDF obok wyniku; 5 aktów wylosowanych spośród 194
obowiązujących z lat 1998–1999, ziarno 5401; w 11-stronicowej umowie pierwsza i ostatnia strona):
- bez uwag poza pojedynczymi znakami, 3: DU/1998/456 (§ 1–15 i podpis; „,rejestrem””, „RE- GON”), DU/1999/268
  („Nr90”), DU/1998/617 („8 1.” zamiast „§ 1.”, więc § 1 nie jest nagłówkiem);
- DU/1999/1069: tekst kompletny z załącznikiem; w zmienianych przepisach „§” odczytany jako „8”, „38”, „82.'”,
  numer „III.” jako „.”;
- DU/1999/890 (umowa z Islandią): obie strony kompletne; „Artykuł N” jest akapitem (niżej), blok podpisów stron
  przemieszany.

We wszystkich 5 akt jest wycięty bez tekstu sąsiednich pozycji (DU/1999/1069: poz. 1070; DU/1999/890: poz. 891, 892)
i łamy są czytane we właściwej kolejności. Próba jest mała: odsetka błędnych aktów na tej podstawie nie da się ocenić.

**Losowa próbka 32 aktów** (2026-10-08, pliki z tego repozytorium; 3 akty z każdego roku 1990–1997 i 4 z lat
1998–1999; pierwsze 100 słów i pierwsza strona porównane z obrazem strony):
- poprawnych słów w pierwszych 100: mediana 99%, co najmniej 90% w 29 z 32 aktów;
- usterka na pierwszej stronie w 4 z 32 aktów, czyli w zbiorze prawdopodobnie 5–28% aktów (95% przedział):
  przeplatane łamy (DU/1993/592, DU/1997/248), fragmenty tabeli przed § 1 (DU/1995/790) albo koniec poprzedniego
  aktu przed początkiem (DU/1994/208).

Metoda, próbka i oceny per akt:
[eval/scans_1990_1999/probka_2026-10-08](https://github.com/PolskiAgentW/eli2md/tree/main/eval/scans_1990_1999/probka_2026-10-08).

## Znane usterki

- Błędy OCR: litery, sklejone wyrazy, „§” odczytany jako „8” albo „$”. Konwerter poprawia ze słownikiem pl_PL słowa,
  w których brakuje jednego albo dwóch znaków diakrytycznych albo „ł” odczytano jako „t”/„l” („ogtoszenia” →
  „ogłoszenia”), oddziela jednoliterowe przyimki („Wrozporządzeniu”) i poprawia „§” na początku akapitu i po
  przyimku („w § 1”); słowa, dla których poprawka nie jest jednoznaczna, zostają z błędem.
- Na stronach z OCR numer pozycji listy od 50 do 59 albo od 80 do 89 bywa zamieniony na „§ 0.”–„§ 9.”: konwerter
  bierze sklejoną cyfrę „8” albo „5” za „§” (w JSON węzeł `par`; tekst pozycji zostaje, ginie pierwsza cyfra numeru).
  Wykrywacz z 2026-10-06 (pozycja listy N, a zaraz po niej „§ d.”, gdzie „8d” albo „5d” to N + 1) znalazł 241 takich
  wierszy w 25 aktach. Od eli2md 0.6.39 (2026-10-07) pozycja zostaje pozycją listy, jeśli lista ciągnie się na tej
  samej stronie. Zostało 13 wierszy w 2 aktach: DU/1998/7 i DU/1998/1158 (lista przechodzi na następną stronę).
  Wykrywacz może nie znajdować wszystkich.
- Akt może zawierać fragment sąsiedniego aktu z tych samych stron, gdy OCR nie odczyta numeru pozycji, a podpis
  ani nagłówek z datą nie wyznaczą granicy (np. dwa akty tego samego organu z tego samego dnia o prawie tym samym
  temacie, DU/1993/299). Miara zgrubna: akty z co najmniej dwoma nagłówkami rodzaju aktu wersalikami (część to akty
  poprawne, np. z aktem w załączniku). W wersji 0.6.31 było ich 60 (lata 1990–1998), w 0.6.34 z tych 60 zostało 10
  ([lista i skrypt](https://github.com/PolskiAgentW/eli2md/tree/main/eval/scans_1990_1999)).
- Pierwszy akt zeszytu stoi na stronie ze spisem treści. Gdy jest krótki, do jego tekstu trafiają fragmenty spisu
  (numery stron) i kolejność akapitów bywa zła (DU/1999/728).
- Nagłówki załączników, które OCR odczytał przed podpisem aktu, są od 0.6.36 zwykłymi akapitami, nie `## Załącznik`
  (tekst zostaje). Dotyczy to głównie załączników umów międzynarodowych ogłaszanych w akcie, ale też DU/1995/229,
  gdzie OCR ułożył strony w złej kolejności.
- W tekście 122 aktów jest fragment stopki zeszytu („Egzemplarze bieżące i z lat ubiegłych…”, adres sprzedaży),
  nie zawsze na końcu.
- Umowy międzynarodowe, konwencje i podobne akty numerują jednostki „Artykuł N” w osobnym wierszu. W `.md` zostają
  akapitami. W JSON od eli2md 0.6.39 (2026-10-07) są węzłami `art` (292 akty). Ograniczenia: wersje obcojęzyczne
  („Article N”) trafiają pod ostatni polski artykuł, a „Artykuł” zniekształcony przez OCR („Artykuf 23”) nie jest
  artykułem, więc numeracja ma luki (69 aktów).
- PDF części aktów w API ELI nie zawiera ich pierwszych stron: zaczyna się później, a początek aktu (numer, tytuł,
  pierwsze przepisy) jest tylko w PDF poprzedniej pozycji. W zbiorze te akty nie mają początku. Sprawdzone po numerach
  stron w nagłówkach (strona, na której akt się zaczyna, jest wcześniejsza niż pierwsza strona jego PDF): 21 aktów
  (1990/233, 1990/289, 1993/303, 1993/316, 1993/371, 1994/353, 1994/606, 1994/622, 1994/629, 1994/696, 1994/697, 1994/765, 1994/787, 1996/158, 1996/161, 1996/165, 1997/145, 1997/1018, 1998/139, 1998/260, 1999/1161). Np. DU/1998/139 zaczyna się na str. 851, jego PDF na str. 852;
  DU/1994/697 zaczyna się na str. 2430, a jego PDF to tylko str. 2481. Lista może nie być pełna (stan 2026-10-05).
- Strony słabej jakości (przekreślenia, pieczęcie, ciemne tło) dają fragmenty bez sensu (DU/1990/390).
- Tabele są spłaszczone do akapitów; na stronach w dwóch łamach ich komórki mogą się przeplatać.
- Przypisy nie są rozpoznawane jako przypisy (zostają akapitami).

Błędy konwersji zgłaszaj w Issues. Najlepiej podaj pozycję aktu i fragment.

## Licencja

Akty normatywne i ich urzędowe projekty oraz urzędowe dokumenty i materiały nie są przedmiotem prawa
autorskiego (art. 4 pkt 1 i 2 ustawy o prawie autorskim i prawach pokrewnych). Pozostała zawartość
(indeks, skrypty): CC0 1.0.
