# Valószínűségi programozás

Olyan programozási nyelv, amelyben nem csak determinisztikus függvényeket lehet alkalmazni, hanem "pszeudo-random", sztochasztikus függvényeket is. Egy ilyen a Stanford CoCoLab-ban fejlesztett WebPPL nyelv ( http://webppl.org/ ), ami a JavaScript egy funkcionális töredéke, feldúsítva a valószínűségi függvényekkel és eljárásokkal.

Egy konkrét dobás:
 
```javascript
flip(0.7);
```

A `flip()` a szabályos pénzérme eloszlása, de a `flip` és a `flip()` nem ugyanaz:

```javascript
flip; flip(); sample(flip)
```

Sőt, a magáért beszélő `sample` kulcsszó *függvényt* vár, amely valami ilyesmi alakú:

```javascript
function( ... ) { return ... };
```

Pl. `function(x,y) { return x + y };`. Ilyenek programbeli helye egy függvénydeklarációt kíván: `var f = function(x,y) { return (x + y) };`

Így a nyelvileg helyes parancs:

```javascript
sample(flip) // vagy sample(function(){ return flip() })
```

```javascript
repeat(4, flip);
```

Ha cinkelt pénzérmét akarunk, akkor definiálnunk kell egy ezt a kifejezést visszaadó függvényt: 

```javascript
function() { return flip(0.7) };
```
Egyébként `flip(q)` az pontosan ugyanaz, mint `sample(Bernoulli({p: q}))`.

---

# Csofi története

Van egy törpehörcsögünk, amelyikről azt gyanítjuk, hogy rendellenesen fogy. A súlya (tömege) elméletileg egy 22 g közepű 1 g-os szórású normál eloszlás (haranggörbe). El kéne dönteni, hogy orvoshoz kell-e vinni. 

<p align="center">
  <img src="https://raw.githubusercontent.com/mozow01/Bayes2024/main/1_gyak/horcsi.jpeg" alt="Csofi" width="300"><br>
  <b>Csofi</b>
</p>

Például egy közelítő mintavétellel: `Gaussian({mu: 22, sigma: 1})`.

Amúgy nagyon gyanús a $\sigma = 1$ g, mert nem tudjuk, hogy miből jön ki: napi súlyváltozás, a mérleg pontossága, vagy a hörcsögök közötti biológiai variancia? Vagy a tényleges matematikai szórás? A 22 jó lesz populációs átlagnak: $\mu_0 = 22$, de ezzel önmagában nem tudunk mit kezdeni... Ott állunk, kezünkben egy sovány hörcsöggel.

---

## Amit eddig tanultunk: hipotézisvizsgálat

Végül is szeretném tudni, hogy a hörcsög mekkora súlyú. Nyilván valamekkora (valahogy csak vannak a dolgok!!!), de nem tudjuk. Nem örülnénk, ha mondjuk $22 - 1 = 21$-nél kisebb lenne. Legyen a hörcsög valódi súlya $\theta$.

* **H0 (nullhipotézis):** az állat egészséges, $\theta \geq 20$.
* **H1 (alternatív hipotézis):** az állat rendellenesen sovány, $\theta < 20$.

Hogyan férek hozzá Csofi súlyához? Megmérem. Nyilván többször, mert egy mérés, nem mérés. A súlymérés normál eloszlást követ, Csofi súlyméréseinek adatai: 

$$ x \sim \mathcal{N}(\theta, \sigma) $$

Az adatok átlagára ugyanez: 

$$ \bar{x} \sim \mathcal{N}(\theta, \sigma/\sqrt{n}) $$

Ezt még mi magunk is le tudnánk vezetni. A probléma, hogy $\sigma$-t, a mérési adatok eloszlásának szórását nem tudjuk (egy képzeletbeli világban megvan minden mérés, és abban a világban az adatok szórása lenne az). Ezen a ponton feladjuk, és megkérdezzük a matematikust (Pearson, Gosset), hogy: 

*Mi az adataink $p$ valószínűsége, ha adaton mondjuk 3 mérést értünk és az átlagot gondoljuk a $\theta$ mért értékének, feltéve, hogy $\theta \geq 20$?*

A válasz a következő lesz. Eldöntötted-e, hogy:
1.  hány mérést végzel?
2.  mekkora valószínűséget gondolsz problémásnak? 

Válaszunk: 
1.  Igen, 3 mérés.
2.  Mondjuk $\alpha = 0.05$ alatt már nagyon gyanús (szignifikancia szint).

Akkor számold ki ezt: 

$$ t = \frac{\bar{x} - 21}{s_x/\sqrt{n}} $$

ahol $s_x^2 = \frac{\sum (x_i-\bar{x})^2}{n-1}$. Majd fogj egy t-táblázatot, és keresd ki, hogy mennyi a $p$ a $t$-hez.

Mivel $p = 0.019$, ezért 0.05 szinten szignifikáns az eltérés, azaz 1000 mérésből csak 19 lenne olyan, amelyik ilyen, mint ez. Ez nagyon kevés. Valami baj van, Csofi súlya nincs összhangban azzal, hogy a 20-nál nagyobbak az egészséges állatok értékei. A nullhipotézist elvethetjük.

### Naiv, álnaiv kérdések (és a két tábor válaszai)

**1. Értem a haranggörbét, de mi az a t-statisztika? Mi ez a "made-up" dolog?**
* **Frekventista:** Ez egy standardizált távolság, megmutatja, hogy a standard hiba hányszorosa a mért átlag eltérése a középtől.
* **Bayesiánus:** Ez egy teljesen felesleges absztrakció. Mi nem $t$-értékkel fogunk számolni, hanem közvetlenül Csofi súlyának valószínűségét fogjuk modellezni.

**2. Miért az az alternatív hipotézis, hogy $\theta < 20$? Miért nem vizsgáljuk a beteg (17 g) vs. egészséges (22 g) állapotot közvetlenül?**
* **Frekventista:** Mert csak a nullhipotézist és a mintavételezési eljárást tudunk matematikailag tesztelni, és egy küszöböt ($\alpha$) vizsgálni.
* **Bayesiánus:** Pont ezt fogjuk tenni! Felveszünk egy modellt az egészséges és a beteg állatra, és megnézzük, hogy az adatok melyiket támogatják jobban.

**3. Csak "elvetem / nem vetem el" választ kapok. Engem a súlya érdekel!**
* **Frekventista:** A valós súly fix, csak nem ismerjük. A döntésünk hosszú távú megbízhatóságát tudjuk tesztelni.
* **Bayesiánus:** A frekventista módszer nem arra ad válasz, ami a kérdés. Mi visszaadjuk a tényleges súly legvalószínűbb eloszlását az adatok tükrében.

**4. Mi ez a t-képlet? Miért kell statisztikusnak lennem?**
* **Frekventista:** Ez egy analitikus megoldás a számítógépek előtti korból, amivel a normál eloszlás területét közelítjük.
* **Bayesiánus:** Nem kell statisztikusnak lenned! Leírod a folyamatot egy kódba, a gép pedig elvégzi a számítást.

**5. Miért kéne 100 méréssel nyaggatni a hörcsögöt? Miért úgy mérünk, mint egy sörgyáros minőségellenőr, miért nem úgy, ahogy egy állatorvos?**
* **Frekventista:** Mert kis minta esetén a t-próba statisztikai ereje gyenge. 
* **Bayesiánus:** Az állatorvosnak van *előzetes tudása* (prior) arról, milyen egy hörcsög. Ha ezt a tudást **beépítjük** a modellbe, már kevés mérésből is pontos diagnózist kapunk!

---

## Bayes-féle megközelítés

Csofi súlya egy bizonytalan érték (inherensen bizonytalan). Két dolgot tudunk: $Gauss(22,1)$ egy egészséges állat súlya, $Gauss(17,1)$ egy betegé. Csofi a mérések alapján 19, 18, 18 g. Ez eléggé leszűkíti a lehetőségeket. 

Ha felállítunk két modellt, a gép ki tudja számolni, melyik a valószínűbb!

```javascript
var data = [
  {k: 19},
  {k: 18},
  {k: 18}
];

var Model = function() {
  // Prior: modell index: 1 = egészséges, 2 = beteg
  // Kezdetben 50-50% esélyt adunk mindkettőnek
  var i = categorical({vs: [1, 2], ps: [0.5, 0.5]});

  // i-től függő populációs átlag (m)
  var m = (i === 1) ? gaussian(22, 1) : gaussian(17, 1);

  // Likelihood: a mérések beillesztése a modellbe
  map(function(d){
    observe(Gaussian({mu: m, sigma: 1}), d.k);
  }, data);

  // A modell indexét kapjuk vissza (vajon 1 vagy 2 lesz?)
  return {i: i};
};

// Inferencia (A számítógép "gondolkodik")
var opts = {method: 'SMC', particles: 3000, rejuvSteps: 5};
var post = Infer(opts, Model);

viz.marginals(post);
print("Poszterior eloszlás a modellekre (i)");

// ---------------------------------------------------
// Priorok ellenőrzése (Mik voltak az eredeti feltevések?)
var PriorHealthy = function() {
  var m = gaussian(22, 1);
  return {m: m};
};

var PriorSick = function() {
  var m = gaussian(17, 1);
  return {m: m};
};

var priorOpts = {method: 'forward', samples: 5000};
var prior1 = Infer(priorOpts, PriorHealthy);
var prior2 = Infer(priorOpts, PriorSick);

viz.marginals(prior1);
print("Egészséges prior: N(22,1)");

viz.marginals(prior2);
print("Beteg prior: N(17,1)");

print("A mért adatok átlaga:");
print((19+18+18)/3);
```

---

## Összevetés

| Kritérium | Frekventista statisztika | Bayesiánus statisztika |
| :--- | :--- | :--- |
| **Alapelvek** | Egyetlen feltételezett mintatérből vett tömeges minták alapján hoz döntést. | Az előzetes ismereteket és a friss adatokat ötvözve mutatja meg a lehetséges válaszok valószínűségét. |
| **Előzetes elvárások** | Nem vesz figyelembe semmilyen előzetes tudást a vizsgálat tárgyáról. | A kezdeti szakmai ismereteket tudatosan beépíti a matematikai modellbe. |
| **Paraméterek értelmezése** | A keresett értékeket ismeretlen, de állandó fix számoknak tekinti. | A keresett értékeket természetes bizonytalansággal rendelkező eloszlásokként kezeli. |
| **Bizonytalanság kezelése** | Nehezen értelmezhető intervallumokkal próbálja megbecsülni a tévedés esélyét. | Egy teljes és könnyen leolvasható valószínűségi térképet ad a lehetséges kimenetelekről. |
| **Adatkövetelmény** | Megbízható eredmény eléréséhez nagyszámú mérési adatra van szüksége. | Az előzetes tudás felhasználása miatt akár néhány mérés is elegendő lehet a döntéshez. |
| **Adatfeldolgozás** | Szigorú feltételekhez kötött, kész képletekbe helyettesíti be az adatokat. | Számítógépes szimuláció segítségével alkot egységes elméleti keretet a következtetéshez. |
| **Interpretáció** | Csak elvetni tud egy feltételezést egy határérték alapján, megerősíteni nem. | Közvetlenül össze tudja mérni és rangsorolni a különböző magyarázó modellek valószínűségét. |
