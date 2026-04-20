## 1. hét / Közepes feladat – Diszkalkuliás ítéletmodell

Ebben a feladatban egy olyan ágenst vizsgálunk, amely egyszerű következtetéseket próbál megítélni, de a `csendes` és `cserfes` szavakat néha összekeveri.

### A modell lényege

A program véletlenszerűen generál egy ilyen alakú feladatot:

- Premissza: `Panni könyvtáros/tanár és/vagy csendes/cserfes.`
- Konklúzió: `Tehát Panni csendes/cserfes.`

A **hibátlan klasszikus logika** szerint:

- ha a premisszában `és` szerepel, akkor a konklúzió pontosan akkor **érvényes**, ha ugyanaz a tulajdonság szerepel benne, mint a premisszában;
- ha a premisszában `vagy` szerepel, akkor az ilyen típusú konklúziót ebben a modellben **nem tekintjük érvényesnek**.

Az ágens azonban nem mindig olvassa helyesen a szavakat:

- minden `csendes` vagy `cserfes` szóelőfordulást `q` valószínűséggel felcserél a fejében.

### Mit jelent a helyes ítélet?

Azt mondjuk, hogy az ágens **helyesen ítél**, ha ugyanazt mondja, mint a hibátlan klasszikus logika:

- vagy mindkettő szerint **érvényes** a konklúzió,
- vagy mindkettő szerint **nem érvényes**.

### Feladatok

1. Futtasd a megadott programot.
2. Írj egy `pontossag(q)` függvényt, amely megadja annak valószínűségét, hogy az ágens helyesen ítél.
3. Írj külön két függvényt:
   - `pontossagEs(q)` – csak azokra az esetekre, amikor a premisszában `és` szerepel;
   - `pontossagVagy(q)` – csak azokra az esetekre, amikor a premisszában `vagy` szerepel.
4. A következő `q` értékekre írass ki egy táblázatot `print` segítségével:
   - `0`
   - `0.1`
   - `0.2`
   - `0.3`
   - `0.4`
   - `0.5`
5. A beadandó végén röviden értelmezd az eredményt.

### Megadott alapprogram (WebPPL)

