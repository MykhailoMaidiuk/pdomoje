# 📘 Cílové skupiny dokumentace a jejich interakce

Tato dokumentace je určena pro různé skupiny uživatelů, kteří pracují s BEMS (Building Energy Management Systems). Obsahuje praktické návody, technické informace a doporučení pro optimální využití.
# Dokumentace projektu

Veškerá technická dokumentace je nyní v souboru [Dokumentace.md](./Dokumentace.md).


# 👨‍💻 1. Vývojáři a integrátoři BEMS

🔹 **Použití:** Nasazení a propojení backendů (Haxall), vývoj .NET klientů pro Project Haystack, správa dat.

📄 **Interakce s dokumentací:**

🛠 **Formát:** Technické návody, ukázkové C# kódy, Docker příkazy.

📚 **Návod:**

- **Spuštění Haxall** – krok‑za‑krokem Docker příkaz pro lokální server Haystack.
- **Project Haystack API (.NET)** – jak se připojit z C# aplikace ke Haxall serveru.
- **SPARQL dotazy** – příklady pro testování a automatizaci.

---

# 🏢 2. Správci budov

🔹 **Použití:** Vizualizace dat, kontrola provozu a jednoduše nasaditelné řešení bez vývoje.

📄 **Interakce s dokumentací:**

📊 **Formát:** Přehledové kroky, screenshoty Fuseki UI, ukázky SPARQL dotazů.

📚 **Návod:**

- **Nahrání Brick ontologie** – jak uploadovat `Brick+imports.ttl` do Apache Jena Fuseki.
- **Ověření importu** – kontrola počtu triple, základní SPARQL dotaz pro ověření tříd.
- **Interaktivní dotazování** – jednoduché příklady, jak získat přehled o entitách.

---

# 🏭 3. Firmy a dodavatelé technologií pro BEMS

🔹 **Použití:** Výběr a implementace vhodných standardů (Haystack, Brick), integrace do vlastních produktů.

📄 **Interakce s dokumentací:**

📑 **Formát:** Srovnání přístupů, doporučené distribuce ontologií, use‑case studie.

📚 **Návod:**

- **Volba distribuce Brick** – rozdíly mezi `Brick+imports.ttl`, `Brick.ttl`, `Brick-only.ttl`.
- **Enterprise nasazení Fuseki** – doporučené konfigurace pro produkci.
- **Standardizované API volání** – jak integrovat Project Haystack klienta do vlastních řešení.

---


