# Kombinatorikai és valószínűségi kísérletek modellezése

**A fő kérdésünk:** Hogyan fordíthatjuk le a középiskolából ismert "kombinatorikai okoskodást" számítógépes, generatív modellekké? 

## 1. Kockadobás: a véletlen, mint függvény

<img src="https://github.com/mozow01/cog_compsci/blob/main/SciCamp/1200px-2-Dice-Icon.svg.png" width="100">

Hagyományosan a kockadobás egy fizikai kísérlet. A WebPPL-ben ezt egy **függvénnyel** helyettesítjük.

**A WebPPL modell fontos kucsszavai:**
* `var ... = ...`: Így definiálunk egy értéket vagy függvényt.
* **Üres zárójelek `()`:** A `dobas()` függvénynek nincs bemenete, mert az eredményt a beépített *véletlengenerátor* adja.
* **`categorical` eloszlás:** Megmondja, hogy mik a lehetséges értékek (`vs`), és ezek milyen valószínűséggel (`ps`) fordulnak elő.

**Eredmények megjelenítése:**
* Egy dobás kiírása: `print(dobás());`
* Sok dobás (pl. 10) generálása: `var sokdobás = repeat(10, dobás);`
* Vizualizáció: `viz.auto(sokdobás);` 

**Hasznos linkek:** Vizualizáció: http://probmods.github.io/webppl-viz/

````javascript
// Generatív modell: két kocka dobása
var dobás = function () {
  var kocka1 = categorical({ps: [1/6, 1/6, 1/6, 1/6, 1/6, 1/6], vs: [1, 2, 3, 4, 5, 6]});
  var kocka2 = categorical({ps: [1/6, 1/6, 1/6, 1/6, 1/6, 1/6], vs: [1, 2, 3, 4, 5, 6]});
  
  return { kockapár: [kocka1, kocka2] };
}

// Futassuk 10-szer!
var sokdobás = repeat(10, dobás); 
viz.auto(sokdobás);

// A teljes elméleti (egzakt) eloszlás kiszámítása:
var output = Enumerate(dobás);
viz.auto(output);
````


## 2. Kockadobás feltételekkel (kedvező esetek)

<img src="https://github.com/mozow01/cog_compsci/blob/main/SciCamp/2381778-200.png" width="100">

**Feladat:** Mi annak a valószínűsége, hogy két kockával dobva *legalább az egyik hatos*?

### A középiskolai út: Kombinatorikai okoskodás
* **Összes eset (Eseménytér):** $6 \cdot 6 = 36$ lehetséges rendezett pár (▢ ▢).
* **Kedvező esetek:** Azok a párok, ahol van hatos: (6, ▢) vagy (▢, 6). Ez összesen 11 darab eset.
* **Eredmény:** $P = \frac{11}{36}$

### A számítógépes út: Generatív modellépítés
Ahelyett, hogy mi számolnánk, **lefejtjük** az eseteket szimulációval a `condition()` parancs segítségével.

* `||`: VAGY operátor.
* `&&`: ÉS operátor.
* `!`: NEM operátor.
* `==`: értékazonosság vizsgálata.

`condition(kocka1 == 6 || kocka2 == 6)`: A gép eldobja azokat a kísérleteket, amik nem felelnek meg ennek a feltételnek.

````javascript
var dobás = function () {
  var kocka1 = randomInteger(6) + 1; // Rövidebb írásmód a categorical helyett
  var kocka2 = randomInteger(6) + 1;
  return [kocka1, kocka2];
}

var kedvező_dobás = function () {
  var kocka1 = randomInteger(6) + 1;
  var kocka2 = randomInteger(6) + 1;
  
  // A MODELL SZŰRŐJE: a feltétel megadása
  condition((kocka1 == 6 || kocka2 == 6));
  
  return [kocka1, kocka2];
}

var összes = Enumerate(dobás);
var kedvező = Enumerate(kedvező_dobás);

print("Kiszámolt: 11/36 = " + 11/36);
print("Szimulált p = " + Math.exp((összes.score)([6,6]))/Math.exp((kedvező.score)([6,6])));
````

### Hogyan számol a háttérben az `Infer`?
1.  **`Enumerate`:** Végignézi az *összes* lehetséges matematikai ágat (egzakt).
2.  **`forward` sampling:** `Infer({method: 'forward', samples: 10000, model: dobás})` – Lefuttatja a kódot 10000-szer, és a gyakoriságokból csinál eloszlást.

<img src="https://github.com/mozow01/cog_compsci/blob/main/SciCamp/277680373_341339591289591_2928453617509407729_n.jpg" width="200">

## 3. Golyók elosztása

**Feladat:** Lerakunk 5 helyre 2 golyót, egy helyen csak 1 golyó lehet. 
a) Mindkét golyó az első 3 helyen van?
b) Valamelyik az utolsó 2 helyen van?

○ ○ ○ ○ ○
○ ○ ● ● ○

**Modellépítési stratégiák (kétféle reprezentáció):**
1.  **Helyek szerint:** Kiválasztunk két helyet (1-5), kikötve, hogy nem lehetnek egyenlőek.
2.  **Állapotok szerint:** Minden helyhez egy 0-1 érték tartozik, azzal a globális megszorítással, hogy az összegük pontosan 2 legyen.

````javascript
// 1. Modell: Hová tesszük a golyókat?
var lerakas1 = function () {
  var golyo1_helye = randomInteger(5) + 1;
  var golyo2_helye = randomInteger(5) + 1;
  condition(golyo1_helye !== golyo2_helye); // Nem lehetnek egy helyen!
  return [golyo1_helye, golyo2_helye];
}

