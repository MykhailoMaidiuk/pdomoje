
# Technická dokumentace

## Obsah

- [Úvod](#úvod)
- [Cíle dokumentace](#cíle-dokumentace)
- [Spuštění Haxall serveru pomocí Docker](#případ-použití-1--spuštění-haxall-serveru-pomocí-docker)
  - [3.1 Popis a účel](#31-popis-a-účel)
  - [3.2 Technická architektura a pracovní postup](#32-technická-architektura-a-pracovní-postup)
  - [3.3 Testování a ověření](#34-testování-a-ověření)
- [Připojení .NET klienta k API](#případ-použití-2--integrace-dat-pomocí-brick-a-project-haystack)
  - [4.1 Popis a účel](#41-popis-a-účel)
  - [4.2 Technická architektura a pracovní postup](#42-technická-architektura-a-pracovní-postup)
  - [4.3 Ukázkové kódy](#43-ukázkové-kódy)
- [Nahrání Brick ontologie na server](#případ-použití-3-nahrání-brick-ontologie-na-server)
  - [5.1 Popis a účel](#51-popis-a-účel)
  - [5.2 Technická architektura a pracovní postup](#52-technická-architektura-a-pracovní-postup)
  - [5.3 Kroky nahrání Brick ontologie](#53-kroky-nahrání-brick-ontologie)

## Úvod

Chytré budovy představují moderní přístup ke správě a optimalizaci provozu, kdy jsou data ze senzorů a IoT zařízení využívána pro zvýšení energetické účinnosti, komfortu a bezpečnosti. Tato dokumentace popisuje implementaci datových modelů a nástrojů v rámci BEMS (Building Energy Management Systems) a je zaměřena na tři konkrétní případy použití. 

## Cíle dokumentace

Cílem této dokumentace je poskytnout praktický průvodce nasazením a využitím klíčových nástrojů pro správu dat chytrých budov (BEMS). Popisuje tři hlavní scénáře:

- Spuštění Haxall serveru pomocí Dockeru – rychlé a stabilní nasazení Project Haystack backendu pro ukládání a správu senzorových dat.
- Připojení .NET klienta k API – sjednocení heterogenních datových zdrojů do standardizovaného modelu s využitím .NET klienta pro snadné dotazování a analýzu.
- Nahrání Brick ontologie na RDF server – import kompletního Brick schématu (např. „Brick+imports.ttl“) do Apache Jena Fuseki pro SPARQL dotazování a sémantické ověřování modelů.

<br><br><br>
## 3. Spuštění Haxall serveru pomocí Docker
<a name="případ-použití-1--spuštění-haxall-serveru-pomocí-docker"></a>

### 3.1 Popis a účel
<a name="31-popis-a-účel"></a>

Cílem tohoto případu je nasadit lokální instanci Haxall serveru, která slouží jako platforma pro správu a analýzu dat z chytrých budov.  
Haxall implementuje Project Haystack – otevřený standard pro sémantické modelování dat.  
Díky nasazení pomocí Dockeru je zajištěna jednoduchá instalace, správa prostředí a persistování dat pomocí lokálního úložiště.

### 3.2 Technická architektura a pracovní postup
<a name="32-technická-architektura-a-pracovní-postup"></a>

**Předpoklady:**
- Nainstalovaný Docker (verze 20.x nebo novější)
- Oprávnění pro práci s Docker kontejnery a lokálními složkami
- Internetové připojení k stažení Docker obrazu z GitHub Container Registry

**Přístup k rozhraní:**
- Příkazy spusťte v libovolném shellu: Bash (Linux/macOS), PowerShell nebo CMD (Windows), případně přes integrovanou konzoli v IDE (Visual Studio, Rider, VS Code).
- Můžete použít Docker CLI, Docker Desktop GUI nebo Docker Compose s vlastním docker-compose.yml.

**Persistentní úložiště:**
- Vytvoření lokální složky (např. `haxall`), která bude připojena do Docker kontejneru pro uchování databázových dat
```bash
mkdir haxall
```

**Spuštění kontejneru:**
- Docker kontejner se spouští s odpovídající konfigurací portů a volume, což umožňuje přístup k webovému rozhraní Haxall serveru na adrese [http://localhost:8080](http://localhost:8080)
```bash
docker run -v ./haxall:/app/haxall/dbs -p 8080:8080 --name haxall_run ghcr.io/haxall/haxall
```


**➡️ Po úspěšném spuštění kontejneru sledujte výstup v terminálu pro zprávy o inicializaci**


### 3.3 Testování a ověření
<a name="34-testování-a-ověření"></a>

**Zkontrolujte běžící kontejner:**
```bash
docker ps
```

– ověřte, že `haxall_run` je spuštěný.

**Sledujte logy v reálném čase:**

```bash
docker logs -f haxall_run
```

**Otevřete webový prohlížeč a přejděte na adresu:**
  [http://localhost:8080](http://localhost:8080)


  
  Měli byste vidět přihlašovací obrazovku nebo dashboard Haxall serveru
![přihlašeni](https://github.com/MykhailoMaidiuk/pdomoje/blob/main/haxall_login.png?raw=true)
[🔙 Zpět na obsah](#obsah)


<br><br><br>
## 4. Připojení .NET klienta k API
<a name="případ-použití-2--integrace-dat-pomocí-brick-a-project-haystack"></a>

### 4.1 Popis a účel
<a name="41-popis-a-účel"></a>

`.NET Haystack` klientská knihovna zjednodušuje komunikaci s libovolným serverem kompatibilním s Project Haystack.  
Balíček `ProjectHaystack.Client` (NuGet) nabízí:

- **Autentizaci**  
  Oficiálně podporuje [SCRAM](https://project-haystack.org/doc/Auth#scram), neoficiálně i Basic aj., vše přes handshake, aby server sdělil, které metody podporuje.

- **Konverzi**  
  Automatický mapping mezi HZinc textem a C# modely pomocí tříd `HZincReader` / `HZincWriter`.

Knihovna poskytuje jednotné rozhraní `HaystackClient`, které v pozadí využívá `HttpClient` a `IAuthenticator`.

### 4.2 Technická architektura a pracovní postup
<a name="42-technická-architektura-a-pracovní-postup"></a>

**Instalace NuGet balíčku:**

```bash
dotnet add package ProjectHaystack.Client
```

**Výběr autentizátoru:**

- `AutodetectAuthenticator` – detekuje SCRAM, Basic apod.
- `ScramAuthenticator` – doporučeno, nejméně režie
- `BasicAuthenticator` / `NonHaystackBasicAuthenticator`

**Inicializace klienta**

*Standardní konstruktor:*

```csharp
var auth   = new ScramAuthenticator(user, pass);
var client = new HaystackClient(auth, new Uri("http://localhost:8080/api/"));
```

*S vlastním `HttpClient`:*

```csharp
var handler    = new HttpClientHandler { UseCookies = true };
var httpClient = new HttpClient(handler);
var client     = new HaystackClient(httpClient, auth, uri);
```

**Otevření spojení**

```csharp
await client.OpenAsync();
```

### 4.3 Ukázkové kódy
<a name="43-ukázkové-kódy"></a>

**Připojení a základní volání pomocí C#:**
```csharp
using System;
using System.Threading.Tasks;
using ProjectHaystack.Client;

public class HaystackConnector
{
    public async Task ConnectAsync()
    {
        // 1) Nastavení přihlašovacích údajů
        var user = "someuser";
        var pass = "somepassword";
        var uri  = new Uri("https://someserver/api/");
        
        // 2) Vytvoření SCRAM autentizátoru
        var auth = new ScramAuthenticator(user, pass) { AddLegacySpaceToProof = true };
        
        // 3) Inicializace klienta Haystack
        var client = new HaystackClient(auth, uri);
        
        // 4) Otevření spojení k API
        await client.OpenAsync();
        Console.WriteLine("Connected to Project Haystack API.");
    }
}
```
[🔙 Zpět na obsah](#obsah)

<br><br><br>
## 5. Nahrání Brick ontologie na server
<a name="případ-použití-3-nahrání-brick-ontologie-na-server"></a>

### 5.1 Popis a účel
<a name="51-popis-a-účel"></a>

Cílem této části je demonstrovat, jak jednoduše nahrát Brick ontologii (schéma) na server pro následnou správu a dotazování.  
Pro ukázku využíváme Apache Jena Fuseki, což je populární open-source řešení pro správu RDF dat.  
Brick ontologie může být nahrána buď ve variantě **„Brick+imports.ttl“** (doporučeno), která obsahuje všechny potřebné závislosti, nebo jiné distribuce dle vašich potřeb.

### 5.2 Technická architektura a pracovní postup
<a name="52-technická-architektura-a-pracovní-postup"></a>

**Stažení Brick distribuce:**

- Vyberte odpovídající soubor — např. `Brick+imports.ttl` — z oficiálního [Releases](https://brickschema.org/resources/) Bricku.

**Instalace Apache Jena Fuseki:**

- Fuseki můžete spustit jako službu či samostatnou aplikaci.

- Pro rychlé nasazení pomocí Dockeru klikněte [zde](https://jena.apache.org/documentation/fuseki2/fuseki-docker.html) a postupujte podle oficiálního návodu.

**Vytvoření datasetu:**

- Na adrese [http://localhost:3030](http://localhost:3030) otevřete Fuseki UI a založte nový dataset.

**Nahrání souboru:**

- Vyberte dataset, klikněte na „add data“ a nahrajte soubor s Brick ontologií.

### 5.3 Kroky nahrání Brick ontologie
<a name="53-kroky-nahrání-brick-ontologie"></a>

**1. Manage datasets**  
V seznamu datasetů klikněte na dataset, kam chcete nahrát Brick ontologii (např. `/brickdb`).

**2. Add data**  
V horním menu datasetu zvolte **add data**.
![add data](https://github.com/MykhailoMaidiuk/pdomoje/blob/main/brick1.png?raw=true)

**3. Select files**  
Klikněte na **select files** a zvolte soubor, např. `Brick+imports.ttl`.
![select files](https://github.com/MykhailoMaidiuk/pdomoje/blob/main/brick2.png?raw=true)

**4. Upload all**  
Potvrďte nahrání kliknutím na **upload all**. Fuseki načte RDF data a zobrazí počet načtených triple.

**5. Ověření importu**  
Po úspěšném nahrání se zobrazí status (např. `Triples uploaded: 54601`).  
Tlačítkem **query** můžete otevřít SPARQL rozhraní a dotazovat se na data z Brick ontologie.
![upload all](https://github.com/MykhailoMaidiuk/pdomoje/blob/main/brick3.png?raw=true)
[🔙 Zpět na obsah](#obsah)

