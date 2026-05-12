# Bayes-faktor

## Modell-összehasonlítás

A bayesi modell-összehasonlítás célja annak eldöntése, hogy két vagy több modell közül melyik magyarázza jobban a megfigyelt adatokat.

Ezt úgy is megfogalmazhatjuk, hogy építünk egy nagyobb, hierarchikus modellt, amelyben maga a modellválasztás is egy valószínűségi változó. Jelöljük ezt \(m\)-mel. Ekkor \(m\) mondja meg, hogy éppen melyik modellt használjuk. Az adott modellhez tartozó látens paramétereket jelöljük \(\vartheta\)-val, a megfigyelt adatot pedig \(D\)-vel.

Vagyis a teljes modellben három fontos komponens szerepel:

- \(m\): a modellindex, például \(m_1\) vagy \(m_2\),
- \(\vartheta\): az adott modell látens paramétere,
- \(D\): a megfigyelt adat.

A modell-összehasonlításban nem az a kérdés, hogy egyetlen paraméterérték mellett mennyire valószínű az adat. Hanem az, hogy az egész modell, a saját priorjával együtt, mennyire jól jósolja az adatot.

Ez vezet el a Bayes-faktorhoz.

<img src="https://github.com/mozow01/Bayes2024/blob/main/modcomp_1.png" height="300">

---

## Bayes-faktor

Két modell versengését úgy mérhetjük, hogy összehasonlítjuk: mennyire valószínűek a megfigyelt adatok az egyik, illetve a másik modellben.

A Bayes-faktor definíciója:

$$BF_{12}=\frac{\Pr(D\mid m_1)}{\Pr(D\mid m_2)}.$$

Ez azt méri, hogy az adat hányszor valószínűbb az \(m_1\) modellben, mint az \(m_2\) modellben.

- Ha \(BF_{12}>1\), akkor az adat inkább \(m_1\)-et támogatja.
- Ha \(BF_{12}<1\), akkor az adat inkább \(m_2\)-t támogatja.
- Ha \(BF_{12}=1\), akkor az adat a két modellt egyformán támogatja.

Nagyon fontos, hogy mindig figyeljük az indexek sorrendjét. Ha ezt számoljuk:

$$BF_{12}=\frac{\Pr(D\mid m_1)}{\Pr(D\mid m_2)},$$

akkor a nagy érték \(m_1\)-et támogatja. Ha viszont ezt számoljuk:

$$BF_{21}=\frac{\Pr(D\mid m_2)}{\Pr(D\mid m_1)},$$

akkor a nagy érték \(m_2\)-t támogatja.

---

## Marginális likelihood, vagyis prior prediktív valószínűség

A Bayes-faktorban szereplő mennyiség:

$$\Pr(D\mid m_i)$$

az adott modell **marginális likelihoodja**. Ugyanezt gyakran **prior prediktív valószínűségnek** is nevezzük.

Ez azt jelenti, hogy nem egyetlen paraméterérték mellett nézzük az adat valószínűségét, hanem az adott modell összes lehetséges paraméterértékén átlagolunk, a prior szerint súlyozva.

Diszkrét paraméter esetén:

$$\Pr(D\mid m_i) = \sum_{\vartheta} \Pr(D,\vartheta\mid m_i).$$

A szorzatszabály alapján:

$$\Pr(D\mid m_i) = \sum_{\vartheta} \Pr(D\mid \vartheta,m_i)\Pr(\vartheta\mid m_i).$$

Folytonos paraméter esetén az összeg helyett integrál szerepel:

$$\Pr(D\mid m_i) = \int \Pr(D\mid \vartheta,m_i)\Pr(\vartheta\mid m_i)\,d\vartheta.$$

Ez a mennyiség tehát azt mondja meg, hogy az adott modell a saját priorjával együtt mennyire tartotta előre várhatónak a megfigyelt adatot.

A Bayes-faktor ilyen értelemben **ex ante** mennyiség: nem azt méri, hogy a poszterior mennyire jól illeszkedik az adatra, hanem azt, hogy az adat mennyire volt előre jósolható a modell prior prediktív eloszlása alapján.

---

## Bayes-faktor és modellposzterior

A Bayes-tétel alapján:

$$\Pr(m_i\mid D) = \frac{\Pr(D\mid m_i)\Pr(m_i)}{\Pr(D)}.$$

