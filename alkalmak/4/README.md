# Eloszlások modellezése

## Ismétlés: kártyahúzás visszatevéssel (binomiális eloszlás)

Egy 52 lapos francia kártyapakliban annak a valószínűsége, hogy egy kártya kőr (♥): p = 13/52 = 0.25. Keressük annak az X valószínűségi változónak az eloszlását, ami azt mondja meg, hogy ha _visszatevéssel_ kiveszünk a pakliból 3 lapot, akkor hány ebből a kőr, tehát

X := ,,kőrök száma 3 visszatevéses húzásból, francia kártyapakliban''

````javascript
var model = function() {
  var H1 = flip(0.25);
  var H2 = flip(0.25);
  var H3 = flip(0.25);
  var X = H1 + H2 + H3;
  return {'X': X}
}
var eloszlás = Enumerate(model);

var binom = Binomial({p: 0.25, n: 3});

viz.auto(binom);
viz.auto(eloszlás);
````

````flip(0.25)```` most a categorical egy spéci, spórolós változata. Boole-értéket ad vissza (azaz 0-t vagy 1-et) mégpedig 0.25 arányban az 1 javára. Húzás után visszatesszük a lapokat, tudjuk jól, ez a binomiális eloszlás és ezért a ````binom```` változó ugyanolyan eloszlású lesz. Lásd még a webppl dokumentációját! 

### Visszafelé következtetés ### 

**Érdekesebb** probléma a következő. Ha tudunk valamit a szituációból, ez az eloszlás változni fog. Pl.: mi akkor X eloszlása, **ha tudjuk,** hogy az első esetben kőrt húzunk. 

````javascript

var model2 = function() {
  var H1 = flip(0.25)
  var H2 = flip(0.25)
  var H3 = flip(0.25)
  condition( H1 == 1 )
  var X = H1 + H2 + H3
  return {'X': X}
}
var eloszlás2 = Enumerate(model2)

viz.auto(eloszlás2)
````
Itt ismét ````condition( H1 == 1 )```` játszotta a fő szerepet. Világos, hogy X már nem vehet fel 0 értéket, mert már H1 == 1.

Még érdekezebb a dolog, ha az X-et tudjuk, vagyis ő a megfigyelt változó:

````javascript

var model3 = function() {
  var H1 = flip(0.25)
  var H2 = flip(0.25)
  var H3 = flip(0.25)
  var X = H1 + H2 + H3
  condition( X == 1 )
  return {'H1': H1}
}
var eloszlás3 = Enumerate(model3)

viz.auto(eloszlás3)
````

Ez már egy inferálás: a H1, H2, H3 látens változókat inferáljuk (következtetjük ki) az ismert X változóból.  

### Általában egy _X_ binomiális változó eloszlása ###

