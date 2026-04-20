## 1. hét / Közepes feladat – Diszkalkuliás ítéletmodell

Ebben a feladatban egy olyan ágenst vizsgálunk, amelyik egyszerű logikai következtetéseket próbál elvégezni, de a `csendes` és `cserfes` szavakat néha összekeveri.

### Mit modellezünk?

A program véletlenszerűen generál egy premisszát és egy konklúziót az alábbi alakban:

- Premissza: `Panni könyvtáros/tanár és/vagy csendes/cserfes.`
- Konklúzió: `Panni csendes/cserfes.`

A **hibátlan** klasszikus logika szerint:

- ha a premisszában az operátor `és`, akkor a konklúzió pontosan akkor **érvényes**, ha a konklúzióban szereplő melléknév megegyezik a premisszában szereplő melléknévvel;
- ha a premisszában az operátor `vagy`, akkor az ilyen alakú konklúziót **nem tekintjük érvényesnek**.

Az ágens azonban nem mindig olvassa helyesen a mellékneveket:

- minden egyes `csendes` vagy `cserfes` szóelőfordulást `q` valószínűséggel felcserél a belső reprezentációjában.

### Mit jelent ebben a feladatban a „helyes ítélet valószínűsége”?

Azt a valószínűséget kell meghatározni, hogy az ágens ugyanazt az ítéletet adja, mint a hibátlan klasszikus logika, vagyis:

- vagy mindkettő szerint **érvényes** a konklúzió,
- vagy mindkettő szerint **nem érvényes** a konklúzió.

Tehát **nem** azt vizsgáljuk, hogy a világban igaz-e a mondat, hanem azt, hogy az ágens helyesen ítéli-e meg a következtetés **érvényességét**.

### Feladatok

1. Futtasd a megadott alapmodellt.
2. Egészítsd ki úgy, hogy ki tudja számolni a helyes ítélet valószínűségét tetszőleges `q` érték mellett.
3. Számold ki külön:
   - a helyes ítélet valószínűségét akkor, ha a premisszában `és` szerepel;
   - a helyes ítélet valószínűségét akkor, ha a premisszában `vagy` szerepel.
4. Készíts egy **szöveges táblázatot** a következő `q` értékekre:
   - `0`
   - `0.1`
   - `0.2`
   - `0.3`
   - `0.4`
   - `0.5`
5. A beadandó végén 4–6 mondatban értelmezd az eredményt.

### Megadott alapmodell (WebPPL)

```javascript
var occupations = ['könyvtáros', 'tanár'];
var adjectives = ['csendes', 'cserfes'];
var operators = [' és ', ' vagy '];

// A csendes/cserfes felcserélése q valószínűséggel
var flipAdjective = function(word, q) {
  if (word !== 'csendes' && word !== 'cserfes') {
    return word;
  }
  return flip(q)
    ? (word === 'csendes' ? 'cserfes' : 'csendes')
    : word;
};

// Véletlen premissza + véletlen konklúzió
var sampleItem = function() {
  return {
    occupation: uniformDraw(occupations),
    op: uniformDraw(operators),
    premiseAdj: uniformDraw(adjectives),
    conclusionAdj: uniformDraw(adjectives)
  };
};

// A hibátlan klasszikus logika ítélete
var goldValidity = function(item) {
  if (item.op === ' és ') {
    return item.premiseAdj === item.conclusionAdj;
  } else {
    return false;
  }
};

// A diszkalkuliás ágens ítélete
var agentValidity = function(item, q) {
  var internalPremiseAdj = flipAdjective(item.premiseAdj, q);
  var internalConclusionAdj = flipAdjective(item.conclusionAdj, q);

  if (item.op === ' és ') {
    return internalPremiseAdj === internalConclusionAdj;
  } else {
    return false;
  }
};

// Egyetlen próba
var baseModel = function(q) {
  var item = sampleItem();
  var gold = goldValidity(item);
  var agent = agentValidity(item, q);

  return {
    item: item,
    gold: gold,
    agent: agent,
    correct: (gold === agent)
  };
};

// Segédfüggvény: P(true)
var probTrue = function(dist) {
  return Math.exp(dist.score(true));
};

// Összesített pontosság egy adott q mellett
var overallAccuracy = function(q) {
  var dist = Infer({
    method: 'enumerate',
    model: function() {
      return baseModel(q).correct;
    }
  });
  return probTrue(dist);
};

// Minta-futtatás
print('Összesített pontosság q = 0.2 esetén:');
print(overallAccuracy(0.2));

// ------------------------------------------------------------------
// FELADAT:
// 1. Írj a fenti overallAccuracy mintájára olyan függvényt,
//    amely csak azokra az esetekre számol pontosságot,
//    ahol a premissza operátora ' és '.
// 2. Írj egy másik függvényt a ' vagy ' operátorra.
// 3. Írasd ki a 0, 0.1, 0.2, 0.3, 0.4, 0.5 q-értékekhez
//    az összesített, az 'és'-re feltételes és a 'vagy'-ra feltételes
//    pontosságot.
// ------------------------------------------------------------------
```
