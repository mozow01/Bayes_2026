# 1.

## 1. Közepes feladat – Diszkalkuliás ítéletmodell

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
## 1. Nehezebb feladat – Kettős torzítású ágens

Ebben a feladatban egy olyan ágenst modellezünk, amelyik egyszerű következtetéseket próbál megítélni, de **kétféleképpen is hibázhat**.

### A modell lényege

A program véletlenszerűen generál egy ilyen alakú premisszát:

- `Panni könyvtáros/tanár és/vagy csendes/cserfes.`

Ezután generál egy atomi konklúziót az alábbi négy lehetőség egyikeként:

- `Tehát Panni könyvtáros.`
- `Tehát Panni tanár.`
- `Tehát Panni csendes.`
- `Tehát Panni cserfes.`

### A hibátlan klasszikus logika szerint

- ha a premisszában `és` szerepel, akkor a konklúzió pontosan akkor **érvényes**, ha a konklúzió a premissza egyik részét ismétli meg;
- ha a premisszában `vagy` szerepel, akkor az ilyen atomi konklúziót ebben a modellben **nem tekintjük érvényesnek**.

### Az ágens két hibája

1. **Tulajdonságcsere:**  
   a `csendes` és `cserfes` szavakat `q` valószínűséggel felcseréli.

2. **Operátorhiba:**  
   a premisszában szereplő logikai kapcsolatot nem mindig olvassa helyesen:
   - ha a leírt kapcsolat `és`, akkor az ágens ezt `0.95` valószínűséggel helyesen `és`-nek, `0.05` valószínűséggel hibásan `vagy`-nak olvassa;
   - ha a leírt kapcsolat `vagy`, akkor az ágens ezt `0.20` valószínűséggel helyesen `vagy`-nak, `0.80` valószínűséggel hibásan `és`-nek olvassa.

### Mit jelent a helyes ítélet?

Azt mondjuk, hogy az ágens **helyesen ítél**, ha ugyanazt mondja, mint a hibátlan klasszikus logika:

- vagy mindkettő szerint **érvényes** a konklúzió,
- vagy mindkettő szerint **nem érvényes**.

### Feladatok

1. Futtasd a megadott alapprogramot.
2. Írj egy `pontossagTeljes(q)` függvényt, amely megadja annak valószínűségét, hogy az ágens helyesen ítél.
3. Írj külön két függvényt:
   - `pontossagEs(q)` – csak azokra az esetekre, amikor a **leírt** premisszában `és` szerepel;
   - `pontossagVagy(q)` – csak azokra az esetekre, amikor a **leírt** premisszában `vagy` szerepel.
4. Készíts egy egyszerűbb modellt is, amelyben **nincs operátorhiba**, tehát az ágens az `és`/`vagy` kapcsolatot mindig helyesen olvassa, és csak a `csendes`/`cserfes` csere marad meg.
5. Írj ehhez is egy `pontossagEgyszeru(q)` függvényt.
6. A következő `q` értékekre írass ki egy táblázatot `print` segítségével:
   - `0`
   - `0.1`
   - `0.2`
   - `0.3`
   - `0.4`
   - `0.5`
7. Minden `q` értéknél írass ki:
   - `pontossagTeljes(q)`
   - `pontossagEs(q)`
   - `pontossagVagy(q)`
   - `pontossagEgyszeru(q)`
8. A beadandó végén röviden válaszolj az alábbi kérdésekre:
   - Melyik hibatípus ront többet a teljesítményen?
   - Az `és`-es vagy a `vagy`-os feladatoknál nagyobb a romlás?
   - Miért?

### Megadott alapprogram (WebPPL)