Két modellre felírva:

$$\frac{\Pr(m_1\mid D)}{\Pr(m_2\mid D)} = \frac{\Pr(D\mid m_1)}{\Pr(D\mid m_2)} \cdot \frac{\Pr(m_1)}{\Pr(m_2)}.$$

Vagyis:

$$\text{poszterior odds} = \text{Bayes-faktor} \times \text{prior odds}.$$

Ha a két modell prior valószínűsége azonos, például

$$\Pr(m_1)=\Pr(m_2)=0.5,$$

akkor a prior odds értéke 1, ezért:

$$\frac{\Pr(m_1\mid D)}{\Pr(m_2\mid D)} = BF_{12}.$$

Tehát ha a két modellnek azonos prior valószínűséget adunk, akkor a modellek poszterior aránya közvetlenül a Bayes-faktort adja.

---

## A Bayes-faktor értelmezése

Az alábbi táblázat egy gyakran használt értelmezési skálát mutat. A táblázatban \(BF_{12}\) az \(m_1\) modell melletti bizonyítékot méri az \(m_2\) modellel szemben.

| \(BF_{12}\) értéke | Az \(m_1\) melletti bizonyíték erőssége |
|---|---|
| \(BF_{12}<1\) | Az adat inkább \(m_2\)-t támogatja |
| \(1<BF_{12}<3.16\) | Alig említhető bizonyíték |
| \(3.16<BF_{12}<6\) | Anekdotikus bizonyíték |
| \(6<BF_{12}<10\) | Szubsztanciális bizonyíték |
| \(10<BF_{12}<31.62\) | Erős bizonyíték |
| \(31.62<BF_{12}<100\) | Nagyon erős bizonyíték |
| \(100<BF_{12}\) | Döntő bizonyíték |

Ez nem mechanikus döntési szabály, hanem értelmezési segédlet. A legfontosabb továbbra is az, hogy tudjuk, melyik irányban számoltuk a Bayes-faktort.

---

# Példa: Winden vagy Hawkins?

<img src="https://github.com/mozow01/cog_compsci/blob/main/SciCamp/winden.jpg" height="200"><img src="https://github.com/mozow01/cog_compsci/blob/main/SciCamp/nancy_leave_hawkins_stranger_things_netflix_ringer.jpg" height="200">

Képzeljük el, hogy hirtelen egy ismeretlen városban találjuk magunkat. Két lehetőség van: vagy Windenben vagyunk, vagy Hawkinsban.

A kérdés:

- Melyik városban vagyunk, ha csak azt figyeljük meg, hogy milyen az ég?
- Ha nem akarunk kimenni otthonról, tudunk-e következtetni arra, hogy fog-e esni?

Ebben a példában a **város** lesz a modellindex. Vagyis:

$$m\in\{\text{Winden},\text{Hawkins}\}.$$

A két modell abban különbözik, hogy milyen felhősségi eloszlást tart valószínűnek.

Winden priorja:

$$\text{Dirichlet}(0.1,0.2,0.7).$$

Ez azt jelenti, hogy Windenben előzetesen az „erősen felhős” ég a legvalószínűbb.

Hawkins priorja:

$$\text{Dirichlet}(0.5,0.3,0.2).$$

Ez azt jelenti, hogy Hawkinsban előzetesen a „derült” ég a legvalószínűbb.

Ha azt figyeljük meg, hogy az ég **derült**, akkor ez az adat inkább Hawkins mellett szól, mert Hawkins modellje eleve nagyobb valószínűséget adott a derült égnek.

A prior prediktív valószínűségek ebben az egyszerű esetben:

$$\Pr(\text{derült}\mid \text{Winden})=\frac{0.1}{0.1+0.2+0.7}=0.1,$$

$$\Pr(\text{derült}\mid \text{Hawkins})=\frac{0.5}{0.5+0.3+0.2}=0.5.$$

Ezért:

$$BF_{\text{Hawkins},\text{Winden}} = \frac{\Pr(\text{derült}\mid \text{Hawkins})} {\Pr(\text{derült}\mid \text{Winden})} = \frac{0.5}{0.1}=5.$$

Vagyis a derült ég 5-ször valószínűbb Hawkinsban, mint Windenben.

Ha a két város prior valószínűsége azonos, akkor:

