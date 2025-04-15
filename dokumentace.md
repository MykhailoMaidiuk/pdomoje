
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

Chytré budovy představují moderní přístup ke správě a optimalizaci provozu, kdy jsou data ze senzorů a IoT zařízení využívána pro zvýšení energetické účinnosti, komfortu a bezpečnosti. Tato dokumentace popisuje implementaci datových modelů a nástrojů v rámci BEMS (Building Energy Management Systems) a je zaměřena na tři konkrétní případy použití. První případ obsahuje podrobný návod, jak spustit Haxall server pomocí Dockeru – kritickou základnu pro následnou integraci dat a další analýzy.

## Cíle dokumentace

- Poskytnout kompletní průvodce instalací a nastavením Haxall serveru pro správu dat chytrých budov.
- Ulehčit práci vývojářům a správcům tím, že nabídnou přesné kroky, ukázkové kódy a doporučení pro ověření správného chodu systému.
- Podpořit integraci dat z různých zdrojů (Brick, Project Haystack) a jejich následné využití pro prediktivní analýzu a optimalizaci provozu budov.

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

Tento případ se zaměřuje na integraci dat z různých senzorů do jednotného datového modelu pomocí otevřených standardů  Project Haystack.  
Cílem je sjednotit data, zajistit jejich interoperabilitu a sémantické obohacení, čímž se zjednoduší další zpracování a analýza.

### 4.2 Technická architektura a pracovní postup
<a name="42-technická-architektura-a-pracovní-postup"></a>

**Nasazení Haxall serveru:**

- Využijte dříve spuštěnou instanci Haxall jako backend pro integraci dat.

**Integrace v .NET aplikaci:**

- Instalace NuGet balíčku ProjectHaystack.Client a vytvoření klienta pro připojení k API Haxall pomocí Basic Authentication.

**Dotazování:**

- Provádění testovacích dotazů nad importovanými daty — např. identifikátory zařízení, propojení objektů — pro ověření správnosti integrace.

### 4.3 Ukázkové kódy
<a name="43-ukázkové-kódy"></a>

**Připojení k Project Haystack API pomocí C#:**
```csharp
using System;
using System.Threading.Tasks; 
using ProjectHaystack.Client;

public class HaystackIntegration  
{  
public async Task ConnectToHaystackAsync()  
        {
            var user = "someuser";
            var pass = "somepassword";
            var uri = new Uri("https://someserver/api/");
            var auth = new ScramAuthenticator(user, pass);
            auth.AddLegacySpaceToProof = true;
            var client = new HaystackClient(auth, uri);
            await client.OpenAsync();
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