```javascript
// ------------------------------
// 1. Alapadatok
// ------------------------------

var foglalkozasok = ['könyvtáros', 'tanár']
var tulajdonsagok = ['csendes', 'cserfes']
var kapcsolatok = ['és', 'vagy']


// ------------------------------
// 2. Segédfüggvények
// ------------------------------

// q valószínűséggel felcseréli a csendes/cserfes szót
var csereTulajdonsag = function(szo, q) {
  return flip(q) ?
    (szo === 'csendes' ? 'cserfes' : 'csendes') :
    szo
}

// Az ágens belsőleg hogyan olvassa az operátort
var olvasKapcsolat = function(kapcsolat) {
  if (kapcsolat === 'és') {
    return flip(0.05) ? 'vagy' : 'és'
  } else {
    return flip(0.80) ? 'és' : 'vagy'
  }
}

// Véletlen konklúzió generálása
var sorsolKonkluzio = function() {
  return flip(0.5) ?
    {tipus: 'foglalkozas', szo: uniformDraw(foglalkozasok)} :
    {tipus: 'tulajdonsag', szo: uniformDraw(tulajdonsagok)}
}

// Véletlen feladat generálása
var sorsolFeladat = function() {
  return {
    foglalkozas: uniformDraw(foglalkozasok),
    kapcsolat: uniformDraw(kapcsolatok),
    tulajdonsag: uniformDraw(tulajdonsagok),
    konkluzio: sorsolKonkluzio()
  }
}

// A feladat szöveges alakja
var feladatSzoveg = function(f) {
  return 'Panni ' + f.foglalkozas + ' ' + f.kapcsolat + ' ' +
         f.tulajdonsag + '. Tehát Panni ' + f.konkluzio.szo + '.'
}

// Szöveg az ítélethez
var iteletSzoveg = function(x) {
  return x ? 'érvényes' : 'nem érvényes'
}

// Szöveg a konklúzióhoz
var konkluzioSzoveg = function(k) {
  return 'Tehát Panni ' + k.szo + '.'
}


// ------------------------------
// 3. Helyes logikai ítélet
// ------------------------------

var logikaSzerint = function(f) {
  if (f.kapcsolat === 'vagy') {
    return false
  }

  if (f.konkluzio.tipus === 'foglalkozas') {
    return f.konkluzio.szo === f.foglalkozas
  } else {
    return f.konkluzio.szo === f.tulajdonsag
  }
}


// ------------------------------
// 4. A teljes, kettős hibás ágens ítélete
// ------------------------------

var agensSzerintTeljes = function(f, q) {
  var belsoKapcsolat = olvasKapcsolat(f.kapcsolat)
  var belsoTulajdonsag = csereTulajdonsag(f.tulajdonsag, q)

  var belsoKonkluzio =
    (f.konkluzio.tipus === 'tulajdonsag') ?
      {
        tipus: 'tulajdonsag',
        szo: csereTulajdonsag(f.konkluzio.szo, q)
      } :
      f.konkluzio

  if (belsoKapcsolat === 'vagy') {
    return false
  }

  if (belsoKonkluzio.tipus === 'foglalkozas') {
    return belsoKonkluzio.szo === f.foglalkozas
  } else {
    return belsoKonkluzio.szo === belsoTulajdonsag
  }
}


// ------------------------------
// 5. Az egyszerűbb ágens ítélete
//    (csak tulajdonságcsere van,
//     operátorhiba nincs)
// ------------------------------

var agensSzerintEgyszeru = function(f, q) {
  var belsoKapcsolat = f.kapcsolat
  var belsoTulajdonsag = csereTulajdonsag(f.tulajdonsag, q)

  var belsoKonkluzio =
    (f.konkluzio.tipus === 'tulajdonsag') ?
      {
        tipus: 'tulajdonsag',
        szo: csereTulajdonsag(f.konkluzio.szo, q)
      } :
      f.konkluzio

  if (belsoKapcsolat === 'vagy') {
    return false
  }

  if (belsoKonkluzio.tipus === 'foglalkozas') {
    return belsoKonkluzio.szo === f.foglalkozas
  } else {
    return belsoKonkluzio.szo === belsoTulajdonsag
  }
}


// ------------------------------
// 6. Egyetlen futás részletesen
// ------------------------------

var egyFutasTeljes = function(q) {
  var f = sorsolFeladat()

  var belsoKapcsolat = olvasKapcsolat(f.kapcsolat)
  var belsoTulajdonsag = csereTulajdonsag(f.tulajdonsag, q)

  var belsoKonkluzio =
    (f.konkluzio.tipus === 'tulajdonsag') ?
      {
        tipus: 'tulajdonsag',
        szo: csereTulajdonsag(f.konkluzio.szo, q)
      } :
      f.konkluzio

  var helyesItelet = logikaSzerint(f)
  var agensItelet = agensSzerintTeljes(f, q)
  var joValasz = (helyesItelet === agensItelet)

  return {
    feladat: feladatSzoveg(f),
    leirtKapcsolat: f.kapcsolat,
    agensKapcsolata: belsoKapcsolat,
    agensFejeben:
      'Panni ' + f.foglalkozas + ' ' + belsoKapcsolat + ' ' +
      belsoTulajdonsag + '. Tehát Panni ' + belsoKonkluzio.szo + '.',
    logika: iteletSzoveg(helyesItelet),
    agens: iteletSzoveg(agensItelet),
    joValasz: joValasz
  }
}


// ------------------------------
// 7. Mit ad vissza a modell?
// ------------------------------

// Milyen feladatokat sorsol a program?
viz(Infer({
  method: 'enumerate',
  model: function() {
    return feladatSzoveg(sorsolFeladat())
  }
}))

// Ha a leírt kapcsolat 'és', az ágens hogyan olvassa?
viz(Infer({
  method: 'enumerate',
  model: function() {
    return olvasKapcsolat('és')
  }
}))

// Ha a leírt kapcsolat 'vagy', az ágens hogyan olvassa?
viz(Infer({
  method: 'enumerate',
  model: function() {
    return olvasKapcsolat('vagy')
  }
}))

// Egy fix q mellett milyen ítéletet ad a teljes modell?
viz(Infer({
  method: 'enumerate',
  model: function() {
    return iteletSzoveg(agensSzerintTeljes(sorsolFeladat(), 0.2))
  }
}))

// Egy fix q mellett milyen gyakran helyes a teljes modell?
viz(Infer({
  method: 'enumerate',
  model: function() {
    return egyFutasTeljes(0.2).joValasz ? 'helyes' : 'hibás'
  }
}))

// Egy fix q mellett együtt a két ítélet
viz(Infer({
  method: 'enumerate',
  model: function() {
    var e = egyFutasTeljes(0.2)
    return 'logika: ' + e.logika + ' | ágens: ' + e.agens
  }
}))

// Egy fix q mellett az is látszódjon,
// hogy a leírt és a belső kapcsolat eltérhet
viz(Infer({
  method: 'enumerate',
  model: function() {
    var e = egyFutasTeljes(0.2)
    return 'leírt kapcsolat: ' + e.leirtKapcsolat +
           ' | ágens szerint: ' + e.agensKapcsolata
  }
}))


// ------------------------------
// 8. Segédfüggvény szám kiolvasásához
// ------------------------------

var igazValoszinuseg = function(eloszlas) {
  return Math.exp(eloszlas.score(true))
}


// ------------------------------
// 9. Ezt kell majd a hallgatóknak megírni
// ------------------------------

// Összesített pontosság a teljes modellben
var pontossagTeljes = function(q) {
  var eloszlas = Infer({
    method: 'enumerate',
    model: function() {
      return egyFutasTeljes(q).joValasz
    }
  })

  return igazValoszinuseg(eloszlas)
}

// Összesített pontosság az egyszerű modellben
var pontossagEgyszeru = function(q) {
  var eloszlas = Infer({
    method: 'enumerate',
    model: function() {
      var f = sorsolFeladat()
      var helyesItelet = logikaSzerint(f)
      var agensItelet = agensSzerintEgyszeru(f, q)
      return helyesItelet === agensItelet
    }
  })

  return igazValoszinuseg(eloszlas)
}


// ------------------------------
// 10. Minta futtatás
// ------------------------------

print('Teljes modell pontossága q = 0.2 esetén:')
print(pontossagTeljes(0.2))

print('Egyszerű modell pontossága q = 0.2 esetén:')
print(pontossagEgyszeru(0.2))


// ------------------------------
// 11. HALLGATÓI FELADAT
// ------------------------------
//
// 1. Írd meg a pontossagEs(q) függvényt úgy,
//    hogy csak azokat az eseteket vegye figyelembe,
//    ahol a LEÍRT premisszában a kapcsolat 'és'.
//
// 2. Írd meg a pontossagVagy(q) függvényt úgy,
//    hogy csak azokat az eseteket vegye figyelembe,
//    ahol a LEÍRT premisszában a kapcsolat 'vagy'.
//
// 3. Írasd ki print segítségével a következő q értékekre:
//    0, 0.1, 0.2, 0.3, 0.4, 0.5
//
//    - pontossagTeljes(q)
//    - pontossagEs(q)
//    - pontossagVagy(q)
//    - pontossagEgyszeru(q)
//
// 4. Röviden értelmezd, hogy
//    - mennyit ront az operátorhiba,
//    - melyik kapcsolattípusnál nagyobb a különbség,
//    - és miért.
//
```
# 2.

