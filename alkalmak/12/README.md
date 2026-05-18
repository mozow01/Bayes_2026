# King–Ace paradoxon WebPPL-ben

## 1. Mit akarunk megmérni?

A vizsgálatban azt nézzük meg, hogy a diákok hogyan ismernek fel egy logikai következtetést.

A kiinduló feladat a King–Ace probléma:

```text
"If there is a king in the hand, then there is an ace in the hand,"
or
"If there is not a king in the hand, then there is an ace in the hand,"

but not both of these if-thens are true.
```

Magyarul:

```text
Vagy igaz az, hogy ha király van a kézben, és akkor ász van a kézben,
vagy nem igaz az, hogy király a kézben, és akkor ász van a kézben.
```

Jelölések:

```text
K = király van a kézben
A = ász van a kézben
```

Formálisan:

```text
(K → A) vagy (¬K → A)
```

A kérdés:

```text
Következik-e ebből, hogy van ász?
Következik-e ebből, hogy nincs ász?
```

A vizsgálatban két lehetséges belső modellt hasonlítunk össze.

| modell | alapötlet |
|---|---|
| mentális modell | a diák a könnyen elképzelhető eseteket látja |
| intuicionista modell | a diák azt fogadja el, amire van levezetés |

A program nem azt kérdezi, hogy egy diák „okos” vagy „hibázott-e”.

A program ezt kérdezi:

```text
A válaszok inkább melyik belső modellhez hasonlítanak?
```

Vagyis:

```text
P(mentális modell | válaszok)

P(intuicionista modell | válaszok)
```

## 2. Mi az a szignáldetekció ebben a feladatban?

A szignáldetekciós modellben minden kérdés olyan, mintha a diáknak azt kellene eldöntenie:

```text
Ez most jel vagy zaj?
```

Nálunk ez így néz ki:

```text
jel  = a következtetés az adott modell szerint érvényes
zaj  = a következtetés az adott modell szerint nem érvényes
```

A diák válasza pedig csak kétféle lehet:

```text
1 = érvényesnek tartja
0 = nem tartja érvényesnek
```

Így négy eset lehetséges:

| valódi állapot a modell szerint | diák válasza | név |
|---|---:|---|
| jel | 1 | találat |
| jel | 0 | kihagyás |
| zaj | 1 | téves riasztás |
| zaj | 0 | helyes elutasítás |

Például ha az intuicionista modell szerint egy item érvényes, és a diák is érvényesnek mondja, akkor ez találat.

Ha az intuicionista modell szerint egy item nem érvényes, de a diák mégis érvényesnek mondja, akkor ez téves riasztás.

---

## 3. Mit jelent a `d` és a `c`?

A szignáldetekciós modell két dolgot becsül.

### `d` = diszkriminabilitás

A `d` azt mutatja meg, hogy a diákok mennyire tudják elkülöníteni a jelet a zajtól.

```text
nagy d  = jól megkülönböztetik az érvényes és nem érvényes itemeket
kis d   = alig különböztetik meg őket
```

Ebben a jegyzetben `d`-t írunk, nem `d'`-t.

Az SDT-irodalomban gyakori a `d'` jelölés, de a szakdolgozatban a Lee–Wagenmakers-modellnél `d` szerepel, ezért itt is ezt használjuk.

### `c` = kritérium vagy bias

A `c` azt mutatja meg, hogy a diákok mennyire bátran mondják valamire, hogy érvényes.

```text
c < 0  = könnyen mondanak igent, sok az "érvényes" válasz
c = 0  = nincs erős válaszbias
c > 0  = óvatosak, nehezen mondják, hogy "érvényes"
```

Egyszerűen:

```text
d = mennyire látják a különbséget
c = mennyire bátran mondanak igent
```

---

## 4. Mitől hierarchikus a modell?

Egy egyszerű modellben lenne egy közös `d` és egy közös `c` az egész osztályra.

```text
osztály → d, c
```

Ez gyors és áttekinthető, de nem veszi figyelembe, hogy a diákok különbözhetnek.

A hierarchikus modellben minden diáknak lehet saját értéke:

```text
Anna  → d₁, c₁
Bence → d₂, c₂
Csilla → d₃, c₃
...
```

De ezek nem teljesen függetlenek. Úgy képzeljük el, hogy az egyéni értékek egy közös osztályszintű eloszlásból jönnek:

```text
osztályszintű d és c
        ↓
egyéni dᵢ és cᵢ
        ↓
válaszok
```

Ez azért jó, mert kis létszámnál sem engedi teljesen elszállni az egyéni becsléseket.

A mi órai verziónkban egyszerűsítünk:

```text
nem becsülünk külön minden diáknak saját dᵢ és cᵢ értéket,
hanem egy közös osztályszintű d és c értékkel dolgozunk.
```

Ez nem teljes Lee–Wagenmakers-féle hierarchikus modell, hanem annak órai, kicsinyített változata.

A kérdés tehát továbbra is ez:

```text
Melyik modell címkézi úgy a kérdéseket jelnek és zajnak,
hogy a diákok válaszai mellett nagy legyen a d,
és értelmezhető legyen a c?
```
## 5. A mini-kérdőív

A kérdőívben hat rövid item szerepel.

Minden item ugyanazt az alapszerkezetet használja:

```text
K = király van a kézben
A = ász van a kézben
```

A diákok feladata minden esetben ugyanaz:

```text
Következik-e a megadott konklúzió?
```

A válasz kétértékű:

```text
1 = érvényes
0 = nem érvényes
```

---

### Q1

```text
Tegyük fel, hogy igaz:

(K → A) vagy (¬K → A)

Konklúzió:

A
```

