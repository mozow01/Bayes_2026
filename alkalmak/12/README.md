# King–Ace paradoxon WebPPL-ben

## 0. Áttekintés

A jegyzet egy Bayes-i szignáldetekciós modellt ad a King–Ace típusú következtetések vizsgálatához. A cél két kognitív-logikai modell összehasonlítása:

| modell | rövid leírás |
|---|---|
| mentális modell | az explicit, könnyen elképzelhető esetek alapján címkézi a következtetést |
| intuicionista levezetési modell | a konklúziót akkor fogadja el, ha van rá konstruktív levezetés |

A modell a válaszadatokat jel–zaj döntésekként kezeli. A szignáldetekciós paraméterezés két fő mennyisége:

```text
d = diszkriminabilitás
c = kritérium / válaszbias
```

A modellkiválasztás Bayes-i módon történik:

```text
P(mental_model | adatok)
P(intuitionistic_model | adatok)
```

A `d`–`c` diagram a két modell posterior eloszlását jeleníti meg.

---

## 1. A King–Ace probléma

A kiinduló feladat:

```text
"If there is a king in the hand, then there is an ace in the hand,"
or
"If there is not a king in the hand, then there is an ace in the hand,"

but not both of these if-thens are true.
```

Jelölések:

```text
K = király van a kézben
A = ász van a kézben
```

Az eredeti King–Ace szerkezet:

```text
(K → A) ⊕ (¬K → A)
```

ahol `⊕` a kizáró vagy:

```text
P ⊕ Q := (P ∨ Q) ∧ ¬(P ∧ Q)
```

A vizsgálatban nemcsak az eredeti kizáró vagyos szerkezet szerepel, hanem összehasonlító kontrollként az inkluzív vagy és az és is:

```text
(K → A) ∨ (¬K → A)
(K → A) ∧ (¬K → A)
(K → A) ⊕ (¬K → A)
```

Minden szerkezethez két konklúzió tartozik:

```text
A
¬A
```

A válasz minden esetben bináris:

```text
1 = a konklúzió érvényes
0 = a konklúzió nem érvényes
```

---

## 2. A két modell

### 2.1. Mentális modell

A mentális modell a mondat felszíni, esetszerű reprezentációját követi. A két kondicionális így jelenik meg:

```text
K     A
¬K    A
```

Ebből a modell a `van ász` konklúziókat támogatja. A kérdőívben ezért a mentális modell jelnek tekinti azokat az itemeket, amelyek konklúziója `A`.

```text
mental_model signal itemek: Q1, Q3, Q5
```

### 2.2. Intuicionista levezetési modell

Az intuicionista modell a konklúziót levezethetőség alapján címkézi. Egy állítás elfogadása nem pusztán azt jelenti, hogy nem vezet ellentmondáshoz, hanem azt, hogy rendelkezésre áll egy konstrukció vagy bizonyítás.

A kártyahelyzetet eldönthetőnek tekintjük:

```text
K ∨ ¬K
```

Ez a háttérfeltevés azt fejezi ki, hogy egy konkrét kézben vagy van király, vagy nincs.

Az `és` item esetén:

```text
(K → A) ∧ (¬K → A), K ∨ ¬K ⊢ A
```

A kizáró vagyos King–Ace item esetén:

```text
(K → A) ⊕ (¬K → A) ⊢ ¬A
```

Rövid levezetési vázlat:

```text
Tegyük fel: A.
Ekkor K → A is igaz.
Ekkor ¬K → A is igaz.
Tehát mindkét kondicionális igaz.
Ez ellentmond a kizáró vagy feltételének.
Következésképpen: ¬A.
```

Az intuicionista modell jelnek tekintett itemei:

```text
intuitionistic_model signal itemek: Q3, Q6
```

---

## 3. Szignáldetekciós értelmezés

A szignáldetekciós modellben minden item kétféleképpen jelenhet meg:

