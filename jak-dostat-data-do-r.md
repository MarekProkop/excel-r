Jak dostat data do R
================

Do Excelu nebo Google Sheets asi nejčastěji nějaká data přímo napíšete
nebo odněkud zkopírujete a vložíte. Můžete je také importovat odjinud,
třeba z CSV souborů, různých databází nebo API. To vše plus několik
dalších způsobů můžete použít i v R.

## Co myslím daty

Spreadsheet je datově velmi jednoduchý. Má jen listy, ty se skládají z
buněk a každá buňka může (i nemusí) obsahovat hodnotu. Hodnoty moho být
jen pár typů: číslo, text, logická hodnota (TRUE/FALSE).

R to má trochu složitější. Pro dočasné ukládání dat používá pojmenované
objekty (někdy se jim říká proměnné prostředí), které mohou být mnoha
typů, jak primitivních (číslo, text apod.), tak složitějších (kterým se
většinou říká třída neboli class).

### Data frame

Jednomu listu spreadsheetu se nejvíc podobá eRková třída *data frame*.
Je to vlastně klasická datová tabulka s řádky a sloupci. Různé sloupce
mohou obsahovat různé typy dat – některé čísla, jiné text – ale v jednom
sloupci mohou být data jen jednoho typu. Ukázkovou tabulkou je třeba
ceník:

| produkt      | jednotka | cena | skladem |
|:-------------|:---------|-----:|:--------|
| Malý šroubek | 100 g    | 10.0 | TRUE    |
| Velký šroub  | ks       |  5.0 | TRUE    |
| Malá matička | 100 g    |  8.5 | FALSE   |
| Velká matka  | ks       |  6.0 | TRUE    |

První dva sloupce obsahují text, třetí sloupec čísla a poslední sloupec
logické hodnoty (někdy označované jako boolean): TRUE je pravda, FALSE
je nepravda.

Teď už tedy víte, co je data frame, a v dalším textu vám ukážu, jak ho
vytvořit a dostat do něj data.

## Ruční vstup dat

V Excelu data ručně vkládáte rovnou do buněk listu. To sice jde v R také
(ukážu později), ale většinou se to nedělá. Místo toho se napíše příkaz
(nebo krátký skript čili posloupnost příkazů), který vytvoří data frame,
naplní ho daty a výsledek uloží do objektu, se kterým můžete dál
pracovat.

### Funnkce data.frame

Konkrétně ceník výše jsem vytvořil tímto příkazem:

``` r
sample_df <- data.frame(
  produkt = c("Malý šroubek", "Velký šroub", "Malá matička", "Velká matka"),
  jednotka = c("100 g", "ks", "100 g", "ks"),
  cena = c(10, 5, 8.5, 6),
  skladem = c(TRUE, TRUE, FALSE, TRUE)
)
```

Vysvětlím podrobněji:

1.  Do objektu `sample_df` přiřadím (neboli uložím) výsledek funkce
    `data.frame`. To jde zjednodušeně napsat jako
    `sample_df <- data.frame(...)`, kde tři tečky reprezentují tzv.
    argumenty (nebo též parametry) funkce `data.frame`. Znaky `<-` se v
    R používají k přiřazení (uložení) hodnoty do objektu a v RStudiu je
    můžete zadat klávesou zkratkou <kbd>Alt</kbd>+<kbd>-</kbd> (Alt
    pomlčka).
2.  Argumenty funkce `data.frame` jsou jednotlivé sloupce, které má data
    frame mít. Zde to je *produkt*, *jednotka*, *cena* a *skladem*.
    Vzájemně jsou odděleny čárkou, na rozdělení do řádků nezáleží.
3.  Sloupce se definují jako `název_sloupce = vektor`, příčemž `vektor`
    musí určovat hodnoty všech řádků data framu.
4.  V příkladu vektory zadávám jako výsledek funkce `c`. Ta vezme
    všechny své argumenty (oddělené čárkou) a vytvoří z nich vektor.
    Všimněte si, že textové hodnoty se píší do uvozovek, kdežto číselné
    a logické ne.