Válasz:

```text
Érvényes / Nem érvényes
```

---

### Q2

```text
Tegyük fel, hogy igaz:

(K → A) vagy (¬K → A)

Konklúzió:

¬A
```

Válasz:

```text
Érvényes / Nem érvényes
```

---

### Q3

```text
Tegyük fel, hogy igaz:

(K → A) és (¬K → A)

Konklúzió:

A
```

Válasz:

```text
Érvényes / Nem érvényes
```

---

### Q4

```text
Tegyük fel, hogy igaz:

(K → A) és (¬K → A)

Konklúzió:

¬A
```

Válasz:

```text
Érvényes / Nem érvényes
```

---

### Q5

```text
Tegyük fel, hogy igaz:

(K → A) kizáró vagy (¬K → A)

Konklúzió:

A
```

Válasz:

```text
Érvényes / Nem érvényes
```

---

### Q6

```text
Tegyük fel, hogy igaz:

(K → A) kizáró vagy (¬K → A)

Konklúzió:

¬A
```

Válasz:

```text
Érvényes / Nem érvényes
```

---

## 6. Hogyan kódoljuk az itemeket?

A programnak nem elég az, hogy látja a diákok válaszait.

Azt is meg kell mondanunk neki, hogy a két modell szerint melyik item számít jelnek.

```text
jel = a modell szerint érvényes következtetés
zaj = a modell szerint nem érvényes következtetés
```

A két modell eltérően címkézi az itemeket.

| item | konnektívum | konklúzió | mentális modell | intuicionista modell |
|---|---|---|---|---|
| Q1 | vagy | A | jel | zaj |
| Q2 | vagy | ¬A | zaj | zaj |
| Q3 | és | A | jel | jel |
| Q4 | és | ¬A | zaj | zaj |
| Q5 | kizáró vagy | A | jel | zaj |
| Q6 | kizáró vagy | ¬A | zaj | jel |

A mentális modell egyszerű heurisztikája:

```text
ha a konklúzió A, akkor jel
ha a konklúzió ¬A, akkor zaj
```

Az intuicionista modell egyszerűsített órai címkézése:

```text
az "és + A" item jel
a "kizáró vagy + ¬A" item jel
a többi zaj
```

A program ezután azt nézi meg, hogy a diákok válaszai melyik címkézéshez illeszkednek jobban.

## 7. Adatfelvétel és kódolás

A diákok minden kérdésnél csak két választ adhatnak:

```text
1 = érvényesnek tartja a következtetést
0 = nem tartja érvényesnek a következtetést
```

A válaszokat egy egyszerű táblázatban rögzítjük.

Példa 5 diákkal:

| diák | Q1 | Q2 | Q3 | Q4 | Q5 | Q6 |
|---|---:|---:|---:|---:|---:|---:|
| Anna | 0 | 0 | 1 | 0 | 0 | 1 |
| Bence | 1 | 0 | 1 | 0 | 1 | 0 |
| Csilla | 0 | 0 | 1 | 0 | 0 | 1 |
| Dávid | 1 | 0 | 1 | 0 | 1 | 0 |
| Eszter | 0 | 0 | 1 | 0 | 0 | 1 |

Ez csak példaadat.

A táblázatban kétféle válaszminta keveredik.

Anna, Csilla és Eszter inkább az intuicionista mintát követik:

```text
Q3 = érvényes
Q6 = érvényes
```

Bence és Dávid inkább a mentális modellt követik:

```text
Q1 = érvényes
Q3 = érvényes
Q5 = érvényes
```

A WebPPL-kódban ugyanezt az adatot így fogjuk megadni:

```javascript
var responses = [
  {subj: "Anna",   item: "Q1", response: 0},
  {subj: "Anna",   item: "Q2", response: 0},
  {subj: "Anna",   item: "Q3", response: 1},
  {subj: "Anna",   item: "Q4", response: 0},
  {subj: "Anna",   item: "Q5", response: 0},
  {subj: "Anna",   item: "Q6", response: 1},

  {subj: "Bence",  item: "Q1", response: 1},
  {subj: "Bence",  item: "Q2", response: 0},
  {subj: "Bence",  item: "Q3", response: 1},
  {subj: "Bence",  item: "Q4", response: 0},
  {subj: "Bence",  item: "Q5", response: 1},
  {subj: "Bence",  item: "Q6", response: 0},

  {subj: "Csilla", item: "Q1", response: 0},
  {subj: "Csilla", item: "Q2", response: 0},
  {subj: "Csilla", item: "Q3", response: 1},
  {subj: "Csilla", item: "Q4", response: 0},
  {subj: "Csilla", item: "Q5", response: 0},
  {subj: "Csilla", item: "Q6", response: 1},

  {subj: "Dávid",  item: "Q1", response: 1},
  {subj: "Dávid",  item: "Q2", response: 0},
  {subj: "Dávid",  item: "Q3", response: 1},
  {subj: "Dávid",  item: "Q4", response: 0},
  {subj: "Dávid",  item: "Q5", response: 1},
  {subj: "Dávid",  item: "Q6", response: 0},

  {subj: "Eszter", item: "Q1", response: 0},
  {subj: "Eszter", item: "Q2", response: 0},
  {subj: "Eszter", item: "Q3", response: 1},
  {subj: "Eszter", item: "Q4", response: 0},
  {subj: "Eszter", item: "Q5", response: 0},
  {subj: "Eszter", item: "Q6", response: 1}
];
```

Ha az órán valódi adatot veszünk fel, akkor csak ezt a részt kell átírni.

A program többi része változatlan marad.
