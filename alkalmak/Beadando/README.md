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