## 2. Közepes feladat – Bayes-féle modellösszevetés Csofival

Ebben a feladatban egy törpehörcsög, Csofi súlyát modellezzük.

### A modell lényege

Két lehetséges magyarázatunk van:

- **1. modell:** Csofi egészséges  
- **2. modell:** Csofi beteg

A két modellhez a következő feltevéseket használjuk:

- ha Csofi egészséges, akkor a valódi súlya egy `Gauss(22,1)` eloszlásból jön;
- ha Csofi beteg, akkor a valódi súlya egy `Gauss(17,1)` eloszlásból jön.

A mérések:

- `19`
- `18`
- `18`

A cél az, hogy a mért adatok alapján eldöntsük, melyik modell valószínűbb. Ez közvetlenül a 2026-os 2. alkalom Csofi-példájára épül. :contentReference[oaicite:2]{index=2}

### Mit jelent itt a modell?

A modellben háromféle dolgot érdemes külön nézni:

1. **Melyik modellt választotta a világ?**  
   Ez egy diszkrét változó: `1 = egészséges`, `2 = beteg`.

2. **Mennyi Csofi valódi súlya?**  
   Ez egy folytonos, bizonytalan mennyiség.

3. **Mi lehet a következő mérés eredménye?**  
   Ez is egy eloszlás lesz, mert a mérés zajos.

### Feladatok

1. Futtasd a megadott alapprogramot.
2. Írj egy `pEgeszseges()` függvényt, amely megadja az egészséges modell poszterior valószínűségét.
3. Írj egy `pBeteg()` függvényt, amely megadja a beteg modell poszterior valószínűségét.
4. Írj egy `atlagosSuly()` függvényt, amely a valódi súly poszterior várható értékét adja meg.
5. Készíts egy rövid szöveges összefoglalót a futás végén `print` segítségével:
   - melyik modell lett valószínűbb,
   - mekkora az egészséges modell valószínűsége,
   - mekkora a beteg modell valószínűsége,
   - mennyi a valódi súly becsült átlaga.
6. A beadandó végén 1–2 mondatban értelmezd az eredményt.

### Megadott alapprogram (WebPPL)