Jakmile máte data frame vytvořený a uložený do objektu `sample_df`,
můžete s ním dál pracovat, např. vypsat jeho obsah:

``` r
sample_df
```

    ##        produkt jednotka cena skladem
    ## 1 Malý šroubek    100 g 10.0    TRUE
    ## 2  Velký šroub       ks  5.0    TRUE
    ## 3 Malá matička    100 g  8.5   FALSE
    ## 4  Velká matka       ks  6.0    TRUE

### Funkce tibble

Já raději používám funkce z ekosystému *tidyverse*, ve kterém funkci
*data.frame* nahrazuje funkce *tibble*. Používá se úplně stejně, ale
místo objektu třídy *data frame* vytvoří objekt třídy *tibble*, který je
s data framem plně kompatibilní, ale o něco hezčeji se vypisuje na
konsoli.

Příkaz s touto funkcí pak vypadá takto:

``` r
library(tidyverse)

sample_df <- tibble(
  produkt = c("Malý šroubek", "Velký šroub", "Malá matička", "Velká matka"),
  jednotka = c("100 g", "ks", "100 g", "ks"),
  cena = c(10, 5, 8.5, 6),
  skladem = c(TRUE, TRUE, FALSE, TRUE)
)
```

Povšimněte si, že abych mohl funkci *tibble* použít, musím nejdřív
načíst balíček *tidyverse* funkcí *library*.

### Funkce tribble

Oba výše popsané zpsoby zadávání dat do data framu mají jednu nevýhodu:
data zapisujete po sloupcích, ačkoli je většinou přirozenější je psát po
řádcích. Tuto nevýhodu řeší funkce *tribble*, které je taktéž součástí
ekosystému *tidyverse*.

Používá se takto:

``` r
sample_df <- tribble(
  ~produkt, ~jednotka, ~cena, ~skladem,
  "Malý šroubek", "100 g", 10, TRUE,
  "Velký šroub", "ks", 5, TRUE,
  "Malá matička", "100 g", 8.5, FALSE,
  "Velká matka", "ks", 6, TRUE
)
```

Názvy sloupců jsou v prvním řádku a každý začíná znakem `~`. Pak
následují jednotlivé řádky se hodnotami. Vče je navzájem odděleno
čárkou.

Pokud chcete mít funkci *tribble* v nějakém skriptu úhledně upravenou a
dobře čitelnou, můžete přidat i mezery, např. takto:

``` r
sample_df <- tribble(
  ~produkt,       ~jednotka, ~cena, ~skladem,
  "Malý šroubek", "100 g",   10,    TRUE,
  "Velký šroub",  "ks",       5,    TRUE,
  "Malá matička", "100 g",    8.5,  FALSE,
  "Velká matka",  "ks",       6,    TRUE
)
```

### Co když chci něco opravit?

To je všechno moc hezké, říkáte si, ale v Excelu mohu nejen ručně zadat
data, ale i kdykoli cokoli dodatečně opravit. Jak to udělám v R?

Inu, skoro stejně. V Excelu něco píšete, pak máte už hotový výsledek a
ten opravíte. V R nepíšete rovnou výsledek, nýbrž postup, jak výsledek
vytvořit. Opravíte tedy postup, znovu ho spustíte a získáte opravený
výsledek. Vypadá to o trochu složitěji, ale ve skutečnosti je to o dost
lepší, protože informace o postupu zůstane zaznamenaná.

1.  Pokud máte postup uložený ve skriptu, opravíte ho a znovu pusíte
    skript.
2.  Pokud máte postup v souboru *rmarkdown* (.Rmd), opravíte ho a znovu
    spustíte jen opravený blok.
3.  Pokud jste postup napsali jen do konzole, vrátité se k němu v
    historii (v konsoli šipka nahoru, případně panel *History* v
    RStudiu), opravíte a znovu provedete.

### Funkce edit