```javascript
// ------------------------------
// 1. Alapadatok
// ------------------------------

var foglalkozasok = ['könyvtáros', 'tanár'];
var tulajdonsagok = ['csendes', 'cserfes'];
var kapcsolatok = ['és', 'vagy'];

// ------------------------------
// 2. Segédfüggvények
// ------------------------------

// q valószínűséggel felcseréli a csendes/cserfes szót
var csereTulajdonsag = function(szo, q) {
  return flip(q) ? 
    (szo === 'csendes' ? 'cserfes' : 'csendes') : 
    szo;
};

// Véletlen feladat generálása
var sorsolFeladat = function() {
  return {
    foglalkozas: uniformDraw(foglalkozasok),
    kapcsolat: uniformDraw(kapcsolatok),
    premisszaTulajdonsag: uniformDraw(tulajdonsagok),
    konkluzioTulajdonsag: uniformDraw(tulajdonsagok)
  };
};

// A feladat szöveges alakja
var feladatSzoveg = function(f) {
  return 'Panni ' + f.foglalkozas + ' ' + f.kapcsolat + ' ' +
         f.premisszaTulajdonsag + '. Tehát Panni ' +
         f.konkluzioTulajdonsag + '.';
};

// Szép szöveg logikai ítélethez
var iteletSzoveg = function(x) {
  return x ? 'érvényes' : 'nem érvényes';
};

// ------------------------------
// 3. Helyes logikai ítélet
// ------------------------------

var logikaSzerint = function(f) {
  if (f.kapcsolat === 'és') {
    return f.premisszaTulajdonsag === f.konkluzioTulajdonsag;
  } else {
    return false;
  }
};

// ------------------------------
// 4. A diszkalkuliás ágens ítélete
// ------------------------------

var agensSzerint = function(f, q) {
  var belsoPremissza = csereTulajdonsag(f.premisszaTulajdonsag, q);
  var belsoKonkluzio = csereTulajdonsag(f.konkluzioTulajdonsag, q);

  if (f.kapcsolat === 'és') {
    return belsoPremissza === belsoKonkluzio;
  } else {
    return false;
  }
};

// ------------------------------
// 5. Egyetlen futás részletesen
// ------------------------------

var egyFutas = function(q) {
  var f = sorsolFeladat();

  var belsoPremissza = csereTulajdonsag(f.premisszaTulajdonsag, q);
  var belsoKonkluzio = csereTulajdonsag(f.konkluzioTulajdonsag, q);

  var helyesItelet = logikaSzerint(f);

  var agensItelet = (f.kapcsolat === 'és') ?
    (belsoPremissza === belsoKonkluzio) :
    false;

  var joValasz = (helyesItelet === agensItelet);

  return {
    feladat: feladatSzoveg(f),
    agensFejeben:
      'Panni ' + f.foglalkozas + ' ' + f.kapcsolat + ' ' +
      belsoPremissza + '. Tehát Panni ' + belsoKonkluzio + '.',
    logika: iteletSzoveg(helyesItelet),
    agens: iteletSzoveg(agensItelet),
    joValasz: joValasz
  };
};

// ------------------------------
// 6. Mit ad vissza a modell?
// ------------------------------

// Milyen feladatokat sorsol a program?
viz(Infer({
  method: 'enumerate',
  model: function() {
    return feladatSzoveg(sorsolFeladat());
  }
}));

// Egy fix q mellett az ágens milyen ítéletet ad?
viz(Infer({
  method: 'enumerate',
  model: function() {
    return iteletSzoveg(agensSzerint(sorsolFeladat(), 0.2));
  }
}));

// Egy fix q mellett milyen gyakran helyes az ágens?
viz(Infer({
  method: 'enumerate',
  model: function() {
    return egyFutas(0.2).joValasz ? 'helyes' : 'hibás';
  }
}));

// Egy fix q mellett együtt is nézzük a két ítéletet
viz(Infer({
  method: 'enumerate',
  model: function() {
    var e = egyFutas(0.2);
    return 'logika: ' + e.logika + ' | ágens: ' + e.agens;
  }
}));

// ------------------------------
// 7. Segédfüggvény egy szám kiolvasásához
// ------------------------------

var igazValoszinuseg = function(eloszlas) {
  return Math.exp(eloszlas.score(true));
};

// ------------------------------
// 8. Ezt kell majd a hallgatóknak megírni
// ------------------------------

// Összesített pontosság
var pontossag = function(q) {
  var eloszlas = Infer({
    method: 'enumerate',
    model: function() {
      return egyFutas(q).joValasz;
    }
  });

  return igazValoszinuseg(eloszlas);
};

// ------------------------------
// 9. Minta futtatás
// ------------------------------

print('Összesített pontosság q = 0.2 esetén:');
print(pontossag(0.2));

// ------------------------------
// 10. HALLGATÓI FELADAT
// ------------------------------
//
// 1. Írd meg a pontossagEs(q) függvényt úgy,
//    hogy csak azokat az eseteket vegye figyelembe,
//    ahol a premisszában a kapcsolat 'és'.
//
// 2. Írd meg a pontossagVagy(q) függvényt úgy,
//    hogy csak azokat az eseteket vegye figyelembe,
//    ahol a premisszában a kapcsolat 'vagy'.
//
// 3. Írasd ki print segítségével a következő q értékekre:
//    0, 0.1, 0.2, 0.3, 0.4, 0.5
//
//    - pontossag(q)
//    - pontossagEs(q)
//    - pontossagVagy(q)
//
// 4. Röviden értelmezd, mit mutatnak az eredmények.
```