```javascript
// ------------------------------
// 1. Adatok
// ------------------------------

var meresek = [19, 18, 18];

// 1 = egészséges, 2 = beteg
var modellNevek = function(i) {
  return i === 1 ? 'egészséges' : 'beteg';
};


// ------------------------------
// 2. Prior modellek külön
// ------------------------------

// Egészséges modell priorja
var priorEgeszseges = Infer({
  method: 'forward',
  samples: 4000,
  model: function() {
    var suly = gaussian(22, 1);
    return {modell: 'egészséges', suly: suly};
  }
});

// Beteg modell priorja
var priorBeteg = Infer({
  method: 'forward',
  samples: 4000,
  model: function() {
    var suly = gaussian(17, 1);
    return {modell: 'beteg', suly: suly};
  }
});

viz(priorEgeszseges);
print('Egészséges prior: a valódi súly eloszlása');

viz(priorBeteg);
print('Beteg prior: a valódi súly eloszlása');


// ------------------------------
// 3. A teljes Bayes-modell
// ------------------------------

var teljesModell = function() {
  // Kezdetben 50-50% esélyt adunk a két modellnek
  var modell = categorical({
    vs: [1, 2],
    ps: [0.5, 0.5]
  });

  // A választott modellhez tartozó valódi súly
  var suly = (modell === 1) ? gaussian(22, 1) : gaussian(17, 1);

  // A mérések zajos megfigyelések a valódi súly körül
  map(function(x) {
    observe(Gaussian({mu: suly, sigma: 1}), x);
  }, meresek);

  // Következő mérés predikciója
  var kovetkezoMeres = sample(Gaussian({mu: suly, sigma: 1}));

  return {
    modell: modellNevek(modell),
    suly: suly,
    kovetkezoMeres: kovetkezoMeres
  };
};


// ------------------------------
// 4. Poszterior inferencia
// ------------------------------

var poszterior = Infer({
  method: 'SMC',
  particles: 3000,
  rejuvSteps: 5,
  model: teljesModell
});


// ------------------------------
// 5. Mit ad vissza a modell?
// ------------------------------

// A modellválasztás poszteriorja
viz(Infer({
  method: 'SMC',
  particles: 3000,
  rejuvSteps: 5,
  model: function() {
    return teljesModell().modell;
  }
}));
print('Poszterior a modellekre');

// A valódi súly poszteriorja
viz(Infer({
  method: 'SMC',
  particles: 3000,
  rejuvSteps: 5,
  model: function() {
    return teljesModell().suly;
  }
}));
print('Poszterior a valódi súlyra');

// A következő mérés prediktív eloszlása
viz(Infer({
  method: 'SMC',
  particles: 3000,
  rejuvSteps: 5,
  model: function() {
    return teljesModell().kovetkezoMeres;
  }
}));
print('Prediktív eloszlás a következő mérésre');


// ------------------------------
// 6. Segédfüggvények
// ------------------------------

// Diszkrét eloszlásból egy konkrét érték valószínűsége
var valoszinuseg = function(eloszlas, ertek) {
  return Math.exp(eloszlas.score(ertek));
};

// Egyszerű átlag egy mintából
var atlag = function(lista) {
  return sum(lista) / lista.length;
};


// ------------------------------
// 7. Ezt kell majd a hallgatóknak megírni
// ------------------------------

// Poszterior a modellre
var modellPoszterior = Infer({
  method: 'SMC',
  particles: 3000,
  rejuvSteps: 5,
  model: function() {
    return teljesModell().modell;
  }
});

// Poszterior a valódi súlyra
var sulyPoszterior = Infer({
  method: 'SMC',
  particles: 3000,
  rejuvSteps: 5,
  model: function() {
    return teljesModell().suly;
  }
});

// Az egészséges modell poszterior valószínűsége
var pEgeszseges = function() {
  return valoszinuseg(modellPoszterior, 'egészséges');
};

// A beteg modell poszterior valószínűsége
var pBeteg = function() {
  return valoszinuseg(modellPoszterior, 'beteg');
};

// A valódi súly poszterior várható értékének közelítése mintavétellel
var atlagosSuly = function() {
  var mintak = sample(sulyPoszterior, 3000);
  return atlag(mintak);
};


// ------------------------------
// 8. Minta futtatás
// ------------------------------

print('A mért adatok:');
print(meresek);

print('A mért adatok átlaga:');
print(atlag(meresek));

print('Egészséges modell poszterior valószínűsége:');
print(pEgeszseges());

print('Beteg modell poszterior valószínűsége:');
print(pBeteg());

print('A valódi súly becsült poszterior átlaga:');
print(atlagosSuly());


// ------------------------------
// 9. HALLGATÓI FELADAT
// ------------------------------
//
// 1. Futtasd a programot.
//
// 2. Ellenőrizd, hogy a priorokból tényleg az látszik-e,
//    amit a modell állít:
//    - az egészséges állat súlya 22 körül van,
//    - a beteg állat súlya 17 körül van.
//
// 3. Nézd meg a modellPoszterior eloszlást,
//    és döntsd el, melyik modell lett valószínűbb.
//
// 4. Írd meg vagy egészítsd ki a következő függvényeket:
//    - pEgeszseges()
//    - pBeteg()
//    - atlagosSuly()
//
// 5. A futás végén írd ki print segítségével:
//    - pEgeszseges()
//    - pBeteg()
//    - atlagosSuly()
//
// 6. Röviden értelmezd az eredményt:
//    - melyik modell nyert,
//    - mennyire egyértelmű a döntés,
//    - hogyan viszonyul a becsült valódi súly
//      a 22 g-os és a 17 g-os feltevéshez.
//
```

## 2. Nehezebb feladat – Beteg hörcsög vagy hibás mérleg?

Ebben a feladatban tovább bővítjük a Csofi-modellt.

### A modell lényege