Ve skutečnosti můžete i v R opravaovat data skoro stejně, jako v Excelu.
Slouží k tomu funkce *edit*, která otveře jednoduchý tabulkový editor,
do něj načte data frame uvedený v jejím arguentu, a když v editoru
cokoli změníte a pak ho zavřete, vrátí zpátky opravená data.

V následujícím příkladu vyvolám editor pro objekt *sample\_df* a
výsledek zapíšu zpět do stejného objektu, takže pak bude obsahovat již
opravená data. Tímto způsobem se ale ztratí postup vzniku dat, a proto
se v R skoro nepoužívá.

``` r
sample_df <- edit(sample_df)
```

## Chci si jen rychle něco vyzkoušet

V základním R i v některých balíčcích je docela dost vzrových data setů
již zabudovaných. Slouží jako příklady pro demonstraci některých postupů
a můžete si s nimi zkusit hrát. Často se třeba něco ukazuje na data setu
*iris*, který obsahuje vlastnosti několika druhů kosatců.

``` r
head(iris)
```

    ##   Sepal.Length Sepal.Width Petal.Length Petal.Width Species
    ## 1          5.1         3.5          1.4         0.2  setosa
    ## 2          4.9         3.0          1.4         0.2  setosa
    ## 3          4.7         3.2          1.3         0.2  setosa
    ## 4          4.6         3.1          1.5         0.2  setosa
    ## 5          5.0         3.6          1.4         0.2  setosa
    ## 6          5.4         3.9          1.7         0.4  setosa

A v *tidyverse* ze zabudovaný i krásný data set postav ze Star Wars.

``` r
head(starwars)
```

    ## # A tibble: 6 x 14
    ##   name     height  mass hair_color  skin_color eye_color birth_year sex   gender
    ##   <chr>     <int> <dbl> <chr>       <chr>      <chr>          <dbl> <chr> <chr> 
    ## 1 Luke Sk~    172    77 blond       fair       blue            19   male  mascu~
    ## 2 C-3PO       167    75 <NA>        gold       yellow         112   none  mascu~
    ## 3 R2-D2        96    32 <NA>        white, bl~ red             33   none  mascu~
    ## 4 Darth V~    202   136 none        white      yellow          41.9 male  mascu~
    ## 5 Leia Or~    150    49 brown       light      brown           19   fema~ femin~
    ## 6 Owen La~    178   120 brown, grey light      blue            52   male  mascu~
    ## # ... with 5 more variables: homeworld <chr>, species <chr>, films <list>,
    ## #   vehicles <list>, starships <list>

Mimochodem, funkce `head` vrátí z data frame jen několik prvních řádků
(zde 10, ale počet jde ovlivnit parametrem `n`).

## Práce s CSV soubory

V reálné praxi se data do R nejčastějí načítají z CSV souborů. Abyste si
mohli hned vyzkoušet i v případě, že zrovna nemáte žádný CSV po ruce,
nejdřív vám ukážu, jak do CSV zapisovat.

### Funkce write\_csv

CSV soubor pro další pokusy vytvořím z data framu `sample_df`. S funcí
*write\_csv* z balíčku *readr* (součást *tidyverse*) Je to takhle
jednoduché:

``` r
write_csv(sample_df, "sample_df.csv")
```

### Funkce read\_csv

Obdobně lze data z CSV souboru načíst funkcí *read\_csv*:

``` r
sample_df <- read_csv("sample_df.csv")
```

    ## Rows: 4 Columns: 4
    ## -- Column specification --------------------------------------------------------
    ## Delimiter: ","
    ## chr (2): produkt, jednotka
    ## dbl (1): cena
    ## lgl (1): skladem
    ## 
    ## i Use `spec()` to retrieve the full column specification for this data.
    ## i Specify the column types or set `show_col_types = FALSE` to quiet this message.

Všiněte si, že funkce vypsala specifikaci sloupců a jejich datových
typů. Datové typy odhaduje z obsahu souboru a ne vždy je trefí správně,
takže někdy může být potřeba je určit ručně, ať už v parametrech funkce
*read\_csv*, nebo v dodatečné úpravě získaného data framu.

