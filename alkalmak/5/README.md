# 🎲 Bayesiánus modellezés és valószínűségelmélet

Tudjuk:

1. Mit jelent egy valószínűségi algoritmikus modell.

Kell még:

2. Hogyan írjuk le a valószínűségeket a **Kolmogorov-axiómák** segítségével?

3. Hogyan jutunk el a **feltételes valószínűségig** és a **Bayes-inferenciáig**?

---

## 1. A Kolmogorov-axiómák (a játékszabályok)

A valószínűség matematikai reprezentációjának alapja az $(\Omega,\Sigma,P)$ hármas, amit **valószínűségi mezőnek** nevezünk:
* $\Omega$: az **elemi események tere** (az összes lehetséges kimenetel halmaza, ami megtörténhet).
* $\Sigma$: az **eseménytér** (az elemi eseményekből álló halmazok, pl. "páros számot dobunk").
* $P$: a **valószínűségi mérték** (egy függvény, ami megmondja, mi mekkora eséllyel történik).

Formálisan a $P$ függvénynek három feltételt kell betartania:

> **1. Nemnegativitás:** Minden $A\in\Sigma$ eseményre a valószínűség sosem negatív:
> $$P(A)\ge 0$$
> 
> **2. Normáltság:** A teljes eseménytér (valaminek biztosan történnie kell) valószínűsége 1, a lehetetlen eseményé pedig 0:
> $$P(\Omega)=1 \qquad \text{és} \qquad P(\varnothing)=0$$
> 
> **3. Additivitás:** Ha $A,B\in\Sigma$ **egymást kizáró események** (azaz $A\cap B=\varnothing$), akkor:
> $$P(A\cup B)=P(A)+P(B)$$

### 💻 WebPPL példa
Egy egyszerű fair érmefeldobás modellje, ahol látszik, hogy a kimenetelek (Fej=true, Írás=false) összege pontosan 1 (Normáltság).

```javascript
var model = function () {
  var X = flip(0.5);   // 50% true (fej), 50% false (írás)
  return X;
};

// Az enumerate módszer végigveszi az összes (Ω) eseményt:
viz(Infer({method: "enumerate", model: model}));
```

---

## 2. Következmények és további fogalmak

Az axiómákból nagyon hasznos hétköznapi szabályok következnek.

### 🔄 Komplementer-szabály
Ha egy $A$ esemény valószínűsége $P(A)$, akkor annak az esélye, hogy **NEM** következik be:
$$P(A^c)=1-P(A) \qquad \text{vagy informálisan:} \qquad P(\neg A)=1-P(A)$$

### Logikai szita formula (VAGY kapcsolat)
Ha két esemény nem zárja ki egymást, vigyázni kell, nehogy kétszer számoljuk a közös részt!
$$P(A\cup B)=P(A)+P(B)-P(A\cap B)$$

### 🔗 Függetlenség (ÉS kapcsolat)
**Definíció:** Két esemény (A és B) akkor **független** egymástól, ha metszetük (együttes bekövetkezésük) valószínűsége a szorzatuk:
$$P(A\cap B)=P(A)\cdot P(B)$$

Azaz ha az egyik bekövetkezése nem ad információt a másikról.

### 💻 WebPPL Példa: Függetlenség és Unió
Nézzük meg, mi történik, ha két független eseményünk van (A és B)!

```javascript
var model = function() {
  var A = flip(0.3); // P(A) = 0.3
  var B = flip(0.4); // P(B) = 0.4
  
  return {
    metszet: A && B, // P(A ∩ B) = 0.3 * 0.4 = 0.12
    unio: A || B     // P(A ∪ B) = 0.3 + 0.4 - 0.12 = 0.58
  };
};

viz.marginals(Infer({method: "enumerate", model: model}));
```

---

## 3. Eloszlások (hogyan oszlik el a valószínűség?)

Egy valószűnűségi **eloszlás** azt mondja meg, hogyan rendeljük hozzá a valószínűséget ($P$) a lehetséges kimenetekhez.
* **Diszkrét esetben:** egyszerűen felsoroljuk a kimeneteleket és a %-os esélyüket.
* **Folytonos esetben:** sűrűségfüggvénnyel vagy eloszlásfüggvénnyel dolgozunk (később).

### 💻 WebPPL példa