$$\Pr(\text{Hawkins}\mid \text{derült}) = \frac{0.5\cdot 0.5}{0.5\cdot 0.5+0.1\cdot 0.5} = \frac{0.25}{0.30} \approx 0.833.$$

Tehát derült ég esetén a modell poszteriorja körülbelül:

- Hawkins: 83.3%,
- Winden: 16.7%.

Ez pontosan ugyanaz a gondolat, mint a Bayes-faktornál: megnézzük, hogy a megfigyelt adat melyik modell szerint volt előre valószínűbb.

Fontos megjegyzés: az alábbi kód az esőt közvetlenül a megfigyelt felhősségből jósolja. Ha azt kondicionáljuk, hogy az ég derült, akkor az eső valószínűsége a modellben 0.1. Ez nem valódi időbeli „másnapi” előrejelzés, mert a modellben nincs külön mai és holnapi időpont. Ahhoz külön időbeli átmeneti modellre lenne szükség.

## Javított WebPPL-modell

```javascript
var HiperModel = Infer({method: 'rejection', samples: 10000}, function() {

  // A látens változó a város. Ez itt a modellindex.
  var Varos = categorical({
    ps: [0.5, 0.5],
    vs: ['Winden', 'Hawkins']
  });

  // Ez csak azért kell, hogy az érintetlen hiperpriort is ábrázolni tudjuk.
  var Varos_hiper_prior = categorical({
    ps: [0.5, 0.5],
    vs: ['Winden', 'Hawkins']
  });

  // Winden priorja a felhősségi kategóriákra.
  var Winden = dirichlet({alpha: Vector([0.1, 0.2, 0.7])});
  var x1 = (Winden.data)[0];
  var x2 = (Winden.data)[1];
  var x3 = (Winden.data)[2];

  // Hawkins priorja a felhősségi kategóriákra.
  var Hawkins = dirichlet({alpha: Vector([0.5, 0.3, 0.2])});
  var y1 = (Hawkins.data)[0];
  var y2 = (Hawkins.data)[1];
  var y3 = (Hawkins.data)[2];

  // Érintetlen Winden-prior a prior prediktív ábrázolásához.
  var Winden_prior = dirichlet({alpha: Vector([0.1, 0.2, 0.7])});
  var u1 = (Winden_prior.data)[0];
  var u2 = (Winden_prior.data)[1];
  var u3 = (Winden_prior.data)[2];

  // Érintetlen Hawkins-prior a prior prediktív ábrázolásához.
  var Hawkins_prior = dirichlet({alpha: Vector([0.5, 0.3, 0.2])});
  var v1 = (Hawkins_prior.data)[0];
  var v2 = (Hawkins_prior.data)[1];
  var v3 = (Hawkins_prior.data)[2];

  // A mért változó: a felhősség.
  var Felhosseg = (Varos === 'Winden')
    ? categorical({
        ps: [x1, x2, x3],
        vs: ['derült', 'enyhén felhős', 'erősen felhős']
      })
    : categorical({
        ps: [y1, y2, y3],
        vs: ['derült', 'enyhén felhős', 'erősen felhős']
      });

  // Megfigyelés: derült az ég.
  condition(Felhosseg === 'derült');

  // Prior prediktív felhősség Windenben.
  var Felhos_Winden_prior = categorical({
    ps: [u1, u2, u3],
    vs: ['derült', 'enyhén felhős', 'erősen felhős']
  });

  // Prior prediktív felhősség Hawkinsban.
  var Felhos_Hawkins_prior = categorical({
    ps: [v1, v2, v3],
    vs: ['derült', 'enyhén felhős', 'erősen felhős']
  });

  // Eső valószínűsége a felhősség alapján.
  var Esik = (Felhosseg === 'derült')
    ? flip(0.1)
    : (Felhosseg === 'enyhén felhős')
      ? flip(0.6)
      : flip(0.9);

  return {
    varos_hiper_prior: Varos_hiper_prior,
    Winden_prior: Felhos_Winden_prior,
    Hawkins_prior: Felhos_Hawkins_prior,
    varos_poszterior: Varos,
    felhosseg_poszterior: Felhosseg,
    esik: Esik
  };
});

viz.marginals(HiperModel);
```

## Mit mutat ez a példa?

Ez a példa a Bayes-faktor gondolatát szemlélteti hierarchikus modellként.

