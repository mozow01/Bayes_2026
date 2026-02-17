# Valószínűségi programozás

Olyan programozási nyelv, amelyben nem csak determinisztikus függvényket lehet alkalmazni, hanem "pszeudo-random", sztochasztikus függvényeket is. Egy ilyan a Stanford CoCoLab-ban fejlesztett WebPPL nyelv ( http://webppl.org/ ), ami a JavaScript egy funkcionális töredéke, feldúsítva a valószínűségi függvényekkel és eljárásokkal.

Egy konkrét dobás:
 
````javascript
flip(0.7);
````

flip() a szabályos pénzérme eloszlása, de flip és flip() nem ugyanaz

````javascript
flip; flip(); sample(flip)
````

sőt, a magáért beszélő sample kulcsszó _függvényt_ vár amely valami, ami ilyen alakú:

````javascript
function( ... ) { return ... };
````

Pl. ````function(x,y) { return x + y };````. Ilyenek programbeli helye egy függvénydeklarációt kíván: ````var f = function(x,y) { return (x + y) }; ````

Így a nyelvileg helyes parancs:

````javascript
sample(flip())
````

````javascript
repeat(4,flip());
````

Ha cinkelt pénzérmét akarunk, akkor definiálunk kell egy ezt a kifejezést visszaadó függvényt: 

````javascript
function() { return flip(0.7) };
````
Egyébként ````flip(q)```` az ugyanaz mint ````sample(Bernoulli({p:q}))````.

# Csofi története

Van egy törpehörcsögünk, amelyikről azt gyanítjuk, hogy rendellenesen fogy. A súlya (tömege :) ) elméletileg egy 22 g közepű 1 g-os szórású normál eloszlás (haranggörbe). El kéne dönteni, hogy orvoshoz kell-e vinni. 

<p align="center">
  <img src="https://raw.githubusercontent.com/mozow01/Bayes2024/main/1_gyak/horcsi.jpeg" alt="Csofi"><br>
  <b>Csofi</b>
</p>

Pl. egy közelítő mintavételle: ````Gaussian({mu: 22, sigma: 1})````.

Amúgy nagyon gyanús a $\sigma=1$ g, mert nem tudjuk, hogy miből jön ki: -- napi súlyváltozás, -- mérleg pontossága, -- hörcsögök közötti biológiai variancia? Vagy a tényleges matematikai szórás? A 22 jó lesz populációs átlagnak: $\mu_0=22$, de ezzel önmagában nem tudunk mit kezdeni... Ott vagyunk a kezünkben egy sovec hörcsöggel.

## Amit eddig tanultunk: hipotézisvizsgálat

Végül is szeretném tudni, hogy a hürcsög mekkora súlyú. Nyilván valamekkora (valahogy csak vannak a dolgok!!!), de nem tudjuk. Nem örülnénk, ha mondjuk 22-1=21-nél kisebb lenne. Legyen a hörcsög valói súlya $\theta$.

H0 (nullhipotézis) : az állat egészséges, $\theta\geq 20$.

H1 (alternatív hipotézis) : az állat rendellenesen sovány, $\theta<20$.

Hogyan férek hozzá Csofi súlyához? Megmérem. Nyilván többször, mert egy mérés, nem mérés. A súlymérés normál eloszlást követ, Csofi súlyméréseinek adatai: 

$$x\sim \mathcal{N}(\theta,\sigma).$$

Az adatok átlagára ugyenez: 

$$\bar{x}\sim \mathcal{N}(\theta,\sigma/\sqrt{n})$$

Ezt még mi magunk is le tudnánk vezetni. A probléma, hogy $\sigma$-t, a mérési adatok eloszlásának szórását nem tudjuk (egy képzeletbeli világban megvan minden mérés és abban a világban az adatok szórása lenne az). Ezen a ponton feladjuk, és megkérdezzük a matematikust (Pearson, Gosset), hogy 

_mi az adataink p valószínűsége, ha adaton mondjuk 3 mérést értünk és az átlagot gondoljuk a_ $\theta$ _mért értékének, feltéve, hogy_ $\theta\geq 20$.

A válasz a következő lesz. Eldöntötted-e, hogy 1. hány mérést végzel és hogy 2. mekkora valószínűséget gondolsz problémásnak? Válaszunk: 

1. igen, 3 mérés.
2. mondjuk $\alpha=0.05$ alatt már nagyon gyanús (szignifikancia szint).

Akkor számold ki ezt: 

$$t=\dfrac{\bar{x}-21}{s_x/\sqrt{n}}$$