[![\\ \Pr(X = k) = \binom{n}{k}p^k(1-p)^{n-k}](https://latex.codecogs.com/svg.latex?%5C%5C%20%5CPr(X%20%3D%20k)%20%3D%20%5Cbinom%7Bn%7D%7Bk%7Dp%5Ek(1-p)%5E%7Bn-k%7D)](#_)

Tehát van egy _p_ valószínűségű Boole-változó (Bernoulli-változó) és definiálunk egy új változót: _X_ jelentse azt, hogy ha _n_-szer egy ilyen ($p$ szerint igazat vagy hamisat adó) kísérletet végrehajtunk (egymástól **függetlenül**), akkor ebből hányszor lesz _igaz_.

A képlet magyarázata röviden a következő. Ha pontosan tudnánk, hogy az $n$ hosszú kísérletsorozatból az első $k$-ban teljesül ($p$ valószínűséggel) a vizsgált tulajdonság, a többiben nem, akkor ennek a sorozatban a valószínűsége: $p^k(1-p)^{n-k}$, hiszen az elemi kimenetelek függetlenek és a komplementer esemény valószínűsége $1-p$, amiből $n-k$ van. No, most már képzeljünk el $n$ helyet egymás mellett, amelyekre az igaz szót tesszük le. Amikor az a kérdés, hogy $k$ igazat hányféleképpen tudunk erre az $n$ helyre letenni, akkor a válasz $\binom{n}{k}$. Mindegyik elrendezés megfelel a $k$ db igaz feltételnek, továbbá az ilyen lerakások kölcsönösen kizárják egymást, ezért ezeket csak össze kell adni, ezt megteszi az n-alatt a k szorzó. 

## Kudarcorientált sikervadászat: negatív binomiális eloszlás 

Józsit, a Blaha Lujza téri aluljáró életművésze és egy nagyon speciális küldetése van: pontosan 3 szál cigit akar tarhálni az arra járóktól, hogy utána nyugodtan visszavonulhasson a haverokhoz a pékség elé. Józsi stratégiája. Minden szembejövőt leszólít: "Ne haragudjon, hogy megszólítom! Egy szál cigit tudna-e adni? Buszjegyre kell." Minden leszólítás egy független statisztikai kísérlet.

* **"Siker" (p)** : a kiszemelt megáll, sóhajt egyet, és ad egy szál cigit. Mivel Pesten rohanunk, ennek a valószínűsége elég kicsi (p=0.05).

* **"Kudarc" (k)**: a járókelő leszegett fejjel felgyorsít, a távolba bámul, vagy bedobja a "Jaj, jön a 4-es!" védekezést.

* **Cél (r)**: Józsinak pontosan r=3 sikerre (cigire) van szüksége.

(Egyéb példa: tinder 3 randi, álláskeresés 3 visszajelzés.) Megjegyzés az elnevezésre: a siker száma kőbe van  vésve (r=3), de a próbálkozások/kudarcok száma a végtelenbe nyúlhat, próbára téve Józsi tűrőképességét.

### Algoritmikus modellezés

Keressük annak az X valószínűségi változónak az eloszlását, ami azt mondja meg, hogy ha visszatevéssel addig húzunk, amíg meg nem kapjuk a 3. feature-t, akkor addig hány negatív kimenetelt kapunk. Tehát amit visszaad a negatív esetek száma:

X := „nem-feature-ök száma a 3. feature előtt, visszatevésesen”

````javascript
var negBinFailures = function(r, p) {
  var loop = function(successes, failures) {
    if (successes === r) {
      return failures;
    } else {
      var s = flip(p);
      return loop(
        successes + (s ? 1 : 0),
        failures + (s ? 0 : 1)
      );
    }
  };
  return loop(0, 0);
};

var model = function() {
  return negBinFailures(3, 0.25);
};

var eloszlas = Infer({
  method: 'forward',
  samples: 10000,
  model: model
});

viz.auto(eloszlas);
````

A **forward sampling** sokszor lefuttatja a programot, és megnézi, milyen értékeket ad vissza. Aztán megszámolja, melyik érték hányszor jött ki. Ebből lesz az eloszlás közelítése. Ez egy **Monte Carlo módszer** és nem is nagyon lehet Enumerate-tel, mert végtelen ciklusba kerülne az algoritmus.

### Elméletben egy $X$ negatív binomiális változó eloszlása ###

Ha $X$ azt jelenti, hogy **hány kudarc történik az $r$-edik siker előtt**, és egyetlen kísérletben a siker valószínűsége $p$, akkor

$$
\Pr(X = k) = \binom{k+r-1}{k}(1-p)^k p^r,
\qquad k=0,1,2,\dots
$$

A negatív binomiális eloszlás szokásos paraméterezése: az $r$ rögzített, a próbákat egymástól **függetlenül** ismételjük, és azt nézzük, hány kudarc ($k$) gyűlik össze, mire megérkezik az $r$-edik siker.

A számítás. Tudjuk, hogy az utolsó eset siker. Egy ilyen konkrét sorozat valószínűsége

$$(1-p)^k p^{r-1}$$

Az első $k+r-1$ helyen a $k$ kudarcot $\binom{k+r-1}{k}$ féleképpen lehet elrendezni. Az egymást kölcsönösen kizáró lehetőségeket összeadva kapjuk a fenti összefüggést.

### Picit érdekesebb kérdés

Mi az eloszlása az első kísérletnek, ha tudjuk, hogy mennyi lett a kudarcok száma. A legelső ember, akit Józsi aznap reggel leszólított, adott-e neki cigit (1) vagy elhajtotta (0)?

````javascript
var model2 = function() {
  var H1 = flip(0.25) ? 1 : 0;

  var loop = function(successes, failures) {
    if (successes === 3) {
      return failures;
    } else {
      var H = flip(0.25) ? 1 : 0;
      return loop(successes + H, failures + (1 - H));
    }
  };

  var X = (H1 == 1) ? loop(1, 0) : loop(0, 1);
  condition(X == 1);

  return {'H1': H1}
}

var eloszlás2 = Infer({
  method: 'rejection',
  samples: 5000,
  model: model2
});

viz.auto(eloszlás2);
````

## Monty Hall- (vos Savant-) paradoxon

<img src="https://github.com/mozow01/cog_compsci/blob/main/SciCamp/The-Monty-Hall-Problem-e1623680322430.png" width=200>

Adott 3 csukott ajtó mögött egy-egy nyeremény: 1 autó és 1-1 plüsskecske. Monty, a showman megkér minket arra, hogy tippeljük meg, hol az autó (ha eltaláljuk, a miénk lesz). Amikor ez megtörtént, akkor Monty kinyit egy ajtót, éspedig szigorúan azok közül egyet, amelyek mögött egy kecske van és nem mutattunk rá. Majd felteszi újra a kérdést: hol az autó. Érdemes-e megmásítanunk a döntésünket?

A feladatot a joint eloszlás feltérképezésével oldjuk meg.

X a tippünk, Y az autó helye, Z az, hogy Monty melyik ajtót nyitja ki.

|      |     | Y=1 |     |     | Y=2 |     |     | Y=3 |     | P(X) |
| ---  | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
|      | Z=1 | Z=2 | Z=3 | Z=1 | Z=2 | Z=3 | Z=1 | Z=2 | Z=3 |     | 
| X=1  | 0   | 1/18| 1/18| 0   | 0   | 1/9 | 0   | 1/9 |   0 | 1/3 | 
| X=2  | 0   |  0  |1/9  | 1/18   | 0   |  1/18  | 1/9 | 0 |   0 |  1/3| 
| X=3  | 0   | 1/9 |  0 | 1/9   | 0   | 0 | 1/18 | 0  |   1/18 |  1/3 | 
| P(Y) |    | 1/3 |    |    | 1/3 |   |   | 1/3 |    |   1  | 

**A táblázat kitöltéséhez** érdemes végiggondolni, hogy mi függ mitől. Pl. **X és Y biztosan független:** P(X*Y)=P(X)*P(Y). Ezt fel fogjuk a számolásban használni. Itt persze P(X) egy _marginális eloszlás,_ amikor összegzünk (kilőjük) mind Y-t, mind Z-t.  

Aztán azt is feltehetjük, hogy X, Y is egyenletes, azaz nem részesít előnyben egy ajtót se egy berendező se. És mi is úgy választunk, hogy az találomra megy.

Monty egyenletes eloszlással (találomra?) választ ajtót, azaz P(X=1,Y=1,Z=2) = P(X=1,Y=1,Z=3), csak arra vigyáz, hogy se a mi jelöltünket, se az autót ne fedje fel. Monty mindig kecskét mutat meg!!!

**Számoljuk ki egy esetben, mi annak a valószínűsége, hogy ugyanazon ajtó mögött van a nyeremény, amire mutattunk.** Pl.: P(X=1 és Y=1) = 1/9. Persze ezt mindhárom esetben ki tudjuk számolni, és az eredmény (a független lehetőségek összeadási szabály amiatt)

P(X=Y) = 1/3

Ez annak az esélye, hogy elsőre eltaláljuk a kedvező ajtót (ez világos is). Annak a valószínűsége, hogy nem a választottunk mögött van az autó, a komplementer szabály szerint:

P(X=/=Y) = 1 - 1/3 = 2/3

Monty kinyitja a megmaradó kettő közül azt az ajtót, ami mögött nincs autó és nem is mutattunk rá, ezért utólag behatárolja azt a _két_ ajtót, ami mögött az autó van. Nyilván eredetileg nem bökhettünk volna rá két ajtóra, amelyek persze kétszer annyi valószínűséggel rejtik az autót. De most, hogy ebből a kettőből mutatott Monty egy rossz ajtót, már érvényesíthetjük a P(X=/=Y) = 2/3 valószínűségű nyerést egyetlen ajtóra való rámutatással. Ami persze nem jelenti, hogy ott is lesz az autó, de kétszer akkora eséllyel lesz ott, mint nem. 

Marilyn vos Savant egy szellemes példán mutatta be, hogy miért igaz az, hogy messze jobb váltani. Az érvelése analógiás és a következő. Lefordítunk 10000 kagylót egy parkolóban és az egyik alá rejt Marilyn egy gyöngyöt. Rámutatunk az egyikre azzal, hogy ott van a gyöngy. Találatot ezzel 1/10000 eséllyel érünk el. 

                    ✨

    📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀📀

Most Marilyn pontosan kettő kivételével az összes kagylót elveszi, éspedig az igaz erre a fel nem fordított kettőre, hogy közte van az is, amire mutattam, és az is, ahol a gyöngy van. 

                    ✨
  
    📀               📀


Érdemes-e váltani? Természetesen, hiszen így 9999/10000 az esélye, hogy azalatt van a gyöngy, amire nem szavaztunk. Gyakorlatilag Marilyn megmutatta, hogy hol a gyöngy és 10000-ből 1-szer lesz csak pechünk, amikor is eredetileg jól választottunk.

````javascript
var vosSavantProblem = function () {
    var Autó = categorical({ps:[1/3,1/3,1/3], vs:[1, 2, 3]})
    var Tipp = categorical({ps:[1/3,1/3,1/3], vs:[1, 2, 3]})
    var Monty = (Autó == Tipp) 
                ? ( (Autó == 1) 
                   ? categorical({ps:[1/2,1/2], vs:[2, 3]}) : 
                   ( (Autó == 2) ? categorical({ps:[1/2,1/2], vs:[1, 3]}) :
                    categorical({ps:[1/2,1/2], vs:[1, 2]}) ) )
                : ( (1 !== Autó && 1 !== Tipp ) ? 1 :
                   ( (2 !== Autó && 2 !== Tipp ) ) ? 2 : 3 )
    
    var stratégia_maradás = (Autó == Tipp) ? 'nyer' : 'veszít'
    
    var ÚjTipp = (Autó !== Tipp) 
                ? Autó
                : ( (Tipp == 1 && Monty == 2) ? 3 : 
                   ( (Tipp == 1 && Monty == 3) ? 2 : 
                   ( (Tipp == 2 && Monty == 1) ? 3 :
                   ( (Tipp == 2 && Monty == 3) ? 1 :
                   ( (Tipp == 3 && Monty == 1) ? 2 : 1 ) ) ) ) ) 
    
    var stratégia_váltás = (Autó == ÚjTipp) ? 'nyer' : 'veszít'
    
    return  {
             stratégia_maradás: stratégia_maradás 
             //stratégia_váltás: stratégia_váltás
            } 
}

var eloszlás = Enumerate(vosSavantProblem)

viz.marginals(eloszlás)
````

Láthatóan a váltás a nyerő stratégia.




