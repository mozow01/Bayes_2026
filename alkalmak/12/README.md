# King–Ace paradoxon - mentális modell / bizonyék alapú következtető modell

## 1. Bev.

A kísérletben a résztvevők rövid következtetésekről döntenek. Minden esetben azt kell megítélniük, hogy a megadott konklúzió **érvényesen következik-e** a premisszákból.

Azt szeretnénk modellezni, hogy a válaszok mögött milyen **belső kognitív-logikai modell** állhat.

Két modellt hasonlítunk össze:

1. **Mentális modell**
2. **Bizonyék alapú következtető modell**

A kérdőívben szerepelhet a három mondat konnektívum:

- `vagy`
- `és`
- `kizáró vagy`

A statisztikai modellben két elméleti válaszmodellt használunk:

```text
M1 = mentális modell
M2 = bizonyék alapú következtető modell
```

A cél az, hogy a válaszadatok alapján megbecsüljük:

```text
P(mentális modell | adatok)
P(intuicionista modell | adatok)
```

Vagyis azt kérdezzük:

> Az csoport válaszai inkább egy intuitív, mentális-modelles feldolgozást tükröznek, vagy inkább egy levezetés-alapú logikai feldolgozást?

---

## 2. A King–Ace probléma alapformája

Legyen:

```text
K = király van a kezemben
A = ász van a kezemben
```

A klasszikus King–Ace jellegű mondat informálisan így hangzik:

> Vagy király van a kezemben, és akkor ász van a kezemben, vagy nem király van a kezemben, és akkor is ász van a kezemben.

A kognitív érdekesség abból származik, hogy sok ember a mondatot úgy dolgozza fel, mintha két explicit lehetőséget kapna:

```text
K     A
¬K    A
```

Ebből természetesnek tűnik a következtetés:

```text
A
```

vagyis:

> Van ász a kezemben.

Ez a mentális modell szempontjából érthető válasz, mert a két fejben tartott lehetőség mindegyikében szerepel az ász.

A formálisabb levezetési megközelítés azonban másként működik. Ott nem az számít, hogy milyen esetek tűnnek fel elsőként a fejünkben, hanem az, hogy milyen következtetésre van tényleges bizonyításunk.

Ebben a jegyzetben a döntő kérdés tehát ez:

```text
A résztvevő az explicit, könnyen elképzelhető eseteket követi?
vagy
A résztvevő levezetési szempontból értékeli a következtetést?
```

---

## 3. A két vizsgált modell

### 3.1. Mentális modell

A mentális modell szerint a résztvevő nem teljes logikai levezetést végez, hanem néhány konkrét, könnyen elképzelhető helyzetet tart fejben.

A `ha K, akkor A` mondatot például gyakran nem teljes igazságfeltételes formában reprezentálja, hanem így:

```text
K     A
```

A `ha nem K, akkor A` mondat pedig így jelenik meg:

```text
¬K    A
```

A két együtt fejben tartott lehetőség:

```text
K     A
¬K    A
```

Mivel mindkét sorban szerepel `A`, a mentális modell hajlamos ezt a következtetést elfogadni:

```text
A
```

Ebben a modellben tehát a résztvevő könnyen azt válaszolja:

> A „van ász a kezemben” konklúzió érvényes.

Ez akkor is előfordulhat, ha a teljes levezetési elemzés szerint a következtetés nem lenne elfogadható.

A mentális modell ebben a vizsgálatban ezért azt jósolja, hogy a résztvevők viszonylag gyakran elfogadják a **van ász** típusú konklúziókat.

Röviden:

```text
Mentális modell:
ha a konklúzió = "van ász",
akkor a résztvevő hajlamos érvényesnek tartani.
```

---

### 3.2. Bizonyék alapú következtető modell

Az intuicionista levezetési modell nem azt kérdezi, hogy melyik lehetőség tűnik pszichológiailag természetesnek, hanem azt, hogy van-e konstrukció vagy bizonyítás a konklúzióra.

Egy állítást akkor fogadunk el, ha van rá levezetésünk.

Például az alábbi esetben:

```text
K
K → A
```

az `A` konklúzió elfogadható, mert `K` és `K → A` alapján modus ponenssel levezethető:

```text
A
```

Más esetekben azonban az, hogy az `A` pszichológiailag természetesnek tűnik, önmagában még nem elég. Az intuicionista modellben a kérdés mindig ez:

```text
Van-e tényleges levezetés A-ra?
```

A kizáró vagyos King–Ace változatban például a levezetési modell nem egyszerűen azt látja, hogy mindkét felszíni esetben ász szerepel. Ehelyett azt vizsgálja, hogy az adott konnektívum és premisszaszerkezet mellett mi bizonyítható.

Ebben a vizsgálatban az intuicionista modell ezért nem a felszíni „van ász” mintázatot követi, hanem itemenként meghatározza, hogy az adott konklúzió valóban levezethető-e.

Röviden:

```text
Intuicionista modell:
a konklúzió csak akkor érvényes,
ha van rá levezetés.
```

---

## 4. Miért hasznos ehhez jel-detektálási modell?

A válaszok kétértékűek:

```text
1 = a résztvevő érvényesnek tartja a következtetést
0 = a résztvevő nem tartja érvényesnek
```

Ez jól illeszkedik a jel-detektálási elmélethez.

A modellben az itemek kétfélék lehetnek:

```text
signal = a vizsgált elméleti modell szerint a következtetés érvényes
noise  = a vizsgált elméleti modell szerint a következtetés nem érvényes
```

Ha egy résztvevő egy signal itemre azt válaszolja, hogy „érvényes”, az találat.

Ha egy noise itemre azt válaszolja, hogy „érvényes”, az téves riasztás.

A jel-detektálási modell két fontos paramétert becsül:

```text
d' = diszkriminabilitás
c  = válaszkritérium vagy bias
```

A `d'` azt mutatja meg, hogy a válaszok mennyire különítik el az elmélet szerint érvényes és nem érvényes itemeket.

A `c` azt mutatja meg, hogy a válaszadó inkább liberális vagy konzervatív irányba hajlik-e.

```text
c < 0  → liberális bias: könnyen mondja, hogy "érvényes"
c = 0  → semleges kritérium
c > 0  → konzervatív bias: nehezebben mondja, hogy "érvényes"
```

A modell tehát nemcsak azt mondja meg, hogy melyik elmélet illeszkedik jobban, hanem azt is, hogy az osztály válaszai mennyire voltak következetesek és milyen irányú válasz-bias jelent meg.

---

## 5. A vizsgálat alapkérdése

A kérdőív kitöltése után a válaszadatokat kétféle elméleti modellhez hasonlítjuk.

A fő kérdés:

```text
A válaszok inkább a mentális modell szerint rendeződnek?
vagy
A válaszok inkább az intuicionista levezetési modell szerint rendeződnek?
```

Ha a résztvevők főleg a „van ász” konklúziókat fogadják el, akkor az adatok várhatóan a mentális modell felé húznak.

Ha viszont a résztvevők azoknál az itemeknél mondanak „érvényes” választ, ahol a konklúzió ténylegesen levezethető, akkor az adatok az intuicionista modell felé húznak.