Három lehetséges magyarázatunk van a mért adatokra:

- **1. modell:** Csofi egészséges, és a mérleg jól mér.
- **2. modell:** Csofi beteg, és a mérleg jól mér.
- **3. modell:** Csofi egészséges, de a mérleg lefelé csal.

A három modellhez a következő feltevéseket használjuk:

- ha Csofi **egészséges**, akkor a valódi súlya `Gauss(22,1)` eloszlásból jön;
- ha Csofi **beteg**, akkor a valódi súlya `Gauss(17,1)` eloszlásból jön;
- ha a mérleg **lefelé csal**, akkor van egy `eltolas` paraméter, amely `Gauss(-3,0.5)` eloszlásból jön.

A mérési modell:

- minden mérés a `valodiSuly + eltolas` körül ingadozik, `sigma = 1` szórással.

A mérések:

- `19`
- `18`
- `18`

### Mit érdemes külön nézni?

A modellben többféle bizonytalan mennyiség van:

1. **Melyik modell igaz?**
   - egészséges
   - beteg
   - hibás mérleg

2. **Mennyi Csofi valódi súlya?**

3. **Mekkora a mérleg eltolása?**
   - az első két modellben ez `0`
   - a harmadik modellben ez általában negatív

4. **Mi lehet a következő mérés eredménye?**

### Feladatok

1. Futtasd a megadott alapprogramot.
2. Írj egy `pEgeszsegesJol()` függvényt, amely megadja az 1. modell poszterior valószínűségét.
3. Írj egy `pBetegJol()` függvényt, amely megadja a 2. modell poszterior valószínűségét.
4. Írj egy `pHibasMerleg()` függvényt, amely megadja a 3. modell poszterior valószínűségét.
5. Írj egy `atlagosSuly()` függvényt, amely a valódi súly poszterior várható értékét adja meg.
6. Írj egy `atlagosEltolas()` függvényt, amely a mérlegeltolás poszterior várható értékét adja meg.
7. A futás végén írass ki `print` segítségével egy rövid összefoglalót:
   - melyik modell a legvalószínűbb,
   - mekkora az egyes modellek valószínűsége,
   - mennyi a valódi súly becsült átlaga,
   - mennyi a mérlegeltolás becsült átlaga.
8. A beadandó végén 1-2 mondatban értelmezd az eredményt:
   - inkább betegségre utalnak-e az adatok,
   - vagy inkább mérési hibára,
   - és miért.

### Megadott alapprogram (WebPPL)