```text
signal = a modell szerint érvényes következtetés
noise  = a modell szerint nem érvényes következtetés
```

A résztvevő válasza:

```text
1 = érvényes
0 = nem érvényes
```

A négy lehetséges eset:

| modell szerinti állapot | válasz | SDT-név |
|---|---:|---|
| signal | 1 | találat |
| signal | 0 | kihagyás |
| noise | 1 | téves riasztás |
| noise | 0 | helyes elutasítás |

A modell célja annak becslése, hogy a válaszok melyik elméleti jel–zaj felosztáshoz illeszkednek jobban.

---

## 4. A `d` és `c` paraméterek

A szignáldetekciós modell két paraméterrel írja le a válaszadást.

### `d`: diszkriminabilitás

A `d` a signal és noise eloszlások távolsága.

```text
nagy d  = a jel és a zaj jól elkülönül
kis d   = a jel és a zaj kevésbé különül el
```

A jegyzet `d` jelölést használ. Az SDT-irodalomban gyakori a `d'` jelölés, de a Lee–Wagenmakers-féle hierarchikus SDT-írásmódban és az erre épülő szakdolgozati keretben a `d` jelölés szerepel.

### `c`: kritérium / válaszbias

A `c` a döntési kritérium eltolódását méri.

```text
c < 0  = liberális kritérium, több "érvényes" válasz
c = 0  = közel semleges kritérium
c > 0  = konzervatív kritérium, kevesebb "érvényes" válasz
```

A modellben az „érvényes” válasz valószínűsége:

```text
signal esetén: Φ(d / 2 - c)
noise esetén:  Φ(-d / 2 - c)
```

Itt `Φ` a standard normális eloszlás eloszlásfüggvénye.

---

## 5. Aggregált Bayes-i SDT-modell

A teljes hierarchikus SDT-modell résztvevőnként külön `dᵢ` és `cᵢ` paramétert rendel, ezek pedig csoportszintű eloszlásból származnak:

```text
csoportszintű paraméterek
        ↓
egyéni dᵢ, cᵢ
        ↓
válaszok
```

A jelen implementáció aggregált Bayes-i SDT-modell. Az itemenkénti válaszarányokat használja:

```text
y = hány válasz volt "érvényes"
n = hány válasz érkezett összesen
```

Az aggregált likelihood:

```text
yᵢ ~ Binomial(nᵢ, pᵢ)
```

Ahol:

```text
pᵢ = Φ(d / 2 - c),   ha az item signal
pᵢ = Φ(-d / 2 - c),  ha az item noise
```

A modell Bayes-i értelemben egyszerre becsli:

```text
modell ∈ {mental_model, intuitionistic_model}
d
c
```

---

## 6. Kérdőív és modellcímkézés

A hat item:

| item | konnektívum | premissza | konklúzió | mentális modell | intuicionista modell |
|---|---|---|---|---|---|
| Q1 | vagy | Ha király van a kezemben, akkor ász is van, vagy ha nem király van a kezemben, akkor is ász van. | Van ász a kezemben. | jel | zaj |
| Q2 | vagy | Ha király van a kezemben, akkor ász is van, vagy ha nem király van a kezemben, akkor is ász van. | Nincs ász a kezemben. | zaj | zaj |
| Q3 | és | Ha király van a kezemben, akkor ász is van, és ha nem király van a kezemben, akkor is ász van. | Van ász a kezemben. | jel | jel |
| Q4 | és | Ha király van a kezemben, akkor ász is van, és ha nem király van a kezemben, akkor is ász van. | Nincs ász a kezemben. | zaj | zaj |
| Q5 | kizáró vagy | Vagy az van, hogy ha király van a kezemben, akkor ász is van, vagy az van, hogy ha nem király van a kezemben, akkor is ász van. | Van ász a kezemben. | jel | zaj |
| Q6 | kizáró vagy | Vagy az van, hogy ha király van a kezemben, akkor ász is van, vagy az van, hogy ha nem király van a kezemben, akkor is ász van. | Nincs ász a kezemben. | zaj | jel |


