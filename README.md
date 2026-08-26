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

### 1. Abilitazione Packages su Home Assistant
Assicurati che nel tuo file `configuration.yaml` sia abilitata la gestione dei packages:

```yaml
homeassistant:
  packages: !include_dir_named packages
```

---

### 2. Configurazione Bot Telegram & Notifiche
Per consentire a Home Assistant di inviare notifiche e ricevere i comandi dei pulsanti inline (`/irrigazione_orto_si`, `/irrigazione_orto_no`), è necessario un Bot Telegram configurato:

#### A. Creazione del Bot Telegram
1. Apri Telegram e cerca **`@BotFather`** (l'account ufficiale con la spunta blu).
2. Invia il comando `/newbot`.
3. Scegli un **nome** per il bot (es. *Home Assistant Orto*) e uno **username** che deve terminare per `bot` (es. *mio_orto_ha_bot*).
4. BotFather ti fornirà un **API Token HTTP** (es. `123456789:ABCdefGhIJKlmNoPQRsTUVwxyZ`).

#### B. Recupero del Chat ID
1. Cerca su Telegram il bot **`@userinfobot`** (o **`@IDBot`**) e invia `/start`.
2. Annota il valore numerico indicato come **`Id`** (es. `987654321`).
3. Apri la chat con il bot creato al punto precedente e premi **Avvia** (`/start`) per abilitarlo all'invio di messaggi verso di te.

#### C. Integrazione in `configuration.yaml`
Aggiungi o verifica la configurazione dell'integrazione Telegram nel tuo `configuration.yaml`:

```yaml
telegram_bot:
  - platform: polling
    api_key: !secret telegram_bot_api_key
    allowed_chat_ids:
      - !secret telegram_chat_ids_orto

notify:
  - platform: telegram
    name: "telegram_bot"
    chat_id: !secret telegram_chat_ids_orto
```

---

### 3. Account OpenWeatherMap
1. Registrati su [OpenWeatherMap](https://openweathermap.org/).
2. Genera una chiave API e assicurati di avere accesso al piano **One Call API 3.0** (i primi 1.000 chiamate/giorno sono gratuite).

---

## ⚙️ Installazione e Configurazione

1. Salva il file come `irrigazione_orto.yaml` all'interno della cartella `/homeassistant/packages/`.
2. Aggiungi i seguenti segreti al tuo file `secrets.yaml`:

```yaml
owm_api_key_orto: "TUA_CHIAVE_API_OPENWEATHERMAP"
telegram_bot_api_key: "IL_TUO_TOKEN_BOTFATHER"
telegram_chat_ids_orto: 987654321 # Oppure lista: [12345678, 87654321]
```

3. **Personalizzazioni nel file `irrigazione_orto.yaml`:**
   * **Coordinate:** Inserisci latitudine (`lat`) e longitudine (`lon`) del tuo orto nei parametri del sensore REST.
   * **Entità Switch:** Modifica `switch.irrigazione_orto` con l'ID reale della tua elettrovalvola o relè.
   * **Orario di avvio:** Di default scatta al tramonto (`sunset`). Puoi impostare un `offset` (es. `"-01:00:00"` per anticipare di un'ora).
   * **Durata irrigazione:** Di default impostata su 30 minuti (`00:30:00`).

4. Ricarica la configurazione YAML da **Strumenti per gli sviluppatori > YAML** oppure riavvia Home Assistant.

---

## 📱 Controlli Telegram Supportati

Il bot invia notifiche interattive con pulsanti inline:
* `✅ Accendi`: Forza l'irrigazione manuale quando viene saltata per pioggia.
* `❌ Spegni`: Interrompe immediatamente l'irrigazione in corso.
