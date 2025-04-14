
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
- [Prediktivní analýza spotřeby energie](#případ-použití-3--prediktivní-analýza-spotřeby-energie)
  - [5.1 Popis a účel](#51-popis-a-účel)
  - [5.2 Technická architektura a pracovní postup](#52-technická-architektura-a-pracovní-postup)
  - [5.3 Ukázkové kódy](#53-ukázkové-kódy)
  - [5.4 Testování a ověření](#54-testování-a-ověření)
- [Závěr](#závěr)

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
## 5. Analýza a řízení provozu budovy pomocí Brick Schema (.NET)
<a name="případ-použití-3--prediktivní-analýza-spotřeby-energie"></a>

### 5.1 Popis a účel
<a name="51-popis-a-účel"></a>

Cílem tohoto případu je využít otevřený standard Brick Schema k modelování zařízení a prostor v budově.  
Pomocí .NET aplikace načteme RDF data (Brick model) a provedeme SPARQL dotaz na vyhledání HVAC zařízení.  
Tím získáme sémanticky bohatý digitální dvojník budovy, který usnadní identifikaci provozních anomálií a optimalizaci řízení.

### 5.2 Technická architektura a pracovní postup
<a name="52-technická-architektura-a-pracovní-postup"></a>

**Integrace dat:**  
- Data z různých senzorů a systémů jsou zmapována do Brick modelu a exportována do RDF formátu — např. „brick_model.ttl“.

**Dotazování:**  
- Pomocí knihovny dotNetRDF načteme RDF graf a SPARQL dotazem vyhledáme všechna zařízení typu Brick:HVAC.

**Výstup:**  
- Výsledky dotazu slouží jako základ pro další analýzu a případnou automatizaci řízení HVAC.

### 5.3 Ukázkový kód (.NET – C#)
<a name="53-ukázkové-kódy"></a>

```csharp
using System;
using VDS.RDF;
using VDS.RDF.Query;
using VDS.RDF.Parsing;

class Program {
    static void Main() {
        Graph g = new Graph();
        FileLoader.Load(g, "brick_model.ttl");
        g.NamespaceMap.AddNamespace("brick", new Uri("https://brickschema.org/schema/1.1/Brick#"));
        string query = @"PREFIX brick: <https://brickschema.org/schema/1.1/Brick#>
                         SELECT ?device WHERE { ?device a brick:HVAC . }";
        SparqlResultSet results = (SparqlResultSet)g.ExecuteQuery(query);
        foreach (var r in results) Console.WriteLine(r["device"]);
    }
}
```

### 5.4 Testování a ověření
<a name="54-testování-a-ověření"></a>

**Validace RDF modelu:**  
- Ověřte, že soubor „brick_model.ttl“ obsahuje správně definované entity pomocí RDF editoru — např. Protégé.

**Ověření dotazu:**  
- Spusťte aplikaci a zkontrolujte výpis HVAC zařízení v konzoli.

**Další integrace:**  
- Výsledky lze dále zpracovávat a propojovat s automatizačními logikami pro optimalizaci provozu budovy.