A két modell jel–zaj címkézése:

| item | konnektívum | konklúzió | mentális modell | intuicionista modell |
|---|---|---|---|---|
| Q1 | vagy | `A` | signal | noise |
| Q2 | vagy | `¬A` | noise | noise |
| Q3 | és | `A` | signal | signal |
| Q4 | és | `¬A` | noise | noise |
| Q5 | kizáró vagy | `A` | signal | noise |
| Q6 | kizáró vagy | `¬A` | noise | signal |

---

## 7. Példaadatok

A modell bemeneteként az itemenként összesített válaszadatok szerepelnek.

| item | konnektívum | konklúzió | `y` | `n` |
|---|---|---|---:|---:|
| Q1 | vagy | `A` | 2 | 5 |
| Q2 | vagy | `¬A` | 0 | 5 |
| Q3 | és | `A` | 5 | 5 |
| Q4 | és | `¬A` | 0 | 5 |
| Q5 | kizáró vagy | `A` | 2 | 5 |
| Q6 | kizáró vagy | `¬A` | 3 | 5 |

Az adatszerkezet WebPPL-ben:

```javascript
var items = [
  {id: 'OR_A',       label: 'Q1: or / ace',          y: 2, n: 5},
  {id: 'OR_NOT_A',   label: 'Q2: or / no ace',       y: 0, n: 5},
  {id: 'AND_A',      label: 'Q3: and / ace',         y: 5, n: 5},
  {id: 'AND_NOT_A',  label: 'Q4: and / no ace',      y: 0, n: 5},
  {id: 'XOR_A',      label: 'Q5: xor / ace',         y: 2, n: 5},
  {id: 'XOR_NOT_A',  label: 'Q6: xor / no ace',      y: 3, n: 5}
];
```

---

## 8. WebPPL-modell

Fájlnév:

```text
king_ace_two_model_sdt.wppl
```

