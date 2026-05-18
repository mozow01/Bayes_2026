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
## 8. A modell alapötlete WebPPL-ben

A WebPPL-modellben három dolgot kell megadnunk:

```text
1. melyik két modellt hasonlítjuk össze,
2. melyik item melyik modell szerint jel vagy zaj,
3. hogyan lesz a jel/zaj állapotból válaszvalószínűség.
```

A két modell:

```javascript
var models = ["mental", "intuitionistic"];
```

Az itemek:

```javascript
var items = ["Q1", "Q2", "Q3", "Q4", "Q5", "Q6"];
```

A legfontosabb függvény azt mondja meg, hogy egy item az adott modell szerint jel-e.

```javascript
var isSignal = function(model, item) {
  if (model === "mental") {
    return item === "Q1" || item === "Q3" || item === "Q5";
  }

  if (model === "intuitionistic") {
    return item === "Q3" || item === "Q6";
  }

  return false;
};
```

A mentális modell szerint ezek a jelek:

```text
Q1, Q3, Q5
```

Mert ezekben a konklúzió:

```text
A
```

vagyis:

```text
van ász
```

Az intuicionista modell szerint ezek a jelek:

```text
Q3, Q6
```

Mert ezekben van levezethető konklúzió:

```text
Q3: és + A
Q6: kizáró vagy + ¬A
```

---

## 9. Hogyan lesz ebből válaszvalószínűség?

A modellben a diák nem közvetlenül a logikai igazságot látja.

Inkább kap egy belső „erősségérzetet”.

Ha ez az erősség átlépi a kritériumot, akkor azt mondja:

```text
érvényes
```

Ha nem lépi át, akkor azt mondja:

```text
nem érvényes
```

Ezt két paraméter szabályozza:

```text
d = mennyire válik szét a jel és a zaj
c = mennyire szigorú a döntési kritérium
```

A WebPPL-ben először rácson próbáljuk ki a lehetséges értékeket:

```javascript
var dGrid = [0.2, 0.6, 1.0, 1.4, 1.8, 2.2, 2.6, 3.0];
var cGrid = [-1.2, -0.8, -0.4, 0.0, 0.4, 0.8, 1.2];
```

Ez nem elméleti állítás, csak számítási egyszerűsítés.

Azért használunk rácsot, mert így a WebPPL `enumerate` módszerrel gyorsan és stabilan végig tudja próbálni a lehetőségeket.

---

## 10. A normális eloszlás eloszlásfüggvénye

Az SDT-képletben szükségünk van a normális eloszlás eloszlásfüggvényére.

Ezt `Phi`-vel jelöljük.

WebPPL-ben megadhatjuk így:

```javascript
var erf = function(x) {
  var sign = x >= 0 ? 1 : -1;
  var ax = Math.abs(x);

  var a1 = 0.254829592;
  var a2 = -0.284496736;
  var a3 = 1.421413741;
  var a4 = -1.453152027;
  var a5 = 1.061405429;
  var p = 0.3275911;

  var t = 1.0 / (1.0 + p * ax);
  var y = 1.0 - (((((a5 * t + a4) * t) + a3) * t + a2) * t + a1) * t * Math.exp(-ax * ax);

  return sign * y;
};

var Phi = function(x) {
  return 0.5 * (1.0 + erf(x / Math.sqrt(2.0)));
};
```

---

## 11. Az SDT-válaszfüggvény

Most jön a lényeg.

Ha az item jel, akkor az „érvényes” válasz valószínűsége:

```text
Phi(d / 2 - c)
```

Ha az item zaj, akkor az „érvényes” válasz valószínűsége:

```text
Phi(-d / 2 - c)
```

WebPPL-ben:

```javascript
var pValid = function(signal, d, c) {
  if (signal) {
    return Phi(d / 2.0 - c);
  } else {
    return Phi(-d / 2.0 - c);
  }
};
```

