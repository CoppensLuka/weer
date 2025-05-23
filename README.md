# WeerEventsApi

**Simuleert en logt metingen van weerstations via een REST API.**

---

## 🔧 Build, Run & Test

### Vereisten
- .NET 9 SDK
- Visual Studio met ASP.NET workload of VS Code + REST Client extensie

### Build
```bash
dotnet build
```

### Run
```bash
dotnet run
```

### Test met .http bestand
Gebruik een REST Client (zoals VS Code REST Client extensie) en open `api_test_requests.http`

---

## Known Issues
- Geen persistente opslag: data gaat verloren bij restart
- Mogelijke race conditions bij parallelle metingen (momenteel geen locking voorzien)
- Bij meerdere weerbericht-aanvragen kort na elkaar: wachttijd door lazy-loading proxy (cache = 1 minuut)

### Mogelijke oplossingen
- Async cachingstrategie voor weerberichtproxy
- Toevoegen van memory-database (bv. LiteDB of InMemoryCollection)

---

## Klassendiagram (informeel)
+---------------------+           +-------------------------+
|    Program.cs       | ------->  |     ApiController       |
| (Startpunt app)     |           | (Externe API-routes)    |
+---------------------+           +-------------------------+
                                           |
                                           v
+------------------------+        +---------------------------+
| DomeinController       |<------>| MetingLogger              |
| (Bedrijfslogica)       |        | (Logging van metingen)   |
+------------------------+        +---------------------------+
                                           ^
                                           |
                             +-----------------------------+
                             | MetingLoggerFactory         |
                             | (Maakt logger-instanties)   |
                             +-----------------------------+

+------------------+      +------------------+      +--------------------+
| Stad             |<---->| StadRepository   |<---->| IStadRepository    |
| (Data object)    |      | (Data toegang)   |      | (Interface repo)   |
+------------------+      +------------------+      +--------------------+
         ^                          ^
         |                          |
+-------------------+     +-------------------+
| StadManager       |<----| IStadManager      |
| (Stadlogica)      |     | (Interface logic) |
+-------------------+     +-------------------+

+--------------------+      +----------------------------+
| WeerStation        |<-----| AbstractWeerstation        |
| (Concrete klasse)  |      | (Abstracte klasse)         |
+--------------------+      +----------------------------+
         ^
         |
+--------------------------+
| WeerstationFactory       |
| (Maakt weerstations)     |
+--------------------------+

+----------------------------+
| Weerbericht               |
| (Bevat weerinfo)          |
+----------------------------+
         ^
         |
+-----------------------------+
| IWeerberichtGenerator      |
| + EchteWeerberichtGenerator|
| + WeerberichtGeneratorProxy|
+-----------------------------+

