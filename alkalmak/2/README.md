# Bayes-féle megközelítés

Van egy törpehörcsögünk, amelyikről azt gyanítjuk, hogy rendellenesen fogy. A súlya (tömege) elméletileg egy 22 g közepű 1 g-os szórású normál eloszlás (haranggörbe). El kéne dönteni, hogy orvoshoz kell-e vinni. 

<p align="center">
  <img src="https://raw.githubusercontent.com/mozow01/Bayes2024/main/1_gyak/horcsi.jpeg" alt="Csofi" width="300"><br>
  <b>Csofi</b>
</p>

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
| **Alapelvek** | Egyetlen feltételezett matematikai mintatér alapján hoz döntést. | Az előzetes ismereteket és a friss adatokat ötvözve mutatja meg a lehetséges válaszok valószínűségét. |
| **Előzetes elvárások** | Nem vesz figyelembe előzetes tudást a vizsgálat tárgyáról. | A kezdeti szakmai ismereteket tudatosan beépíti a matematikai modellbe. |
| **Paraméterek értelmezése** | A keresett értékeket ismeretlen, de állandó fix számoknak tekinti. | A keresett értékeket természetes bizonytalansággal rendelkező eloszlásokként kezeli. |
| **Bizonytalanság kezelése** | Nehezen értelmezhető intervallumokkal próbálja megbecsülni a tévedés esélyét. | Egy teljes és könnyen leolvasható valószínűségi térképet ad a lehetséges kimenetelekről. |
| **Adatkövetelmény** | Megbízható eredmény eléréséhez nagyszámú mérési adatra van szüksége. | Az előzetes tudás felhasználása miatt akár néhány mérés is elegendő lehet a döntéshez. |
| **Adatfeldolgozás** | Szigorú feltételekhez kötött, kész képletekbe helyettesíti be az adatokat. | Számítógépes szimuláció segítségével alkot egységes elméleti keretet a következtetéshez. |
| **Interpretáció** | Csak elvetni tud egy feltételezést egy határérték alapján, megerősíteni nem. | Közvetlenül össze tudja mérni és rangsorolni a különböző magyarázó modellek valószínűségét. |