```javascript
// ------------------------------
// 1. Adatok
// ------------------------------

var meresek = [19, 18, 18]


// ------------------------------
// 2. A modellek nevei
// ------------------------------

var modellNev = function(m) {
  if (m === 1) {
    return 'egeszseges_es_jol_mer'
  } else if (m === 2) {
    return 'beteg_es_jol_mer'
  } else {
    return 'egeszseges_de_hibas_merleg'
  }
}


// ------------------------------
// 3. Priorok külön megmutatva
// ------------------------------

// Egészséges állat prior súlya
viz(Infer({
  method: 'forward',
  samples: 4000,
  model: function() {
    return gaussian(22, 1)
  }
}))
print('Prior a valódi súlyra, ha Csofi egészséges')

// Beteg állat prior súlya
viz(Infer({
  method: 'forward',
  samples: 4000,
  model: function() {
    return gaussian(17, 1)
  }
}))
print('Prior a valódi súlyra, ha Csofi beteg')

// Hibás mérleg prior eltérése
viz(Infer({
  method: 'forward',
  samples: 4000,
  model: function() {
    return gaussian(-3, 0.5)
  }
}))
print('Prior a mérleg eltolására, ha a mérleg lefelé csal')


// ------------------------------
// 4. A teljes Bayes-modell
// ------------------------------

var teljesModell = function() {
  // Kezdetben mindhárom modell egyformán valószínű
  var modell = categorical({
    vs: [1, 2, 3],
    ps: [1/3, 1/3, 1/3]
  })

  // Valódi súly
  var valodiSuly =
    (modell === 1) ? gaussian(22, 1) :
    (modell === 2) ? gaussian(17, 1) :
                     gaussian(22, 1)

  // Mérlegeltolás
  var eltolas =
    (modell === 3) ? gaussian(-3, 0.5) : 0

  // Megfigyelt mérések
  map(function(x) {
    observe(Gaussian({mu: valodiSuly + eltolas, sigma: 1}), x)
  }, meresek)

  // Következő mérés predikciója
  var kovetkezoMeres = sample(Gaussian({mu: valodiSuly + eltolas, sigma: 1}))

  return {
    modell: modellNev(modell),
    valodiSuly: valodiSuly,
    eltolas: eltolas,
    kovetkezoMeres: kovetkezoMeres
  }
}


// ------------------------------
// 5. Mit ad vissza a modell?
// ------------------------------

// A modellválasztás poszteriorja
viz(Infer({
  method: 'SMC',
  particles: 4000,
  rejuvSteps: 5,
  model: function() {
    return teljesModell().modell
  }
}))
print('Poszterior a modellekre')

// A valódi súly poszteriorja
viz(Infer({
  method: 'SMC',
  particles: 4000,
  rejuvSteps: 5,
  model: function() {
    return teljesModell().valodiSuly
  }
}))
print('Poszterior a valódi súlyra')

// A mérlegeltolás poszteriorja
viz(Infer({
  method: 'SMC',
  particles: 4000,
  rejuvSteps: 5,
  model: function() {
    return teljesModell().eltolas
  }
}))
print('Poszterior a mérleg eltolására')

// A következő mérés prediktív eloszlása
viz(Infer({
  method: 'SMC',
  particles: 4000,
  rejuvSteps: 5,
  model: function() {
    return teljesModell().kovetkezoMeres
  }
}))
print('Prediktív eloszlás a következő mérésre')


// ------------------------------
// 6. Segédfüggvények
// ------------------------------

// Diszkrét eloszlásból egy adott érték valószínűsége
var valoszinuseg = function(eloszlas, ertek) {
  return Math.exp(eloszlas.score(ertek))
}

// Eloszlás várható értéke
var atlagEloszlas = function(eloszlas) {
  return expectation(eloszlas, function(x) { return x })
}


// ------------------------------
// 7. Poszterior eloszlások
// ------------------------------

var modellPoszterior = Infer({
  method: 'SMC',
  particles: 4000,
  rejuvSteps: 5,
  model: function() {
    return teljesModell().modell
  }
})

var sulyPoszterior = Infer({
  method: 'SMC',
  particles: 4000,
  rejuvSteps: 5,
  model: function() {
    return teljesModell().valodiSuly
  }
})

var eltolasPoszterior = Infer({
  method: 'SMC',
  particles: 4000,
  rejuvSteps: 5,
  model: function() {
    return teljesModell().eltolas
  }
})


// ------------------------------
// 8. Ezt kell majd a hallgatóknak megírni
// ------------------------------

// 1. modell: egészséges és jól mér
var pEgeszsegesJol = function() {
  return valoszinuseg(modellPoszterior, 'egeszseges_es_jol_mer')
}

// 2. modell: beteg és jól mér
var pBetegJol = function() {
  return valoszinuseg(modellPoszterior, 'beteg_es_jol_mer')
}

// 3. modell: egészséges, de hibás a mérleg
var pHibasMerleg = function() {
  return valoszinuseg(modellPoszterior, 'egeszseges_de_hibas_merleg')
}

// A valódi súly poszterior várható értéke
var atlagosSuly = function() {
  return atlagEloszlas(sulyPoszterior)
}

// A mérlegeltolás poszterior várható értéke
var atlagosEltolas = function() {
  return atlagEloszlas(eltolasPoszterior)
}


// ------------------------------
// 9. Minta futtatás
// ------------------------------

print('A mért adatok:')
print(meresek)

print('Az 1. modell valószínűsége (egészséges és jól mér):')
print(pEgeszsegesJol())

print('A 2. modell valószínűsége (beteg és jól mér):')
print(pBetegJol())

print('A 3. modell valószínűsége (egészséges, de hibás mérleg):')
print(pHibasMerleg())

print('A valódi súly becsült poszterior átlaga:')
print(atlagosSuly())

print('A mérlegeltolás becsült poszterior átlaga:')
print(atlagosEltolas())


// ------------------------------
// 10. HALLGATÓI FELADAT
// ------------------------------
//
// 1. Futtasd a programot.
//
// 2. Nézd meg a priorokat:
//    - egészséges állat súlya,
//    - beteg állat súlya,
//    - hibás mérleg eltolása.
//
// 3. Nézd meg a modellPoszterior eloszlást,
//    és döntsd el, melyik modell lett a legvalószínűbb.
//
// 4. Írd meg vagy ellenőrizd a következő függvényeket:
//    - pEgeszsegesJol()
//    - pBetegJol()
//    - pHibasMerleg()
//    - atlagosSuly()
//    - atlagosEltolas()
//
// 5. A futás végén írd ki print segítségével:
//    - pEgeszsegesJol()
//    - pBetegJol()
//    - pHibasMerleg()
//    - atlagosSuly()
//    - atlagosEltolas()
//
// 6. Röviden értelmezd az eredményt:
//    - az adatok inkább betegséget támogatnak-e,
//    - vagy inkább hibás mérleget,
//    - és mennyire marad esélye annak,
//      hogy Csofi egészséges és a mérleg is jó.
//
```

# 3. 

## 3. Közepes feladat

Írj programot, amelyik kiszámolja, hogy mi annak a valószínűsége, hogy 52 lapos francia kártyából 2 kártyát választva az egyik király, a másik nem király!

## 3 Nehezebb feladat – Monty Hall kémmel

Ebben a feladatban a klasszikus Monty Hall-probléma egy módosított változatát modellezzük.

### A modell lényege

Három ajtó van:

- az egyik mögött autó van,
- a másik kettő mögött kecske.

A játék menete:

1. A játékos kiválaszt egy ajtót.
2. Monty kinyit egy másik ajtót úgy, hogy:
   - azt az ajtót a játékos nem választotta,
   - és biztosan nincs mögötte autó.
3. Ezután két csukott ajtó marad:
   - a játékos eredeti ajtaja,
   - és még egy másik csukott ajtó.
4. Ekkor megjelenik egy kém, aki a két csukott ajtó egyikére mutat.
   - `p` valószínűséggel a nyerő ajtóra mutat,
   - `1-p` valószínűséggel a vesztes ajtóra mutat.

