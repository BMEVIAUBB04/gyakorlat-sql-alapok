# SQL alapok

## Célkitűzés

A gyakorlat célja, hogy a hallgatók átismételjék a korábban tanult SQL alapokat, Microsoft SQL szerver környezetben, és elsajátítsák a fejlesztőeszköz használatát.

## Előfeltételek

A labor elvégzéséhez szükséges eszközök:

- Microsoft SQL Server (LocalDB vagy Express edition)
- SQL Server Management Studio
- Adatbázis létrehozó script: [mssql.sql](https://raw.githubusercontent.com/BMEVIAUBB04/gyakorlat-mssql/master/mssql.sql)

Amit érdemes átnézned:

- SQL nyelv
- Microsoft SQL Server használata [segédlet](https://BMEVIAUBB04.github.io/gyakorlat-mssql/mssql-hasznalat.html) és [videó](https://youtu.be/gmY8reqSL7U)
- A használt adatbázis [sémája](https://BMEVIAUBB04.github.io/gyakorlat-mssql/sema.html)

Felkészülés ellenőrzése:
TODO
- A gyakorlatra való felkészüléshez használható [ezen kérdőív](https://forms.office.com/Pages/ResponsePage.aspx?id=q0g1anB1cUKRqFjaAGlwKf73d6yoiM1FuK24ZUEWuzFUOEVCUjFVVUdNS0pYWDI1TUhETkwwNThXMS4u).
- A gyakorlaton beugróként ugyanezen kérdőívet fogjuk használni, legalább 50% elérése szükséges.
  - Gyakorlatvezetői instrukció: a hallgató nyissa meg a kérdőívet és töltse ki. A válaszok megadása után az utolsó oldalon a "View results" gombra kattintva megtekinthető az eredmény és az elért pontszám. A hallgató ezen az oldalon álljon meg és mutassa meg eredményét a gyakorlatvezetőnek.

## Gyakorlat menete

Az első négy TODO feladatot (beleértve a megoldások tesztelését is) a gyakorlatvezetővel együtt oldjuk meg. Az utolsó feladat önálló munka, amennyiben marad rá idő.

Emlékeztetőként a megoldások is megtalálhatóak az útmutatóban is. Előbb azonban próbáljuk magunk megoldani a feladatot!

## Feladat 1: Adatbázis létrehozása

Első lépésként szükségünk lesz egy adatbázisra. Az adatbázis tipikusan egy központi kiszolgálón helyezkedik el, de fejlesztés közben sokszor a saját gépünkön fut. Mi ez utóbbi opciót választjuk.

1. Kapcsolódjon a Microsoft SQL Serverhez SQL Server Management Studio Segítségével. Indítsa el az alkalmazást, és az alábbi adatokkal kapcsolódjon.

   - Server name: `(localdb)\mssqllocaldb` vagy `.\sqlexpress`
   - Authentication: `Windows authentication`

1. Hozzon létre egy új adatbázist (ha még nem létezik)! Az adatbázis neve legyen a Neptun kódja: _Object Explorer_-ben Databases-en jobb kattintás, és _Create Database_.


## Feladat 2: Adatbázis séma, mintaadatok létrehozása

1. Hozza létre a minta adatbázist a generáló script lefuttatásával. Nyisson egy új _Query_ ablakot, másolja be a [script](https://raw.githubusercontent.com/BMEVIAUBB04/gyakorlat-mssql/master/mssql.sql) tartalmát, és értelmezzük az utasításokat! A standard SQL-en kívül néhány MSSQL-specifikus kódot is találunk:

   - A script elején a meglévő táblák ellenőrzése
   - Automatikusan generált elsődleges kulcsok az IDENTITY kulcsszó segítségével
   - A SET IDENTITY_INSERT ... ON/OFF használata, hogy általunk választott azonosítókat szúrhassunk be a generált helyett
Futtassa le az utasításokat! Ügyeljen az eszköztáron levő legördülő menüben a megfelelő adatbázis kiválasztására.

   ![Adatbázis kiválasztása](images/sql-management-database-dropdown.png)

1. Ellenőrizze, hogy létrejöttek-e a táblák. Ha a _Tables_ mappa ki volt már nyitva, akkor frissíteni kell.

   ![Adatbázis kiválasztása](images/sql-managment-tablak.png).


## Feladat 3: SQL parancsok közösen

Írjon SQL lekérdezés/utasítást az alábbi feladatokhoz.

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
   </details>
   
   A `join` segítségével kapcsoljuk össze a két táblát. A join, ha külön nem rendelkezünk róla, egy inner join lesz, amiben nem szerepelnek olyan termékek, amiknek nincs párjuk a MegrendelesTetel táblában. Fontos a `distinct` kulcsszó is, amivel kiszűrjük az ismétlődéseket.

1. Hány nem teljesített megrendelésünk van (a státusz alapján)?

   <details><summary markdown="span">Megoldás</summary>

   ```sql
   select count(*)
   from Megrendeles m join Statusz s on m.StatuszID = s.ID
   where s.Nev != 'Kiszállítva'
   ```

   A `join` mellett az oszlopfüggvény (aggregáció) használatára látunk példát. (A táblák kapcsolására nem csak ez a szintaktika használható.)

   </details>
   
1. Melyek azok a fizetési módok, amit soha nem választottak a megrendelőink?

   <details><summary markdown="span">Megoldás</summary>

   ```sql
   select f.Mod
   from Megrendeles m right outer join FizetesMod f on m.FizetesModID = f.ID
   where m.ID is null
   ```

   A megoldás kulcsa az `outer join`, aminek köszönhetően láthatjuk, mely fizetési mód rekordhoz _nem_ tartozik egyetlen megrendelés se.

   </details>

1. Rögzítsünk be egy új vevőt! Kérdezzük le az újonnan létrejött rekord kulcsát!

   <details><summary markdown="span">Megoldás</summary>

   ```sql
   insert into Vevo(Nev, Login, Jelszo, Email)
   values ('Teszt Elek', 't.elek', '********', 't.elek@email.com')

   select @@IDENTITY
   ```

   Az `insert` után javasolt kiírni az oszlopneveket az egyértelműség végett, bár nem kötelező. Vegyük észre, hogy az ID oszlopnak nem adunk értéket, mert azt a tábla definíciójakor meghatározva a szerver adja automatikusan. Ezért kell utána lekérdeznünk, hogy tudjuk, milyen ID-t adott.

   </details>

1. A kategóriák között hibásan szerepel az _Fajáték_ kategória név. Javítsuk át a kategória nevét *Fakockák*ra!

   <details><summary markdown="span">Megoldás</summary>

   ```sql
   update Kategoria
   set Nev = 'Fakockák'
   where Nev = 'Fajáték'
   ```

   </details>

1. Melyik termék kategóriában van a legtöbb termék?

   <details><summary markdown="span">Megoldás</summary>

   ```sql
   select top 1 Nev, (select count(*) from Termek where Termek.KategoriaID = k.ID) as db
   from Kategoria k
   order by db desc
   ```

   A kérdésre több alternatív lekérdezés is eszünkbe juthat. Ez csak egyike a lehetséges megoldásoknak. Itt láthatunk példát az allekérdezésre (subquery) is.

   </details>

## Feladat 3: SQL parancsok önállóan