ahol $s_x^2=(\sum (x_i-\bar{x})^2)/(n-1)$. Majd fogj egy t táblázatot és keresd ki, hogy mennyi a p a t-hez. ( https://www.socscistatistics.com/tests/tsinglesample/calculator/ ).

Mivel $p=0.019$, ezért $0.05$ szinten szignifikáns az eltérés, azaz 1000 mérésből csak 19 lenne olyan, amelyik ilyen, mint ez. Ez nagyon kevés. Valami baj van, Csofi súlya nincs összhangban azzal, hogy a 20-nál nagyobbak az egészséges állatok értékei.

A nullhipotézist elvethetjük.

### Naiv, álnaiv kérdések

1. Értem, hogy a súly haranggörbe, mert nem vagyok hülye, de akkor mi az a t statisztika? Mi ez a made-up dolog?
2. Mennyiben "betegesen" rendellenes egy 20-nál kisebb érték? Miért ez az alternatív hipotézis? Miért nem mondjuk az, hogy 17 g +- 1 g ? 
3. A próba alapján két választ kaphatok: A) nem vethető el a nullhipotézis, B) elvethető a nullhipotézis. Egyik esetben sem a minket érdeklő kérdésre kapunk választ.
4. Mi az a t-képlet? Jelent valamit vagy nem jelent semmit? (Miért kell statisztikusnak lennem?)
5. Tudom, hogy a próba annál jobb, minél többször mérem a hörcsit. Miért kéne nyaggatni szegényt mondjuk 100 méréssel egy értelmezhetetlen eredmény miatt? Miért úgy csinálom a mérést, mint egy sörgyáros? Miér nem úgy, ahogy egy állatorvos?  

## Bayes-féle megközelítés

Csofi súlya egy bizonytalan érték (inherensen bizonytalan). Két dolgot tudunk, hogy Gauss(22,1) egy egészséges állat súlya, Gauss(17,1) egy betegé. Csofi a mérések alapján 19, 18, 18 g. Ez eléggé leszűkíti a lehetősőgeket. Ha kiszórjuk azokat a szcenáriókat, amelyekben ezek a számok nagyon pici valószínűségűek, akkor egy olyan eloszlást kapunk a súlyára, amelyik közel állhat a valósághoz. 

````javascript
var data = [
  {k: 19},
  {k: 18},
  {k: 18}
];

var Model = function() {
  // modell index: 1 = rgészséges, 2 = beteg
  var i = categorical({vs: [1, 2], ps: [0.5, 0.5]});

  // i-től függő m
  var m = (i === 1) ? gaussian(22, 1) : gaussian(17, 1);

  // likelihood
  map(function(d){
    observe(Gaussian({mu: m, sigma: 1}), d.k);
  }, data);

  // a modell indexét és a közepet kapjuk vissza
  return {i: i};
};


var opts = {method: 'SMC', particles: 3000, rejuvSteps: 5};
var post = Infer(opts, Model);


viz.marginals(post);
print("i");

// Priorok:
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
print("N(22,1)");


viz.marginals(prior2);
print("N(17,1)");

print((19+18+18)/3);
````

## Összevetés

|                   | Frekventista statisztika                             | Bayesiánus statisztika                                 |
|-----------------------------|------------------------------------------------------|------------------------------------------------------|
| Alapelvek                   | Egyetlen matematikailag kifundált mintatérből vesz mintákat és ezek alapján következtet. | Előzetes tudással (prior), adattal (megfigyelt változó) és adatfelvétel után levont (poszterior)  következtetésekkel dolgozik. |
| Előzetes elvárások          | Nem utal előzetes tudásra, csak a mintavételezéskor keletkező mintára összpontosít. Az előzetes tudás tacit. | Bevezeti a prior elvárásokat, amelyek a kezdeti ismereteket és hozzáértést tükrözik. Explicit előzetes tudással dolgozik.                |
| Paraméterek értelmezése     | A paraméterek fix értékek, amelyek ismeretlenek, de konstansok.                                | A paraméterek valószínűségi eloszlások formájában jelennek meg.  |
| Bizonytalanság kezelése      | A bizonytalanságot konfidencia intervallumokkal fejezi ki, az intervallum végpontjai egyfajta kétpontú pontbecslés. A 95%-os konfidenciaintervallum azt jelenti, hogy az ismeretlen paraméter 95%-os valószínűséggel található meg a kiszámított tartományban.                                             | A paraméternek inherens bizonytalansága van, amit valószínűségi eloszlásos formájában feltételez. Így egy ilyen következtetés nem pontszerű, hanem eloszlást ad vissza. Ennek legsűrűbb intervalluma a 95%-os HDI.   |
| Adatkövetelmény              | Gyakran nagy mintaméretet igényel, hogy az eredmények stabilak legyenek.                                  | Használható kis minta esetén is, mivel a prior tudás rásegít az adatokra. |       
| Adatfeldolgozás              | Kész analitikusan levezetett képletek a számítógép előtti korból, normalitási, függetlenségi feltételekkel. Táblázatokra, statisztikusi tapasztalatra hivatkozik.                            | Egységes elméleti keretrendszert ajánl fel: előzetes tudás + aktuális adat -> aktuális tudás, számítógéppel számolja a következtetéseket. |                        |
|  Jelenségek modellezése              | Mintavételzési eljárásokkal dolgozik.  | Igazodik a természettudományos, valódi kísérletekhez, ezeket formalizálja, explicitté teszi.    |               
| Interpretáció               | Gyakran konfidencia intervallumokkal és hipotézisvizsgálatal dolgozik, amelyek értelmezése nehézkes, körmönfont.  | A valószínűségi értelmezés miatt könnyebb értelmezni a statisztikai eredményeket.                   |