- A város a modellindex.
- A felhősség a megfigyelt adat.
- A két város két különböző prior prediktív eloszlást ad a felhősségre.
- A megfigyelt derült ég a Hawkins-modellt támogatja.

Mivel a városokra 50-50%-os priort tettünk, a városok poszterior odds-a közvetlenül a Bayes-faktorral egyezik meg.

A kód futása után a `varos_poszterior` eloszlásában körülbelül ezt várjuk:

- Hawkins: 0.83,
- Winden: 0.17.

Az `esik` változónál pedig derült ég mellett körülbelül ezt várjuk:

- `true`: 0.1,
- `false`: 0.9.

---

# Példa: erős döntési helyzet

Egy 24 fős átlagos gimnáziumi osztályban 6 diák válaszolta azt, hogy nem volt gondja az absztrakt matematikai jelölésekkel. Ez az osztály 25%-a.

Egy elitgimnáziumi osztályban ugyanez az arány 31-ből 17 fő volt.

A kérdés: az elitgimnáziumi adat tekinthető-e úgy, mintha ugyanabból az eloszlásból származna, mint az átlagos gimnáziumi adat?

Két modellt hasonlítunk össze.

Az \(m_1\) modell szerint az elitgimnáziumi arány lényegében az átlagos gimnáziumi arány körül várható. Ezért \(p\)-re egy 0.25 várható értékű, erősen informatív béta-priort teszünk:

$$p\sim \text{Beta}(30,90).$$

Ennek várható értéke:

$$E(p)=\frac{30}{30+90}=0.25.$$

Az \(m_2\) modell szerint az elitgimnáziumok között nagy eltérések lehetnek, ezért \(p\)-re egyenletes priort teszünk:

$$p\sim \text{Beta}(1,1).$$

Ez ugyanaz, mint a \([0,1]\) intervallumon vett uniform prior.

A megfigyelt adat:

$$D: k=17,\quad n=31.$$

Vagyis azt figyeltük meg, hogy 31 diákból 17 mondta azt, hogy nem volt gondja az absztrakt matematikai jelölésekkel.

## WebPPL-kód egy modell prior- és poszterior prediktív ábrázolásához

```javascript
var model = function() {
  // m1 modell: az átlagos gimnáziumi arány köré koncentrált prior
  var p = beta(30, 90);

  // Megfigyelt elitgimnáziumi adat: 31-ből 17
  observe(Binomial({p: p, n: 31}), 17);

  // Poszterior prediktív: új adat szimulálása a frissített p alapján
  var posterior_predictive = binomial(p, 31);

  // Prior prediktív: új adat szimulálása az érintetlen priorból
  var prior_p = beta(30, 90);
  var prior_predictive = binomial(prior_p, 31);

  return {
    Prior: prior_p,
    Posterior: p,
    PriorPredictive: prior_predictive,
    PosteriorPredictive: posterior_predictive
  };
};

var output = Infer({
  model: model,
  samples: 10000,
  method: 'MCMC'
});

viz.marginals(output);
```

A fenti kód az \(m_1\) modellt mutatja. Ugyanezt külön lefuttathatjuk az \(m_2\) modellre is, ahol a prior:

```javascript
var p = uniform(0, 1);
```

vagy ezzel ekvivalensen:

```javascript
var p = beta(1, 1);
```

## Az eredmény értelmezése

A Bayes-faktorhoz a prior prediktív valószínűségekre van szükségünk:

$$\Pr(17\mid m_1)$$

és

$$\Pr(17\mid m_2).$$

Ha az \(m_2\) modellt akarjuk az \(m_1\)-hez képest értékelni, akkor ezt számoljuk:

$$BF_{21} = \frac{\Pr(17\mid m_2)}{\Pr(17\mid m_1)}.$$

A megadott priorokkal:

$$m_1: p\sim \text{Beta}(30,90),$$

$$m_2: p\sim \text{Beta}(1,1),$$

az egzakt béta-binomiális prior prediktív valószínűségek alapján:

$$\Pr(17\mid m_1)\approx 0.000966,$$

$$\Pr(17\mid m_2)=\frac{1}{32}=0.03125.$$

Ezért:

$$BF_{21} = \frac{0.03125}{0.000966} \approx 32.35.$$

Ez azt jelenti, hogy a megfigyelt adat körülbelül 32-szer valószínűbb az \(m_2\) modellben, mint az \(m_1\) modellben.

