# Super Mario Galaxy Multiplayer

Mod per giocare a **Super Mario Galaxy** online, in più persone, su **Dolphin** (con Riivolution). Ognuno gioca nella propria copia del gioco e vede gli altri Mario correre nella stessa galassia, in tempo reale.

> Basato sul mod multiplayer di **Headpenguin**. Questo progetto aggiunge un server per Windows con pannello di controllo e una serie di funzioni nuove, descritte sotto.

---

## Cosa si può fare

- **Vedere gli altri giocatori** – fino a 8 Mario insieme, con posizione, direzione e animazione della camminata (non scivolano più).
- **Friendly fire** (si accende e spegne dal server, anche a partita in corso)
  - schianto a terra addosso a un altro: gli toglie 1 di vita
  - piroetta: lo fa cadere, senza danno
- **Stelle condivise** – quando uno prende una Power Star, la stella compare addosso agli altri che sono nella stessa galassia e la prendono insieme a lui.
- **Nemici condivisi**
  - un nemico sconfitto da uno sparisce per tutti
  - posizione e stato dei nemici (fermo, insegue, attacca…) arrivano da un giocatore che comanda, così tutti vedono la stessa scena
  - chi colpisce un nemico lo controlla per 2 secondi, così può finirlo
  - i colpi vengono inoltrati a chi comanda, così contano anche per i boss
- **Boss condivisi** – Topmaniac, Kamella, Bugaboom, Pianta Dino, Bowser, Bouldergeist, Kingfin, Tarantox, Megaleg, Major Burrows, Baron Brrr, aeronave di Bowser Jr. Comanda chi ha visto più cutscene nella galassia, cioè chi è più avanti nel combattimento.
- **Teletrasporto** – dal pannello del server, un tasto porta tutti i Mario da te (come `/tp` in Minecraft).
- **Logo personalizzato** nella schermata del titolo, così si vede subito se la mod è attiva.

## Cosa non fa (ancora)

- Le **monete** non sono condivise.
- Piattaforme mobili, interruttori e altri oggetti del livello non sono sincronizzati.
- La rotazione dei nemici non viene trasmessa: a volte un nemico guarda dalla parte sbagliata.
- Chi è indietro in un boss non vede la cutscene: il suo boss passa direttamente alla fase nuova.
- Il teletrasporto funziona solo tra giocatori nella stessa galassia.

---

## Requisiti

- **Dolphin** (versione recente)
- **Super Mario Galaxy, versione USA** – codice disco **RMGE01**. Con la versione europea (RMGP01) non funziona: le patch usano indirizzi di memoria dell'eseguibile americano.
- Una copia del gioco **di tua proprietà**. Questo progetto non contiene e non distribuisce file del gioco.
- Per giocare in rete senza aprire porte sul router: **Radmin VPN** o simili (oppure un server raggiungibile da internet).

## Installazione (giocatore)

1. Estrai il kit e fai doppio clic su **`Connetti al server.bat`**.
2. Scrivi l'indirizzo del server e premi **Prova**: ti dice subito se risponde.
3. Premi **Installa / aggiorna**: copia i file della mod nelle cartelle giuste di Dolphin.
4. In Dolphin: tasto destro sul gioco → **Start with Riivolution Patches** → *Syati Loader* su **Enabled** → Start.

Se nel titolo compare **SUPER MARIO GALAXY MULTIPLAYER**, la mod è attiva. Entra in una galassia: gli altri compaiono da soli.

## Server

### Windows
Avvia **`Avvia server.bat`**: si apre un pannello con
- Avvia / Ferma e registro dei collegamenti
- indirizzo da dare agli amici, con tasto *Copia*
- casella **Friendly fire**
- **Stella per tutti** (per provare la stella condivisa senza finire un livello)
- **Teletrasporta tutti da me**
- **Test player** (Mario finti per provare da soli)

### Linux / VPS
Nella cartella `server-linux` ci sono sorgenti, servizio systemd e script di installazione.

```bash
./SMGServer 5029 0.0.0.0 --friendly-fire=off --controllo friendlyfire.txt --tp tp.txt
```

| Opzione | Cosa fa |
|---|---|
| `--friendly-fire=on\|off` | stato iniziale del friendly fire |
| `--controllo FILE` | file con `1`/`0`, riletto ogni secondo: cambia il friendly fire senza riavviare |
| `--tp FILE` | scrivendoci il numero di uno slot, tutti vengono portati da quel giocatore |

Porta di default: **UDP 5029**.

---

## Come funziona (per chi è curioso)

Il mod client originale si compila solo con CodeWarrior per PowerPC (licenza Gekko), che non avevo. Per questo tutte le funzioni nuove sono **patch scritte a mano in assembly PowerPC** direttamente dentro `CustomCode_USA.bin` (formato Kamek):

- il codice nuovo viene aggiunto dopo quello originale e agganciato con dei salti in pochi punti, controllati uno per uno
- un emulatore PowerPC scritto apposta (`simulate.py`) esegue il codice nuovo contro funzioni finte del gioco e verifica ogni comportamento prima di ogni rilascio
- le vtable dei nemici non si prendono dalla mappa dei simboli: si cercano in memoria all'avvio, per firma
- il pacchetto posizione è passato da 56 a 124 byte (il massimo è 128), ma resta compatibile: le versioni vecchie leggono solo i primi 56

Il server si limita a inoltrare i pacchetti. Toglie i flag d'attacco se il friendly fire è spento, accende quello del teletrasporto e riaccetta i giocatori rimasti collegati dopo un riavvio del server.

Strumenti inclusi: `Sniff` (mostra cosa trasmette ogni giocatore, utile per capire cosa non va), `TestPlayers`, `StarSpawn`, `ConnTest`.

---

## Problemi comuni

| Problema | Soluzione |
|---|---|
| `SYA_ERR - Binary too small` | Ripremi *Installa / aggiorna* nel pannello del client |
| Logo del titolo normale | La patch non è attiva: avvia con Riivolution e metti *Enabled* |
| Non vedo nessuno | VPN collegata? Server acceso (*Prova*)? Siete **dentro** una galassia? Niente save state di Dolphin: riavvia il gioco |
| Cambio indirizzo ma non succede niente | Il gioco legge l'indirizzo solo all'avvio: riavvialo |
| Nemici o camminata non sincronizzati | Tutti devono avere **lo stesso** `CustomCode_USA.bin` |

---

## Crediti

- **Headpenguin** – mod multiplayer originale per Super Mario Galaxy
- **Syati / Kamek** – caricamento del codice personalizzato
- **Petari** e le mappe dei simboli USA della community – per capire com'è fatto il gioco dentro

Super Mario Galaxy è un marchio di Nintendo. Progetto amatoriale, non affiliato né approvato da Nintendo.
