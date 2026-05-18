# King–Ace paradoxon WebPPL-ben

## 1. Mit akarunk megmérni?

A vizsgálatban azt nézzük meg, hogy a diákok hogyan ismernek fel egy logikai következtetést.

A kiinduló feladat a King–Ace probléma:

```text
"If there is a king in the hand, then there is an ace in the hand,"
or
"If there is not a king in the hand, then there is an ace in the hand,"

but not both of these if-thens are true.
```

Magyarul:

```text
Vagy igaz az, hogy ha király van a kézben, és akkor ász van a kézben,
vagy nem igaz az, hogy király a kézben, és akkor ász van a kézben.
```

Jelölések:

```text
K = király van a kézben
A = ász van a kézben
```

Formálisan:

```text
(K → A) vagy (¬K → A)
```

A kérdés:

```text
Következik-e ebből, hogy van ász?
Következik-e ebből, hogy nincs ász?
```

A vizsgálatban két lehetséges belső modellt hasonlítunk össze.

| modell | alapötlet |
|---|---|
| mentális modell | a diák a könnyen elképzelhető eseteket látja |
| intuicionista modell | a diák azt fogadja el, amire van levezetés |

A program nem azt kérdezi, hogy egy diák „okos” vagy „hibázott-e”.

A program ezt kérdezi:

```text
A válaszok inkább melyik belső modellhez hasonlítanak?
```

Vagyis:

```text
P(mentális modell | válaszok)

P(intuicionista modell | válaszok)
```
