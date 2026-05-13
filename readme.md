# ShopNova — Dispensa completa
## Guida all'installazione, avvio e presentazione

---

## 1. Struttura del progetto

```
ecommerce/
│
├── server.py                  ← Applicazione Flask (file principale)
├── .env                       ← Variabili d'ambiente (NON caricare su Git)
├── ecommerce.sql              ← Export del database con dati demo
├── README.md                  ← Questa dispensa
│
├── cert/                      ← Certificati SSL (opzionale per HTTPS)
│   ├── cert.pem
│   └── private.pem
│
├── static/
│   ├── css/style.css          ← Stile personalizzato
│   ├── js/main.js             ← JavaScript
│   └── img/                   ← Immagini prodotti locali (creata automaticamente)
│
└── templates/
    ├── base.html              ← Layout base (navbar, footer, flash)
    ├── index.html             ← Home page
    ├── catalogo.html          ← Catalogo prodotti con filtri
    ├── login.html             ← Accesso
    ├── registrazione.html     ← Registrazione
    ├── carrello.html          ← Carrello acquisti
    ├── ordini.html            ← Ordini utente
    ├── profilo.html           ← Profilo utente
    ├── errore.html            ← Pagine di errore (404, 500)
    └── admin/
        ├── dashboard.html     ← Dashboard amministratore
        ├── prodotti.html      ← Gestione prodotti
        ├── categorie.html     ← Gestione categorie
        ├── ordini.html        ← Gestione ordini
        ├── spedizioni.html    ← Gestione spedizioni
        ├── coupon.html        ← Gestione coupon (Redis)
        ├── corrieri.html      ← Gestione corrieri
        ├── metodi_pagamento.html ← Gestione metodi di pagamento
        └── utenti.html        ← Gestione utenti
```

**Cartelle create automaticamente all'avvio** (non devono essere presenti):
- `flask_session/` — sessioni Flask
- `static/img/` — immagini prodotti
- `.venv/` — da creare manualmente (vedi punto 3)

---

## 2. Prerequisiti software

| Software | Versione | Note |
|----------|----------|------|
| Python | 3.9 o superiore | Verificare con `python --version` |
| XAMPP | qualsiasi | Avviare **Apache + MySQL** |
| Redis | 7.x | Vedi link sotto per Windows |
| Mozilla Firefox | qualsiasi | Browser richiesto per i test |

**Redis su Windows:**
Scarica da: https://github.com/zkteco-home/redis-windows/releases
Avvia `redis-server.exe` e tienilo aperto in background.

**Redis su macOS:**
```bash
brew install redis
brew services start redis
```

---

## 3. Installazione passo per passo

### 3.1 — Prima di tutto: avvia MySQL e Redis

1. Apri **XAMPP Control Panel** (Windows) o **MAMP** (macOS)
2. Avvia **Apache** e **MySQL**
3. Avvia **Redis** (`redis-server.exe` su Windows, `brew services start redis` su macOS)

### 3.2 — Crea l'ambiente virtuale Python

Apri un terminale nella cartella del progetto:

```bash
# Windows
python -m venv .venv
.venv\Scripts\activate

# macOS / Linux
python3 -m venv .venv
source .venv/bin/activate
```

Dovresti vedere `(.venv)` all'inizio del prompt.

### 3.3 — Installa le dipendenze

Con il venv attivo:

```bash
pip install flask flask-session mysql-connector-python redis python-dotenv
```

### 3.4 — Configura il file .env

Il file `.env` è già presente. Aprilo e verifica/modifica se necessario:

```env
SECRET_KEY=shopnova_secret_key_cambiami
MYSQL_HOST=127.0.0.1
MYSQL_USER=root
MYSQL_PASSWORD=         ← vuoto se XAMPP non ha password MySQL
MYSQL_DB=ecommerce
REDIS_HOST=127.0.0.1
REDIS_PORT=6379
APP_HOST=0.0.0.0
APP_PORT=5000
CARRELLO_TTL_ORE=72
SPEDIZIONE_GRATUITA_SOGLIA=49.00
COSTO_SPEDIZIONE=5.99
```

**Su macOS con MAMP:** la password MySQL di default potrebbe essere `root`. Imposta `MYSQL_PASSWORD=root`.

### 3.5 — Avvia l'applicazione

```bash
python server.py
```

All'avvio vedrai nel terminale:
```
===========================================
  ShopNova — Setup avvio in corso...
===========================================
[INFO] Database "ecommerce" verificato/creato.
[INFO] Tabella "metodi_pagamenti" verificata/creata.
[INFO] Tabella "utenti" verificata/creata.
...
[INFO] Utente admin creato  -->  username: admin  |  password: Admin1234
[INFO] Coupon WELCOME10 creato su Redis.
===========================================
  Setup completato. Avvio server Flask...
===========================================
```

