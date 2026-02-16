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

# Mélyvíz

Van egy törpehörcsögünk, amelyikről azt gyanítjuk, hogy rendellenesen fogy. A súlya (tömege :) ) elméletileg egy 22 g közepű 1 g-os szórású normál eloszlás (haranggörbe). El kéne dönteni, hogy orvoshoz kell-e vinni. 

<p align="center">
  <img src="https://raw.githubusercontent.com/mozow01/Bayes2024/main/1_gyak/horcsi.jpeg" alt="Csofi"><br>
  <b>Csofi</b>
</p>

