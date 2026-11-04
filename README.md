# Pokémon-watcher

Controleert elke 30 minuten Marktplaats op nieuwe Pokémon-kaarten binnen 25 km van Venray. Komt er een nieuwe advertentie bij die aan je filters voldoet, dan krijg je een Telegram-bericht met foto, prijs, plaats en een knop naar de advertentie. Het draait gratis op GitHub Actions, dus ook als je pc uit staat.

| Bestand | Wat het doet |
|---|---|
| `zoekopdrachten.toml` | Je zoekopdrachten. **Dit bestand pas je zelf aan.** |
| `marktplaats_watcher.py` | Het script dat zoekt en de berichten stuurt. |
| `.github/workflows/watcher.yml` | Zorgt dat GitHub het script elke 30 minuten draait. |
| `gezien.json` | Houdt bij wat al gemeld is. Wordt automatisch bijgewerkt, niet aankomen. |

## Zoekopdrachten aanpassen

Open `zoekopdrachten.toml`, klik op het potloodje, pas aan en klik op **Commit changes**. Bij de volgende run worden je wijzigingen gebruikt. Bij een nieuwe zoekopdracht krijg je eerst een bevestiging; de advertenties die er op dat moment al staan worden overgeslagen.

```toml
[[zoekopdracht]]
naam = "Charizard"
zoekterm = "charizard"
categorie = 1099
subcategorie = 930
max_prijs = 50
uitsluiten = ["proxy", "nep"]
```

Alle instellingen staan bovenin het bestand uitgelegd.

### Categorienummers

Pokémon-kaarten: `categorie = 1099` (Hobby en Vrije tijd) met `subcategorie = 930` (Verzamelkaartspellen | Pokémon).

## Handig om te weten

**Soort meldingen.** Bovenin `zoekopdrachten.toml` staat `berichten = "overzicht"`: na elke check krijg je één bericht met alle nieuwe advertenties (wat, prijs, plaats en link). Zet je het op `"los"`, dan krijg je per advertentie een apart bericht met foto.

**Met de hand starten.** Ga naar **Actions**, klik op **Pokemon-watcher** en dan op **Run workflow**.

**Pauzeren.** Ga naar **Actions**, klik op **Pokemon-watcher**, dan op de drie puntjes en op **Disable workflow**.

**Kosten.** GitHub Actions is gratis voor openbare repositories.

**Vaker of minder vaak checken.** Pas in `.github/workflows/watcher.yml` de regel `cron: "7,37 * * * *"` aan. Elk uur is `"7 * * * *"`. Elke 15 minuten is `"7,22,37,52 * * * *"`.

**Ontvangers.** De chat-id's staan in het secret `TELEGRAM_CHAT_ID`, met komma's ertussen; de eerste ben jij. Wil iemand anders ook meldingen? Laat diegene je bot een bericht sturen. Bij de volgende check krijg jij in Telegram een melding met de naam en het chat-id. Voeg dat chat-id toe aan het secret (Settings, dan Secrets and variables, Actions, `TELEGRAM_CHAT_ID`, Update). Diegene krijgt daarna een welkomstbericht. Iemand verwijderen: haal het nummer uit het secret.

**Nachtpauze.** Tussen 02:00 en 06:00 (Nederlandse tijd) worden de automatische checks overgeslagen. Aanpassen kan bovenin `zoekopdrachten.toml` (`nachtpauze`). Met de hand starten werkt altijd.

**Openbaar.** Deze repository is openbaar, omdat GitHub automatische runs alleen in openbare repositories betrouwbaar start. Er staan geen persoonlijke gegevens in: het token en de chat-id's staan als secrets en worden in de logs vervangen door `***`.

**Telegram-token wijzigen.** Het token van je bot staat als secret `TELEGRAM_TOKEN` onder **Settings**, dan **Secrets and variables** en **Actions**.

**Mail van GitHub dat een run mislukt is?** Kijk bij **Actions** in de log van die run:
- `TELEGRAM_TOKEN ontbreekt`: het secret is niet ingesteld.
- `Geen chat-id gevonden`: stuur je bot een bericht en start de run opnieuw.
- `Telegram ... fout 401`: het token klopt niet.
- `Telegram ... fout 400: chat not found`: de chat-id klopt niet, of je hebt je bot nog geen bericht gestuurd.
- `FOUT bij Marktplaats ... HTTP 403/429`: Marktplaats weigert het verzoek. Meestal gaat dit vanzelf over. Blijft het gebeuren, check dan minder vaak.

**Let op.** Het script gebruikt de zoekfunctie achter de Marktplaats-website. Dat is geen officiële API, en volgens de robots.txt van Marktplaats horen bots deze zoekfunctie niet te gebruiken. Houd het bij persoonlijk gebruik en een rustig tempo. Als Marktplaats de website aanpast, kan het script stoppen met werken.
