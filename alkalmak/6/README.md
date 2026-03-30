# Bayesiánus adatelemzés I. (Bayesian Data Analysis)

## 1. Bemelegítés: A cinkelt érme Bernoulli-eloszlás

Egy érmét dobálunk. Kérdés: szabályos-e? Ehhez egy bayesiánus modellt építünk.

A megfigyeljók az adat jellegét:  fej (true) vagy írás (false). Mivel minden dobás bináris kimenetelű, a **Bernoulli-eloszlást** használjuk a modellezés alapjául, ami szintén kategorikus, két értékű választ ad vissza, $p$ valószínűséggel. Igaz-hamisokat fog generálni a modell:

$$b\sim \mathrm{Bernoulli}(p),\qquad P(X=\mathrm{true})=p$$

valószínűséggel. Megengedjük, hogy a $p$ érték ésszerű módon a $[0;1]$ intervallumból érkezzen: erre **Beta-eloszlást** választunk:

<img height="300" alt="kép" src="https://github.com/user-attachments/assets/92db2e94-5b07-46c8-8af1-d116b66f545c" />

https://mozow01.github.io/WebpplHelp/distributions/beta.html

https://en.wikipedia.org/wiki/Beta_distribution

## Beta eloszlás: a „Fantom-kísérlet” intuíciója

Egy elvont valószínűséget megbecsülni a semmiből rendkívül nehéz feladat. A Béta-eloszlás igazi haszna abban rejlik, hogy lehetővé teszi a megérzéseink számszerűsítését egy ideális eseten keresztül: úgy fogalmazhatjuk meg a prior tudásunkat, **mintha már elvégeztünk volna egy képzeletbeli, ideális kísérletet**, mielőtt egyetlen valós adatot láttunk volna.

Ebben a modellben az $\alpha$ és $\beta$ paraméterek „fantom” vagy „pszeudo” megfigyeléseket jelentenek:

* **$\alpha - 1$:** A fejben már lejátszott képzeletbeli **sikerek** száma.
* **$\beta - 1$:** A fejben már lejátszott képzeletbeli **kudarcok** száma.

Ha 5 sikert akarok feltételezni, miért kell $\alpha = 6$-ot írnom?

A választ a **Bayesiánus nullpont** adja meg. A totális tudatlanság állapota, amikor semmit nem tudunk a világról, a $Beta(1, 1)$ eloszlás.
* Ebben az állapotban még nulla ($0$) sikert és nulla ($0$) kudarcot láttunk.
* Mivel a képlet szerint $\text{sikerek} = \alpha - 1$ és $\text{kudarcok} = \beta - 1$, ebből következik: $0 = 1 - 1$.
* Tehát az **1-es érték a matematikai „beugró”**. Ez biztosítja, hogy az eloszlás görbéje értelmezhető maradjon (ne osszunk nullával), és minden, ami ezen felül van, képviseli a ténylegesen tapasztalt vagy feltételezett „extra” adatokat.

### Három forgatókönyv a megértéshez

1.  **A „Tiszta lap” (Tudatlanság)**
    Sosem láttad még ezt az érmét. A képzeletbeli kísérletedben 0 dobás szerepel.
    $\alpha = 1, \beta = 1 \implies$ **Beta(1, 1)**
    Ez egy tökéletesen vízszintes vonal (egyenletes eloszlás). Számodra minden valószínűség 0% és 100% között pontosan ugyanannyira hihető.

2.  **A „Gyanakvó szemlélő” (gyenge prior)**
    Úgy sejted, hogy az érme szabályos, de nem tennéd rá az életedet. Olyan, mintha a fejedben már dobtál volna vele kettőt, és 1 fej (siker), illetve 1 írás (kudarc) jött volna ki.
    $\alpha = 1 (\text{alap}) + 1 (\text{fantom}) = 2$
    $\beta = 1 (\text{alap}) + 1 (\text{fantom}) = 2 \implies$ **Beta(2, 2)**
    Ez egy lankás dombot hoz létre 50%-nál. Van már egy elképzelésed, de a valós adatok még könnyen eltolhatják a hitedet.