Ez a függvény mondja meg, hogy egy adott modell, `d` és `c` mellett mekkora eséllyel válaszolja a diák azt, hogy:

```text
érvényes
```

Például:

```javascript
pValid(true, 2.0, 0.0);
```

Ez egy signal itemre ad válaszvalószínűséget.

```javascript
pValid(false, 2.0, 0.0);
```

Ez egy noise itemre ad válaszvalószínűséget.

Ha `d` nagy, akkor a két érték távol lesz egymástól.

Ha `d` kicsi, akkor a modell alig tud különbséget tenni jel és zaj között.
## 12. Teljes WebPPL-modell

Az alábbi kód egyben futtatható WebPPL-ben.

A modell két lehetséges belső modellt hasonlít össze:

```text
mental
intuitionistic
```

A program azt becsli, hogy a válaszadatok melyik modellhez illeszkednek jobban.

```javascript
// king_ace_sdt.wppl

// ---------- Adatok ----------

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

// ---------- Modellcímkézés ----------

var isSignal = function(model, item) {
  if (model === "mental") {
    return item === "Q1" || item === "Q3" || item === "Q5";
  }

  if (model === "intuitionistic") {
    return item === "Q3" || item === "Q6";
  }

  return false;
};

// ---------- Segédfüggvények ----------

var erf = function(x) {
  var sign = x >= 0 ? 1 : -1;
  var ax = Math.abs(x);

  var a1 = 0.254829592;
  var a2 = -0.284496736;
  var a3 = 1.421413741;
  var a4 = -1.453152027;
  var a5 = 1.061405429;
  var p = 0.3275911;

  var t = 1.0 / (1.0 + p * ax);
  var y = 1.0 - (((((a5 * t + a4) * t) + a3) * t + a2) * t + a1) *
    t * Math.exp(-ax * ax);

  return sign * y;
};

var Phi = function(x) {
  return 0.5 * (1.0 + erf(x / Math.sqrt(2.0)));
};

var clamp = function(p) {
  return Math.max(0.001, Math.min(0.999, p));
};

// ---------- SDT válaszmodell ----------

var pValid = function(signal, d, c) {
  var p = signal ?
    Phi(d / 2.0 - c) :
    Phi(-d / 2.0 - c);

  return clamp(p);
};

// ---------- Paraméterrács ----------

var dGrid = [0.2, 0.6, 1.0, 1.4, 1.8, 2.2, 2.6, 3.0];
var cGrid = [-1.2, -0.8, -0.4, 0.0, 0.4, 0.8, 1.2];

// ---------- Bayesiánus modell ----------

var posterior = Infer({method: "enumerate"}, function() {
  var model = uniformDraw(["mental", "intuitionistic"]);
  var d = uniformDraw(dGrid);
  var c = uniformDraw(cGrid);

  map(function(r) {
    var signal = isSignal(model, r.item);
    var p = pValid(signal, d, c);
    observe(Bernoulli({p: p}), r.response);
  }, responses);

  return {
    model: model,
    d: d,
    c: c
  };
});

print("Posterior:");
print(posterior);

// ---------- Modellposterior külön ----------

var modelPosterior = Infer({method: "enumerate"}, function() {
  var sample = sample(posterior);
  return sample.model;
});

print("Model posterior:");
print(modelPosterior);
```

A kódban a legfontosabb sor ez:

```javascript
var model = uniformDraw(["mental", "intuitionistic"]);
```

Ez azt jelenti, hogy a program induláskor még egyik modellt sem részesíti előnyben.

A válaszadatok alapján frissíti ezt a bizonytalanságot.

A végén ezt kapjuk:

```text
P(mental | adatok)
P(intuitionistic | adatok)
```

A `d` és `c` értékek pedig azt mutatják meg, hogy az adott modell mellett mennyire jól különül el a jel és a zaj, illetve milyen irányú a válaszbias.
