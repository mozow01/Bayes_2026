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
| **Alapelvek** | Egyetlen feltételezett matematikai mintatér alapján hoz döntést. | Az előzetes ismeretek és a mért adatokat ismeretében adja meg a modellek és lehetséges értékek valószínűségét. |
| **Előzetes elvárások** | Nem vesz figyelembe előzetes tudást a vizsgálat tárgyáról. | A kezdeti szakmai ismereteket tudatosan beépíti a matematikai modellbe. |
| **Paraméterek értelmezése** | A keresett értékeket ismeretlen, de fix számoknak tekinti. | A keresett értékeket természetes (inherens) bizonytalansággal rendelkező eloszlásoknak tekinti. |
| **Bizonytalanság kezelése** | Intervallumokkal (konfidencia-) próbálja megbecsülni a tévedés esélyét. | Egy teljes és könnyen leolvasható valószínűségi térképet ad a lehetséges kimenetelekről (eloszlást ad vissza). |
| **Adatkövetelmény** | Megbízható eredmény eléréséhez nagyszámú mérési adatra van szüksége. | Az előzetes tudás felhasználása miatt akár néhány mérés is elegendő lehet a döntéshez. |
| **Adatfeldolgozás** | Szigorú feltételekhez kötött, kész, előre gyártott képletekbe helyettesíti be az adatokat. | Számítógépes szimuláció segítségével alkot egységes elméleti keretet a következtetéshez. |
| **Interpretáció** | Csak elvetni tud egy feltételezést egy határérték alapján, megerősíteni nem. | Közvetlenül össze tudja mérni és rangsorolni tudja a különböző magyarázó modellek valószínűségét. |

## Konfidencia intervallum és kredibilitási intervallum

Most, hogy döntöttünk, hogy Csofi beteg vagy sem, a súlyára valamit kéne mondanunk. Nézzük a két megközelítést.

### Konfidencia intervallum (frekventizmus)

**Konfidencia intervallum:** egy adott paramétert mérő mintavételezési eljárás 95%-os konfidenciaintervalluma egy olyan, a mintából számított tartomány, ami az esetek 95%-ában tartalmazza a valódi paramétert, ha a mintavételt végtelen sokszor megismételnénk. 

Tehát ez az intervallum jó a mérések 95%-ában, abban az értelemben, hogy tartalmazza Csofi fix súlyát.

*Mintaátlag* ($\bar{x}$): $\frac{19 + 18 + 18}{3} = 18.33 \text{ g}$

*Korrigált tapasztalati szórás* ($S$): Az átlagtól vett négyzetes eltérés per $n-1$ ($3-1=2=df$ degrees of freedom) négyzetgyöke. $S \approx 0.577 \text{ g}$

*Standard hiba ($SE$):* $0.577 / \sqrt{3} \approx 0.333 \text{ g}$

*Kritikus $t$-érték:* 2 szabadságfoknál, egyoldali 95%-os szintre a táblázatból: $t_{0.05} = 2.92$ ( https://goodcalculators.com/student-t-value-calculator/ ) 

A felső korlát ($c$):
$$c = \bar{x} + (t_{0.05} \times SE) = 18.33 + (2.92 \times 0.333) = 19.30 \text{ g}$$

A 95%-os egyoldali intervallumunk tehát: $(-\infty \text{ ; } 19.30]$.

Illetve még egy kör ugyanarra: a $H_0$-ban feltételezett 20 gramm ezen kívül esik, a nullhipotézist $\alpha=0.05$-ös szinten elvetjük.

Ez egy úgy nevezett intervallumbecslés.

### Kredibilitási intervallum

A Bayesiánus episztemológiában a hörcsög súlya nem fix, hanem egy **eloszlás** reprezentálja. Sőt, van előzetes tudásunk: Csofi vagy egészséges (22 g körüli) vagy beteg (mondjuk 17 g körüli). Beépítjük az adatainkat (19, 18, 18) egy WebPPL modellbe:

```javascript
var model = function() {

  // Beteg hörcsög (mu=17, sigma=1)
  var súly = gaussian(17, 1);

  // A mérés hibája (a mintából becsült bizonytalanság)
  var SE = 0.577;

  // A mért adatokkal ütköztetjük a modell előzetes információit
  observe(Gaussian({mu: súly, sigma: 1}), 19);
  observe(Gaussian({mu: súly, sigma: 1}), 18);
  observe(Gaussian({mu: súly, sigma: 1}), 18);

  return súly;
}

var prior_modell = function() {
  return gaussian(17, 1);
}

// Generált előzetes beteg hörcsög súly
var frissítettlen_modell = Infer({method: 'MCMC', samples: 10000}, prior_modell);

// Generált amegfigyelt beteg hörcsög súly
var frissített_modell = Infer({method: 'MCMC', samples: 10000}, model);

viz.auto(frissítettlen_modell)

viz.auto(frissített_modell)

var also_hatar = 18.8;

var hdi_valoszinuseg = expectation(frissített_modell, 
                              function(x) { return x < also_hatar; });

print(hdi_valoszinuseg);
```

Ebből az eloszlásból utólag kivágjuk a legsűrűbb 95%-ot, ezt hívjuk **HDI-nek (Highest Density Interval)**.

**Kredibilitási intervallum (Credible interval):** Egy adott paraméter 95%-os kredibilitási intervalluma (HDI-je) egy olyan tartomány, amelybe a valódi paraméter – a rendelkezésre álló adatok és az előzetes tudásunk ismeretében – *ténylegesen 95%-os valószínűséggel esik*.