```javascript
// king_ace_two_model_sdt.wppl

var items = [
  {id: 'OR_A',       label: 'Q1: or / ace',          y: 2, n: 5},
  {id: 'OR_NOT_A',   label: 'Q2: or / no ace',       y: 0, n: 5},
  {id: 'AND_A',      label: 'Q3: and / ace',         y: 5, n: 5},
  {id: 'AND_NOT_A',  label: 'Q4: and / no ace',      y: 0, n: 5},
  {id: 'XOR_A',      label: 'Q5: xor / ace',         y: 2, n: 5},
  {id: 'XOR_NOT_A',  label: 'Q6: xor / no ace',      y: 3, n: 5}
];

var candidateModels = ['mental_model', 'intuitionistic_model'];

var isSignal = function(modelName, itemId) {
  if (modelName === 'mental_model') {
    return itemId === 'OR_A' || itemId === 'AND_A' || itemId === 'XOR_A';
  }
  if (modelName === 'intuitionistic_model') {
    return itemId === 'AND_A' || itemId === 'XOR_NOT_A';
  }
  return false;
};

var erf = function(x) {
  var sign = x < 0 ? -1 : 1;
  var ax = Math.abs(x);
  var t = 1.0 / (1.0 + 0.3275911 * ax);
  var y = 1.0 -
    (((((1.061405429 * t - 1.453152027) * t) + 1.421413741) * t
    - 0.284496736) * t + 0.254829592) *
    t * Math.exp(-ax * ax);
  return sign * y;
};

var phi = function(x) {
  return 0.5 * (1.0 + erf(x / Math.sqrt(2.0)));
};

var clamp = function(x) {
  return Math.max(0.000001, Math.min(0.999999, x));
};

var pValid = function(signal, d, c) {
  var p = signal ?
    1.0 - phi(c - d / 2.0) :
    1.0 - phi(c + d / 2.0);
  return clamp(p);
};

var dGrid = [0.2, 0.6, 1.0, 1.4, 1.8, 2.2, 2.6, 3.0, 3.4];
var cGrid = [-1.5, -1.0, -0.5, 0.0, 0.5, 1.0, 1.5];

var observeItems = function(i, modelName, d, c) {
  if (i === items.length) {
    return true;
  } else {
    var item = items[i];
    var signal = isSignal(modelName, item.id);
    var p = pValid(signal, d, c);
    factor(Binomial({p: p, n: item.n}).score(item.y));
    return observeItems(i + 1, modelName, d, c);
  }
};

var fit = function() {
  var modelName = categorical({vs: candidateModels, ps: [0.5, 0.5]});
  var d = uniformDraw(dGrid);
  var c = uniformDraw(cGrid);
  observeItems(0, modelName, d, c);
  return {model: modelName, d: d, c: c};
};

var posterior = Infer({method: 'enumerate'}, fit);

var posteriorModel = Infer({method: 'enumerate'}, function() {
  return fit().model;
});

var pMental = Math.exp(posteriorModel.score('mental_model'));
var pIntuitionistic = Math.exp(posteriorModel.score('intuitionistic_model'));

console.log('Posterior model probabilities');
console.log('mental_model = ' + pMental.toPrecision(4));
console.log('intuitionistic_model = ' + pIntuitionistic.toPrecision(4));
console.log('Bayes factor intuitionistic / mental = ' + (pIntuitionistic / pMental).toPrecision(4));

console.log('\nOverall posterior means');
console.log('mean d = ' + expectation(posterior, function(x) { return x.d; }).toFixed(3));
console.log('mean c = ' + expectation(posterior, function(x) { return x.c; }).toFixed(3));

var fixedFit = function(fixedModelName) {
  var d = uniformDraw(dGrid);
  var c = uniformDraw(cGrid);
  observeItems(0, fixedModelName, d, c);
  return {d: d, c: c};
};

var summarizeFixed = function(name) {
  var p = Infer({method: 'enumerate'}, function() { return fixedFit(name); });
  console.log('\nFixed model: ' + name);
  console.log('mean d = ' + expectation(p, function(x) { return x.d; }).toFixed(3));
  console.log('mean c = ' + expectation(p, function(x) { return x.c; }).toFixed(3));
};

summarizeFixed('mental_model');
summarizeFixed('intuitionistic_model');

```

Futtatás:

```bash
webppl king_ace_two_model_sdt.wppl
```

A példaadatokra kapott kimenet:

```text
Posterior model probabilities
mental_model = 0.05733
intuitionistic_model = 0.9427
Bayes factor intuitionistic / mental = 16.44

Overall posterior means
mean d = 1.713
mean c = 0.007

Fixed model: mental_model
mean d = 1.146
mean c = 0.312

Fixed model: intuitionistic_model
mean d = 1.747
mean c = -0.012
undefined
```

---

## 9. `d`–`c` diagram WebPPL-ből

A diagram külön WebPPL-fájlban készül. A program SVG-t ír ki.

Fájlnév:

```text
king_ace_two_model_sdt_plot.wppl
```

