---
name: thesis-pipeline
description: "Navigation and state-awareness skill — call this FIRST if you are new to the plugin or unsure what to do next. Triggers on: 'zacznij', 'od czego zacząć?', 'gdzie jestem?', 'co powinienem teraz zrobić?', 'jaki jest następny krok?', 'jak wygląda plan pracy?', 'pokaż mi pipeline', 'chcę zrozumieć kolejność skillów', 'co mam już zrobione?', 'jak mam zorganizować pracę nad licencjatem?', 'skończyłem analizę co teraz?', 'mam listę problemów co dalej?', 'zrobiłem edycję co następne?', 'wróćmy do struktury', 'chcę spojrzeć na całość', 'nie wiem od czego zacząć'. Also triggers when the user provides thesis material without specifying which skill to call."
metadata:
  version: "1.0"
  pipeline_position: "0 — meta-skill; navigation and state awareness only; does not perform analysis, editing, or review"
---

# Thesis Pipeline

## Purpose

`thesis-pipeline` is the **map and compass** for the licencjat editing workflow. It does not do analysis, editing, or review. Each of the other skills does its own work and can be called directly at any time.

Call this skill when you want to:
- Understand what skills exist and what they do
- Find out what you should do next given what you've already completed
- Understand the dependencies between skills (what skill needs what output from another)
- Orient yourself after a long break or after switching between micro and macro work
- Get a personalised recommendation for where to focus

---

## The Pipeline Map

```
STAN 0 │ thesis-reference-calibrator
       │ Kalibruje standard jakości z materiałów referencyjnych (prace promotora,
       │ przykłady z wydziału). Wynik: rubryk kalibracyjny.
       │ Wymagane przez: wszystkie inne skille.
       │ Kiedy: raz, na początku. Jeśli nie masz rubryki, zacznij tutaj.

STAN 1 │ thesis-structure-decision
       │ Decyduje o optymalnej strukturze pracy (co zatrzymać, co wyciąć,
       │ jak ułożyć rozdziały). Wynik: rekomendacja opcji strukturalnej (A/B/C/D).
       │ Wymaga: rubryka (STAN 0).
       │ Kiedy: raz, zanim zaczniesz cokolwiek edytować lub analizować szczegółowo.

STAN 2 │ thesis-macro-auditor
       │ Ocenia architekturę pracy z lotu ptaka — czy sekcje pełnią swoją funkcję,
       │ czy argumentacja jest spójna na poziomie całości, co brakuje, co jest zbędne.
       │ Wejście: spis treści + pierwsze i ostatnie zdanie każdej sekcji.
       │ Wymaga: rubryka (STAN 0), decyzja struktury (STAN 1).
       │ Kiedy: przed analizą mikro — i ZAWSZE gdy chcesz wrócić do widoku całości,
       │ nawet po wykonaniu wielu sesji analizy szczegółowej.

STAN 3 │ thesis-analyzer
       │ Granularna analiza tekstu zdanie po zdaniu, akapit po akapicie.
       │ Iteracyjna: ~800–1000 słów na sesję. Wynik: lista problemów + blok handoff.
       │ Wymaga: rubryka + wyniki makro-audytu.
       │ Kiedy: po STAN 2; powtarzaj na każdy fragment pracy.

STAN 4 │ thesis-editor
       │ Jedyny skill który modyfikuje tekst. Implementuje zmiany na podstawie
       │ listy problemów z thesis-analyzer, thesis-macro-auditor lub thesis-reviewer.
       │ Wymaga: lista problemów z poprzedniego skilla diagnostycznego.
       │ Kiedy: po każdej sesji analizy (STAN 3) lub po makro-audycie (STAN 2).

STAN 5 │ thesis-reviewer
       │ Symuluje recenzję promotora WNE UW. Cztery tryby: Standard, Hostile,
       │ Viva (symulacja obrony), Bibliography.
       │ Wymaga: rubryka + ukończona edycja.
       │ Kiedy: gdy praca jest bliska gotowości do złożenia.

AD-HOC │ thesis-section-writer
       │ Pisze nową sekcję od zera (wstęp, wnioski, case study, rozdział metodologiczny).
       │ Wymaga: rubryka.
       │ Kiedy: na każdym etapie — gdy brakuje sekcji wymaganej przez WNE.
```

---

## How to determine your current state

Answer YES/NO to each question:

1. Czy masz **rubrykę kalibracyjną** z `thesis-reference-calibrator`? (Jeśli tak — powinna być zapisana jako `rubric.md` w folderze projektu; jeśli nie — zacznij od `thesis-reference-calibrator`)
2. Czy masz **decyzję strukturalną** (opcja A/B/C/D) z `thesis-structure-decision`?
3. Czy masz **raport makro-audytu** z `thesis-macro-auditor`?
4. Czy przeprowadziłeś przynajmniej jedną **sesję analizy mikro** (`thesis-analyzer`)?
5. Czy masz **changelog z edycji** (`thesis-editor`)?

| Ostatnie TAK | Stan | Co teraz |
|---|---|---|
| Żadne | — | Wywołaj `thesis-reference-calibrator` |
| 1 | STAN 1 | Wywołaj `thesis-structure-decision` |
| 2 | STAN 2 | Wywołaj `thesis-macro-auditor` |
| 3 | STAN 3 | Wywołaj `thesis-analyzer` (następny fragment) |
| 4 | STAN 4 | Wywołaj `thesis-editor` z listą problemów |
| 5 | STAN 5 | Wywołaj `thesis-reviewer` |