3.  **A „Makacs szakértő” (erős (dogmatikus) prior)**
    Tudod, hogy az érme egy precíziós üzemből érkezett, és rendkívül biztos vagy a szabályosságában. Ez olyan, mintha a fejedben már elvégeztél volna 1000 dobást, amiből 500-500 lett az eredmény.
    $\alpha = 1 + 500 = 501$
    $\beta = 1 + 500 = 501 \implies$ **Beta(501, 501)**
    Ez egy hatalmas, tűhegyes tüskét hoz létre pontosan 50%-nál. Ha ezután dobsz 10 valós fejet, a hited alig fog megmozdulni, mert a 10 valós adat eltörpül az 1000 fantom-adatod mellett.

Mivel a Béta-eloszlás a Bernoullihoz és a Binomiálishoz asszociált „fantom-megfigyelésként” viselkedik. Amikor megérkeznek a valódi adatok, csak össze kell önteni a képzeletbeli és a valós adatokat:

$$\alpha_{új} = (\text{Fantom sikerek}) + (\text{Valós sikerek}) + 1$$
$$\beta_{új} = (\text{Fantom kudarcok}) + (\text{Valós kudarcok}) + 1$$

A Béta-eloszlás tehát zökkenőmentesen egyesíti, amit korábban *gondoltunk*, azzal, amit éppen most *tapasztaltunk*.

Ez lesz később a **konjugált prior** ideálképe.

Tegyük fel, hogy dobunk 10-et, és gyanúsan sok, 8 fej lesz az eredmény. Nézzük meg, hogyan tolódik el a prior a WebPPL kód segítségével!

```javascript
var model = function() {
  // A priorunk egy Beta eloszlás, ami a 'p' (fej valószínűsége) paramétert írja le
  var p = beta(2, 2); 
  
  // Az adatsorunk: 8 fej (true), 2 írás (false)
  var data = [true, true, true, true, true, true, true, true, false, false];
  
  // Minden egyes adatpontot egy Bernoulli eloszláson keresztül figyelünk meg
  mapData({data: data}, function(datum){
    observe(Bernoulli({p: p}), datum);
  });
  
  // Visszaadjuk a poszteriort
  return p;
};

// Következtetés MCMC (Markov Chain Monte Carlo) módszerrel
var poszterior = Infer({model: model, samples: 10000, method: 'MCMC'});

viz.auto(poszterior);
print("Várható érték (p): " + expectation(poszterior));
```

Az eredményen látható lesz, hogy a `Beta(2, 2)` prior a megfigyelt adatok (sok fej) hatására erősen eltolódik jobbra, a 0.8 körüli értékek felé.

---

## 2. A konjugált prior

A fenti példában szándékosan választottuk a *Beta-eloszlást* a Bernoulli modell mellé. De miért? Azért, mert a Beta-eloszlás a Bernoulli (és a Binomiális) eloszlás **konjugált priorja**. 

**Mit jelent ez?**
Egy prior eloszlást akkor nevezünk konjugált priornak egy adott adatgeneráló modell (likelihood) függvényre nézve, ha a prior és a belőle frissített poszterior eloszlás **ugyanabba az eloszláscsaládba** tartozik. Magyarul: ha Betát teszel be, és Bernoulli-val figyeled meg az adatot, a végén garantáltan egy újabb Beta-eloszlást kapsz vissza. Ez analitikusan kiszámolhatóvá teszi a poszteriort, MCMC szimulációk nélkül. Amíg nem volt kéznél számítógép, addig ez egy nagyon fontos matematikusi segítség volt.

**Bayes-tétel:**