```javascript
// king_ace_two_model_sdt_plot.wppl

var items = [
  {id: 'OR_A',       label: 'Q1: or / ace',          y: 2, n: 5},
  {id: 'OR_NOT_A',   label: 'Q2: or / no ace',       y: 0, n: 5},
  {id: 'AND_A',      label: 'Q3: and / ace',         y: 5, n: 5},
  {id: 'AND_NOT_A',  label: 'Q4: and / no ace',      y: 0, n: 5},
  {id: 'XOR_A',      label: 'Q5: xor / ace',         y: 2, n: 5},
  {id: 'XOR_NOT_A',  label: 'Q6: xor / no ace',      y: 3, n: 5}
];

var candidateModels = ['mental_model', 'intuitionistic_model'];

var isSignal = function(modelName, itemId) {
  if (modelName === 'mental_model') {
    return itemId === 'OR_A' || itemId === 'AND_A' || itemId === 'XOR_A';
  }
  if (modelName === 'intuitionistic_model') {
    return itemId === 'AND_A' || itemId === 'XOR_NOT_A';
  }
  return false;
};

var erf = function(x) {
  var sign = x < 0 ? -1 : 1;
  var ax = Math.abs(x);
  var t = 1.0 / (1.0 + 0.3275911 * ax);
  var y = 1.0 -
    (((((1.061405429 * t - 1.453152027) * t) + 1.421413741) * t
    - 0.284496736) * t + 0.254829592) *
    t * Math.exp(-ax * ax);
  return sign * y;
};

var phi = function(x) {
  return 0.5 * (1.0 + erf(x / Math.sqrt(2.0)));
};

var clamp = function(x) {
  return Math.max(0.000001, Math.min(0.999999, x));
};

var pValid = function(signal, d, c) {
  var p = signal ?
    1.0 - phi(c - d / 2.0) :
    1.0 - phi(c + d / 2.0);
  return clamp(p);
};

var dGrid = [0.2, 0.6, 1.0, 1.4, 1.8, 2.2, 2.6, 3.0, 3.4];
var cGrid = [-1.5, -1.0, -0.5, 0.0, 0.5, 1.0, 1.5];

var itemLogLikelihood = function(modelName, d, c, item) {
  var signal = isSignal(modelName, item.id);
  var p = pValid(signal, d, c);
  return Binomial({p: p, n: item.n}).score(item.y);
};

var sumLogLikelihood = function(i, modelName, d, c) {
  if (i === items.length) {
    return 0;
  } else {
    return itemLogLikelihood(modelName, d, c, items[i]) +
      sumLogLikelihood(i + 1, modelName, d, c);
  }
};

var makePointsForC = function(modelName, d, cIndex) {
  if (cIndex === cGrid.length) {
    return [];
  } else {
    var c = cGrid[cIndex];
    var logPrior = Math.log(0.5) + Math.log(1.0 / dGrid.length) + Math.log(1.0 / cGrid.length);
    var logW = logPrior + sumLogLikelihood(0, modelName, d, c);
    var point = [{model: modelName, d: d, c: c, logW: logW}];
    return point.concat(makePointsForC(modelName, d, cIndex + 1));
  }
};

var makePointsForD = function(modelName, dIndex) {
  if (dIndex === dGrid.length) {
    return [];
  } else {
    var d = dGrid[dIndex];
    return makePointsForC(modelName, d, 0).concat(makePointsForD(modelName, dIndex + 1));
  }
};

var makePointsForModel = function(modelIndex) {
  if (modelIndex === candidateModels.length) {
    return [];
  } else {
    var modelName = candidateModels[modelIndex];
    return makePointsForD(modelName, 0).concat(makePointsForModel(modelIndex + 1));
  }
};

var rawPoints = makePointsForModel(0);

var maxLogW = function(points, i, currentMax) {
  if (i === points.length) {
    return currentMax;
  } else {
    var nextMax = points[i].logW > currentMax ? points[i].logW : currentMax;
    return maxLogW(points, i + 1, nextMax);
  }
};

var sumWeights = function(points, i, maxW) {
  if (i === points.length) {
    return 0;
  } else {
    return Math.exp(points[i].logW - maxW) + sumWeights(points, i + 1, maxW);
  }
};

var normalizePoints = function(points, i, maxW, totalW) {
  if (i === points.length) {
    return [];
  } else {
    var p = points[i];
    var point = [{model: p.model, d: p.d, c: p.c, weight: Math.exp(p.logW - maxW) / totalW}];
    return point.concat(normalizePoints(points, i + 1, maxW, totalW));
  }
};

var maximumLogWeight = maxLogW(rawPoints, 0, -1000000000);
var totalWeight = sumWeights(rawPoints, 0, maximumLogWeight);
var points = normalizePoints(rawPoints, 0, maximumLogWeight, totalWeight);

var maxPosteriorWeight = function(points, i, currentMax) {
  if (i === points.length) {
    return currentMax;
  } else {
    var nextMax = points[i].weight > currentMax ? points[i].weight : currentMax;
    return maxPosteriorWeight(points, i + 1, nextMax);
  }
};

var makeCircle = function(p, maxW, xScale, yScale) {
  var x = xScale(p.d);
  var y = yScale(p.c);

  var r = 2 + 20 * Math.sqrt(p.weight / maxW);

  var fill = p.model === 'mental_model' ? '#4E79A7' : '#F28E2B';
  var opacity = p.model === 'mental_model' ? 0.55 : 0.65;

  return '<circle cx="' + x.toFixed(1) +
    '" cy="' + y.toFixed(1) +
    '" r="' + r.toFixed(1) +
    '" fill="' + fill +
    '" fill-opacity="' + opacity +
    '" stroke="#222222" stroke-width="0.6" />';
};

var makeCircles = function(points, i, maxW, xScale, yScale) {
  if (i === points.length) {
    return '';
  } else {
    return makeCircle(points[i], maxW, xScale, yScale) + '\n' +
      makeCircles(points, i + 1, maxW, xScale, yScale);
  }
};

var makeDTicks = function(i, xScale, axisY) {
  if (i === dGrid.length) {
    return '';
  } else {
    var d = dGrid[i];
    var x = xScale(d);
    return '<line x1="' + x + '" y1="' + axisY + '" x2="' + x + '" y2="' +
      (axisY + 6) + '" stroke="black" />' +
      '<text x="' + x + '" y="' + (axisY + 24) +
      '" text-anchor="middle" font-size="12">' + d + '</text>' + '\n' +
      makeDTicks(i + 1, xScale, axisY);
  }
};

var makeCTicks = function(i, yScale, axisX) {
  if (i === cGrid.length) {
    return '';
  } else {
    var c = cGrid[i];
    var y = yScale(c);
    return '<line x1="' + (axisX - 6) + '" y1="' + y + '" x2="' + axisX +
      '" y2="' + y + '" stroke="black" />' +
      '<text x="' + (axisX - 10) + '" y="' + (y + 4) +
      '" text-anchor="end" font-size="12">' + c + '</text>' + '\n' +
      makeCTicks(i + 1, yScale, axisX);
  }
};

var drawSVG = function(points) {
  var width = 760;
  var height = 520;
  var left = 80;
  var right = 40;
  var top = 50;
  var bottom = 70;
  var plotW = width - left - right;
  var plotH = height - top - bottom;

  var dMin = 0.0;
  var dMax = 3.6;
  var cMin = -1.6;
  var cMax = 1.6;

  var xScale = function(d) {
    return left + (d - dMin) / (dMax - dMin) * plotW;
  };

  var yScale = function(c) {
    return top + plotH - (c - cMin) / (cMax - cMin) * plotH;
  };

  var axisY = top + plotH;
  var axisX = left;
  var maxW = maxPosteriorWeight(points, 0, 0);

  var xAxis = '<line x1="' + left + '" y1="' + axisY + '" x2="' +
    (left + plotW) + '" y2="' + axisY + '" stroke="black" stroke-width="1" />';

  var yAxis = '<line x1="' + axisX + '" y1="' + top + '" x2="' + axisX +
    '" y2="' + axisY + '" stroke="black" stroke-width="1" />';

  var title = '<text x="' + (width / 2) +
    '" y="28" text-anchor="middle" font-size="18" font-weight="bold">' +
    'King-Ace SDT posterior: d-c diagram</text>';

  var labels = '<text x="' + (left + plotW / 2) + '" y="' + (height - 20) +
    '" text-anchor="middle" font-size="16">d = discriminability</text>' +
    '<text x="24" y="' + (top + plotH / 2) +
    '" text-anchor="middle" font-size="16" transform="rotate(-90 24 ' +
    (top + plotH / 2) + ')">c = criterion / bias</text>';

  var legend =
  '<circle cx="545" cy="70" r="8" fill="#4E79A7" fill-opacity="0.55" stroke="#222222" />' +
  '<text x="565" y="75" font-size="13">mentalis modell</text>' +
  '<circle cx="545" cy="95" r="8" fill="#F28E2B" fill-opacity="0.65" stroke="#222222" />' +
  '<text x="565" y="100" font-size="13">intuicionista modell</text>';

  return '<svg xmlns="http://www.w3.org/2000/svg" width="' + width +
    '" height="' + height + '">' +
    '<rect width="100%" height="100%" fill="white" />' +
    title + xAxis + yAxis + makeDTicks(0, xScale, axisY) +
    makeCTicks(0, yScale, axisX) + makeCircles(points, 0, maxW, xScale, yScale) +
    labels + legend + '</svg>';
};

console.log(drawSVG(points));

```