// 2. Modell: Melyik hely foglalt?
var lerakas2 = function () {
  var hely1 = randomInteger(2); // 0 vagy 1
  var hely2 = randomInteger(2);
  var hely3 = randomInteger(2);
  var hely4 = randomInteger(2);
  var hely5 = randomInteger(2);
  condition(hely1 + hely2 + hely3 + hely4 + hely5 == 2); // Pontosan 2 golyó van!
  return [hely1, hely2, hely3, hely4, hely5];
}

viz.hist(Enumerate(lerakas1));
viz.hist(Enumerate(lerakas2));
````

## 4. Kártyahúzás (függő események modellezése)

**Feladat:** 52 lapból húzunk 2-t visszatevés nélkül. Mi az esélye a kőr királynak?

### A középiskolai út
Rendezetlen modell: 
Összes eset: $\binom{52}{2}$
Kedvező esetek: $\binom{1}{1} \cdot \binom{51}{1}$

### A számítógépes út (joint / többváltozós eloszlás)
A két húzás (*X* és *Y* Boole-változók) **összefüggenek**, hiszen a kihúzott lap eltűnik a pakliból. A gép ezt a függőséget egy táblázattal (joint eloszlás) és a feltételkezeléssel oldja fel:

````javascript
var kedvezo_kartya = function () {
  var szin1 = randomInteger(4) + 1;
  var figura1 = randomInteger(13) + 1;
  var szin2 = randomInteger(4) + 1;
  var figura2 = randomInteger(13) + 1;
  
  // A valóság fizikai korlátja (nincs visszatevés)
  condition(szin1 !== szin2 || figura1 !== figura2);
  
  // A kérdésünk feltétele (van-e kőr király?)
  condition(
    (figura1 == 13 && szin1 == 1) || 
    (figura2 == 13 && szin2 == 1)
  );
  
  return [[szin1, figura1], [szin2, figura2]];
}
  
viz.hist(Enumerate(kedvezo_kartya));
````

## 5. A binomiális eloszlás felépítése

Változtassunk a játékszabályon: Húzzunk 3 lapot, de **visszatevéssel**! Keressük meg, hogy hány kőr lesz a 3 lapból (X változó). A siker (kőr húzása) esélye folyamatosan $p = 0.25$. 

### A középiskolai út: Képletek és fák
Felrajzolunk egy fát. Hány úton érhetünk el mondjuk $k$ sikert $n$ próbából?

Ebből születik a klasszikus képlet:
$$P(X=k) = \binom{n}{k} p^k (1-p)^{n-k}$$

### A számítógépes út: Generatív építkezés
A képlet felelevenítése helyett szimuláljuk a folyamatot elemi lépésekből! A `flip(p)` az elemi "Bernoulli-kísérlet", ami $p$ eséllyel ad 1-et (sikert), egyébként 0-t (kudarcot).

**A lift analógia:** 3 manager utazik a liftbe,. $p=0.25$ az esélye, hogy egy embernek megcsörren a telója. Ha egy managernek megszólal a telőja, fel is veszi mindig. Az $X$ változó eloszlása megmutatja az esélyét annak, hogy 0, 1, 2 vagy 3 ember produkálja ezt a nem várt dolgot.

````javascript
// Generatív építkezés (binomiális eloszlás géppel)
var model = function() {
  var H1 = flip(0.25); // első ember
  var H2 = flip(0.25); // második ember
  var H3 = flip(0.25); // harmadik ember
  
  var X = H1 + H2 + H3; // összes eset
  return {'X': X};
}

// Mivel ez gyakori felépítés, persze van rá beépített, kész függvény is:
var binom = Binomial({p: 0.25, n: 3});

viz.auto(Enumerate(model));
viz.auto(binom); // A kettő grafikonja hajszálpontosan ugyanaz!
````

### A generatív modell igazi haszna: instant alkalmazkodás
Mi történik a formulákkal és az okoskodással, ha hozzájutunk egy extra információhoz? Pl.: "Tudjuk, hogy az első húzás kőr volt". A képletet újra kéne gondolni. A modellben viszont csak beszúrunk egy `condition`-t!

````javascript
var model2 = function() {
  var H1 = flip(0.25);
  var H2 = flip(0.25);
  var H3 = flip(0.25);
  
  // Beérkezett az extra információ!
  condition(H1 == 1); 
  
  var X = H1 + H2 + H3;
  return {'X': X};
}

viz.auto(Enumerate(model2)); 
// Látható: X értéke már nem is vehet fel 0-t!
````


### Gyakorló Feladatok
1.  **Hipergeometrikus eloszlás:** 52 lapos francia kártyából húzunk 5 lapot (visszatevés nélkül). Mi annak a valószínűsége, hogy a) lesz benne pontosan egy treff, b) legalább egy király?
2.  **Kompetenciamérés:** Egy iskolában 1000 gyerek tanul, a mérésen mindig ugyanaz a 10 gyerek lesz rosszul. A termekbe sorsolják a diákokat. Mi annak a valószínűsége, hogy a Földszint VI-ban lévő 20 diákból legfeljebb 3 lesz rosszul?
3.  **Érettségi:** Sokéves átlagban p = 0.02 valószínűséggel lesz egy érettségiző az írásbelin rosszul. Mi annak a valószínűsége, hogy a 30 fős 12. C. osztályában legfeljebb 1 ember lesz rosszul?
