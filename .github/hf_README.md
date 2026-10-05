---
language:
- pl
license: cc0-1.0
pretty_name: Dziennik Ustaw 1990–1999 (akty bez HTML w API ELI) w Markdown/JSON, OCR skanów
size_categories:
- 1K<n<10K
tags:
- legal
- law
- poland
- ocr
configs:
- config_name: default
  data_files:
  - split: train
    path: data/*.parquet
---

# Dziennik Ustaw 1990–1999 — teksty aktów, których API ELI nie ma w HTML

Nieoficjalne teksty aktów z Dziennika Ustaw z lat 1990–1999, które API ELI Sejmu podaje tylko jako PDF (7 117
z 8 441 aktów tych lat), odczytane przez OCR (tesseract) ze skanów i przekonwertowane otwartym konwerterem
[eli2md](https://github.com/PolskiAgentW/eli2md) (od 0.6.26).

*Unofficial plain-text (Markdown) and structured (JSON tree of units) versions of the acts of the Polish Journal of
Laws (Dziennik Ustaw) of 1990–1999 that the Sejm ELI API serves only as scanned PDF, read by OCR. The PDF is the
binding text.*

**Dlaczego:** PDF-y z tych lat to skany całych zeszytów (dwa łamy, kilka aktów na stronie) z niewidoczną warstwą
OCR Adobe Acrobata, która przestawia wyrazy między wierszami. Szczegóły i pomiar:
[repozytorium na GitHubie](https://github.com/PolskiAgentW/dziennik-ustaw-1990-1999-md#dlaczego).

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

## Użycie

```python
from datasets import load_dataset
import json

ds = load_dataset("PolskiAgentW/dziennik-ustaw-1990-1999-md", split="train")
print(ds[0]["eli"], ds[0]["title"])
tree = json.loads(ds[0]["tree"])  # drzewo jednostek
obow = ds.filter(lambda r: r["legal_status"] == "obowiązujący")  # 692 akty (stan 2026-10-05)
```

Status „obowiązujący” wg API ELI (odczyt z 2026-10-05) ma 692 z 7 117 aktów. Teksty są w brzmieniu ogłoszonym,
bez późniejszych zmian.

## Kolumny

Jeden wiersz = jeden akt.
- `eli` (np. `DU/1997/78`), `year`, `pos`, `type`, `title`, `display_address`, `announcement_date`, `promulgation`,
  `entry_into_force`, `legal_status`, `keywords`, `change_date`, `source_pdf`, `pdf_sha256`: metadane z API ELI
  (bez poprawek, więc z jego błędami; `legal_status` — stan w chwili konwersji);
- `pages`, `words`, `no_text_pages`, `image_pages`, `ocr_pages`, `image_ocr_pages`: strony PDF, słowa wyniku,
  strony bez użytecznej warstwy tekstowej (skany), strony z dużymi obrazami, strony odczytane przez OCR, strony,
  na których OCR odczytał obraz tekstu;
- `markdown`: tekst aktu (przed tekstem każdej strony notka, że odczytał go OCR);
- `tree`: ten sam akt jako drzewo jednostek w JSON (tekst; opis formatu w
  [README eli2md](https://github.com/PolskiAgentW/eli2md#json-drzewo-jednostek-od-053));
- `converter`, `converted_at`: wersja eli2md i czas konwersji.

## Jakość

Wzorcem są akty z lat 1990–1999, które mają HTML (głównie ustawy). Na 60 losowych takich aktach (próba testowa,
eli2md 0.6.37, wynik jak w 0.6.31–0.6.36) odsetek słów oficjalnego tekstu odczytanych we właściwej kolejności wynosi 0,983, a odsetek słów wyniku
obecnych w oficjalnym tekście 0,974 (warstwa tekstowa Acrobata w tych PDF-ach: 0,664 i 0,568). Akty w tym zbiorze
(bez HTML, głównie rozporządzenia) nie mają wzorca. Typowe błędy: „ł” odczytane jako „t”, sklejone wyrazy, „§” jako
„8”, fragmenty spisu treści w pierwszym akcie zeszytu, tabele spłaszczone do akapitów, przypisy jako zwykłe akapity,
fragment stopki zeszytu („Egzemplarze bieżące…”) w tekście 122 aktów; w umowach międzynarodowych i konwencjach
jednostki „Artykuł N” nie są rozpoznawane (297 aktów, w tym 250 z 692 obowiązujących).
W 21 aktach brakuje początku (numeru, tytułu, pierwszych przepisów): ich PDF w API ELI zaczyna się później,
a początek jest tylko w PDF poprzedniej pozycji (lista w README na GitHubie, „Znane usterki”; może nie być pełna).
Szczegóły: [README na GitHubie](https://github.com/PolskiAgentW/dziennik-ustaw-1990-1999-md#jak-dobre-jest).

**To nie jest urzędowy tekst.** Wiążący jest PDF w Dzienniku Ustaw (`source_pdf`). Błędy konwersji zgłaszaj
w [Issues na GitHubie](https://github.com/PolskiAgentW/dziennik-ustaw-1990-1999-md/issues).

## Źródło i licencja

Źródło: [API ELI Sejmu](https://api.sejm.gov.pl/eli/acts/DU). Ten sam zbiór jako pliki `.md`/`.json`:
[github.com/PolskiAgentW/dziennik-ustaw-1990-1999-md](https://github.com/PolskiAgentW/dziennik-ustaw-1990-1999-md).
Lata 2000–2011: [PolskiAgentW/dziennik-ustaw-2000-2011-md](https://huggingface.co/datasets/PolskiAgentW/dziennik-ustaw-2000-2011-md).
Akty normatywne i urzędowe dokumenty nie są przedmiotem prawa autorskiego (art. 4 pkt 1 i 2 ustawy o prawie
autorskim i prawach pokrewnych); pozostała zawartość: CC0 1.0.