Futtatás:

```bash
webppl king_ace_two_model_sdt_plot.wppl > king_ace_dc_diagram.svg
```

A létrejövő SVG-fájl böngészőben megnyitható.

A diagram olvasata:

| elem | jelentés |
|---|---|
| x tengely | `d`, diszkriminabilitás |
| y tengely | `c`, kritérium / bias |
| paca mérete | posterior valószínűség |
| szürke pont | mentális modell |
| fekete pont | intuicionista modell |

---

## 10. Modellösszehasonlítás

A modellek között a posterior modellvalószínűségek alapján történik döntés:

```text
P(mental_model | adatok)
P(intuitionistic_model | adatok)
```

Azonos priorok mellett:

```text
P(mental_model) = 0.5
P(intuitionistic_model) = 0.5
```

A posterior arány Bayes-faktorként értelmezhető:

```text
BF = P(intuitionistic_model | adatok) / P(mental_model | adatok)
```

A példaadatok esetén:

```text
P(mental_model | adatok) ≈ 0.057
P(intuitionistic_model | adatok) ≈ 0.943
BF ≈ 16.44
```

Értelmezés:

```text
A példaadatok lényegesen nagyobb támogatást adnak az intuicionista levezetési modellnek, mint a mentális modellnek.
```

A `d` és `c` becslése nem önmagában dönt a két modell között. A modellválasztás az egész modell evidenciáját veszi figyelembe, vagyis azt, hogy a modell a lehetséges `d,c` paraméterértékeken átlagolva mennyire jól jósolja az adatokat.

---

## 11. Interpretációs sablon

Az eredmények leírásának semleges formája:

```text
A válaszadatok a [modell neve] signal/noise címkézéséhez illeszkednek jobban.
```

A `d` értelmezése:

```text
Magasabb d esetén a válaszok jobban elkülönítik a modell szerinti érvényes és nem érvényes itemeket.
```

A `c` értelmezése:

```text
Negatív c esetén gyakoribb az "érvényes" válasz.
Pozitív c esetén ritkább az "érvényes" válasz.
```

A diagram és a modellposterior együtt értelmezendő:

```text
A posterior modellvalószínűségek adják a modellösszehasonlítást.
A d–c diagram a paramétertérben mutatja meg az illeszkedés szerkezetét.
```

---

## 12. Hivatkozások

- King–Ace / illuzórikus következtetési feladat: Bringsjord és mtsai., LCCM kézirat.
- Szignáldetekciós és Bayes-i keret: Lee és Wagenmakers típusú SDT-modellezés.
- Intuicionista logikai háttér: természetes levezetés, Brouwer–Heyting–Kolmogorov értelmezés.
- WebPPL: probabilisztikus programozási környezet JavaScript-alapon.
