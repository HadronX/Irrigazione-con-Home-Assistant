# 🌿 Automazione Irrigazione Smart per Orto (Home Assistant)

Pacchetto YAML completo per gestire l'irrigazione automatica dell'orto su **Home Assistant** basandosi sullo storico della pioggia e sulle previsioni meteo tramite **OpenWeatherMap One Call API 3.0**, con supporto per notifiche e controlli interattivi via **Telegram**.

---

## 🚀 Funzionalità

* **Monitoraggio Meteo Continuo:** Campiona ogni ora la pioggia caduta nell'ultima ora e stima quella prevista per le successive 4 ore tramite API REST.
* **Buffer Storico a Scorrimento:** Mantiene in memoria le ultime 10 ore di pioggia effettiva.
* **Decisione al Tramonto:** Calcola la pioggia totale (*ultime 10h + prossime 4h*) e la confronta con una soglia impostata:
  * **Sotto soglia:** Attiva l'irrigazione per 30 minuti e invia una notifica con pulsante per spegnerla.
  * **Sopra soglia:** Salta l'irrigazione e invia una notifica con pulsante per forzare l'accensione manuale.
* **Soglia Personalizzabile:** Possibilità di modificare la soglia di pioggia (in mm) direttamente dalla dashboard Lovelace (`input_number.soglia_pioggia_orto_mm`).
* **Spegnimento Automatico:** Timer di sicurezza impostato su 30 minuti.

---

## 📋 Prerequisiti

1. **Integrazione Telegram configurata:** Bot Telegram attivo con gestione dei callback query.
2. **Account OpenWeatherMap:** Chiave API valida con accesso a **One Call API 3.0**.
3. **Abilitazione Packages su Home Assistant:** Assicurati che nel tuo file `configuration.yaml` sia abilitata la cartella `packages`:

```yaml
homeassistant:
  packages: !include_dir_named packages
```

---

## ⚙️ Installazione e Configurazione

1. Salva il file come `irrigazione_orto.yaml` all'interno della cartella `/config/packages/`.
2. Aggiungi i seguenti segreti al tuo file `secrets.yaml`:

```yaml
owm_api_key_orto: "TUA_CHIAVE_API_OPENWEATHERMAP"
telegram_chat_ids_orto: "TUO_CHAT_ID_TELEGRAM" # oppure [12345678, 87654321]
```

3. **Personalizzazioni nel file `irrigazione_orto.yaml`:**
   * **Coordinate:** Inserisci latitudine (`lat`) e longitudine (`lon`) del tuo orto nei parametri REST.
   * **Entità Switch:** Modifica `switch.irrigazione_orto` con il nome reale della tua elettrovalvola/interruttore.
   * **Orario di avvio:** Di default parte al tramonto (`sunset`). Puoi aggiungere un `offset` (es. `"-01:00:00"` per anticipare di un'ora).
   * **Durata:** Di default è impostata su 30 minuti (`00:30:00`).

4. Riavvia Home Assistant o ricarica le entità e le automazioni da **Strumenti per gli sviluppatori**.

---

## 📱 Controlli Telegram Supportati

Il bot invia messaggi interattivi con tastiera inline:
* `✅ Accendi`: Forza l'irrigazione manuale quando viene saltata per pioggia.
* `❌ Spegni`: Interrompe l'irrigazione avviata automaticamente.
