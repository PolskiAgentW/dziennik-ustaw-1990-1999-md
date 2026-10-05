# Dziennik Ustaw 1990–1999 w Markdown

Teksty aktów z **Dziennika Ustaw** z lat 1990–1999, które API ELI Sejmu podaje tylko jako PDF, w Markdown i jako
drzewo jednostek w JSON, z metadanymi z API ELI. Tekst odczytał OCR (tesseract) ze skanów.
*Texts of Polish Journal of Laws acts of 1990–1999 that the Sejm ELI API serves only as PDF (scans), read by OCR
and converted to Markdown and a JSON tree of units (art./§/ust./pkt/lit.).*

> **Nieoficjalne.** Teksty powstają przez automatyczny OCR skanów, więc zawierają błędy odczytu.
> Wiążący jest PDF w Dzienniku Ustaw (link `source_pdf` w każdym pliku).

<!-- zbiory:start -->
**Wszystkie zbiory** (ten sam format plików, konwerter [eli2md](https://github.com/PolskiAgentW/eli2md)). Akty, które API ELI
podaje w HTML (np. większość Dziennika Ustaw 2012–2024), nie są tu powielane.

| lata | Dziennik Ustaw | Monitor Polski |
|---|---|---|
| od 2012 | [GitHub](https://github.com/PolskiAgentW/dziennik-ustaw-md) · [HF](https://huggingface.co/datasets/PolskiAgentW/dziennik-ustaw-md): od 2025 r. wszystkie, wcześniej 98 aktów bez HTML; codziennie | [GitHub](https://github.com/PolskiAgentW/monitor-polski-md) · [HF](https://huggingface.co/datasets/PolskiAgentW/monitor-polski-md): wszystkie z PDF (API nie ma HTML); codziennie |
| 2000–2011 | [GitHub](https://github.com/PolskiAgentW/dziennik-ustaw-2000-2011-md) · [HF](https://huggingface.co/datasets/PolskiAgentW/dziennik-ustaw-2000-2011-md): akty bez HTML w API | [GitHub](https://github.com/PolskiAgentW/monitor-polski-2000-2011-md) · [HF](https://huggingface.co/datasets/PolskiAgentW/monitor-polski-2000-2011-md): wszystkie z PDF |
| 1990–1999 | [GitHub](https://github.com/PolskiAgentW/dziennik-ustaw-1990-1999-md) · [HF](https://huggingface.co/datasets/PolskiAgentW/dziennik-ustaw-1990-1999-md): akty bez HTML w API (OCR skanów) | brak |

Kolumny są we wszystkich zbiorach te same, więc lata można wczytać razem (nadal bez aktów, które API ELI podaje w HTML):

```python
from datasets import load_dataset

du = load_dataset("parquet", split="train", data_files=[
    "hf://datasets/PolskiAgentW/dziennik-ustaw-1990-1999-md/data/*.parquet",
    "hf://datasets/PolskiAgentW/dziennik-ustaw-2000-2011-md/data/*.parquet",
    "hf://datasets/PolskiAgentW/dziennik-ustaw-md/data/*.parquet",
])  # 30 018 aktów (2026-10-05); Monitor Polski: monitor-polski-2000-2011-md + monitor-polski-md
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

<!-- stats:start -->
Stan na 2026-10-05 10:32 UTC (liczone z `index.csv`, aktualizowane automatycznie).

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

Akty ze stronami bez warstwy tekstowej (skany, grafiki): 7117, razem 28650 z 29140 stron. Tekst z OCR (oznaczony) ma 28318 z nich w 7117 aktach; treści pozostałych brak.
Akty ze stronami z dużymi obrazami (wzory, rysunki; ich treści brak): 46.

Rodzaje aktów: Rozporządzenie 6303, Oświadczenie rządowe 362, Umowa międzynarodowa 182, Uchwała 91, Konwencja 87, Protokół 24, Traktat 20, Porozumienie 20, Zarządzenie 15, Układ 8, Statut 3, Ustawa 1, Postanowienie 1.
Wersje konwertera: eli2md 0.6.37 (7117).
<!-- stats:end -->

## Akty obowiązujące

Według API ELI (pole `status`, odczyt z 2026-10-05) status „obowiązujący” ma 692 z 7 117 aktów tego zbioru. Status
jest w front matter każdego pliku (`status_pl`) i w kolumnie `legal_status` w Parquet na Hugging Face (`index.csv`
go nie ma). Wybór w klonie repozytorium: `grep -l '^status_pl: "obowiązujący"' DU/*/*.md`. Status nie jest
aktualizowany po konwersji. Teksty są w brzmieniu ogłoszonym w Dzienniku Ustaw, bez późniejszych zmian (tekst
jednolity, jeśli go ogłoszono, jest osobną pozycją Dziennika Ustaw).

## Zmiana 2026-10-05

Dodane lata 1998 (1 126 aktów) i 1999 (1 127). Lata 1990–1997 przeliczone eli2md 0.6.37 (było 0.6.34). Porównanie
słów każdego aktu z poprzednią wersją: w 37 aktach doszedł tekst (razem 3 334 słowa; usuniętych słów najwyżej
1/10 dodanych), m.in. prawa kolumna ostatniego aktu zeszytu, którą 0.6.34 obcinało razem z kolofonem (DU/1996/84:
sentencja uchwały Trybunału Konstytucyjnego). W 16 aktach 25 nagłówków „Załącznik…” jest teraz zwykłym akapitem, tekst bez zmian:
załączniki umów międzynarodowych ogłaszanych w akcie, załącznik cytowany w akcie zmieniającym, załącznik innego aktu
z tych samych stron oraz DU/1995/229 (zob. „Znane usterki”). Innych zmian słów nie ma.

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

## Znane usterki

- Błędy OCR: litery, sklejone wyrazy, „§” odczytany jako „8” albo „$”. Konwerter poprawia ze słownikiem pl_PL słowa,
  w których brakuje jednego albo dwóch znaków diakrytycznych albo „ł” odczytano jako „t”/„l” („ogtoszenia” →
  „ogłoszenia”), oddziela jednoliterowe przyimki („Wrozporządzeniu”) i poprawia „§” na początku akapitu i po
  przyimku („w § 1”); słowa, dla których poprawka nie jest jednoznaczna, zostają z błędem.
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
- Umowy międzynarodowe, konwencje i podobne akty numerują jednostki „Artykuł N” w osobnym wierszu. Konwerter ich nie
  rozpoznaje: zostają akapitami, a w JSON nie ma węzłów `art`. Dotyczy 297 aktów (wiersz „Artykuł N”, żadnego nagłówka
  artykułu), w tym 250 z 692 obowiązujących (stan 2026-10-05).
- Strony słabej jakości (przekreślenia, pieczęcie, ciemne tło) dają fragmenty bez sensu (DU/1990/390).
- Tabele są spłaszczone do akapitów; na stronach w dwóch łamach ich komórki mogą się przeplatać.
- Przypisy nie są rozpoznawane jako przypisy (zostają akapitami).

Błędy konwersji zgłaszaj w Issues. Najlepiej podaj pozycję aktu i fragment.

## Licencja

Akty normatywne i ich urzędowe projekty oraz urzędowe dokumenty i materiały nie są przedmiotem prawa
autorskiego (art. 4 pkt 1 i 2 ustawy o prawie autorskim i prawach pokrewnych). Pozostała zawartość
(indeks, skrypty): CC0 1.0.