Eddig ez még nem volt, mert beteg voltam :( DE most! Nézzük a jointot kétféleképpen, a feltételes valószínűsleg definíciója alapján:

$$\Pr(XY)=\Pr(X\mid Y)\cdot\Pr(Y) $$

$$\Pr(YX)=\Pr(Y\mid X)\cdot\Pr(X) $$

És ezek egyenlők, mert a logikai szorzás kommutatív:

$$\Pr(X\mid Y)\cdot\Pr(Y)=\Pr(Y\mid X)\cdot\Pr(X) $$

Innen 

$$\Pr(X\mid Y)=\dfrac{\Pr(Y\mid X)\cdot\Pr(X)}{\Pr(Y)}$$

(Laplace ezt az inverz valószínűség tételének nevezte, mert az egyik feltételestől a fordított irányúra lehet vele áttérni.)

A Bayes-tétel alapján a poszterior valószínűség arányos a generáló modellből kijövő eloszlás és a prior szorzatával:

$$P(p \mid \text{adat}) \propto P(\text{adat} \mid p) \cdot P(p)$$

1. **Generáló modell: (Bernoulli likelihood):** k = 0 vagy 1

$$P(\text{adat} \mid p) =  p^k (1-p)^{1-k}$$

(Vegyük észre, hogy tényleg az történik, hogy a Bernoulli-paramérter = p szcenárióban ez a "random generátor" adatokat ad vissza a fenti valószínűségi eloszlás alapján.)

3. **Prior (Beta):** A beta eloszlás $\alpha$ és $\beta$ paraméterekkel az alábbi formát ölti (a normalizáló konstansokat elhagyva, csak az arányosságra fókuszálva):
$$P(p) \propto p^{\alpha-1} (1-p)^{\beta-1}$$

4. **Poszterior:** Szorozzuk össze a kettőt megfordítva a feltételeket:
$$P(p \mid \text{adat}) \propto \left( p^k (1-p)^{1-k} \right) \cdot \left( p^{\alpha-1} (1-p)^{\beta-1} \right)$$

Vonjuk össze az azonos alapú hatványokat:
$$P(p \mid \text{adat}) \propto p^{(k + \alpha) - 1} (1-p)^{(1 - k + \beta) - 1}$$

Ha ránézünk az eredményre, ez pontosan egy új Beta-eloszlás képlete, ahol az új paraméterek:
* $\alpha_{\text{új}} = \alpha + k$ (a régi $\alpha$ plusz a sikerek/fejek száma)
* $\beta_{\text{új}} = \beta + n - k$ (a régi $\beta$ plusz a kudarcok/írások száma)

Tehát az analitikusan (kéziszámolással) frissítés annyi, hogy hozzáadjuk az adatokból származó megfigyeléseket a prior paramétereihez!

---

## 3. Bayesiánus bestiárium
 
**Generatív modell:**
Egy generatív modell olyan függvény, ami adattokat képes algoritmikusan generálni. Az algoritmus bemenete a **paraméterek**, kimenete a **szimulált adat**. Pszeudo-random generátor, amely úgy produkálja az adatokat, hogy azok nagy átlagban egy adott valószínűségi eloszlásnak megfelelőek legyenek.

Ilyennel már találkoztunk. Nem dobáltunk kockát, nem húztunk kártyát, a gép elvégezte helyettünk. Képesek voltunk kockadobást, laphúzást szimulálni programmal.

**Bayesiánus következtetés:** Az előbbi program feladatát megfordítjuk: megpróbálunk visszakövetkeztetni arra, hogy egy valóságosan mért (tehát nem szimulált) $Y = y$ **adat** a generatív modell milyen $X = x$ **paraméterértékeire** tud generálódni. 

**Többváltozós (joint) eloszlást** kapunk, ha a paraméterek (**látens** $X$ változó) és a **megfigyelt** $Y$ változó terének szorzatán feltételezünk egy $P(X,Y)$ valószínűségi eloszlást, amelyet a szorzatszabállyal számítunk ki (kétféleképpen):

1. $\Pr(Y \mid X)$ annak a valószínűsége, hogy adott paraméter mellett az adat éppen a megfigyelt:
$$ \Pr(X, Y) = \Pr(Y \mid X) \cdot \Pr(X)$$

2. Illetve megfordítva, $\Pr(X \mid Y)$ annak a valószínűsége, hogy a megfigyelt adat estén a látens paraméterérték éppen az adott:
$$ \Pr(X, Y) = \Pr(X \mid Y) \cdot \Pr(Y)$$

Az adat és a generatív modell még nem elég, mert a paraméterteret is be kell népesíteni paraméterértékekkel, és ehhez valami előzetes tudással kell rendelkeznünk arról, hogy mit gondolunk ezek eloszlásáról. Ez a joint eloszlás egy marginális eloszlása, a $P(X)$ **prior eloszlás**.

A **likelihood függvény** az:
$$x \mapsto \Pr(Y=y \mid X=x)$$
függvény. Ha tudjuk a megfigyelt változó értékét (az adatot), akkor a likelihood megmondja, hogy a modellekben ennek az adatnak mekkora a valószínűsége. **Ilyen pszeudo-adatokat generál a generatív modell.** Arra is használhatjuk, hogy a legvalószínűbb paraméterértéket meghatározzuk belőle (Maximum Likelihood módszer). 

Világos, hogy ez nem ugyanaz, mint az adat eloszlása:
$$y \mapsto \Pr(Y=y \mid X=x)$$
ami egy igazi valószínűségi eloszlás. 

Ami minket **igazán érdekel,** az a $P(X \mid Y=y)$ **posteriori eloszlás**, ami a Bayes-formuma:
$$\Pr(X \mid Y) = \frac{\Pr(Y \mid X) \cdot \Pr(X)}{\Pr(Y)}$$

> **A bayesiánus eljárás lépései tehát:**
> 1. A $\Pr(X)$ priornak megfelelő $X$-eket generálva,
> 2. elkészíti azoknak az $X$-eknek az eloszlását, amire $GM(X) = y$,
> 3. ebből legyártja a $\Pr(X \mid Y=y)$ poszteriort a Bayes-tétel felhasználásával.
> 
> Mivel $\Pr(Y=y)$ egy konstans, érvényes az arányosság:
> $$\Pr(X \mid Y=y) \propto \Pr(Y=y \mid X) \cdot \Pr(X)$$
> Így a gyakorlatban (például az MCMC algoritmusokkal) elég csak ezt az arányosságot kiszámolni és normálni.

---

## 4. Óvodások és a pillangó

Tudjuk, hogy az óvodások még nem feltétlenül tudnak különbséget tenni állat és növény között. Jó példa erre a pillangó. Elég magas kompetenciaszint egy kiscsoportostól, ha meg tudja mondani, hogy a pillangó növény vagy másféle élőlény. 20 óvodást kérdeztünk meg arról, hogy a pillangó állat-e. 5 óvodás szerint virág, a többiek szerint valami bogárkaféle. Ismerve az adatot, mi annak az eloszlásnak a várható értéke és 95%-hoz tartozó *hihetőségi* intervalluma (credible interval), amelyből ez az adat származhatott? 

### Generatív modell

A bayesiánus adatelemzés elkezdéséhez kell: 1. egy generatív modell, 2. egy prior.

1. **Modellgyártás:** Az adatszimuláló algoritmus *binomiális eloszlás* kell, hogy legyen. $n=20$ elemből kell kiválasztani véges sokat (akik virágnak nézik a pillangót), és ezt $p$ valószínűséggel teszik.
2. **Prior:** A prior határozatlanabb, az alapfeltevés, hogy $p$-t egy egyenletes eloszlásból származtatjuk, azaz véletlenszerűen adunk neki 0 és 1 között értéket.

Hogyan generálunk $p$-t a priorból?

```javascript
var prior_szerint_generalt_p = function() {
  var p = uniform(0,1);   // p egy ilyen valószínűségi változó
  return p;
};

var p_eloszlasa = Infer({model: prior_szerint_generalt_p, samples: 1000, method: 'MCMC'});
viz(p_eloszlasa);
```

Hogyan választjuk ki a $GM(p) = 5$-nek megfelelő paramétereket és mi lesz a poszterior eloszlás? 

```javascript
var posterior_p = function() {
  var p = uniform(0,1);   
  observe(Binomial({p : p, n: 20}), 5); // itt választódnak ki az adatnak megfelelő p-k
  return p;
};

var poszterior = Infer({model: posterior_p, samples: 1000, method: 'MCMC'});
viz(poszterior);
```

Készen is volnánk. De vajon ezzel az új paramétereloszlással milyen lehetséges értékek jöhetnek ki (*prediktív poszterior*) és mik voltak a korábbi eloszlás szerinti értékek (*prediktív prior*)?

```javascript
var model = function() {
  var p = uniform(0,1);
  observe(Binomial({p : p, n: 20}), 5);
  
  var poszterior_prediktiv = binomial({p: p, n: 20}); // ezzel az új p-vel a szimulált adatok
  var prior_p = uniform(0,1); // érintetlen paraméter
  var prior_prediktiv = binomial({p: prior_p, n: 20}); // érintetlen paraméterből szimulált adatok

  return {
    prior: prior_p, 
    priorPredictive: prior_prediktiv,
    posterior: p, 
    posteriorPredictive: poszterior_prediktiv
  };
};

var output = Infer({model: model, samples: 1000, method: 'rejection'});
viz.marginals(output);
```

### Várható érték, kredibilitási intervallum

A maximum likelihood módszer pusztán pontbecslést ad. Most viszont a teljes poszterior eloszlás megvan, ezért ki tudjuk számítani az eloszlás *várható értékét* és a *kredibilitási intervallumot* is (pl. 95%-ra).

```javascript
var model = function() {
  var p = uniform(0,1);
  observe(Binomial({p : p, n: 20}), 5);
  return p;
};

var output = Infer({model: model, samples: 10000, method: 'MCMC'});
viz.auto(output);
print("Várható érték: " + expectation(output));

// 95%-os intervallum empirikus keresése
var prob_in_interval = expectation(output, function(p){ return 0.05 < p && p < 0.45; });
print("Valószínűség a (0.05, 0.45) intervallumban: " + prob_in_interval);
```

A várható érték $E(p) \approx 0.28$, a 95%-os hihetőségi intervallum pedig nagyjából (0.05, 0.45). Ez azt mondja meg, hogy **a $p$ paraméter hol helyezkedik el 95%-os valószínűséggel.** A bayesianizmusban tehát nem a $p$ rögzített (mint a klasszikus statisztikában az intervallum), hanem maga a paraméter valószínűségi változó.

### Modell 2: A megengedőbb (de határozottabb) Beta prior

A fentebb már bizonyított konjugáltság miatt a Binomiális eloszlásnál érdemes priornak Beta eloszlást választani. A `beta(50, 200)` egy olyan eloszlás, ami jó erősen a bal lábára terhel (a várható értéke alacsony). Ezzel priorként azt feltételezzük, hogy az óvodások nagyrészt jól felismerik a pillangót, így a virágnak nézés valószínűsége eleve kicsi.

```javascript
var model = function() {
  var p = beta(50,200);
  observe(Binomial({p : p, n: 20}), 5);

  var poszterior_prediktiv = binomial({p: p, n: 20}); 
  var prior_p = beta(50,200); 
  var prior_prediktiv = binomial({p: prior_p, n: 20}); 

  return {
    prior: prior_p, 
    priorPredictive: prior_prediktiv,
    posterior: p, 
    posteriorPredictive: poszterior_prediktiv
  };
};

var output = Infer({model: model, samples: 10000, method: 'MCMC'});
viz.marginals(output);
```

Ilyen erős (dogmatikus) prior esetén a megfigyelt adat már kisebb súllyal esik latba. Ha viszont több adatunk is van, és mondjuk becsúszik egy nyilvánvalóan hibás mérési adat (pl. $k=19$), ezt a határozott prior ügyesen tompítani fogja:

```javascript
var model = function() {
  var p = beta(50,200);
  
  observe(Binomial({p : p, n: 20}), 5);
  observe(Binomial({p : p, n: 20}), 6); 
  observe(Binomial({p : p, n: 20}), 19); // Erős kiugró érték (outlier)

  var poszterior_prediktiv = binomial({p: p, n: 20});
  var prior_p = beta(50,200); 
  var prior_prediktiv = binomial({p: prior_p, n: 20}); 

  return {
    prior: prior_p, 
    priorPredictive: prior_prediktiv,
    posterior: p, 
    posteriorPredictive: poszterior_prediktiv
  };
};

var output = Infer({model: model, samples: 10000, method: 'MCMC'});
viz.marginals(output);
```

Szemben az egyenletes priorral (amely naivan komolyan venné a $k=19$ értéket is), az erős szkeptikus prior diszkreditálja ezt a kiugró értéket, megvédve a modellünket a szélsőséges torzítástól.
