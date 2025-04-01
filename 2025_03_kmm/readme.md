# A kiírás

A könyvtárban szereplő kódok az alábbi feladatkiírásra készültek pályamunkaként: 
[Kódolj Miniatűr Mesterművet](https://hcc.njszt.hu/hcc_challenge/2025-03).
A legelső, megszületett kód a "Szilva_HCC_2025_KMM.bdt", ennek továbbfejlesztett munkaverziói sorban a "SzilvaVx_HCC_2025_KMM.bdt" nevűek. A beküldésre érdemesnek tartott verziók a "Szilvasy_Zoltan"-t tartalmazó nevűek, szintén a verziójelölés nélküli a legelső, aztán a Vx jelölések sorban egymás után.

# A feladat elemzése

A képernyőn megjelenítendő ábra tanulmányozása során rögtön szembe tűnik a szabályosság, szimmetria. A képernyőn lévő karakterek tárolása a bal felső sarokban kezdődik és folyamatos balról jobbra illetve a sorokban egymás alatt (sorfolytonos).

Már ránézésre felmerül az ötlet a szemlélőben, hogy talán érdemes rövidebb vagy hosszabb ismétlődő szakaszokra bontani a képet és ezeknek a variálásával összerakni azt. Ennek a módszernek az a hátulütője, hogy a mintákat valahol definiálni kell, ezek eleve programmemóriát foglalnak, és még azt a logikát is össze kell a programban rakni, hogy milyen sorrendben váltakozva legyenek kirakva egymás után.

Mivel a kiírás a legrövidebb program előállítására vonatkozott, ezért ezt az utat elvetettem és arra a következtetésre jutottam, hogy talán a legrövidebben úgy érek célt, ha egy nagyon egyszerű ciklussal a képernyőn végigszaladva minden egyes karakter helyére valamilyen képlettel számolom ki az oda kiírandó karaktert.

További fontos megállapítás, hogy az ábra megalkotásához mindössze két karakterre van szükségünk (ezeknek a kódjai ráadásul csak 1-gyel térnek el, 30 és 31), ez máris felveti annak a lehetőségét, hogy a megjelenítendő karaktert egyetlen bittel határozzuk meg, ezt a bitet kell minden egyes pozícióhoz meghatározni a programmal.

A fentiek figyelembe vételével tehát az lett a cél, hogy meghatározzuk azoknak a képernyőpozícióknak a helyeit, ahol a kiírandó karakter vált. A mintakép alapján az alábbi periodicitásokat írhatjuk le:

- minden 4. karakternél (\*1)
- minden 20. karakternél (fél sor)
- minden 160. karakternél (4 sor) (\*2)
- a képernyő felénél (12 sor = 480 karakter)

az alábbi megjegyzésekkel:

(\*1): a képernyő legelején 2-vel eltolva

(\*2): a képernyő legelején 2 sorral (80 karakter) eltolva

# A nulladik megoldás

## Egy váltakozás kiszámítása 

Amennyiben a képernyő első pozícióját tekintjük a nullának, és az ott megjelenítendő karaktert jelöljük 0-s bittel, az ellenkezőt pedig 1-es bittel, akkor a fenti lista első sorához tartozó bitet az I. pozícióra vonatkozóan megkaphatjuk az INT((I+2)/4) kifejezéssel. Ez a kifejezés I=0 és I=1 esetén 0-t ad eredményül, I=2...5 esetben 1, I=6...9 esetben 2 lesz az eredmény, és így tovább, innen már négyesével lépkedve. A kifejezés eredményének párossága adja meg a várt bitet: páros eredmény 0-s bitet jelent, páratlan pedig 1-est.

Az egész számok 2-es számrendszerbeli ábrázolásából adódóan kijelenthetjük, hogy egy szám akkor páros, ha annak 0. bitje 0-s értékű, ezt a bitet kell tehát az eredményből "lecsípni". Ezt szokták maszkolásnak is nevezni, a gyakorlatban egy 2-es számrendszerbeli, bitenkénti logikai ÉS kapcsolat végrehajtásával tudjuk elérni, az ÉS kapcsolatban szereplő "maszk"-nak ez esetben egyetlen helyen, a 0. bitben kell egy 1-est tartamlaznia, ez az érték pedig számként értelmezve pontosan az 1.

Tehát az alábbi kifejezéssel egyetlen bitet, egy 0 vagy 1 értéket fogunk megkapni a képernyőpozíció alapján, amely bit a 4 karakterenkénti "forgatást" jellemzi majd az előállítandó képben:

INT((I+2)/4) AND 1

Ha I értékét 0-tól futtatjuk, a fenti kifejezés értékei karakterpozíciónként a következőképp fognak kinézni:

    0011110000111100001111000011110000111100
    0011110000111100001111000011110000111100
    0011110000111100001111000011110000111100
    0011110000111100001111000011110000111100
    0011110000111100001111000011110000111100
    0011110000111100001111000011110000111100
    ...

## Az összes szükséges váltakozás eredője

Az előzőekben leírt logikát követve felírhatjuk az összes periodikus "forgatás"-t leíró bitek kifejezéseit, értékül adva segédváltozóknak:

- minden 4. karakternél 2 eltolással: **A = INT((I+2)/4) AND 1**
- minden 20. karakternél: **B = INT(I/20) AND 1**
- minden 160. karakternél 80 eltolással: **C = INT((I+80)/160) AND 1**
- a képernyő felénél, 480 karakternél: **D = INT(I/480) AND 1**

Mivel egy karakterpozícióban a 4 "fordítás" hatásának egyszerre kell jelen lennie, ezért felfedezhetjük, hogy páros számú "fordítás" esetén az eredeti állapothoz jutunk, míg páratlan számúnál az ellentétes állapothoz. Ez az összes kiszámolt bit összeadásával és az eredmény párosságának megállapításával határozható meg. Viszont ez a párosság is ugyanúgy meghatározható maszkolással, azaz egy logikai ÉS kapcsolattal, mint ahogy a korábbi osztásoknál tettük:

**E = (A+B+C+D) AND 1**

Ezáltal sikerült kiszámolnunk egyetlen bitet, egy 0 vagy 1 értéket felvevő változót, ami az adott karakterpozícióban megjelenítendő szimbólumot határozza meg: 0 érték esetén a képernyő bal felső sarkában elvárt jelet kell kiírnunk, míg 1 érték esetén a másikat. A feladat szerint a bal felső sarokban 31-es kódú karakterrel kezdünk, a másik kiírandó kód a 30, ezért a fent kiszámolt 0 vagy 1 értéket 31-ből kivonva kapjuk meg a megfelelő karakterkódot:

**PRINT CHR$(31-E);**

vagy

**POKE SA+I,31-E**

ahol SA a képernyőmemória kezdőcíme.

## A képernyőt végig író nulladik verzió

A fentiek összegzéseként tehát összeáll az a nulladik, abszolút kiindulásnak tekinthető program, ami a kiírásban szereplő ábrát meg tudja jeleníteni. Az utolsó sor a feladatkiírás miatt szükséges: nem szabad a képernyőn megjelenő grafikát a program befejeztével megjelenő prompttal sem elrontani.

        SA = 49153
        FOR I=0 TO 959
        A = INT((I+2)/4) AND 1
        B = INT(I/20) AND 1
        C = INT((I+80)/160) AND 1
        D = INT(I/480) AND 1
        E = (A+B+C+D) AND 1
        POKE SA+I,31-E
        NEXT I
    \label GOTO \label

# A méret redukálása

A fent bemutatott nulladik verzió ugyan helyes ábrát ad, de a program mérete még sok helyen lerövidíthető. Vannak szinte azonnal látható és kevésbé szembetűnő lehetőségek a program méretének csökkentésére.

## "Mechanikus" összevonások

Először azokat a mindig végrehajtható méretcsönnektéseket végezhetjük el, amikhez "nem kell gondolkodni", vagy legalábbis félig-meddig rutinból megy. Itt a program működési logikájába gyakorlatilag nem nyúlunk bele, minden ugyanúgy fog működni, mint ahogy azt a "nulladik" verzióban leírtuk.

### Nyilvánvaló rövidítések 

Ezek olyan módosítások, amik mindenkinek feltűnnek, ha az a cél, hogy kevesebb memóriát foglaljon a programunk. Első körben joggal feltételezhetjük, hogy ami kisebbnek látszik, az tárolásban is rövidebb.

#### Szóközök elhagyása

Természetesen nagy mértékben rontja az olvashatóságot, de a BASIC interpretert ez nem zavarja, a működésben semmilyen változást nem okoz. A memóriában letároláskor egy szóköz egy byte-ot jelent.

#### A segédváltozók elhagyása, a kifejezések összevonása

Mivel a kiszámolt SA, A, B, C, D és E változókat mindössze egyetlen helyen használjuk, így a használat helyére a kifejezéseket behelyettesítve rövidíthetjük a programot.

#### Ciklusok végén a felesleges ciklusváltozó elhagyása

A BASIC FOR ciklusának végén a NEXT utasításnál általában meg kell adni a ciklusváltozót. Sokszor viszont (itt is) elhagyható, ha a legbelső indított FOR ciklusra vonatkozik a NEXT. Esetünkben nincs is több ciklus, nem kell megadni a változót.

### Kevésbé látványos programkurtítás

Vannak olyan lehetőségek, amik nem feltétlenül jutnak eszünkbe egy BASIC program láttán, pedig az interpreter működéséből adódóan ezek is jelenthetnek helymegtakarítást.

#### BASIC sorok összevonása

Ez ránézésre nem annyira egyértelmű, mert a sorok összevonásánál egy kettősponttal kell elválasztani az egy sorba kerülő utasításokat, ami egy plusz karakter. Viszont az önmagában tárolt sor a memóriában minimum 3 byte-tal több, mint a listában látható, soron belüli utasítások hossza: 2 byte-on a sorszám, plusz 1 byte-on a sor vége jel tárolódik; így tehát 2 egymást követő sor összevonásával 2 byte-ot spórolhatunk.

Természetesen nem minden vonható össze. Egyrészt szokott lenni maximum sorhossz, amit az interpreter kezelni tud, másrészt az elágazásoknál csak sorszámra hivatkozhatunk, ezért azok a célpontok, ahová ugrás történik a programban, csak új sor elején lehetnek. Ezért pl. az utolsó, önmagára ugró GOTO utasítás mindenképpen külön BASIC sorba fog kerülni.

#### Sorszámok minimalizálása

Szintén egy olyan módszer, amit a gyakorlatban inkább kerülünk, szellősen osztjuk a sorszámokat, hogy későbbi programfejlesztés vagy -javítás alkalmával könnyedén lehessen új sorokat beszúrni a meglévő kódba. Ám ha a tárolt méret a lényeg, akkor nem mindegy, hogy egy ugró utasítás után hány számjegyű a cél sorszáma. Emiatt a sorszámozást lehetőleg 1 számjegyen tartom.

#### Összevont kifejezések optimalizálása a precedenciaszabályokkal

A kifejezések összevonásánál az ember automatikusan zárójelez, hogy az eredeti műveletek ne sérüljenek. A zárójelek számát átrendezéssel, a műveleti sorrendek figyelembe vételével sokszor csökkenthetjük, néha el is maradhatnak teljesen.

## A "gondolkodós" optimalizáció

Az előzőekben leírt "favágós" módszerekkel a következő program alakul ki:

        FORI=0TO959:POKE49153+I,31-(((INT((I+2)/4)AND1)+(INT(I/20)AND1)+(INT((I+80)/160)AND1)+(INT(I/480)AND1))AND1):NEXT

    \label GOTO\label

Na itt következik az, amikor az előzőekben már kellően tömörré vált kódban megpróbáljuk felfedezni azokat a lehetőségeket, amikkel még faraghatunk itt-ott 1-1 byte-ot.

### Az ugrás miatti külön sor eltüntetése

A kritérium, hogy a program ne lépjen ki, ezáltal a képernyőtartalom ne sérüljön megkövetel valamilyen végtelen ciklust. A nulladik verzióban ez egy önmagára ugró GOTO utasítás.

Ha viszont számításba vesszük, hogy a program a futása során mindig ugyanazt az ábrát és ugyanoda fogja rajzolni (nincs soremelés a képernyőn), akkor akár azt is megtehetjük, hogy nem "állunk meg" a kirajzolás végén, hanem unos-untalan újrakezdetjük a rajzolást, mindig újraindítjuk a programot.

A fenti logikával a GOTO kicserélhető RUN-ra, amivel a sorszám kiírását (1 számjegyű sorszámnál 1 byte) megspóroljuk. Igen ám, de mivel már nincs szükség a címkére és amiatt az új sorra, ezáltal a RUN is betehető az első sor végére kettősponttal, így további 2 byte-ot megtakarítva.

### Az összevont kifejezések értelmezése, további egyszerüsítések keresése

Vegyük észre, hogy a karakterkód meghatározásánál 1 bites értékekkel végzünk összeadást, majd az eredmény legalsó bitjét tartjuk csak meg. Egész értékeket repretentáló bináris adatok összeadásánál a legalsó bit az összes fentebbi bittől teljesen függetlenül viselkedik, nem keletkezik erre a bitre soha átvitel, ezért az egyes operandusokon végrehajtott bitmaszkolás gyakorlatilag felesleges. A kikerülő "AND1"-ek miatt zárójelek is feleslegessé válnak. A következő sor ugyanazt az eredményt fogja adni, itt már a RUN-os megoldást is beépítettem:

    FORI=0TO959:POKE49153+I,31-(INT((I+2)/4)+INT(I/20)+INT((I+80)/160)+INT(I/480)AND1):NEXT:RUN

### Ciklushatárok eltolása

Mivel a számolásokban szereplő 80 páros számúan osztható 20-szal és 4-gyel is, ezért ha a ciklust 80-nal "eltoljuk" azaz a kezdő- és végértékeket is ennyivel megnöveljük, akkor a 4-gyel és 20-szal számolt váltakozások helye és "fázisa" sem változik, viszont a 80-as eltolással számított váltásból az eltolást kiiktathatjuk. 

Igaz, ennek az az ára, hogy a POKE-nál a képernyő kezdőcímét is le kell 80-nal csökkenteni, valamint egy másik következmény, hogy a képernyő közepét jelző, 480-as osztással kalkulált váltást is el kell tolnunk, az osztó itt 80-nal nő. A ciklus kezdő- és végértékénél is sajnos 1-1 byte-ot vesztünk, 2 illetve 4 számjegyűvé válnak, de 5 byte a nyereség a 80-as eltolásnál, ahol a zárójelek és a -80 is elhagyhatóvá válik.

A fentieket beépítve a kódba a következő sort kapjuk:

    FORI=80TO1039:POKE49073+I,30+(INT((I+2)/4)+INT(I/20)+INT(I/160)+INT(I/560)AND1):NEXT:RUN

### Az egész aritmetika kihasználása

A BASIC a logikai műveleteket, mint itt az AND egész számokon hajtja végre. Emiatt az operandusokat automatikusan átalakítja egésszé, elhagyva a törtrészt. Mivel az általunk használt AND kifejezés egyik oldalán egy 4 tagú összeadás van, az egyik (tetszőleges) tagról elhagyhatjuk az egészrészt képző INT függvényt, az összegben megjelenő törtrész automatikusan figyelmen kívül lesz hagyva. A kialakult kód:

    FORI=80TO1039:POKE49073+I,31-(INT((I+2)/4)+INT(I/20)+I/160+INT(I/560)AND1):NEXT:RUN

### Zárójel lehetséges felbontása

Az összeadás első tagjában szereplő (I+2)/4 kifejezés írható I/4+0.5 formában is a zárójel felbontásával. A két írásmód ugyanannyi karaktert tartalmaz, de a BASIC megengedi a lebegőpontos konstans esetén a bevezető nulla elhagyását, így egy byte-tal kevesebb lesz:

    FORI=80TO1039:POKE49073+I,31-(INT(I/4+.5)+INT(I/20)+I/160+INT(I/560)AND1):NEXT:RUN

### Relációs kifejezés használata

Az összeadás utolsó tagja az egész ciklus alatt egyszer vált értéket, a kép felénél. Gyakorlatilag akkor lesz az értéke 1, ha I nagyobb vagy egyenlő 560-nál, a ciklusban szereplő egész I értékek esetén mondhatjuk azt, hogy ha I nagyobb 559-nél. A jelen használt BASIC-ben a relációs kifejezések eredménye 0 vagy 1, attól függően, hogy a reláció hamis vagy igaz. Esetünkben ha azt írjuk, hogy (I>559), akkor ennek pont akkor lesz 1 értéke (igaz), ha I nagyobb 559-nél, egyébként az értéke nulla. Az egészre kerekített osztást így helyettesíthetjük egy relációs kifejezéssel:

    FORI=80TO1039:POKE49073+I,31-(INT(I/4+.5)+INT(I/20)+I/160+(I>559)AND1):NEXT:RUN

Sajnos ez a legutolsó módosítás már nem hoz további méretcsökkenést, mivel a BASIC belső tárolási mechanizmusából kifolyólag az INT utáni zárójel a függvény nevének része, emiatt a megmaradó zárójel az INT helyét foglalja el a memóriában, az osztás jele helyett pedig a kisebb reláció jele kerül oda, tehát a tárolási méret nem változik.

# Az elkészült program

A leírt optimalizálások végrehajtása után az alábbi kód született meg, ami a feladatkiírásnak megfelelő képet állít elő. Természetesen nem zárom ki a további méretcsökkentések lehetőségét, esetleg teljesen más rajzolási módszerből kiindulva.

    &P=HL2
    &C=1
    &I=1
      
        |FORI=0TO959:POKE49153+I,31-(((INT((I+2)/4)AND1)+(INT(I/20)AND1)+(INT((I+80)/160)AND1)+(INT(I/480)AND1))AND1):NEXTI
        |FORI=0TO959:POKE49153+I,31-(INT((I+2)/4)+INT(I/20)+INT((I+80)/160)+INT(I/480)AND1):NEXT:RUN
        |FORI=80TO1039:POKE49073+I,31-(INT((I+2)/4)+INT(I/20)+INT(I/160)+INT(I/560)AND1):NEXT:RUN
        |FORI=80TO1039:POKE49073+I,31-(INT((I+2)/4)+INT(I/20)+I/160+INT(I/560)AND1):NEXT:RUN
        |FORI=80TO1039:POKE49073+I,31-(INT(I/4+.5)+INT(I/20)+I/160+INT(I/560)AND1):NEXT:RUN
        FORI=80TO1039:POKE49073+I,31-(INT(I/4+.5)+INT(I/20)+I/160+(I>559)AND1):NEXT:RUN

    |\label GOTO\label