### Mit jelent itt a kém tanácsa?

A kém Monty ajtónyitása után csak a **két bent maradó csukott ajtó** egyikére mutat:

- vagy a játékos eredeti ajtajára,
- vagy a másik bent maradó ajtóra.

### A vizsgált stratégiák

Három stratégiát hasonlítunk össze:

1. **Marad**  
   A játékos mindig megtartja az eredeti választását.

2. **Vált**  
   A játékos mindig átmegy a másik bent maradó ajtóra, függetlenül attól, mit mutat a kém.

3. **Követi a kémet**  
   A játékos azt az ajtót választja, amelyikre a kém mutat.

### A feladat célja

A cél nem az, hogy kézzel számoljuk végig az eseteket, hanem az, hogy a helyzetet generatív modellként programozzuk le, és a modellből olvassuk ki:

- a három stratégia nyerési valószínűségét,
- valamint azt is, hogy a kém tanácsa után mikor érdemes maradni és mikor érdemes váltani.

### Feladatok

1. Futtasd a megadott alapprogramot.
2. Írj egy `pMarad(p)` függvényt, amely megadja a **maradás** nyerési valószínűségét.
3. Írj egy `pValt(p)` függvényt, amely megadja a **váltás** nyerési valószínűségét.
4. Írj egy `pKemKovet(p)` függvényt, amely megadja a **kém követésének** nyerési valószínűségét.
5. Írj egy `pEredetiJoHaKemSajatra(p)` függvényt, amely megadja annak valószínűségét, hogy az **eredeti választás** a nyerő ajtó, feltéve hogy a kém a játékos eredeti ajtajára mutatott.
6. Írj egy `pMasikJoHaKemMasikra(p)` függvényt, amely megadja annak valószínűségét, hogy a **másik bent maradó ajtó** a nyerő, feltéve hogy a kém a másik bent maradó ajtóra mutatott.
7. `print` segítségével készíts táblázatot a következő `p` értékekre:
   - `0`
   - `0.1`
   - `0.2`
   - `0.3`
   - `0.4`
   - `0.5`
   - `0.6`
   - `0.7`
   - `0.8`
   - `0.9`
   - `1.0`
8. Minden `p` értéknél írass ki:
   - `pMarad(p)`
   - `pValt(p)`
   - `pKemKovet(p)`
   - `pEredetiJoHaKemSajatra(p)`
   - `pMasikJoHaKemMasikra(p)`
9. A beadandó végén röviden válaszolj az alábbi kérdésekre:
   - Melyik `p` értéknél lesz a **kém követése** jobb stratégia, mint a **mindig váltás**?
   - Ha a kém a **saját eredeti ajtódra** mutat, mely `p` értékektől érdemes inkább **maradni**, mint váltani?
   - Ha a kém a **másik bent maradó ajtóra** mutat, mely `p` értékektől érdemes inkább **váltani**, mint maradni?

### Megadott alapprogram (WebPPL)