```javascript
var model = function () {
  var weather = categorical({
    ps: [0.2, 0.5, 0.3], // Ezeknek is ki kell adniuk az 1-et!
    vs: ["napos", "felhős", "esős"]
  });
  return weather;
};

viz(Infer({method: "enumerate", model: model}));
```

---

## 4. Függő változók és a feltételes valószínűség

A valószínűségi változók gyakran **függenek egymástól**. Például a közlekedési dugó valószínűsége függhet attól, hogy esik-e az eső.

### 🌧️ A "pesti dugó" modell
* Eső ($R$): $P(R=\text{igen})=\frac{1}{3}$, $P(R=\text{nem})=\frac{2}{3}$
* Dugó ($T$), **ha esik**: $P(T=\text{dugó}\mid R=\text{igen})=\frac{1}{2}$
* Dugó ($T$), **ha nem esik**: $P(T=\text{dugó}\mid R=\text{nem})=\frac{1}{4}$

### 💻 WebPPL példa

```javascript
var model = function () {
  var R = categorical({
    ps: [1/3, 2/3],
    vs: ["esik", "nem esik"]
  });

  // A dugó eloszlása FÜGG az eső értékétől (Feltételes eloszlás)
  var T = (R === "esik") 
      ? categorical({ps: [1/2, 1/2], vs: ["dugó", "nincs dugó"]})
      : categorical({ps: [1/4, 3/4], vs: ["dugó", "nincs dugó"]});

  return { Idojaras: R, Kozlekedes: T };
};

viz.table(Infer({method: "enumerate", model: model}));
```

### 🧮 A teljes valószínűség tétele (marginalizáció)
Ha arra vagyunk kíváncsiak, mekkora a dugó esélye *összességében* (függetlenül az esőtől), össze kell adnunk az ágakat:
$$P(T=\text{dugó}) = P(T=\text{dugó}\mid R=\text{igen})P(R=\text{igen}) + P(T=\text{dugó}\mid R=\text{nem})P(R=\text{nem})$$
$$P(T=\text{dugó}) = \frac{1}{2}\cdot\frac{1}{3} + \frac{1}{4}\cdot\frac{2}{3} = \frac{1}{6} + \frac{1}{6} = \frac{1}{3}$$

---

## 5. A Feltételes valószínűség matematikai definíciója

A feltételes valószínűség lényege, hogy **leszűkítjük az elemi események terét** a feltételnek megfelelő esetekre. Ha tudjuk, hogy $B$ megtörtént, már csak $\Omega$ ezen részében gondolkodunk.

> **Definíció:** Ha $P(B)\neq 0$, akkor az $A$ esemény feltételes valószínűsége $B$ bekövetkezése esetén:
> $$P(A\mid B)=\frac{P(A\cap B)}{P(B)}$$

Ebből egyenesen következik a **Szorzatszabály** (ami a Bayes-tétel alapja is):
$$P(A\cap B)=P(A\mid B)\cdot P(B)$$

---

## 6. Joint (Együttes) eloszlás vs. Feltételes eloszlás

### 🃏 A Pénz és Kártya Példa
Legyen $X$ egy érmefeldobás ($1=$ Fej, $0=$ Írás, 50-50%). 
Legyen $Y=1$ az, hogy királyt húzunk egy pakliból. 
* Ha $X=1$ (Fej), magyar kártyából húzunk: $P(Y=1\mid X=1) = \frac{1}{8}$
* Ha $X=0$ (Írás), francia kártyából húzunk: $P(Y=1\mid X=0) = \frac{1}{13}$

| Feltételes eloszlás $P(Y \mid X)$ | $X=1$ | $X=0$ |
| :--- | :---: | :---: |
| $P(Y=1\mid X)$ | $1/8$ | $1/13$ |
| $P(Y=0\mid X)$ | $7/8$ | $12/13$ |

*(Figyeld meg: az oszlopok összege ad ki 1-et, hiszen rögzített X mellett ezek érvényes eloszlások.)*

A **Joint (Együttes) eloszlás** $P(X,Y)$ megkapható a szorzatszabállyal: $P(X,Y) = P(Y \mid X) \cdot P(X)$. Mivel $P(X)=0.5$, minden cellát elosztunk kettővel:

| Joint eloszlás $P(X, Y)$ | $X=1$ | $X=0$ |
| :--- | :---: | :---: |
| $Y=1$ | $1/16$ | $1/26$ |
| $Y=0$ | $7/16$ | $6/13$ |

*(Itt már a teljes táblázat (az összes cella) összege 1, mert ez az összes lehetséges univerzum terét írja le.)*

### 💡 Likelihood (Mi a különbség?)
Ha az egyenletben a kimenet ($Y=y_j$) van rögzítve (pl. megfigyeltük, hogy királyt húztunk), és azt vizsgáljuk, ez mennyire valószínű az $X$ különböző paraméterei (modellek) mellett, azt **Likelihood-függvénynek** hívjuk. Ennek az értékei nem feltétlenül adnak ki 1-et!

---

## 7. A Bayes-i Inferencia Csúcsa: Visszafelé következtetés

Lépjünk szintet! Mi van, ha nem a jövőt jósoljuk, hanem a kimenetből (késés) akarunk visszakövetkeztetni az okokra (eső, dugó)?

A Bayes-i gondolkodásban megkülönböztetünk:
* **Prior eloszlást:** Amit a világ működéséről gondolunk a megfigyelés előtt.
* **Posterior eloszlást:** A frissített tudásunkat az adatok (megfigyelések) megismerése után.

### 💻 WebPPL Példa: "Esett vagy nem esett, ha tudom, hogy késtem?"

Itt jön be a `condition()` utasítás ereje. Leszűkítjük a lehetséges világok számát azokra, ahol a késés tényleg megtörtént.

```javascript
var model = function () {
  // 1. PRIOR MODELLEZÉS (Ahogy a világ generálódik)
  var eso = flip(1/5);
  var dugo = eso ? flip(1/2) : flip(1/4);
  var keses = dugo ? flip(0.9) : flip(0.05);

  // 2. MEGFIGYELÉS (Likelihood beépítése / szűrés)
  // Csak azokat a világokat tartjuk meg, ahol tényleg késtem!
  condition(keses === true);

  // 3. POSTERIOR (Visszaadjuk a frissített valószínűségeket)
  return {
    eso_volt: eso,
    dugo_volt: dugo
  };
};

// Mivel itt kis állapottér van, az 'enumerate' kiszámolja a tökéletesen pontos poszteriort!
var Posterior = Infer({method: "enumerate", model: model});
viz.marginals(Posterior);
```

Amikor ezt lefuttatod, látni fogod: az eső eredeti 20%-os esélye (prior) drasztikusan megugrik a posteriorban, mert a késés ténye (a likelihoodon keresztül) erős bizonyíték arra, hogy az utakon valami gond volt!

---

## 📚 Mini Szótár a Túléléshez

* **$\Omega$ (Elemi események tere):** Minden, ami megtörténhet.
* **$\Sigma$ (Eseménytér):** Az események (részhalmazok), amiknek valószínűséget adhatunk.
* **$P$ (Valószínűségi mérték):** A Kolmogorov-axiómákat teljesítő függvény.
* **Joint (együttes) eloszlás:** Változók együttmozgásának teljes térképe, $P(X,Y)$. A cellák összege 1.
* **Feltételes eloszlás:** Egy változó eloszlása egy másik *rögzített* értéke mellett, $P(Y \mid X)$. Az oszlopok összege 1.
* **Marginalizáció:** Egy vagy több változó "kiejtése" a joint eloszlásból egyszerű összeadással.
* **Prior:** Amit azelőtt tudtunk, hogy megláttuk volna az adatot.
* **Posterior:** A frissített tudásunk az adat megfigyelése után.
* **Likelihood:** Megmutatja, hogy egy megfigyelt adat mennyire valószínű a különböző hipotézisek/modellek fényében.

---

## 🧠 Ellenőrző Kérdések a Villamosra

1. Miben különbözik zeneileg (akarom mondani, statisztikailag) a $P(A)$ és a $P(A\mid B)$?
2. Miért nem ugyanaz a feltételes eloszlás táblázata és a joint eloszlás táblázata? (Segítség: hol adódik össze az 1?)
3. Mit jelent az hétköznapi nyelven, ha $P(X, Y) = P(X) \cdot P(Y)$?
4. Ha a WebPPL kódba beteszel egy `condition()` sort, az melyik eloszlást csinálja meg a másikból: a posteriorból a priort, vagy fordítva?
