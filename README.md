# Real Farm Manager — příznak naléhavosti aktualizace

Tohle repo neobsahuje kód. Je v něm **jediný soubor**, [`update.json`](update.json), a slouží
k jedinému účelu: říct už nainstalované hře, jak naléhavá je novější verze v Google Play.

Servíruje se přes GitHub Pages:

```
https://dniiks.github.io/real-farm-manager-release/update.json
```

Herní repo je privátní (je v něm podpisový klíč), takže obsah nemůže servírovat — proto tenhle
veřejný soused.

## Formát

```json
{
  "criticalBelow": 0,
  "lowBelow": 0
}
```

Obojí je **práh, ne označení konkrétního releasu**. Čte se to jako „kdo běží na míň, spadá
do tohohle stupně":

| stupeň | podmínka | co hráč uvidí |
| --- | --- | --- |
| kritický | `versionCode < criticalBelow` | celoobrazovkové okno Googlu, **nedá se odmítnout**; Play stáhne, nainstaluje a restartuje sám |
| nízký | `versionCode < lowBelow` | malé okno Googlu s Cancel; po souhlasu se stahuje na pozadí a hra pak nabídne restart |
| neutrální | ani jedno | nic, hra mlčí |

Kritický se testuje první, takže při překryvu vyhrává.

### Proč prahy a ne „verze 34 je kritická"

Google Play nabízí vždycky jen **nejnovější** verzi. Kdyby v34 byla kritická oprava a v35
kosmetika, hráč na v30 dostane nabídku na v35 — a kdyby naléhavost visela na konkrétním
releasu, kritičnost v34 by se ztratila, přestože ten hráč opravu nikdy nedostal.
`30 < 34` platí bez ohledu na to, kolik verzí mezitím vyšlo.

## Jak to změnit

Přepiš čísla na `versionCode` toho releasu, který má být dohnán, a commitni. Změna se přes
CDN propíše obvykle do minuty.

- **běžný release** → `lowBelow` na nový `versionCode`
- **kritická oprava** (rozbitá ekonomika, spadlé nákupy, hrozící ztráta postupu) → `criticalBelow`
  na nový `versionCode` a `lowBelow` dorovnat
- **drobnost** (překlep, kosmetika) → **nesahat na nic**

Drž `criticalBelow <= lowBelow`.

## Čtyři věci, které je potřeba vědět

1. **Nic se nespustí, dokud to nepotvrdí Google Play.** Hra sáhne po aktualizaci jedině tehdy,
   když jí Play sám hlásí, že update existuje a jde nainstalovat. Tenhle soubor umí naléhavost
   **jen zvýšit, ne vymyslet** — takže překlep tady nemůže hru zablokovat výzvou k neexistující
   verzi, a při postupném rolloutu nedostane blokující okno nikdo, kdo si tu verzi ještě
   stáhnout nemůže.
2. **Kritický stupeň hráče blokuje.** Nedostane se do hry jinak než aktualizací. Patří to na
   opravdové havárie, ne na „přidal jsem plodinu".
3. **Nedostupný nebo rozbitý soubor = ticho.** Hra spadne zpátky na neutrální a hraje se dál.
   Bezpečné — ale znamená to taky, že **zapomenutá změna se projeví jako mlčení, ne jako chyba**.
4. **Vždycky se to týká už nainstalovaných verzí.** Číslo, které sem napíšeš, ovlivňuje hráče
   se **starší** verzí; na tu, kterou zrovna vydáváš, nemá vliv.

## Nastavení repa

GitHub Pages, větev `main`, kořen. Nechávat **veřejné** — hra ho čte bez přihlášení.
