
# Technická dokumentace

## Obsah

- [Úvod](#úvod)
- [Cíle dokumentace](#cíle-dokumentace)
- [Spuštění Haxall serveru pomocí Docker](#případ-použití-1--spuštění-haxall-serveru-pomocí-docker)
  - [3.1 Popis a účel](#31-popis-a-účel)
  - [3.2 Technická architektura a pracovní postup](#32-technická-architektura-a-pracovní-postup)
  - [3.3 Ukázkové kódy](#33-ukázkové-kódy)
  - [3.4 Testování a ověření](#34-testování-a-ověření)
- [Integrace dat pomocí Brick a Project Haystack](#případ-použití-2--integrace-dat-pomocí-brick-a-project-haystack)
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
- Integrace dat pomocí Project Haystack – sjednocení heterogenních datových zdrojů do standardizovaného modelu s využitím .NET klienta pro snadné dotazování a analýzu.
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

**Persistentní úložiště:**
- Vytvoření lokální složky (např. `haxall`), která bude připojena do Docker kontejneru pro uchování databázových dat

**Spuštění kontejneru:**
- Docker kontejner se spouští s odpovídající konfigurací portů a volume, což umožňuje přístup k webovému rozhraní Haxall serveru na adrese [http://localhost:8080](http://localhost:8080)

### 3.3 Ukázkové kódy
<a name="33-ukázkové-kódy"></a>

1. Vytvoření lokální složky:
```bash
mkdir haxall
```
2. Spuštění Docker kontejneru:
```bash
docker run -v ./haxall:/app/haxall/dbs -p 8080:8080 --name haxall_run ghcr.io/haxall/haxall
```
### 3.4 Testování a ověření
<a name="34-testování-a-ověření"></a>

**Ověření spuštění:**
- Po úspěšném spuštění kontejneru sledujte výstup v terminálu pro zprávy o inicializaci
- Otevřete webový prohlížeč a přejděte na adresu:  
  [http://localhost:8080](http://localhost:8080)


  
  Měli byste vidět přihlašovací obrazovku nebo dashboard Haxall serveru
![přihlašeni](https://github.com/MykhailoMaidiuk/pdomoje/blob/main/haxall_login.png?raw=true)


<br><br><br>
## 4. Integrace dat pomocí Project Haystack
<a name="případ-použití-2--integrace-dat-pomocí-brick-a-project-haystack"></a>

### 4.1 Popis a účel
<a name="41-popis-a-účel"></a>

Tato část dokumentace popisuje, jak v .NET aplikaci navázat spojení s Haxall serverem (Project Haystack API).
Cílem je ukázat, jak bezpečně autentizovat klienta, otevřít spojení a ověřit přístup k datům prostřednictvím jednoduchého API volání.

### 4.2 Technická architektura a pracovní postup
<a name="42-technická-architektura-a-pracovní-postup"></a>

**Předpoklady**

- Běží lokální nebo vzdálený Haxall server (např. na [http://localhost:8080/api/](http://localhost:8080/api/)).
- .NET 6+ projekt, do kterého lze přidat NuGet balíček.

**Instalace klienta**

```bash
dotnet add package ProjectHaystack.Client
```

**Konfigurace autentizace**

- Použijte **SCRAM** nebo **Basic** autentizaci podle nastavení serveru.

**Otevření spojení**

- Vytvořte instanci `HaystackClient`, předáte autentizátor a základní URI API.

**Ověření připojení**

- Po úspěšném `OpenAsync()` můžete volat další operace, jako jsou dotazy nebo importy.


### 4.3 Ukázkové kódy
<a name="43-ukázkové-kódy"></a>

**Připojení k Project Haystack API pomocí C#:**
```csharp
using System;
using System.Threading.Tasks;
using ProjectHaystack.Client;

public class HaystackConnector
{
    public async Task ConnectAsync()
    {
        // 1) Set up credentials
        var user = "someuser";
        var pass = "somepassword";
        var uri  = new Uri("https://someserver/api/");
        
        // 2) Create authenticator
        var auth = new ScramAuthenticator(user, pass) { AddLegacySpaceToProof = true };
        
        // 3) Initialize Haystack client
        var client = new HaystackClient(auth, uri);
        
        // 4) Open connection to the API
        await client.OpenAsync();
        Console.WriteLine("Successfully connected to the Project Haystack API.");
    }
}
```

<br><br><br>
## Nahrání Brick ontologie na server
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

- Apache Jena Fuseki lze stáhnout a spustit jako službu či samostatnou aplikaci.

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

