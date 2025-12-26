# ANOVA und lineare Regression mit R
Prof. Dr. Oleksiy Khoroshun

- [<span class="toc-section-number">1</span>
  Datenmanagement](#datenmanagement)
- [<span class="toc-section-number">2</span> Grundidee](#grundidee)
- [<span class="toc-section-number">3</span> Einfaktorielle ANOVA und
  einfache lineare
  Regression](#einfaktorielle-anova-und-einfache-lineare-regression)
  - [<span class="toc-section-number">3.1</span> Allegmeines lineares
    Modell](#allegmeines-lineares-modell)
  - [<span class="toc-section-number">3.2</span> Allgemeine Angaben zu
    ANOVA](#allgemeine-angaben-zu-anova)
  - [<span class="toc-section-number">3.3</span> Durchführung der
    ANOVA](#durchführung-der-anova)
  - [<span class="toc-section-number">3.4</span> Interpretation der
    Ergebnisse der ANOVA](#interpretation-der-ergebnisse-der-anova)
  - [<span class="toc-section-number">3.5</span> Allgemeine Angaben zur
    linearen Regression](#allgemeine-angaben-zur-linearen-regression)
  - [<span class="toc-section-number">3.6</span> Durchführung der
    Berechnungen und Interpretation der Ergebnisse der einfachen
    linearen
    Regression](#durchführung-der-berechnungen-und-interpretation-der-ergebnisse-der-einfachen-linearen-regression)
    - [<span class="toc-section-number">3.6.1</span>
      Koeffizienten](#koeffizienten)
    - [<span class="toc-section-number">3.6.2</span>
      Signifikanzcodes](#signifikanzcodes)
    - [<span class="toc-section-number">3.6.3</span>
      Modellgüte](#modellgüte)
    - [<span class="toc-section-number">3.6.4</span>
      Zusammenfassung](#zusammenfassung)
- [<span class="toc-section-number">4</span> Mehrfaktorielle
  Varianzanalyse und multiple Regression ohne
  Wechselwirkung](#mehrfaktorielle-varianzanalyse-und-multiple-regression-ohne-wechselwirkung)
  - [<span class="toc-section-number">4.1</span> Allgemeines lineares
    Modell](#allgemeines-lineares-modell)
  - [<span class="toc-section-number">4.2</span> Durchführung und
    Interpretation von
    ANOVA](#durchführung-und-interpretation-von-anova)
  - [<span class="toc-section-number">4.3</span> Durchführung und
    Interpretation der linearen
    Regression](#durchführung-und-interpretation-der-linearen-regression)
    - [<span class="toc-section-number">4.3.1</span>
      Koeffizienten](#koeffizienten-1)
    - [<span class="toc-section-number">4.3.2</span>
      Modellgüte](#modellgüte-1)
    - [<span class="toc-section-number">4.3.3</span>
      Zusammengassung](#zusammengassung)
- [<span class="toc-section-number">5</span> ANOVA und Regression mit
  Wechselwirkung](#anova-und-regression-mit-wechselwirkung)
  - [<span class="toc-section-number">5.1</span> Was ist eine
    Wechselwirkung?](#was-ist-eine-wechselwirkung)
  - [<span class="toc-section-number">5.2</span> Umsetzung in
    R](#umsetzung-in-r)
  - [<span class="toc-section-number">5.3</span> ANOVA mit
    Wechselwirkung](#anova-mit-wechselwirkung)
    - [<span class="toc-section-number">5.3.1</span> Haupteffekt von
      `allges`
      (Gesundheitszustand)](#haupteffekt-von-allges-gesundheitszustand)
    - [<span class="toc-section-number">5.3.2</span> Haupteffekt von
      `wlage` (Beurteilung der
      Wohnverhältnisse)](#haupteffekt-von-wlage-beurteilung-der-wohnverhältnisse)
    - [<span class="toc-section-number">5.3.3</span> Interaktionseffekt
      (allges:wlage)](#interaktionseffekt-allgeswlage)
    - [<span class="toc-section-number">5.3.4</span> Fazit](#fazit)
  - [<span class="toc-section-number">5.4</span> Lineare Regression mit
    Wechselwirkung](#lineare-regression-mit-wechselwirkung)
    - [<span class="toc-section-number">5.4.1</span> Übersicht der
      Modellgüte](#übersicht-der-modellgüte)
    - [<span class="toc-section-number">5.4.2</span> Referenzkategorie
      und Interpretation der
      Haupteffekte](#referenzkategorie-und-interpretation-der-haupteffekte)
    - [<span class="toc-section-number">5.4.3</span> Intercept
      (Achsenabschnitt)](#intercept-achsenabschnitt)
    - [<span class="toc-section-number">5.4.4</span> Haupteffekte für
      allges](#haupteffekte-für-allges)
    - [<span class="toc-section-number">5.4.5</span> Haupteffekte für
      wlage](#haupteffekte-für-wlage)
    - [<span class="toc-section-number">5.4.6</span> Interpretation der
      Interaktionen](#interpretation-der-interaktionen)
    - [<span class="toc-section-number">5.4.7</span> Beispielhafte
      Deutung (allges3:wlage3)](#beispielhafte-deutung-allges3wlage3)
    - [<span class="toc-section-number">5.4.8</span> Beispielhafte
      Deutung (allges4:wlage3)](#beispielhafte-deutung-allges4wlage3)
    - [<span class="toc-section-number">5.4.9</span> Gesamtfazit aus der
      Regression](#gesamtfazit-aus-der-regression)

# Datenmanagement

Datensatz importieren:

``` r
library(tidyverse)
library(haven)
data <- read_sav("ZA7558_v2-0-0.sav")
```

Variablen aussuchen:

``` r
data |>
  select(
    alterl_m1, # Alternserleben: Positive Erfahrungen (Mittelwert, 5 Items, 1-5)
    alterl_m2, # Alternserleben: Negative Erfahrungen (Mittelwert, 5 Items, 1-5)
    ALT_sex, # Geschlecht
    ALT_agegroup, # Alter
    allges, # Allgemeine Gesundheitseinschätzung
    wlage # Beurteilung der Wohnlage
  ) -> small.df
```

Qualitative Variablen als `factor` speichern (wichtig für die
Durchführung der Regression!):

``` r
small.df |>
  mutate(
    ALT_sex = factor(ALT_sex),
    ALT_agegroup = factor(ALT_agegroup),
    allges = factor(allges),
    wlage = factor(wlage)
  ) -> small.df
```

Überblick über die Daten verschaffen:

``` r
summary(small.df)
```

       alterl_m1       alterl_m2     ALT_sex ALT_agegroup  allges    wlage  
     Min.   :1.000   Min.   :1.000   1:927   1:728        1   :134   1:290  
     1st Qu.:2.600   1st Qu.:2.200   2:936   2:625        2   :599   2:912  
     Median :3.200   Median :2.800           3:510        3   :942   3:619  
     Mean   :3.168   Mean   :2.877                        4   :180   4: 38  
     3rd Qu.:3.800   3rd Qu.:3.600                        NA's:  8   5:  4  
     Max.   :5.000   Max.   :5.000                                          
     NA's   :16      NA's   :14                                             

# Grundidee

ANOVA beantwortet Fragen wie: „Unterscheidet sich der Mittelwert einer
abhängigen Variable über verschiedene Gruppen?“

Lineare Regression beantwortet Fragen wie: „Gibt es einen Zusammenhang
zwischen einer oder mehreren unabhängigen Variablen und einer abhängigen
Variable – und wie stark ist dieser Zusammenhang?“

Gemeinsamer Rahmen: Beide Methoden sind Spezialfälle des **allgemeinen
linearen Modells**. ANOVA kann man als Spezialfall der linearen
Regression betrachten, wenn die Prädiktoren Faktoren (kategorische
Variablen) sind.

# Einfaktorielle ANOVA und einfache lineare Regression

## Allegmeines lineares Modell

Positives Erleben des Alterns wird vom Alter beeinflusst:
$$\texttt{alterl\_m1} = f(\texttt{ALT\_agegroup})$$.

Formulierung in R

``` r
formel1 <- small.df$alterl_m1 ~ small.df$ALT_agegroup
```

## Allgemeine Angaben zu ANOVA

Man hat eine kategoriale nominale Variable, die eine Gesamtzahl von $N$
numerischen Beobachtungen in $k$ verschiedene Gruppen unterteilt, wobei
$k \geq 2$ ist. Man möchte die Mittelwerte der $k$ Gruppen,
$\mu_1, \mu_2, \ldots , \mu_k$, statistisch vergleichen, um
festzustellen, ob sie als gleich angesehen werden können. Die
Standardhypothesen lauten wie folgt

Wenn $k = 2$ ist, ist der $t$-Test für zwei Stichproben äquivalent zur
ANOVA; aus diesem Grund wird die ANOVA am häufigsten verwendet, wenn
$k > 2$ ist.

Die folgenden Annahmen müssen erfüllt sein, damit die Ergebnisse eines
ANOVA-Tests als zuverlässig gelten können:

1.  **Angemessenheit der Daten:** Die abhängige Variable sollte *stetig*
    sein, auf Intervall- oder Verhältnisebene gemessen werden und
    unbegrenzt sein (oder zumindest einen großen Bereich abdecken); die
    Faktoren (Gruppenvariablen) sollten *dichotom* oder *kategorial*
    sein.

2.  **Unabhängigkeit:** Die Stichproben, aus denen sich die $k$ Gruppen
    zusammensetzen, müssen *unabhängig* voneinander sein, und die
    Beobachtungen in jeder Gruppe müssen *unabhängig* und *identisch*
    verteilt sein. Dies wäre nicht der Fall, wenn z.

    2.  eine zeitliche Abhängigkeit bestünde oder wenn einige der
        abhängigen Variablen von Probanden gemessen würden, die in
        größeren Einheiten zusammengefasst sind (z. B. Mitglieder
        derselben Familie oder Kinder, die dieselbe Klasse besuchen),
        was ihren Wert in der abhängigen Variable beeinflussen würde.

3.  **Verteilung:** Die stetige Variable ist innerhalb jeder Gruppe
    annähernd normalverteilt.

4.  **Homogenität der Varianz:** Die Varianzen der einzelnen Gruppen
    sollten annähernd gleich sein.

Die ANOVA gilt als robustes Verfahren, d.h. sie kann gute Ergebnisse
liefern, selbst wenn einige Annahmen verletzt werden.

## Durchführung der ANOVA

``` r
alterl_m1.means <- small.df |>
    group_by(ALT_agegroup) |>
    summarize(means = mean(alterl_m1, na.rm = TRUE)) |>
    pull()
boxplot(small.df$alterl_m1 ~ small.df$ALT_agegroup)
points(1:3, alterl_m1.means, pch = 4, cex = 1.5)
```

![](ANOVA_Regression_v2_files/figure-commonmark/unnamed-chunk-6-1.png)

Einfache ANOVA in R

``` r
aov(alterl_m1 ~ ALT_agegroup, data = small.df) |> summary()
```

                   Df Sum Sq Mean Sq F value   Pr(>F)    
    ALT_agegroup    2    9.6   4.803   7.031 0.000908 ***
    Residuals    1844 1259.8   0.683                     
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    16 observations deleted due to missingness

## Interpretation der Ergebnisse der ANOVA

Die ANOVA-Analyse untersucht, ob es signifikante Unterschiede im
Mittelwert der Variable `alterl_m1` zwischen den Altersgruppen
(`ALT_agegroup`) gibt. Die Ergebnisse zeigen:

- `F value 7.031`: Der $F$-Wert testet die Varianz zwischen den Gruppen
  im Verhältnis zur Varianz innerhalb der Gruppen. Ein höherer Wert
  deutet darauf hin, dass die Unterschiede zwischen den Gruppen relativ
  groß sind.
- `Pr(>F) 0,000908`: Der $p$-Wert ist hochsignifikant ($p < 0,001$).
  Dies bedeutet, dass es starke Beweise gegen die Nullhypothese gibt,
  die besagt, dass es keine Unterschiede zwischen den Mittelwerten der
  Altersgruppen gibt.
- Sum of Squares (`Sum Sq`):
  - Between-Groups (`ALT_agegroup`): 9.6
  - Within-Groups (`Residuals`): 1259.8

Die ANOVA zeigt, dass die Altersgruppen einen signifikanten Einfluss auf
die Variable `alterl_m1` haben.

## Allgemeine Angaben zur linearen Regression

Ziel eines linearen Regressionsmodells ist es, eine Funktion zu finden,
die den *Mittelwert* einer Variablen bei einem bestimmten Wert einer
anderen Variablen schätzt. Diese Variablen werden als Responsevariable
$Y$ (die „Ergebnisvariable“ oder abhängige Variable, deren Mittelwert
gesucht wird) und als Erklärungsvariable $X$ (die „Prädiktorvariable“
oder unabhängige Variable, deren Wert bereits bekannt ist) bezeichnet.

Das *einfache lineare Regressionsmodell* besagt, dass der Wert einer
Antwort $Y$ durch die folgende Gleichung ausgedrückt wird:
$$Y\vert X = \beta_0 + \beta_1 X + \epsilon.$$

Die Annahmen eines linearen Regressionsmodells sind:

1.  **Angemessenheit der Daten:** Die abhängige Variable $Y$ sollte
    *stetig* sein, auf Intervall- oder Verhältnisebene gemessen werden
    und unbegrenzt sein (oder zumindest einen großen Bereich abdecken);
    die unabhängige Variablen sollten *stetig* oder *dichotom* sein.
    Kategoriale Variablen mit mehr als zwei Kategorien können in eine
    Reihe von dichotomen **Dummy-Variablen** umkodiert werden.

2.  **Unabhängigkeit:** Jeder Wert der Ergebnisvariablen $Y$ ist
    unabhängig von jedem anderen Wert.

3.  **Linearität:** Die Beziehung zwischen der unabhängigen Variablen
    und der abhängigen Variablen ähnelt einer geraden Linie. Diese
    Annahme wird überprüft, indem die Daten grafisch dargestellt werden.
    Wenn die Beziehung eine andere Form als eine Gerade aufweist, kann
    es erforderlich sein, eine oder beide Variablen zu transformieren
    oder ein anderes Verfahren zu wählen.

4.  **Verteilung:** Die stetigen Variablen sind annähernd
    normalverteilt.

5.  **Homoskedastizität:** Die Störgrößen sind über den gesamten
    Datenbereich konstant. Das bedeutet, dass die Fehler nicht
    beispielsweise kleiner sind, wenn der $y$-Wert klein ist, und
    größer, wenn der $y$-Wert groß ist.

## Durchführung der Berechnungen und Interpretation der Ergebnisse der einfachen linearen Regression

``` r
lm(alterl_m1 ~ ALT_agegroup, data = small.df) |> summary()
```


    Call:
    lm(formula = alterl_m1 ~ ALT_agegroup, data = small.df)

    Residuals:
         Min       1Q   Median       3Q      Max 
    -2.25315 -0.48063  0.06128  0.54685  1.91937 

    Coefficients:
                  Estimate Std. Error t value Pr(>|t|)    
    (Intercept)    3.25315    0.03070 105.975  < 2e-16 ***
    ALT_agegroup2 -0.11443    0.04517  -2.533 0.011388 *  
    ALT_agegroup3 -0.17252    0.04805  -3.590 0.000339 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    Residual standard error: 0.8265 on 1844 degrees of freedom
      (16 observations deleted due to missingness)
    Multiple R-squared:  0.007568,  Adjusted R-squared:  0.006491 
    F-statistic: 7.031 on 2 and 1844 DF,  p-value: 0.0009083

Die lineare Regression analysiert den spezifischen Einfluss jeder
Altersgruppe (`ALT_agegroup`) auf die abhängige Variable `alterl_m1`.
Die erste Altersgruppe (Stufe 1) dient als Referenzgruppe. Die
Ergebnisse zeigen Folgendes:

### Koeffizienten

- `(intercept) 3.25315`: der mittlere Wert von `alterl_m1` in der
  Referenzgruppe (Altersgruppe 1) beträgt 3,25315.
- `ALT_agegroup2 -0,11443`: Personen in Altersgruppe 2 haben im
  Durchschnitt einen um 0,11443 niedrigeren `alterl_m1`-Wert als
  Personen in Altersgruppe 1. Der Unterschied ist statistisch
  signifikant ($p = 0,011388$).
- `ALT_agegroup3 -0,17252`: Personen in Altersgruppe 3 haben im
  Durchschnitt einen um 0,17252 niedrigeren `alterl_m1`-Wert als
  Personen in Altersgruppe 1. Der Unterschied ist hochsignifikant
  ($p = 0,000339$).

### Signifikanzcodes

- `***`: Sehr starke Evidenz gegen die Nullhypothese ($p < 0,001$).
- `*`: Moderate Evidenz gegen die Nullhypothese ($p < 0,05$).

### Modellgüte

- `Residual standard error: 0,8265`: gibt den durchschnittlichen Fehler
  des Modells bei der Vorhersage von `alterl_m1` an.
- `Multiple R-squared (0,007568)`: das Modell erklärt nur 0,76% der
  Varianz in `alterl_m1`. Die erklärte Varianz ist sehr gering.
- `Adjusted R-squared (0,006491)`: nach Anpassung für die Anzahl der
  Prädiktoren bleibt die erklärte Varianz ebenfalls sehr gering.
- `F-statistic: 7.031` und `p-value: 0,0009083`: das Modell als Ganzes
  ist signifikant ($p < 0,001$). Dies bedeutet, dass die Altersgruppe
  einen signifikanten Einfluss auf `alterl_m1` hat.

### Zusammenfassung

- Die Altersgruppe hat einen signifikanten Einfluss auf das Erleben des
  Alters (`alterl_m1`). Ältere Altersgruppen (Gruppe 2 und 3) berichten
  von einer geringfügig schlechteren Erfahrung im Vergleich zur
  Referenzgruppe (Gruppe 1).
- Der Unterschied zwischen den Gruppen ist statistisch signifikant,
  jedoch klein.
- Die erklärte Varianz (R-squared) ist sehr gering, was darauf
  hindeutet, dass andere Variablen, die nicht im Modell enthalten sind,
  einen größeren Einfluss auf `alterl_m1` haben könnten.

# Mehrfaktorielle Varianzanalyse und multiple Regression ohne Wechselwirkung

## Allgemeines lineares Modell

Positives Erleben des Alterns wird durch Gesundheitszustand und
Wohnverhältnisse beeinflusst:
$$\texttt{alterl\_m1} = f(\texttt{allges}, \texttt{wlage}).$$

Formulierung in R

``` r
formel2 <- small.df$alterl_m1 ~ small.df$allges + small.df$wlage
```

## Durchführung und Interpretation von ANOVA

Bei der mehrfaktoriellen ANOVA mit zwei Faktoren sollten die Hypothesen
wie folgt festgelegt werden:

``` r
aov(alterl_m1 ~ allges + wlage, data = small.df) |> summary()
```

                  Df Sum Sq Mean Sq F value   Pr(>F)    
    allges         3    4.6   1.524   2.258   0.0798 .  
    wlage          4   18.9   4.721   6.996 1.38e-05 ***
    Residuals   1831 1235.6   0.675                     
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    24 observations deleted due to missingness

In diesem Modell werden die Variablen `allges` (Gesundheitszustand) und
`wlage` (Wohnverhältnisse) betrachtet. Die Ergebnisse zeigen:

- `allges`: Der Einfluss von `allges` ist nicht signifikant (p \> 0,05).
- `wlage` (F = 6.996, p \< 0,001):\*\* Der Einfluss von `wlage` ist
  hochsignifikant (p \< 0,001).
- Residuals:
  - Between-Groups: 23.5 (4.6 + 18.9)
  - Within-Groups: 1235.6

## Durchführung und Interpretation der linearen Regression

``` r
lm(alterl_m1 ~ allges + wlage, data = small.df) |> summary()
```


    Call:
    lm(formula = alterl_m1 ~ allges + wlage, data = small.df)

    Residuals:
         Min       1Q   Median       3Q      Max 
    -2.55562 -0.49325  0.04933  0.59486  1.94235 

    Coefficients:
                Estimate Std. Error t value Pr(>|t|)    
    (Intercept)  3.55562    0.08615  41.274  < 2e-16 ***
    allges2     -0.20495    0.07910  -2.591 0.009643 ** 
    allges3     -0.20972    0.07655  -2.740 0.006209 ** 
    allges4     -0.20608    0.09451  -2.181 0.029346 *  
    wlage2      -0.19076    0.05608  -3.401 0.000685 ***
    wlage3      -0.29303    0.05921  -4.949 8.15e-07 ***
    wlage4      -0.08396    0.14194  -0.592 0.554228    
    wlage5      -0.88405    0.41416  -2.135 0.032930 *  
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    Residual standard error: 0.8215 on 1831 degrees of freedom
      (24 observations deleted due to missingness)
    Multiple R-squared:  0.01863,   Adjusted R-squared:  0.01488 
    F-statistic: 4.965 on 7 and 1831 DF,  p-value: 1.401e-05

### Koeffizienten

- `Intercept (3.55562): Der mittlere Wert von`alterl_m1\` für die
  Referenzgruppe beträgt 3.55562.

#### Einfluss von `allges` (Gesundheitszustand):

- `allges2 (-0,20495)`: Personen in Gruppe 2 haben im Durchschnitt einen
  um 0,20495 niedrigeren Wert für `alterl_m1` im Vergleich zur
  Referenzgruppe. Der Unterschied ist signifikant (*p = 0,009643*).
- `allges3 (-0,20972)`: Personen in Gruppe 3 haben einen um 0,20972
  niedrigeren Wert, signifikant (*p = 0,006209*).
- `allges4 (-0,20608)`: Personen in Gruppe 4 haben einen um 0,20608
  niedrigeren Wert, signifikant (*p = 0,029346*).

#### Einfluss von `wlage` (Wohnverhältnisse):

- `wlage2 (-0,19076)`: Personen in Gruppe 2 haben im Durchschnitt einen
  um 0,19076 niedrigeren Wert, signifikant (*p = 0,000685*).
- `wlage3 (-0,29303)`: Personen in Gruppe 3 haben einen um 0,29303
  niedrigeren Wert, hochsignifikant (*p \< 0,001*).
- `wlage4 (-0,08396)`: Kein signifikanter Unterschied zur Referenzgruppe
  (*p = 0,554228*).
- `wlage5 (-0,88405)`: Personen in Gruppe 5 haben einen stark
  niedrigeren Wert, signifikant (*p = 0,032930*).

### Modellgüte

- Residual standard error: 0,8215. Dies zeigt, dass der typische Fehler
  bei der Vorhersage von `alterl_m1` klein ist.
- Multiple R-squared (0,01863): Das Modell erklärt 1.86% der Varianz von
  `alterl_m1`. Die erklärte Varianz ist gering.
- Adjusted R-squared (0,01488): Nach Anpassung für die Anzahl der
  Prädiktoren bleibt die erklärte Varianz ebenfalls gering.
- F-Statistik (4.965, p = 1.401e-05): Das Modell ist insgesamt
  signifikant (p \< 0,001).

### Zusammengassung

- Einfluss von `allges`: Der Gesundheitszustand (`allges`) hat einen
  signifikanten Einfluss auf das Erleben des Alters (`alterl_m1`).
  Personen mit schlechterem Gesundheitszustand berichten von einer
  geringeren Alterserfahrung.
- Einfluss von `wlage`: Die Beurteilung der Wohnverhältnisse (`wlage`)
  hat ebenfalls einen signifikanten Einfluss. Personen in den Gruppen
  mit schlechterer Wohnqualität berichten von einer deutlich
  schlechteren Alterserfahrung.
- Die erklärte Varianz des Modells ist jedoch gering, was darauf
  hinweist, dass weitere Variablen zur Erklärung von `alterl_m1`
  herangezogen werden sollten.

# ANOVA und Regression mit Wechselwirkung

## Was ist eine Wechselwirkung?

Der Gesundheitszustand beeinflusst den Einfluss der Wohnbedingungen auf
ein positives Erleben des Alterns:

## Umsetzung in R

``` r
formel3 <- small.df$alterl_m1 ~ small.df$allges +
    small.df$wlage +
    small.df$allges:small.df$wlage
```

oder kürzer, aber weniger übersichtlich

``` r
formel3 <- small.df$alterl_m1  ~ small.df$allges * small.df$wlage
```

## ANOVA mit Wechselwirkung

``` r
aov(alterl_m1 ~ allges * wlage, data = small.df) |> summary()
```

                   Df Sum Sq Mean Sq F value   Pr(>F)    
    allges          3    4.6   1.524   2.258   0.0798 .  
    wlage           4   18.9   4.721   6.996 1.38e-05 ***
    allges:wlage   11    7.4   0.677   1.003   0.4411    
    Residuals    1820 1228.2   0.675                     
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    24 observations deleted due to missingness

### Haupteffekt von `allges` (Gesundheitszustand)

Der $p$-Wert liegt bei etwa 0,08 und damit knapp über dem üblichen
Signifikanzniveau von 0,05. Das bedeutet, dass sich die mittleren Werte
von `alterl_m1` zwischen den Kategorien von `allges` nicht eindeutig
signifikant unterscheiden. Es gibt jedoch einen Trend
($p \approx 0{,}08$) in Richtung eines möglichen Effekts; bei einer
etwas größeren Stichprobe oder einem weniger konservativen
Signifikanzniveau könnte sich hier ein bedeutsamer Effekt zeigen.
Praktisch bedeutet das: Die Einschätzung des eigenen Gesundheitszustands
(`allges`) hängt tendenziell mit dem Erleben des Alters (`alterl_m1`)
zusammen, allerdings nicht stark genug, um in dieser Analyse als
statistisch gesichert zu gelten.

### Haupteffekt von `wlage` (Beurteilung der Wohnverhältnisse)

Der Effekt ist hochsignifikant ($p < 0{,}001$). Das heißt, zwischen den
unterschiedlichen Kategorien von `wlage` gibt es deutliche Unterschiede
im mittleren Wert von `alterl_m1`. Da `wlage` kodiert ist (höhere Stufe
= schlechtere Wohnbedingungen), kann dies darauf hindeuten, dass
Personen mit schlechter eingeschätzten Wohnverhältnissen tendenziell ein
anderes Erleben des Alters (`alterl_m1`) haben als Personen, die ihre
Wohnverhältnisse positiver bewerten.

### Interaktionseffekt (allges:wlage)

Die Interaktion zwischen Gesundheitszustand und Wohnverhältnissen ist
nicht signifikant ($p = 0{,}44$). Das bedeutet, dass sich der Effekt von
`wlage` nicht wesentlich je nach Stufe von `allges` ändert (und
umgekehrt). Mit anderen Worten: Das Zusammenwirken von
Gesundheitszustand und Wohnverhältnissen übt keinen über die
Haupteffekte hinausgehenden gemeinsamen Einfluss auf das Erleben des
Alters aus.

### Fazit

`wlage` (Wohnverhältnisse): Zeigt einen klar signifikanten Zusammenhang
mit dem Erleben des Alters. Die verschiedenen Kategorien von `wlage`
unterscheiden sich in ihren mittleren `alterl_m1`-Werten. `allges`
(Gesundheitszustand): Tendenzieller, aber nicht statistisch gesicherter
Effekt. Interaktion `allges:wlage`: Kein zusätzlicher (moderierender)
Effekt feststellbar. Aus praktischer Sicht kann man schließen, dass
insbesondere die Wohnverhältnisse (bzw. deren subjektive Beurteilung)
mit dem Erleben des Alters verbunden sind. Ein besserer
Gesundheitszustand könnte zwar ebenfalls Einfluss auf das Erleben des
Alters haben, in dieser Analyse jedoch nicht mit ausreichender
statistischer Evidenz.

## Lineare Regression mit Wechselwirkung

``` r
lm(alterl_m1 ~ allges * wlage, data = small.df) |> summary()
```


    Call:
    lm(formula = alterl_m1 ~ allges * wlage, data = small.df)

    Residuals:
         Min       1Q   Median       3Q      Max 
    -2.36224 -0.49479  0.04787  0.57531  1.90521 

    Coefficients: (1 not defined because of singularities)
                   Estimate Std. Error t value Pr(>|t|)    
    (Intercept)      3.1295     0.2278  13.736  < 2e-16 ***
    allges2          0.1693     0.2490   0.680  0.49665    
    allges3          0.2328     0.2368   0.983  0.32578    
    allges4          0.3648     0.2593   1.407  0.15963    
    wlage2           0.2157     0.2493   0.865  0.38694    
    wlage3           0.2629     0.2568   1.023  0.30622    
    wlage4           0.4955     0.4697   1.055  0.29158    
    wlage5          -0.4628     0.8525  -0.543  0.58726    
    allges2:wlage2  -0.3623     0.2728  -1.328  0.18427    
    allges3:wlage2  -0.4169     0.2604  -1.601  0.10957    
    allges4:wlage2  -0.5388     0.2931  -1.838  0.06619 .  
    allges2:wlage3  -0.4668     0.2815  -1.658  0.09740 .  
    allges3:wlage3  -0.5901     0.2690  -2.193  0.02840 *  
    allges4:wlage3  -0.8681     0.3071  -2.827  0.00475 ** 
    allges2:wlage4  -0.7776     0.5529  -1.406  0.15975    
    allges3:wlage4  -0.5816     0.5069  -1.147  0.25138    
    allges4:wlage4  -0.3898     0.6361  -0.613  0.54007    
    allges2:wlage5  -0.2359     1.0364  -0.228  0.81995    
    allges3:wlage5  -0.6994     1.1856  -0.590  0.55532    
    allges4:wlage5       NA         NA      NA       NA    
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

    Residual standard error: 0.8215 on 1820 degrees of freedom
      (24 observations deleted due to missingness)
    Multiple R-squared:  0.02454,   Adjusted R-squared:  0.01489 
    F-statistic: 2.544 on 18 and 1820 DF,  p-value: 0.0003602

### Übersicht der Modellgüte

- Residual standard error = 0,8215  
  → Die durchschnittliche Abweichung der vorhergesagten Werte vom
  beobachteten Wert (in Einheiten der Zielvariable) liegt bei ca.
  0,82.  
- Multiple R-squared = 0,02454 (≈ 2,45 %)  
- Adjusted R-squared = 0,01489 (≈ 1,49 %)  
  → Nur ein geringer Anteil der Varianz von `alterl_m1` wird durch
  dieses Modell erklärt. Viele andere Faktoren (nicht im Modell)
  beeinflussen also das Erleben des Alters.  
- F-statistic = 2.544, p = 0,0003602  
  → Das Modell als Ganzes ist (bei einem Signifikanzniveau von 5 %)
  statistisch signifikant, d. h. mindestens ein Koeffizient
  unterscheidet sich signifikant von 0.

------------------------------------------------------------------------

### Referenzkategorie und Interpretation der Haupteffekte

In R wird bei kategorialen Prädiktoren automatisch eine
Referenzkategorie gesetzt. Hier erscheint dies als:

- `allges1` (niedrigste Stufe von allges)  
- `wlage1` (niedrigste Stufe von wlage)

Die Interpretation der Koeffizienten erfolgt demnach relativ zu dieser
Referenzkategorie.

### Intercept (Achsenabschnitt)

- `(Intercept)` = 3.1295
  - Geschätzter Mittelwert von `alterl_m1`, wenn `allges` = 1 und
    `wlage` = 1 (also „Gesundheitszustand Stufe 1” und „Wohnlage Stufe
    1“).

### Haupteffekte für allges

- `allges2` = 0,1693 ($p =$ 0,497, n. s.)
- `allges3` = 0,2328 ($p =$ 0,326, n. s.)
- `allges4` = 0,3648 ($p =$ 0,160, n. s.)

Keiner dieser Koeffizienten ist auf dem 5 %-Niveau signifikant. Das
bedeutet:  
- Für Personen in allges2, allges3 oder allges4 (bei wlage1) steigt
`alterl_m1` zwar numerisch etwas an, doch ist dieser Anstieg statistisch
nicht signifikant.

### Haupteffekte für wlage

- `wlage2` = 0,2157 ($p =$ 0,387, n. s.)  
- `wlage3` = 0,2629 ($p =$ 0,306, n. s.)  
- `wlage4` = 0,4955 ($p =$ 0,292, n. s.)  
- `wlage5` = -0,4628 ($p =$ 0,587, n. s.)

Auch hier sind die Haupteffekte (für `wlage` = 2, 3, 4 oder 5 bei
`allges` = 1) statistisch nicht signifikant. Numerisch würden sich
Personen mit z.  B. `wlage2` (vs. `wlage1`) zwar etwas im Erleben des
Alters unterscheiden, aber die Unsicherheit ($p$-Wert) ist zu hoch, um
hier einen klaren Effekt zu belegen.

### Interpretation der Interaktionen

Da das Modell die Interaktion `(allges * wlage)` enthält, kann sich der
Effekt einer Variable je nach Stufe der anderen ändern. Die
Interaktionskoeffizienten beziehen sich auf Abweichungen von den
additiven Haupteffekten.

Wichtige (teilweise signifikante) Interaktionen in der Tabelle sind:

1.  `allges3:wlage3` = -0,5901 ($p =$ 0,0284, *signifikant*)  
2.  `allges4:wlage3` = -0,8681 ($p =$ 0,00475, hochsignifikant)  
3.  Einige weitere Interaktionen liegen im Trendbereich ($p <$ 0,10),
    z. B.:
    - `allges4:wlage2` ($p =$ 0,06619)  
    - `allges2:wlage3` ($p =$ 0,09740)

### Beispielhafte Deutung (allges3:wlage3)

- Haupteffekt `allges3` (vs. `allges1`) = 0,2328  
- Haupteffekt `wlage3` (vs. `wlage1`) = 0,2629  
- Interaktionskoeffizient = -0,5901

Die Summe dieser Effekte beträgt:  
\[ 0,2328 + 0,2629 - 0,5901 = -0,0944 \]

Damit fällt das Erleben des Alters (`alterl_m1`) für eine Person mit
`allges3` und `wlage3` (im Vergleich zur Referenz `allges1` & `wlage1`)
insgesamt etwas niedriger aus, als man aufgrund der Haupteffekte allein
erwarten würde. Da das Interaktionsterm signifikant ist, deutet das
darauf hin, dass sich bei dieser Kombination (guter Gesundheitszustand +
mittlere Wohnverhältnisse) das Alterserleben nicht einfach additiv aus
den Haupteffekten ergibt, sondern eben negativ abweicht.

### Beispielhafte Deutung (allges4:wlage3)

- Haupteffekt `allges4` (vs.`allges1`) = 0,3648  
- Haupteffekt `wlage3` (vs. `wlage1`) = 0,2629  
- Interaktion `allges4:wlage3` = -0,8681

Gesamteffekt =  
\[ 0,3648 + 0,2629 - 0,8681 = -0,2404 \]

Hier ist das Ergebnis hochsignifikant ($p <$ 0,01). Auch hier zeigt sich
eine negative Abweichung vom additiven Modell. Das heißt, in diesem Fall
ist das Erleben des Alters bei Personen mit *allges4* und *wlage3* nicht
mehr so hoch wie man erwarten würde, wenn man nur die beiden
Haupteffekte addiert.

### Gesamtfazit aus der Regression

1.  Gesamtmodell
    - Statistisch signifikant, aber niedriges (R^2) (ca. 2,45 %). Ein
      Großteil der Varianz bleibt unerklärt.
2.  Haupteffekte
    - Die Unterschiede zwischen den Stufen von `allges` oder `wlage`
      (getrennt betrachtet) sind nicht statistisch signifikant.  
    - Das deutet darauf hin, dass sich weder der reine Unterschied im
      Gesundheitszustand (`allges`) noch der reine Unterschied in der
      Wohnlage (`wlage`) alleine stark genug auf `alterl_m1` auswirkt,
      um im linearen Modell signifikant zu werden.
3.  Interaktionen
    - Einige Interaktionseffekte sind signifikant (z. B.
      `allges3:wlage3` und `allges4:wlage3`).  
    - Das heißt, bei gewissen Kombinationen aus Gesundheitszustand und
      Wohnlage tritt eine andere (tendenziell *niedrigere*) Ausprägung
      des Alterserlebens auf, als es die Summe der Haupteffekte erwarten
      ließe.  
    - Praktisch gesprochen: Je nach Zusammenspiel von Gesundheitszustand
      und Wohnlage kann das Erleben des Alters *negativ beeinflusst*
      werden, auch wenn man einzeln (nach Haupteffekten) keinen
      deutlichen Effekt sieht.

Wichtig:  
Die Befunde zeigen, dass v. a. das Zusammenspiel bestimmter Stufen
(insbesondere `allges3`/`allges4` mit `wlage3`) relevant ist. Die
lineare Regression liefert damit Hinweise, dass die Wohnverhältnisse und
der Gesundheitszustand zwar jeweils nur schwach auf `alterl_m1` wirken,
aber bestimmte Kombinationen (Interaktionen) das Erleben des Alters
deutlicher verändern können.