**Uwaga:** Tabela podaje *typowy* następny krok — nie ścisłą kolejność. Możesz zawsze wrócić do wcześniejszego stanu. W szczególności:
- Po wielu sesjach `thesis-analyzer` możesz wrócić do `thesis-macro-auditor` żeby sprawdzić czy praca jako całość się poprawiła
- `thesis-editor` można wywołać po każdej sesji analizy, nie dopiero po analizie wszystkich sekcji
- `thesis-section-writer` jest dostępny na każdym etapie

---

## When to call thesis-macro-auditor after micro-work

Wróć do `thesis-macro-auditor` gdy:
- Skończyłeś analizę i edycję kilku sekcji i chcesz zobaczyć czy zmiany poprawiły spójność całości
- Masz wątpliwości czy argumentacja na poziomie rozdziałów nadal trzyma się kupy po lokalnych zmianach
- Promotor zgłosił uwagi strukturalne, a nie tylko stylistyczne
- Nie byłeś w stanie dokończyć STAN 2 za pierwszym razem (np. brakowało materiału)

`thesis-macro-auditor` nie jest jednorazowy — jest dostępny na każdym etapie pracy.

---

## Dependency summary

| Skill | Co potrzebuje |
|---|---|
| `thesis-reference-calibrator` | Materiały referencyjne od użytkownika |
| `thesis-structure-decision` | Rubryka (STAN 0) |
| `thesis-macro-auditor` | Rubryka + opcja struktury |
| `thesis-analyzer` | Rubryka + raport makro-audytu + fragment tekstu (LaTeX source lub odczyt pliku przez Read) |
| `thesis-editor` | Lista problemów z dowolnego skilla diagnostycznego + fragment LaTeX source |
| `thesis-reviewer` | Rubryka + plik `.tex` (Claude czyta go bezpośrednio z zamontowanego folderu) |
| `thesis-section-writer` | Rubryka + opis potrzebnej sekcji |

---

## Output of this skill

Based on the user's answers to the state questions, provide:

1. **Aktualny stan** — "Jesteś na STAN [N]"
2. **Rekomendowany następny krok** — który skill wywołać i z czym
3. **Alternatywne opcje** — czy warto wrócić do makro-audytu, czy kontynuować mikro
4. **Ostrzeżenia o brakujących zależnościach** — np. "nie masz jeszcze rubryki (brak pliku `rubric.md` w folderze projektu) — bez niej thesis-analyzer będzie oceniał względem generycznego standardu, nie Twojego wydziału"

**Format pracy:** Plugin jest przygotowany do pracy z tezą w formacie **LaTeX** (plik `.tex`). Claude może bezpośrednio czytać i edytować plik `.tex` za pomocą narzędzi Read i Edit, jeśli folder projektu jest zamontowany. Jeśli praca jest nadal w formacie `.docx`, sekcje do analizy należy wkleić jako plain text — ale wyniki edycji będą zwracane jako LaTeX i wymagają ręcznej integracji z dokumentem Word.

---

## Session Log — plik historii sesji

Plugin zapisuje historię pracy w pliku `session-log.md` w folderze projektu. Jest to odpowiednik roboczego dziennika — pozwala zachować ciągłość między sesjami bez konieczności ręcznego przekazywania kontekstu.

**Format pliku session-log.md:**
```
# SESSION LOG — [Tytuł pracy]

## Jak używać tego pliku
Na początku każdej sesji pracy nad licencjatem: wczytaj ten plik razem z rubric.md.
Zawiera historię ustaleń ze wszystkich poprzednich etapów pipeline'u.

---

## STAN 0 — thesis-reference-calibrator ✅
Data: [data]
Wynik: Rubryka kalibracyjna zapisana w `rubric.md`
Kluczowe ustalenia:
- [punkt 1]
- [punkt 2]

---

## STAN 1 — thesis-structure-decision ✅
Data: [data]
Rekomendacja: Opcja [A/B/C/D] — [krótki opis]
Uzasadnienie: [1–2 zdania]
Brakujące elementy (do uzupełnienia):
1. [element]
2. [element]

---

## STAN 2 — thesis-macro-auditor ✅
Data: [data]
Sekcje z problemami: [lista]
Kluczowe problemy makro:
- M1: [opis]
- M2: [opis]

---

## STAN 3 — thesis-analyzer (sesja N)
Data: [data]
Przeanalizowany fragment: [sekcja, akapity P1–PN]
Problemy:
- C2: [opis]
- H1: [opis]
Następny fragment: [pierwsze słowa]

---

## STAN 4 — thesis-editor (sesja N)
Data: [data]
Edytowany fragment: [sekcja]
Zmiany: Zmiana #1 (C2), Zmiana #2 (E1) — zaakceptowane
Oczekujące: H3 — wymaga decyzji autora

---
```

**Zasady obsługi pliku przez Claude:**
1. **Załaduj** na początku każdej sesji: użyj Read tool, jeśli folder projektu jest zamontowany. Jeśli plik nie istnieje — stwórz go (Write tool) po pierwszej zakończonej sesji. Jeśli folder nie jest zamontowany — zapytaj użytkownika czy chce wkleić log ręcznie.
2. **Zapisz** po zakończeniu każdej sesji: użyj Edit tool (dopisz nową sekcję na końcu) lub Write tool (jeśli tworzysz po raz pierwszy). Nie nadpisuj poprzednich wpisów.
3. **Uwaga:** Każdy skill ma sekcję "Krok 0" która ładuje rubric.md i session-log.md. Jeśli oba pliki są dostępne, ładuj oba. Rubric.md to standard jakości — session-log.md to historia pracy.

Nie wykonuj żadnej analizy, edycji ani recenzji. Wskaż tylko właściwy skill i co do niego dostarczyć.

Language: Always respond in the same language the user writes in.