### 3.6 — Apri il browser

Apri **Mozilla Firefox** e vai su:

```
http://localhost:5000
```

---

## 4. Primo accesso

### Credenziali admin predefinite

| Campo | Valore |
|-------|--------|
| Username | `admin` |
| Password | `Admin1234` |

**Cambia subito la password** dopo il primo accesso: vai su **Account → Profilo → Nuova password**.

### Come promuovere altri utenti ad admin

1. Accedi come admin
2. Vai su **Admin → Utenti**
3. Clicca **"Rendi Admin"** accanto all'utente desiderato

---

## 5. Funzionalità per l'utente normale

| Funzione | Come accedervi |
|----------|----------------|
| Registrazione | Pulsante "Registrati" in alto a destra |
| Login | Pulsante "Accedi" in alto a destra |
| Catalogo prodotti | Menu "Catalogo" nella navbar |
| Filtri catalogo | Sidebar sinistra nella pagina catalogo |
| Aggiungere al carrello | Pulsante "Aggiungi" su ogni prodotto |
| Coupon sconto | Campo "Codice coupon" nel carrello (usa WELCOME10) |
| Checkout | Pulsante "Procedi al checkout" nel carrello |
| Storico ordini | Account → I miei ordini |
| Tracking spedizione | Nella pagina ordini, link sotto ogni ordine spedito |
| Modifica profilo | Account → Profilo |

---

## 6. Funzionalità per l'amministratore

Accessibili dal menu **Admin** nella navbar (visibile solo se loggato come admin).

### Dashboard
Mostra statistiche in tempo reale: numero prodotti, utenti, ordini e categorie.