Vagyis az adat **nagyon erősen \(m_2\)-t támogatja**.

Értelmezés: az elitgimnáziumi arány nagyon nehezen fér össze azzal a modellel, amely szerint az elitgimnáziumi osztály is lényegében az átlagos gimnáziumi 25%-os arány körül várható. Az adat sokkal jobban illeszkedik ahhoz a modellhez, amely nagyobb intézmények közötti eltérést enged meg.

<img src="https://github.com/mozow01/cog_compsci/blob/main/orak/files/ketevi_1.png" width="600">

---

# Példa: gyengébb döntési helyzet

Ugyanebben a mérésben egy másik kérdés a hagyományos matematikával kapcsolatos nehézségekre vonatkozott.

Az átlagos gimnáziumi osztályban 24-ből 11 diák mondta azt, hogy a hagyományos matematikával nem volt gondja.

Az elitgimnáziumi osztályban ugyanez 31-ből 19 diák volt.

A megfigyelt elitgimnáziumi adat tehát:

$$D: k=19,\quad n=31.$$

Itt fontos egy technikai megjegyzés.

Ha valóban a 11/24-es átlagos gimnáziumi arány köré akarunk priort tenni, akkor a prior várható értékének ennek kell lennie:

$$\frac{11}{24}\approx 0.458.$$

Például egy ilyen prior megfelel ennek:

$$p\sim \text{Beta}(55,65),$$

mert:

$$E(p)=\frac{55}{55+65}=\frac{55}{120}\approx 0.458.$$

Ezzel a priorral az elitgimnáziumi adat, vagyis a 19/31-es eredmény, már nem annyira meglepő. Az egyenletes priorhoz képest:

$$BF_{21} = \frac{\Pr(19\mid m_2)}{\Pr(19\mid m_1)} \approx 0.79.$$

Ez azt jelenti, hogy az adat nem az egyenletes modellt támogatja az átlagos gimnáziumi arány köré tett modell ellenében. Inkább arról van szó, hogy a két modell között nincs erős különbség, sőt ebben a konkrét beállításban \(m_1\) enyhén jobban teljesít.

Ez azért tanulságos, mert itt a különbség ugyan látható:

$$\frac{11}{24}\approx 0.458,$$

$$\frac{19}{31}\approx 0.613,$$

de nem olyan extrém, mint az előző példában, ahol 25%-os várakozással szemben 17/31, vagyis körülbelül 55%-os arány jelent meg.

## Megjegyzés a `beta(30,55)` priorhoz

Ha a második példában mégis ezt a priort használjuk:

$$p\sim \text{Beta}(30,55),$$

akkor ennek várható értéke:

$$E(p)=\frac{30}{30+55}\approx 0.353.$$

Ez tehát **nem** a 11/24-es arányt kódolja.

Ezzel a priorral a 19/31-es adat valóban jobban támogatja az egyenletesebb, nagyobb eltéréseket megengedő modellt. Ebben az esetben:

$$\Pr(19\mid m_1)\approx 0.00529,$$

$$\Pr(19\mid m_2)=\frac{1}{32}=0.03125,$$

tehát:

$$BF_{21} = \frac{\Pr(19\mid m_2)}{\Pr(19\mid m_1)} \approx 5.90.$$

Ez az érték az anekdotikus és a szubsztanciális határán van, de még nem erős bizonyíték.

Ezért a második példánál két lehetőség van:

1. Ha a prior tényleg a 11/24-es arányt akarja tükrözni, akkor ne `beta(30,55)` legyen, hanem például `beta(55,65)`.
2. Ha az a cél, hogy az egyenletes modell anekdotikusan jobban teljesítsen, akkor maradhat a `beta(30,55)`, de nem szabad azt mondani, hogy ez a 11/24-es aránynak felel meg.

---

# Kullback–Leibler-divergencia

Az információ intuitív értelmezése a **meglepettség**. Egy esemény annál informatívabb, minél kevésbé volt várható.

Ha egy esemény valószínűsége nagy, akkor az esemény bekövetkezése kevésbé meglepő. Ha egy esemény valószínűsége kicsi, akkor az esemény bekövetkezése nagyon meglepő.

Egy \(X=x\) esemény információtartalmát így definiáljuk:

