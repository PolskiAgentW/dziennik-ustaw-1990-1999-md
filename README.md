# Dziennik Ustaw 1990–1999 w Markdown

Teksty aktów z **Dziennika Ustaw** z lat 1990–1999, które API ELI Sejmu podaje tylko jako PDF, w Markdown i jako
drzewo jednostek w JSON, z metadanymi z API ELI. Tekst odczytał OCR (tesseract) ze skanów.
*Texts of Polish Journal of Laws acts of 1990–1999 that the Sejm ELI API serves only as PDF (scans), read by OCR
and converted to Markdown and a JSON tree of units (art./§/ust./pkt/lit.).*

> **Nieoficjalne.** Teksty powstają przez automatyczny OCR skanów, więc zawierają błędy odczytu.
> Wiążący jest PDF w Dzienniku Ustaw (link `source_pdf` w każdym pliku).

## Dlaczego

W latach 1990–1999 Dziennik Ustaw ma 8 441 aktów. API ELI Sejmu (`api.sejm.gov.pl/eli`) podaje tekst HTML dla
1 324 z nich. Pozostałe 7 117 są tylko w PDF (stan list API z 2026-10-05). Tutaj jest ich tekst. Nowsze lata:
[dziennik-ustaw-2000-2011-md](https://github.com/PolskiAgentW/dziennik-ustaw-2000-2011-md),
[dziennik-ustaw-md](https://github.com/PolskiAgentW/dziennik-ustaw-md) (od 2012 r., aktualizowany codziennie).

PDF-y z tych lat to zeskanowane strony całych zeszytów: dwa łamy, kilka aktów na jednej stronie. Większość ma
niewidoczną warstwę tekstu z OCR programu Adobe Acrobat (czcionka „HiddenHorzOCR”; w latach 1990–1992 ma ją 1290 z 1315 PDF-ów), część nie ma żadnej. Warstwa
Acrobata przestawia wyrazy między wierszami i ma własne błędy, więc konwerter
[eli2md](https://github.com/PolskiAgentW/eli2md) (od wersji 0.6.26) jej nie używa: czyta każdą stronę tesseractem,
układa wiersze w kolejności łamów, wycina akt spośród sąsiednich na tych samych stronach i rozpoznaje jednostki
(`§`, art.), załączniki i podpisy.

Na 60 losowych aktach z lat 1990–1999, które mają też oficjalny HTML (próba testowa, niewidziana przy pisaniu
konwertera), odsetek słów oficjalnego tekstu odczytanych we właściwej kolejności wynosi: z warstwy Acrobata
(eli2md 0.6.25) 0,664, z OCR eli2md 0.6.31 0,983
([pomiar](https://github.com/PolskiAgentW/eli2md/tree/main/eval/scans_1990_1999)).

## Stan

<!-- stats:start -->
Stan na 2026-10-05 04:41 UTC (liczone z `index.csv`, aktualizowane automatycznie).

| rok | aktów w indeksie | przekonwertowanych | błędów |
|---|---:|---:|---:|
| 1990 | 433 | 433 | 0 |
| 1991 | 421 | 421 | 0 |
| 1992 | 461 | 461 | 0 |
| 1993 | 572 | 572 | 0 |
| 1994 | 701 | 701 | 0 |

Akty ze stronami bez warstwy tekstowej (skany, grafiki): 2588, razem 9150 z 9359 stron. Tekst z OCR (oznaczony) ma 9090 z nich w 2588 aktach; treści pozostałych brak.
Akty ze stronami z dużymi obrazami (wzory, rysunki; ich treści brak): 8.

Rodzaje aktów: Rozporządzenie 2210, Oświadczenie rządowe 160, Umowa międzynarodowa 91, Uchwała 55, Konwencja 29, Protokół 11, Traktat 11, Zarządzenie 10, Układ 7, Porozumienie 3, Statut 1.
Wersje konwertera: eli2md 0.6.31 (2588).
<!-- stats:end -->

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
eli2md 0.6.31 (`eval/evaluate.py --ocr`, słowa bez wielkości liter i interpunkcji):

| próba | treść R | treść P | aktów z R < 0,90 | załączniki R |
|---|---:|---:|---:|---:|
| test, 60 aktów (niewidziana) | 0,983 | 0,974 | 6 | 0,960 (6 aktów) |
| dev, 40 aktów (na niej strojone) | 0,986 | 0,986 | 2 | 0,953 (6 aktów) |

R = odsetek słów oficjalnego tekstu odczytanych we właściwej kolejności, P = odsetek słów wyniku obecnych
w oficjalnym tekście. Dla porównania warstwa Acrobata (0.6.25) na tych samych aktach: R 0,664 i 0,670, P 0,568
i 0,616. Akty w tym zbiorze (bez HTML, w większości rozporządzenia) nie mają wzorca; zakładam, że wynik jest podobny,
ale tego nie zmierzyłem. Liczby i pliki: [README eli2md](https://github.com/PolskiAgentW/eli2md) (wpisy „0.6.26”–„0.6.31”)
i [eval/scans_1990_1999](https://github.com/PolskiAgentW/eli2md/tree/main/eval/scans_1990_1999).

## Znane usterki

- Błędy OCR: litery, sklejone wyrazy, „§” odczytany jako „8” albo „$”. Konwerter poprawia ze słownikiem pl_PL słowa,
  w których brakuje jednego albo dwóch znaków diakrytycznych albo „ł” odczytano jako „t”/„l” („ogtoszenia” →
  „ogłoszenia”), oddziela jednoliterowe przyimki („Wrozporządzeniu”) i poprawia „§” na początku akapitu i po
  przyimku („w § 1”); słowa, dla których poprawka nie jest jednoznaczna, zostają z błędem.
- Gdy OCR nie odczyta numeru pozycji następnego aktu jako osobnego akapitu przed jego rodzajem, akt ma na końcu
  początek następnego aktu z ostatniej wspólnej strony.
- Pierwszy akt zeszytu stoi na stronie ze spisem treści. Gdy jest krótki, do jego tekstu trafiają fragmenty spisu
  (numery stron) i kolejność akapitów bywa zła (DU/1999/728).
- Strony słabej jakości (przekreślenia, pieczęcie, ciemne tło) dają fragmenty bez sensu (DU/1990/390).
- Tabele są spłaszczone do akapitów; na stronach w dwóch łamach ich komórki mogą się przeplatać.
- Przypisy nie są rozpoznawane jako przypisy (zostają akapitami).

Błędy konwersji zgłaszaj w Issues. Najlepiej podaj pozycję aktu i fragment.

## Licencja

Akty normatywne i ich urzędowe projekty oraz urzędowe dokumenty i materiały nie są przedmiotem prawa
autorskiego (art. 4 pkt 1 i 2 ustawy o prawie autorskim i prawach pokrewnych). Pozostała zawartość
(indeks, skrypty): CC0 1.0.
