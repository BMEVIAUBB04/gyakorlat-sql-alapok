# SQL alapok

## Célkitűzés

A gyakorlat célja, hogy a hallgatók átismételjék a korábban tanult SQL alapokat _Microsoft SQL Server_ környezetben, és elsajátítsák a fejlesztőeszköz használatát.

## Előfeltételek

A labor elvégzéséhez szükséges eszközök:

- [Microsoft SQL Server (LocalDB vagy Express edition)](https://learn.microsoft.com/en-us/sql/database-engine/configure-windows/sql-server-express-localdb)
- [SQL Server Management Studio](https://learn.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms)
- Adatbázis-létrehozó script: [mssql.sql](https://raw.githubusercontent.com/BMEVIAUBB04/gyakorlat-mssql/master/mssql.sql)

Amit érdemes átnézned:

- Az SQL nyelv
- A _Microsoft SQL Server_ használata ([segédlet](https://BMEVIAUBB04.github.io/gyakorlat-mssql/mssql-hasznalat.html) és [videó](https://web.microsoftstream.com/video/e3a83d16-b5c4-4fe9-b027-703347951621))
- A használt adatbázis [sémája](https://BMEVIAUBB04.github.io/gyakorlat-mssql/sema.html)

A felkészülés ellenőrzése:
Az alábbi mintakérdésekből 2-t fogunk feltenni, legalább 1-re helyesen kell válaszolni.

1. Hogyan lehet vizsgálni egy kifejezés nullitását?

   _Az `is null` operátor segítségével._

1. Miképp működik a `like` operátor? Milyen jokerkaraktereket lehet használni?

   _`Kif1 like stringminta`_

   _Stringösszehasonlító operátor. A `Kif1`-ben keres a mintának megfelelően, és ha a minta illeszthető a `Kif1`-re, akkor az operátor _igaz_ értékkel tér vissza. A mintaillesztés case sensitive; az annak során használható jokerkarakterek:_
   * _`_`: egy betű helyettesítése_
   * _`%`: tetszőleges hosszúságú szöveg helyettesítése_
   * _`''`: szimpla `'`, mivel ez önmagában a stringhatároló karakter._

1. Hogyan működnek az oszlopfüggvények?

   _Az oszlopfüggvények működését úgy lehet elképzelni, hogy az adatbázis-kezelő szerver először lefuttatja a lekérdezést az oszlopfüggvények nélkül, majd az egyes rekordokat átadja az oszlopfüggvényeknek az értékek kiszámítására. Az oszlopfüggvények használhatóak csoportokra is, azaz a rekordokat csoportosíthatjuk, és ezen csoportok mentén számítunk ki különböző oszlopfüggvényeket._

1. Mire szolgál a `having` kulcsszó?
   
   _Ha egy oszlopfüggvény kimenetére szeretnénk szűrni, akkor azt külön a `having` kulcsszó után lehet megadni._

1. Írja fel a `select` utasítás általános szintaktikáját!
   
   `select [distinct] oszloplista
   from táblalista
   [where logikai kifejezés]
   [group by oszloplista]
   [having logikai kifejezés]
   [order by oszloplista]`

1. Miképp lehet megadni két tábla outer joinját?
   
   A `from` parancsnál `left`, `right` vagy _full_ `outer join`t használva, pl.
   `select * from gyarto left outer join termek on gyartoid=gyarto.id`.



## A gyakorlat menete

Az első három feladatot a gyakorlatvezetővel együtt oldjuk meg. Az utolsó feladat önálló munka. A közös feladatok megoldásai megtalálhatóak az útmutatóban is. Előbb azonban próbáljuk magunk megoldani a feladatokat!

## Beadandó megoldások

A labor teljesítésének feltétele egy olyan `.pdf` formátumú jegyzőkönyv elkészítése és feltöltése, amely azt igazolja, hogy a közös 3. feladatot, valamint az önálló 4. feladatban szereplő 10 lekérdezésből legalább az első 6-ot elkészítetted. Javasoljuk ugyanakkor, hogy a többit is próbáld megoldani. Az egyes lekérdezésekhez olyan képernyőképeket kérünk a jegyzőkönyvbe, melyeken egyszerre látszik a lekérdezés és annak eredménye, valamint a lekérdezésnél kommentként a Neptun-kódod is.

## Feladat 1: Adatbázis létrehozása (közös)

Első lépésként szükségünk lesz egy adatbázisra. Az adatbázis tipikusan egy központi kiszolgálón helyezkedik el, de fejlesztés közben sokszor a saját gépünkön fut. Mi ez utóbbi opciót választjuk.

1. Kapcsolódjon a *Microsoft SQL Server*hez az *SQL Server Management Studio* segítségével! Indítsa el az alkalmazást, és az alábbi adatokkal kapcsolódjon:

   - Server name: `(localdb)\mssqllocaldb`
   - Authentication: `Windows Authentication`

1. Hozzon létre egy új adatbázist (ha még nem létezik):
  
   1. Az *Object Explorer*ben a *Databases*re jobb kattintás
   1. _Create Database_
   1. Az adatbázis neve legyen a Neptun-kódja


## Feladat 2: Adatbázisséma és mintaadatok létrehozása (közös)

1. Hozza létre a mintaadatbázist a generálóscript lefuttatásával! Nyisson egy új _Query_ ablakot, másolja be a [script](https://raw.githubusercontent.com/BMEVIAUBB04/gyakorlat-mssql/master/mssql.sql) tartalmát, és értelmezzük az utasításokat! A standard SQL-en kívül néhány MSSQL-specifikus kódot is találunk:

   - A script elején a meglévő táblák ellenőrzése
   - Automatikusan generált elsődleges kulcsok az `IDENTITY` kulcsszó segítségével
   - A `SET IDENTITY_INSERT ... ON/OFF` használata, hogy általunk választott azonosítókat szúrhassunk be a generált helyett

1. Futtassa le az utasításokat! Ügyeljen az eszköztáron levő legördülő menüben a megfelelő adatbázis kiválasztására.

   ![Adatbázis kiválasztása](images/sql-management-database-dropdown.png)

1. Ellenőrizze, hogy létrejöttek-e a táblák! Ha a _Tables_ mappa ki volt már nyitva, akkor frissíteni kell.

   ![Adatbázis kiválasztása](images/sql-managment-tablak.png).


## Feladat 3: SQL-parancsok (közös)

Írjon SQL-lekérdezést/-utasítást az alábbi feladatokhoz!

1. Listázza ki az összes vevőt!

   <details><summary markdown="span">Megoldás</summary>

   ```sql
   select * from Vevo
   ```
   </details>
   
1. Listázza ki, hogy eddig milyen nevű termékeket rendeltek!

   <details><summary markdown="span">Megoldás</summary>

   ```sql
   select distinct t.nev from Termek t
   join MegrendelesTetel mt on mt.TermekID=t.ID
   ```
   
   A `join` segítségével kapcsoljuk össze a két táblát. A `join`, ha külön nem rendelkezünk róla, egy inner join lesz, amiben nem szerepelnek olyan termékek, amiknek nincs párjuk a `MegrendelesTetel` táblában. Fontos a `distinct` kulcsszó is, amivel kiszűrjük az ismétlődéseket.
   </details>

1. Hány nem teljesített megrendelésünk van (a státusz alapján)?

   <details><summary markdown="span">Megoldás</summary>

   ```sql
   select count(*)
   from Megrendeles m join Statusz s on m.StatuszID = s.ID
   where s.Nev != 'Kiszállítva'
   ```

   Itt a `join` mellett az oszlopfüggvény (aggregáció) használatára látunk példát. (A táblák kapcsolására nem csak ez a szintaktika használható.)

   </details>
   
1. Melyek azok a fizetési módok, amiket soha nem választottak a megrendelőink?

   <details><summary markdown="span">Megoldás</summary>

   ```sql
   select f.Mod
   from Megrendeles m right outer join FizetesMod f on m.FizetesModID = f.ID
   where m.ID is null
   ```

   A megoldás kulcsa az `outer join`, aminek köszönhetően láthatjuk, mely fizetési mód rekordhoz _nem_ tartozik egyetlen megrendelés se.

   </details>

1. Rögzítsünk egy új vevőt! Kérdezzük le az újonnan létrejött rekord kulcsát!

   <details><summary markdown="span">Megoldás</summary>

   ```sql
   insert into Vevo(Nev, Login, Jelszo, Email)
   values ('Teszt Elek', 't.elek', '********', 't.elek@email.com')

   select @@IDENTITY
   ```

   Az `insert` után javasolt kiírni az oszlopneveket az egyértelműség végett, bár nem kötelező. Vegyük észre, hogy az `ID` oszlopnak nem adunk értéket, mert azt a tábla definíciójakor meghatározva a szerver adja automatikusan. Ezért kell utána lekérdeznünk, hogy tudjuk, milyen `ID`-t adott.

   </details>

1. A kategóriák között hibásan szerepel a _Fajáték_ kategórianév. Javítsuk át *Fakockák*ra!

   <details><summary markdown="span">Megoldás</summary>

   ```sql
   update Kategoria
   set Nev = 'Fakockák'
   where Nev = 'Fajáték'
   ```

   </details>

1. Melyik termékkategóriában van a legtöbb termék?

   <details><summary markdown="span">Megoldás</summary>

   ```sql
   select top 1 Nev, (select count(*) from Termek where Termek.KategoriaID = k.ID) as db
   from Kategoria k
   order by db desc
   ```

   A kérdésre több alternatív lekérdezés is eszünkbe juthat. Ez csak egyike a lehetséges megoldásoknak. Itt láthatunk példát az allekérdezésre (subquery) is. Viszont ez nem ad helyes megoldást akkor, amikor több olyan kategória is van, amely ugyanannyi, maximális számú terméket tartalmaz, mert csak az első ilyen kategóriát adja vissza. A tökéletes megoldás:

   ```sql
   select k.Nev 
   from Kategoria k
     join Termek t on t.KategoriaID = k.ID
   group by k.id, k.Nev
   having count(t.id) = 
     (select max(darab) from
       (
	    select count(t.id) AS darab
        from Kategoria k join Termek t on t.KategoriaID = k.ID
		group by k.id, k.Nev
	  ) AS darabszamok
    )
   ```

   

   </details>

## Feladat 4: SQL-parancsok (önálló)

1. Mely termékek áfakulcsa 15%-os?
1. Az egyes telephelyekre hány rendelés volt eddig?
1. Melyik városba kérték a legtöbb rendelést?
1. Melyek azok a vevők, akik már legalább 2-szer rendeltek?
1. Mely számláknál nem egyezik meg a kiállítás és teljesítés dátuma?
1. Írjuk ki a 2008. februári rendeléseket!
1. Írjuk ki azokat a rendeléseket, melyeknél a határidő 5 napnál szűkebb a rendelés dátumához képest!
1. Hány vevőnek van gmailes e-mail címe?
1. Mely vevőknek van egynél több telephelye?
1. Mely vevő(k) adták le a legtöbb tételből álló rendelést? (Több ilyen is lehet!)

---

Az itt található oktatási segédanyagok a BMEVIAUBB04 tárgy hallgatóinak készültek. Az anyagok oly módú felhasználása, amely a tárgy oktatásához nem szorosan kapcsolódik, csak a szerző(k) és a forrás megjelölésével történhet.

Az anyagok a tárgy keretében oktatott kontextusban értelmezhetőek. Az anyagokért egyéb felhasználás esetén a szerző(k) felelősséget nem vállal(nak).