$$I(X=x) = -\log_2 \Pr(X=x).$$

Ekvivalensen:

$$I(X=x) = \log_2\frac{1}{\Pr(X=x)}.$$

A negatív előjelre azért van szükség, mert a valószínűségek 0 és 1 közé esnek, ezért a logaritmusuk nem pozitív. Így az információtartalom nemnegatív lesz.

Például:

- ha \(\Pr(X=x)=1\), akkor \(I(X=x)=0\), vagyis nincs meglepetés;
- ha \(\Pr(X=x)\) kicsi, akkor \(I(X=x)\) nagy, vagyis az esemény meglepő.

Azért használunk logaritmust, mert független események esetén az együttes valószínűség szorzódik, az információtartalmak pedig összeadódnak. Ha \(A\) és \(B\) függetlenek, akkor:

$$\Pr(A\cap B)=\Pr(A)\Pr(B).$$

A logaritmus miatt:

$$-\log \Pr(A\cap B) = -\log\left(\Pr(A)\Pr(B)\right) = -\log \Pr(A)-\log \Pr(B).$$

Vagyis:

$$I(A\cap B)=I(A)+I(B).$$

---

## Entrópia

Az entrópia egy eloszlás átlagos információtartalma.

Diszkrét esetben:

$$H(X) = -\sum_x \Pr(X=x)\log \Pr(X=x).$$

Ha az eloszlást \(P\)-vel jelöljük, akkor:

$$H(P) = -\sum_x P(x)\log P(x).$$

Az entrópia tehát azt méri, hogy átlagosan mennyire bizonytalan az eloszlásból érkező megfigyelés.

---

## Keresztentrópia

Ha az adat valójában a \(P\) eloszlásból jön, de mi a \(Q\) eloszlást használjuk az előrejelzésére vagy kódolására, akkor az átlagos információtartalom:

$$H(P,Q) = -\sum_x P(x)\log Q(x).$$

Ezt keresztentrópiának nevezzük.

A keresztentrópia azt méri, hogy mennyi információra van szükségünk átlagosan, ha a \(Q\) eloszlást használjuk, miközben az adatok ténylegesen a \(P\) eloszlás szerint jönnek.

---

## KL-divergencia

A Kullback–Leibler-divergencia azt méri, hogy mennyi többletinformációra van szükségünk, ha a \(P\) eloszlást a \(Q\) eloszlással közelítjük.

Definíció szerint:

$$D_{KL}(P\parallel Q) = \sum_x P(x)\log\frac{P(x)}{Q(x)}.$$

Ugyanez írható így is:

$$D_{KL}(P\parallel Q) = -\sum_x P(x)\log\frac{Q(x)}{P(x)}.$$

A keresztentrópia és az entrópia segítségével:

$$D_{KL}(P\parallel Q) = H(P,Q)-H(P).$$

Vagyis:

$$D_{KL}(P\parallel Q) = -\sum_x P(x)\log Q(x) - \left( -\sum_x P(x)\log P(x) \right).$$

Ez egyszerűsítve:

$$D_{KL}(P\parallel Q) = \sum_x P(x)\log\frac{P(x)}{Q(x)}.$$

---

## Bayesiánus értelmezés

Bayesiánus nyelven a KL-divergencia azt méri, hogy mekkora információváltozás történik, amikor a priorból poszterior lesz.

Ha \(Q\) a prior és \(P\) a poszterior, akkor:

$$D_{KL}(P\parallel Q)$$

azt mutatja meg, hogy a poszterior mennyire tér el a priortól.

Másképpen fogalmazva: azt méri, hogy mennyi információt nyertünk az adatok megfigyelésével.

- Ha a poszterior nagyon hasonlít a priorhoz, akkor a KL-divergencia kicsi. Ez azt jelenti, hogy az adat kevés új információt adott.
- Ha a poszterior nagyon eltér a priortól, akkor a KL-divergencia nagy. Ez azt jelenti, hogy az adat sokat változtatott a korábbi vélekedésünkön.

Fontos: a KL-divergencia nem szimmetrikus. Általában:

$$D_{KL}(P\parallel Q) \neq D_{KL}(Q\parallel P).$$

Ezért mindig figyelni kell az irányra. A bayesi információnyereségnél tipikusan ezt használjuk:

$$D_{KL}(\text{poszterior}\parallel \text{prior}).$$
