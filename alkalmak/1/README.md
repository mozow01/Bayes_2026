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

