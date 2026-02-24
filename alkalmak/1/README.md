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

Végül is szeretném tudni, hogy a hörcsög mekkora súlyú. Nyilván valamekkora (valahogy csak vannak a dolgok!!!), de nem tudjuk. Nem örülnénk, ha mondjuk 22 - 2 = 20-nél kisebb lenne. Legyen a hörcsög valódi súlya $\theta$.

* **H0 (nullhipotézis):** az állat egészséges, $\theta \geq 20$.
* **H1 (alternatív hipotézis):** az állat rendellenesen sovány, $\theta < 20$.

Hogyan férek hozzá Csofi súlyához? Megmérem. Nyilván többször, mert egy mérés, nem mérés. A súlymérés normál eloszlást követ, Csofi súlyméréseinek adatai: 

$$x \sim \mathcal{N}(\theta, \sigma)$$

Az adatok átlagára ugyanez: 

$$\bar{x} \sim \mathcal{N}(\theta, \sigma/\sqrt{n})$$

Ezt még mi magunk is le tudnánk vezetni. Ha ismernénk a mérések valódi szórását ($\sigma$), akkor ezt a haranggörbét egy egyszerű lépéssel "standardizálhatnánk", hogy könnyen számolható legyen. Ezt a standardizált értéket hívják $Z$-értéknek:

$$Z = \frac{\bar{x} - \theta}{\sigma/\sqrt{n}}$$

A $Z$ érték egy arányszám: azt mondja meg, hogy a mért átlag hányszorosa a standard hibának. Jelen esetben nem bízunk a $\pm 1$ éstékben, nem hisszük, hogy \sigma$ valóban ennyi. A legtöbb esetben $\sigma$ ismeretlen. Most például azért mert nem tudjuk a mi általunk végzett mérések alapját képező normál eloszlás standard hibáját.  

Ha $\sigma$ ismeretlen, kénytelenek vagyunk a rendelkezésre álló mintából (a mi méréseinkből) becsülni azt. Ezt a becslést hívjuk tapasztalati szórásnak ($S$), aminek a statisztikusok által javasolt képlete a következő:

$$S = \sqrt{ \frac{\sum_{i=1}^n (x_i - \bar{x})^2}{n-1} }$$

Mivel az $S$ kiszámításához a véletlenszerűen ingadozó, mért $x_i$ adatokat használjuk, maguk a mérések pedig minden alkalommal mások, ezért az ezekből kiszámolt $S$ is egy **valószínűségi változó** lesz. 

Ha a fix, stabil $\sigma$ helyett ezt a bizonytalan $S$-t tesszük be a standardizáló képletbe, akkor az eredmény már nem egy tökéletes normál eloszlású $Z$-érték lesz. Pont ez az az jelenség, ami a $t$-statisztikát definiálja.

Ahhoz viszont, hogy a matematikai levezetések szépek legyenek, és persze a helyes valószínűségeket adják az $S$ képletének nevezőjében **$n-1$-gyel kell osztanunk** (ahol $n$ a mérések száma). Ez egyszerűen a levezetésből következik, amit persze nincs időnk és türelmünk végigcsinálni, de nem is kell. (Ha véletlenül $n$-nel osztanánk, az rossz lenne, sajnos alábecsülnénk a valódi szórást (mert a mintaátlaghoz viszonyítunk, nem az igazi középhez). A torzítatlan, korrekt eredményhez az $n-1$ a helyes érték, ez némiképp kontra-intuitív, mert középiskolában a szórás képletében nem ezt tanultuk, de ez nem az egyszerű szórás, hanem egy statisztikai becslési paraméter, ami egy számítás során jön ki. (A statisztikában egyébként ezt az $n-1$ paramétert pusztán konvencióból szabadságfoknak nevezik, de nekünk most csak az a lényeg, hogy emiatt lesz a számolásunk matematikailag pontos).

Ezen a ponton feladjuk, és megkérdezzük a matematikust (Pearson, Gosset), hogy: 

*Mi az adataink* $p$ *valószínűsége, ha adaton mondjuk 3 mérést értünk és az átlagot gondoljuk a* $\theta$ *mért értékének, feltéve, hogy* $\theta \geq 20$?

A válasz a következő. 

Eldöntötted-e, hogy:
1.  hány mérést végzel?
2.  mekkora valószínűséget gondolsz problémásnak? 

1.  Igen, 3 mérés.
2.  Mondjuk $\alpha = 0.05$ alatt már nagyon gyanús (szignifikancia szint).

Akkor a fentiek alapján számold ki ezt a $t$-értéket (ahol 20 a vizsgált határ): 

$$t = \frac{\bar{x} - 20}{S/\sqrt{n}}$$

Majd fogj egy t-táblázatot, és keresd ki, hogy mennyi a $p$ a $t$-hez. ( https://www.socscistatistics.com/tests/tsinglesample/calculator/ )

Mivel $p = 0.019$, ezért 0.05 szinten szignifikáns az eltérés, azaz 1000 mérésből csak 19 lenne olyan, amelyik ilyen, mint ez. Ez nagyon kevés. Valami baj van, Csofi súlya nincs összhangban azzal, hogy a 20-nál nagyobbak az egészséges állatok értékei. A nullhipotézist elvethetjük.

### Naiv, álnaiv kérdések (és a két tábor válaszai)

**1. Értem a haranggörbét, de mi az a t-statisztika? Mi ez a "made-up" dolog?**
* **Frekventista:** Ez egy standardizált távolság, megmutatja, hogy a standard hiba hányszorosa a mért átlag eltérése a középtől, belekalkulálva a szórás becslésének bizonytalanságát is.
* **Bayesiánus:** Ez egy teljesen felesleges absztrakció. Mi nem $t$-értékkel fogunk számolni, hanem közvetlenül Csofi súlyának valószínűségét fogjuk modellezni.

**2. Miért az az alternatív hipotézis, hogy** $\theta < 20$ **? Miért nem vizsgáljuk a beteg (17 g) vs. egészséges (22 g) állapotot közvetlenül?**
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

## Útravaló

1. Ha Csofiról (a hörcsögről) nem tudnátok semmit, csak ránéznétek a sziluettjére, és azt hinnétek, hogy egy kiselefánt, vajon hány darab 18 grammos mérés kellene ahhoz, hogy az agyatok elhiggye, hogy ez mégiscsak egy hörcsög?
2. Gondoljatok egy mai, teljesen hétköznapi döntésetekre (pl. melyik úton jöjjek az egyetemre). Ha ezt le kéne programoznotok WebPPL-ben, mi lenne benne az előzetes tudás és menet közben változó operatív döntés?
3. Ha a frekventista statisztika tényleg csak elvetni tud dolgokat, és egy önkényes 0.05-ös határra épül, akkor miért ezt használja a tudományos cikkek 90%-a? Miért kényelmesebb az emberi elmének egy szignifikáns / nem szignifikáns bináris döntés, mint a Bayes-féle inherens bizonytalanság elviselése?
