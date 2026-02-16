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

sample kulcsszó _függvényt_ vár amely valami, ami ilyen alakú:

````javascript
function( ... ) { return ... };
````

Pl. ````function(x,y) { return x + y };````


````javascript
sample(flip)
````

````javascript
repeat(4,flip);
````



sample(Bernoulli({p: p}))

````javascript
repeat(5,function () {return flip(0.9)});
````