### Interaktivní import dat v Rstudiu

Pokud nevíte přesně, jak importovaný soubor vypadá, je v RStudiu
nejjednoduší zvolit příkaz *Import Dataset* z menu *File*. Tam si pak
můžete na živém náhledu dat vybrat, co přesně chcete importovat, které
sloupce jsou kterého typu apod.

Podstatné je, že se vám současně generuje i skript, který si můžete
zkopírovat pro opakované použití se stejným souborem.

### Načtení CSV z webu

Funkcí *read\_csv* a dalšími funkcemi z balíčku *readr* (součást
*tidyverse*) můžete načítat i vzdálené soubory z internetu. Místo cesty
a názvu lokálního souboru pak zadáte jeho URL.

Ukážu trochu komplexnější příklad, který načte data z veřejného datasetu
zvířat k adopci pražské ZOO.

``` r
animals <- read_delim(
  "https://opendata.praha.eu/dataset/9e9ec749-db30-4f0d-bb02-5b48cf090888/resource/f4432746-002d-45dd-bb09-d1719acf35fb/download/959c0e6f-5afb-489f-95ef-c9c2982963de-adopcezvirata.csv", 
  delim = ";", 
  col_types = cols(
    id = col_skip(), 
    nazev_en = col_skip(), 
    k_prohlidce = col_logical(), 
    ...7 = col_skip()
  ), 
  trim_ws = TRUE
)
```

    ## New names:
    ## * `` -> ...7

    ## Warning: One or more parsing issues, see `problems()` for details

``` r
animals
```

    ## # A tibble: 514 x 4
    ##    nazev_cz                   trida_cz    cena k_prohlidce
    ##    <chr>                      <chr>      <dbl> <lgl>      
    ##  1 Šváb syčivý                Bezobratlí  1000 TRUE       
    ##  2 Pakobylka okřídlená        Bezobratlí  1000 TRUE       
    ##  3 Tayra                      Savci       5000 TRUE       
    ##  4 Kajmanka supí              Plazi       2000 TRUE       
    ##  5 Želva žlutočelá            Plazi       1000 TRUE       
    ##  6 Želva stepní (= čtyřprstá) Plazi       1500 TRUE       
    ##  7 Želva sloní                Plazi       5000 TRUE       
    ##  8 Želva obrovská             Plazi       5000 TRUE       
    ##  9 Želva skalní               Plazi       1000 TRUE       
    ## 10 Leguán kubánský            Plazi       3000 TRUE       
    ## # ... with 504 more rows

Co přesně jsem udělal?

-   Protože dataset odděluje sloupce středníky a ne čárkami, použil jsem
    místo funkce *read\_csv* obecnější funkci *read\_delim*, která
    umožňuje oddělovač nastavit parametrem `delim`.
-   V parametru `col_types` jsem funkcí *cols* určil, které sloupce
    nechci importovat (`col_skip`) a sloupci k\_prohlídce jsem nastavil
    logický typ (v původních datech je hodnota 0 a 1).
-   Parametrem `trim_ws` jsem určil, že se mají odstranit nevýznamné
    mezery na začátku a konci hodnot.

## Data na webové stránce

Nakonec vám ukáž, jak snadno můžete načítat data z webových stránek.
Ukážu to na tabulce v HTML, se kterou se pracuje nejsnáz, ale jde to i
se stránkami, kde jsou data uspořádaná jinak.

