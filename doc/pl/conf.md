*Uwaga! Dokument w czasie opracowywania!*

# Kodi Repo Builder – Konfiguracja

W wielu miejscach można stosować zarówno obiekty jak i listy obiektów. 
Ma to na celu skrypcie zapisu (w tym redukcję zagnieżdżenia), gdy jest tylko jeden element na liście.

```yaml
addon:
    -   name: "First repo"
        contains: ...
    -   name: "Second repo"
        contains: ...

addon:
    name: "Single repo"
    contains: ...
```


## Addon

Więcej szczegółów na stronie https://kodi.wiki/view/Addon.xml

Konfiguracja dodatku musi się zawierać w tagu `addon`.
Dla repozytoriów zamiennie można (ale nie trzeba) użyć tagu `repo`.

Dostępne pola:

* `id` – identyfikator repozytorium,
* `name` – nazwa repozytorium, jeśli brak to zostanie wzięta ostatnia sekcja z pola `id`,
* `metadata` – opis, patrz [matadane](#metadane).
* `requires` – opis, patrz [zalezności](#requires).

Opisać dodatek można jeden ze sposobów:
* `matadata` i `requires` – zmienne opisujące repozytorium,
* `addon.xml` – bezpośrednia zawartość pliku `addon.xml`,
* `addon-path` – ścieżka do pliku `addon.xml` (względem głównego pliku yaml).

Dodatkowo można dodać plik licencji (jedno z poniższych):
* `license.txt` – bezpośrednia zawartość pliku `LICENSE.txt`,
* `license-path` – ścieżka do pliku `LICENSE.txt` (względem głównego pliku yaml).


## Repo

Więcej szczegółów na stronie https://kodi.wiki/view/Add-on_repositories

Repozytorium jest także dodatkiem, obowiązują go pola opisanej wyżej sekcji [Addon](#addon).
Dodatkowo `id` musi się zaczynać od `repository.` oraz posiadać pola:

* `repo` – dane repozytorium:
    * `info` – link do pliku `addons.xml`, z którego będzie korzystać Kodi,
    * `datadir` – link do katalogu z paczkami, z którego Kodi będzie je pobierać,
* `contains` – lista paczek wchodzących w skład tego repozytorium.

Kodi definiuje strukturę paczek ZIP:
```
repo.datadir link/
+ addon1_id/
| + addon1_id-ver1.zip
| + addon1_id-ver2.zip
+ addon2_id/
  + addon2_id-ver1.zip
```

Przykład:

```yaml
id:   "repository.cherrytv"
name: "[I][COLOR=blue]CHERRY[/COLOR] [COLOR=red]TV[/COLOR][/I] Repository"
metadata: ...
repo:
    info:    "https://raw.githubusercontent.com/CherryKodi/CHERRY/master/addons.xml"
    datadir: "https://github.com/CherryKodi/CHERRY/raw/master/zips"
contains: ...
```


## Contains

Zawartość repozytorium może być określana na wiele sposobów.
Wiele dodatków można budować z jednego źródła, jeden dodatek z wielu źródeł, 
pobierać gotowe paczki, wybierać tylko niektóre foldery, ~~albo mieszać powyższe~~.

Są dwa podstawowe sposoby definicji:
* dodatek z listą wielu źródeł:
```yaml
addon:
  id: ...
  source:
    - git: ...
      folder: ...
    - git: ...
```
* źródło, z którego budowanych jest wiele dodatków:
```yaml
git: ...
list:
  - addon: ...
  - addon: ...
```

Źródło może też wskazywać na gotowy dodatek zarówno w postaci źródłowej
jak i binarnej ZIP.

### Dodatek

Podstawowym sposobem jest definicja dodatku jak opisano w [Addon](#addon) (`id`, `name`...).
Dodatkowo należy zdefiniować `source` z listą źródeł. Przy jednym źródle
można dane zawrzeć wprost (bez listy) ~~albo wręcz bezpośrednio w dodatku~~.

Dodatek w repo (czyli w sekcji `contains`) może się składać z:
* `addon` – albo ID zdefiniowanego dodatku albo pełnoprawny obiekt dodatku (patrz [Addon](#addon)), 
* *źródło* – jedno z opisanych niżej źródeł wraz z obowiązkową zawartością:
    * `list` – lista dodatków, które można z podanego źródła zbudować

Przykład:

```yaml
id: 'script.module.js2py'
source:
  - git: 'https://github.com/PiotrDabkowski/Js2Py.git'
    folder: js2py
```

```yaml
id: 'script.module.js2py'
source:
  git: 'https://github.com/PiotrDabkowski/Js2Py.git'
  folder: js2py
```

Poniższy przykład jest wadliwy!
```
id: 'script.module.js2py'
git: 'https://github.com/PiotrDabkowski/Js2Py.git'
folder: js2py
```


### Źródła

#### git

Dane są pobierane z repozytorium git, które jest klonowane (git clone) lub synchronizowane (git pull).

#### url

Dane są pobierane bezpośrednio z podanego linku. 
Link może wskazywać na lokalny plik (np. w ramach tego samego projektu na github).

#### build

Wartość `build` wskazuje na inny plik yaml, który należy zbudować.
Może to być plik kub URL.




## Metadane

Metadane są opisane szczegółowo na https://kodi.wiki/view/Addon.xml#xbmc.addon.metadata

Metadane (info, opis) mogą być zawarte w tagu `matadata` albo w pliku `addon.xml`.

Dodatkowo można dodać plik licencji (jedno z poniższych):
* `license.txt` – bezpośrednia zawartość pliku `LICENSE.txt`,
* `license-path` – ścieżka do pliku `LICENSE.txt` (względem głównego pliku yaml).

### Tag metadata

Tag `matadata` zawiera:
* `provider` – opis dostarczającego, np. autorzy po przecinku,
* `summary` – podsumowanie dodatku, zawiera [tłumaczenia](#tłumaczenia),
* `description` – opis dodatku, zawiera [tłumaczenia](#tłumaczenia),
* `disclaimer` – notka o zrzeczeniu się praw, zawiera [tłumaczenia](#tłumaczenia),
* `news` – [Kodi 17+] opis co nowego w dodatku,
* `platform` – platforma, na której działa, `all` gdy na wszystkich,
* `language` – obsługiwane języki (np. „en pl”)
* `license` – nazwa licencji, szczegóły (treść) mogą być w pliku `LICENSE.txt`,
* `forum` – link do forum,
* `website` – strona www modułu,
* `source` – link do kodu źródłowego,
* `email`
* `broken` – informacja o zepsutym dodatku, treść zawiera powód, np. *deprecated* (przestarzały),
* `assets` – [Kodi 17+] aktywa, czyli ikony, zrzuty ekranu,
    * `icon` – ścieżka lub URL do ikony, ważniejsze niż `metadata.icon`,
    * `fanart` – ścieżka lub URL do , ważniejsze niż `metadata.fanart`,
    * `screenshot` – ścieżka lub URL do zrzutu ekranu, może być użyta lista.


### Tłumaczenia

Tagi, które można tłumaczyć (`summary`, `description`, `disclaimer`) zawierają tagi języków i przetłumaczone treści.

```yaml
tag:
    en:    "Hello world of color!"
    en_GB: "Hello world of colour!"
    pl:    "Witaj świecie kolorów!"
```

### Przykład

```yaml
metadata:
    provider: "Yen Chi Hsuan, Remita Amine, Sergey M."
    website:  "https://rg3.github.io/youtube-dl/"
    source:   "https://github.com/rg3/youtube-dl"
    license:  "public domain"
    description:
        en:     "youtube-dl - download videos from youtube.com or other video platforms"
        pl:     "youtube-dl - pobieranie video z youtube.com i innych platform"
    summary:
        en:     "youtube_dl module"
    assets:
        icon:   "youtubedl/icon.png"
        screenshot:
            - "youtubedl/screeshot01.jpg"
            - "youtubedl/screeshot02.jpg"
```

Ten sposób preferowany nad plikiem addon.xml.


### requires

Zależności są opisane na stronie https://kodi.wiki/view/Addon.xml#.3Crequires.3E

## Tag requires

... *TODO* ...


## Plik addon.xml

Plik musi być zgodny z https://kodi.wiki/view/Addon.xml

Plik jest szablonem i można, a nawet trzeba, używać zmiennych:
* `{ID}` – identyfikator modułu,
* `{NAME}` – nazwa modułu, jak nie będzie zdefiniowana, to zostanie użyty ostatni człon ID,
* `{VER}` – wersja modułu.

Można też cały opis zdefiniować za pomocą [matadanych](#metadane) bezpośrednio w pliku yaml. 

Przykład:

```xml
<?xml version='1.0' encoding='UTF-8' standalone='yes'?>
<addon id="{ID}" name="{NAME}" provider-name="Yen Chi Hsuan, Remita Amine, Sergey M." version="{VER}">
  <requires>
    <import addon="xbmc.python" version="2.14.0" />
  </requires>
  <extension library="lib" point="xbmc.python.module" />
  <extension point="xbmc.addon.metadata">
    <summary lang="en">youtube_dl module</summary>
    <description lan="en">youtube-dl - download videos from youtube.com or other video platforms</description>
    <license>public domain</license>
    <platform>all</platform>
    <email></email>
    <website>https://rg3.github.io/youtube-dl/</website>
    <forum></forum>
    <source>https://github.com/rg3/youtube-dl</source>
    <language></language>
   </extension>
</addon>
```
Zamiast addon.xml preferowany jest [tag metadata](#tag-metadaata).