```javascript
// ------------------------------
// 1. Alapadatok
// ------------------------------

var ajtok = [1, 2, 3]


// ------------------------------
// 2. Segédfüggvények
// ------------------------------

var veletlenAjto = function() {
  return uniformDraw(ajtok)
}

var ajtoSzoveg = function(a) {
  return 'ajtó ' + a
}

var maradoAjtok = function(valasztas, monty) {
  return filter(function(a) {
    return (a !== valasztas) && (a !== monty)
  }, ajtok)
}


// ------------------------------
// 3. Monty lépése
// ------------------------------

var montyNyithat = function(valasztas, nyeroAjto) {
  return filter(function(a) {
    return (a !== valasztas) && (a !== nyeroAjto)
  }, ajtok)
}

var montyNyit = function(valasztas, nyeroAjto) {
  return uniformDraw(montyNyithat(valasztas, nyeroAjto))
}


// ------------------------------
// 4. A másik bent maradó ajtó
// ------------------------------

var masikBentMaradoAjto = function(valasztas, monty) {
  return uniformDraw(maradoAjtok(valasztas, monty))
}


// ------------------------------
// 5. A kém tanácsa
// ------------------------------

var kemMutat = function(valasztas, masikAjto, nyeroAjto, p) {
  var nyeroBentMaradoAjto =
    (valasztas === nyeroAjto) ? valasztas : masikAjto

  var vesztesBentMaradoAjto =
    (valasztas === nyeroAjto) ? masikAjto : valasztas

  return flip(p) ? nyeroBentMaradoAjto : vesztesBentMaradoAjto
}


// ------------------------------
// 6. Egyetlen játék
// ------------------------------

var egyJatek = function(p) {
  var nyeroAjto = veletlenAjto()
  var valasztas = veletlenAjto()
  var monty = montyNyit(valasztas, nyeroAjto)
  var masikAjto = masikBentMaradoAjto(valasztas, monty)
  var kemAjto = kemMutat(valasztas, masikAjto, nyeroAjto, p)

  return {
    nyeroAjto: nyeroAjto,
    valasztas: valasztas,
    monty: monty,
    masikAjto: masikAjto,
    kemAjto: kemAjto,

    kemSajatAjtoraMutat: kemAjto === valasztas,
    kemMasikAjtoraMutat: kemAjto === masikAjto,

    maradNyer: valasztas === nyeroAjto,
    valtNyer: masikAjto === nyeroAjto,
    kemetKovetNyer: kemAjto === nyeroAjto
  }
}


// ------------------------------
// 7. Mit ad vissza a modell?
// ------------------------------

// A klasszikus helyzet: az eredeti választás jó-e?
viz(Infer({
  method: 'enumerate',
  model: function() {
    var j = egyJatek(0.7)
    return j.maradNyer ? 'az eredeti választás nyerő' : 'az eredeti választás nem nyerő'
  }
}))
print('A klasszikus Monty Hall-helyzet az eredeti választásról')

// A kém milyen gyakran mutat a saját ajtóra / a másik ajtóra?
viz(Infer({
  method: 'enumerate',
  model: function() {
    var j = egyJatek(0.7)
    return j.kemSajatAjtoraMutat ? 'a kém a saját ajtóra mutat' : 'a kém a másik ajtóra mutat'
  }
}))
print('A kém mutatása p = 0.7 mellett')

// Együtt is nézzük: hová mutat a kém, és jó volt-e az eredeti választás?
viz(Infer({
  method: 'enumerate',
  model: function() {
    var j = egyJatek(0.7)
    var kemSzoveg = j.kemSajatAjtoraMutat ? 'kém: saját ajtó' : 'kém: másik ajtó'
    var eredetiSzoveg = j.maradNyer ? 'eredeti választás: nyerő' : 'eredeti választás: vesztes'
    return kemSzoveg + ' | ' + eredetiSzoveg
  }
}))
print('A kém jelzése és az eredeti választás viszonya p = 0.7 mellett')

// Maradási stratégia eredménye
viz(Infer({
  method: 'enumerate',
  model: function() {
    var j = egyJatek(0.7)
    return j.maradNyer ? 'maradással nyer' : 'maradással veszít'
  }
}))
print('Maradási stratégia p = 0.7 mellett')

// Váltási stratégia eredménye
viz(Infer({
  method: 'enumerate',
  model: function() {
    var j = egyJatek(0.7)
    return j.valtNyer ? 'váltással nyer' : 'váltással veszít'
  }
}))
print('Váltási stratégia p = 0.7 mellett')

// Kém követése
viz(Infer({
  method: 'enumerate',
  model: function() {
    var j = egyJatek(0.7)
    return j.kemetKovetNyer ? 'a kémet követve nyer' : 'a kémet követve veszít'
  }
}))
print('Kémkövető stratégia p = 0.7 mellett')


// ------------------------------
// 8. Segédfüggvények
// ------------------------------

var igazValoszinuseg = function(eloszlas) {
  return Math.exp(eloszlas.score(true))
}


// ------------------------------
// 9. Ezeket kell majd a hallgatóknak megírni
// ------------------------------

// A maradás nyerési valószínűsége
var pMarad = function(p) {
  var eloszlas = Infer({
    method: 'enumerate',
    model: function() {
      return egyJatek(p).maradNyer
    }
  })

  return igazValoszinuseg(eloszlas)
}

// A váltás nyerési valószínűsége
var pValt = function(p) {
  var eloszlas = Infer({
    method: 'enumerate',
    model: function() {
      return egyJatek(p).valtNyer
    }
  })

  return igazValoszinuseg(eloszlas)
}

// A kém követésének nyerési valószínűsége
var pKemKovet = function(p) {
  var eloszlas = Infer({
    method: 'enumerate',
    model: function() {
      return egyJatek(p).kemetKovetNyer
    }
  })

  return igazValoszinuseg(eloszlas)
}


// ------------------------------
// 10. Minta futtatás
// ------------------------------

print('pMarad(0.7):')
print(pMarad(0.7))

print('pValt(0.7):')
print(pValt(0.7))

print('pKemKovet(0.7):')
print(pKemKovet(0.7))


// ------------------------------
// 11. HALLGATÓI FELADAT
// ------------------------------
//
// 1. Írd meg a pEredetiJoHaKemSajatra(p) függvényt.
//    Ez azt adja meg, hogy mekkora annak a valószínűsége,
//    hogy az EREDETI választás a nyerő ajtó,
//    feltéve hogy a kém a SAJÁT eredeti ajtóra mutatott.
//
// 2. Írd meg a pMasikJoHaKemMasikra(p) függvényt.
//    Ez azt adja meg, hogy mekkora annak a valószínűsége,
//    hogy a MÁSIK bent maradó ajtó a nyerő,
//    feltéve hogy a kém a MÁSIK bent maradó ajtóra mutatott.
//
// 3. Írasd ki print segítségével a következő p értékekre:
//    0, 0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0
//
//    - pMarad(p)
//    - pValt(p)
//    - pKemKovet(p)
//    - pEredetiJoHaKemSajatra(p)
//    - pMasikJoHaKemMasikra(p)
//
// 4. Röviden értelmezd:
//
//    (a) Melyik p értéknél lesz a kém követése jobb,
//        mint a mindig váltás?
//
//    (b) Ha a kém a saját eredeti ajtódra mutat,
//        mely p értékektől érdemes inkább maradni,
//        mint váltani?
//
//    (c) Ha a kém a másik bent maradó ajtóra mutat,
//        mely p értékektől érdemes inkább váltani,
//        mint maradni?
//
```