Ideální tabulka je např. na stránce [Seznam hlavních měst států
světa](https://cs.wikipedia.org/wiki/Seznam_hlavn%C3%ADch_m%C4%9Bst_st%C3%A1t%C5%AF_sv%C4%9Bta)
ve Wikipedii. Načtu ji skriptem, který:

1.  Připojí balíček *rvest* pro stahování informací z webu.
2.  Do objektu *hlavni\_mesta* přiřadí výsledek funkce *html\_table*. Ta
    vrátí všechny HTML tabulky, které na stránce jsou.
3.  Jako parametr funkce *html\_table* zavolá funkci *read\_html*, která
    načte webovou stránku ze zadaného URL.
4.  Indexem \[\[1\]\] z vráceného seznamu tabulek vybere tu první.
5.  Zobrazím, co se do objektu *hlavni\_mesta* uložilo.

``` r
library(rvest)
```

    ## 
    ## Attaching package: 'rvest'

    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

``` r
hlavni_mesta <- html_table(
  read_html(
    "https://cs.wikipedia.org/wiki/Seznam_hlavn%C3%ADch_m%C4%9Bst_st%C3%A1t%C5%AF_sv%C4%9Bta"
  )
)[[1]]

hlavni_mesta
```

    ## # A tibble: 197 x 4
    ##    Stát                                `Hlavní město`   Světadíl        Obyvatel
    ##    <chr>                               <chr>            <chr>           <chr>   
    ##  1 Afghánistán Afghánistán             Kábul            Asie            3 042 4~
    ##  2 Albánie Albánie                     Tirana           Evropa          600 339 
    ##  3 Alžírsko Alžírsko                   Alžír            Afrika          2 072 9~
    ##  4 Andorra Andorra                     Andorra la Vella Evropa          22 884  
    ##  5 Angola Angola                       Luanda           Afrika          2 776 1~
    ##  6 Antigua a Barbuda Antigua a Barbuda Saint John's     Amerika         25 150  
    ##  7 Argentina Argentina                 Buenos Aires     Amerika         2 891 0~
    ##  8 Arménie Arménie                     Jerevan          Asie            1 093 4~
    ##  9 Austrálie Austrálie                 Canberra         Austrálie a Oc~ 332 798 
    ## 10 Ázerbájdžán Ázerbájdžán             Baku             Asie            2 100 0~
    ## # ... with 187 more rows

Asi jste si všimli, že získaná data nejsou perfektní. Ve sloupci stát je
název každá země dvakrát. Je to proto, že ve zdrojové tabulce jsou před
názvem země vlajky, které název obsahují v atributu *alt*. Navíc je
počet obyvatel typu text, protože si funkce neporadí s českými mezerami
mezi řády.

Obojí jde snadno napravit. Ukážu vám jak, ale už to zatím nebudu
podrobně vysvětlovat. To si necháme na jindy.

``` r
mutate(
  hlavni_mesta,
  Stát = str_sub(Stát, 1, nchar(Stát) / 2),
  Obyvatel = as.double(str_remove_all(Obyvatel, "\\s"))
)
```

    ## # A tibble: 197 x 4
    ##    Stát              `Hlavní město`   Světadíl            Obyvatel
    ##    <chr>             <chr>            <chr>                  <dbl>
    ##  1 Afghánistán       Kábul            Asie                 3042430
    ##  2 Albánie           Tirana           Evropa                600339
    ##  3 Alžírsko          Alžír            Afrika               2072993
    ##  4 Andorra           Andorra la Vella Evropa                 22884
    ##  5 Angola            Luanda           Afrika               2776125
    ##  6 Antigua a Barbuda Saint John's     Amerika                25150
    ##  7 Argentina         Buenos Aires     Amerika              2891082
    ##  8 Arménie           Jerevan          Asie                 1093499
    ##  9 Austrálie         Canberra         Austrálie a Oceánie   332798
    ## 10 Ázerbájdžán       Baku             Asie                 2100000
    ## # ... with 187 more rows

## Data z jiných zdrojů

Pomocí různých balíčků jde načítat data i z jich typů zdrojů. Já třeba
nečastěji pracuji s daty Google Search Console nebo Google Analytics,
která načítám přes API (aplikační rozhraní) resp. prostřednictvím
specializovaných balíčků, které toto API zpřístupňují. Pracovat jde i s
daty ve všech běžných SQL i NoSQL databázích. Tyto metody jsou už ale
nad rámec tohoto článku.