### Prodotti
- **Aggiungi**: clicca "Nuovo prodotto" → compila il form → Salva
- **Modifica**: icona matita nella riga del prodotto
- **Elimina**: icona cestino (con conferma)
- **Immagini**: inserisci un URL esterno (https://...) oppure il nome del file (es. `scarpe.jpg`) se hai caricato l'immagine nella cartella `static/img/`

### Categorie
- Supporta categorie padre/figlio (es. Elettronica → Smartphone)
- Nel dropdown "Categoria padre" vedi il percorso completo
- Il breadcrumb nel catalogo mostra automaticamente il percorso

### Ordini
- Modifica stato e pagamento per **numero ordine** (aggiorna tutte le righe)
- Stati disponibili: In attesa, In lavorazione, Spedito, Consegnato, Annullato

### Spedizioni
Flusso corretto per gestire una spedizione:
1. Dalla sezione **Ordini**, imposta lo stato a `Spedito`
2. Vai in **Spedizioni** → apparirà l'ordine nel riquadro arancione "Ordini senza spedizione"
3. Clicca **Registra** → seleziona corriere, inserisci URL tracking, data consegna prevista (non nel passato)
4. Quando il pacco viene consegnato → clicca **Conferma consegna** → lo stato diventa automaticamente `Consegnato`

### Coupon (Redis)
- **Crea coupon**: clicca "Nuovo coupon" → inserisci codice, sconto%, data scadenza, numero utilizzi
- Il coupon **WELCOME10** (10% di sconto, 1000 utilizzi) viene creato automaticamente all'avvio
- Un utente non può usare lo stesso coupon due volte
- Se gli utilizzi si esauriscono, appare il messaggio "Coupon esaurito"

### Metodi di pagamento
- Aggiungi, modifica o elimina metodi di pagamento
- Il campo "Tipo carta" è opzionale (per PayPal, bonifico ecc. lasciarlo vuoto)
- Se un utente inserisce un tipo carta già esistente, non viene duplicato

### Corrieri
- Gestisci i corrieri disponibili per le spedizioni
- Le aree coprono Italia, Europa, Internazionale

### Utenti
- Visualizza tutti gli utenti registrati
- Promuovi/retrocedi admin con un clic (con conferma)
- Elimina utenti (gli ordini associati vengono eliminati automaticamente)

---

## 7. Immagini prodotti locali

Per usare immagini locali invece di URL esterni:

1. Copia il file immagine nella cartella `static/img/` (es. `sneakers.jpg`)
2. Nel form prodotto (Aggiungi o Modifica), nel campo **URL Immagine** scrivi solo il nome del file: `sneakers.jpg`
3. Il sistema gestisce automaticamente il percorso `/static/img/sneakers.jpg`

Formati supportati: `.jpg`, `.jpeg`, `.png`, `.gif`, `.webp`, `.svg`

---

## 8. Database — comportamento automatico

Il programma gestisce il database **automaticamente** ad ogni avvio tramite la funzione `setup()`:

| Situazione | Comportamento |
|------------|---------------|
| Database non esiste | Viene creato automaticamente |
| Tabella mancante | Viene creata automaticamente |
| Tabella vuota senza dati base | Vengono inseriti i dati minimi |
| Nessun utente admin | Viene creato `admin` / `Admin1234` |
| Redis offline | Avviso nel terminale, app funziona senza carrello/coupon |
| MySQL offline | Avviso nel terminale, pagine mostrano messaggio di errore |

---

## 9. Redis — struttura dati

| Chiave | Tipo | Contenuto | Scadenza |
|--------|------|-----------|----------|
| `carrello:{id_utente}` | Hash | `{id_prodotto: quantita}` | 72 ore |
| `coupon:{CODICE}` | Hash | `{sconto, scadenza, utilizzi}` | Data scadenza |
| `coupon_usato:{id}:{CODICE}` | String | `"1"` | 365 giorni |

---

## 10. Risoluzione problemi comuni

**"Username o password errati" anche con credenziali giuste**
→ La migrazione ha corretto la collation del database. Se il problema persiste, registra un nuovo account e promuovilo admin con:
```sql
UPDATE utenti SET tipo_u=1 WHERE username_u='tuousername';
```

**Pagina coupon mostra errore 500**
→ Assicurati che Redis sia avviato prima di lanciare server.py

**Immagine prodotto non appare**
→ Verifica che il file sia nella cartella `static/img/` e che il nome nel campo corrisponda esattamente (maiuscole/minuscole incluse)

**"Database non disponibile"**
→ Verifica che MySQL sia avviato in XAMPP/MAMP

**Porta 5000 occupata**
→ Cambia `APP_PORT=5001` nel file `.env`

---

## 11. Checklist per la presentazione

Prima di mostrare il progetto al professore:

- [ ] MySQL avviato in XAMPP (o MAMP su Mac)
- [ ] Redis avviato (`redis-server.exe` o `brew services start redis`)
- [ ] Venv attivo (`.venv\Scripts\activate` o `source .venv/bin/activate`)
- [ ] Dipendenze installate (`pip install flask flask-session mysql-connector-python redis python-dotenv`)
- [ ] File `.env` configurato con la password MySQL corretta
- [ ] `python server.py` avviato senza errori nel terminale
- [ ] Firefox aperto su `http://localhost:5000`
- [ ] Accesso con `admin` / `Admin1234` funzionante

### Sequenza di demo consigliata

1. **Home page** — mostra il catalogo e la topbar con il coupon
2. **Registrazione** — crea un account utente normale
3. **Catalogo** — mostra i filtri per categoria (con breadcrumb padre/figlio)
4. **Carrello** — aggiungi prodotti, applica coupon `WELCOME10`
5. **Checkout** — completa un ordine
6. **Admin → Ordini** — modifica lo stato a "Spedito"
7. **Admin → Spedizioni** — registra la spedizione con corriere e tracking
8. **Admin → Conferma consegna** — l'ordine diventa "Consegnato"
9. **Admin → Coupon** — crea un nuovo coupon
10. **Admin → Utenti** — mostra la gestione ruoli

---

per i file .pem:
I file .pem non sono file di testo che si "scrivono" — sono certificati crittografici generati matematicamente dalla tua macchina. Non esiste un contenuto valido che io possa inventare: un .pem scritto a mano non funzionerebbe perché il certificato e la chiave privata devono essere una coppia matematicamente correlata, generata insieme.
Detto questo, generarli è semplicissimo. Apri il terminale nella cartella ecommerce_flask/cert/ e lancia questo comando:
bashopenssl req -x509 -newkey rsa:2048 -keyout private.pem -out cert.pem -days 365 -subj "/CN=localhost"
Ti chiederà una passphrase — digita test (che è quella già configurata nel codice).
Se non hai OpenSSL installato:

Windows con XAMPP: è già incluso in C:\xampp\apache\bin\openssl.exe — usa quello
Windows senza XAMPP: scarica da https://slproweb.com/products/Win32OpenSSL.html
macOS: è già installato di default, il comando funziona così com'è


Detto questo, i certificati SSL sono opzionali per il tuo progetto. Il tuo server.py già gestisce il caso in cui non li trovi: se la cartella cert/ è vuota o assente, l'app si avvia automaticamente in HTTP sulla porta 5000 e funziona perfettamente. Per la presentazione al professore, HTTP su http://localhost:5000 va benissimo.

--------
ricorda: se vuoi usare l'app senza errori vai in .env e modifica 
APP_PORT=5000
SSL_CERT=
SSL_KEY=

che di norma sarebbero così:
SSL_CERT=cert/cert.pem
SSL_KEY=cert/private.pem
APP_PORT=443

in questo modo così non ci sarà bisogno di fare altre azioni
se ti dice questo all'esecuzione:
Tentativo di accesso al socket con modalità non consentite dalle rispettive autorizzazioni di accesso
--------

*ShopNova — Progetto sviluppato con Flask · MySQL · Redis · Bootstrap 5*
