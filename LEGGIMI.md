> **PRE-ALFA** — progetto non finito: bug noti (boss non condivisi, selezione stelle non sincronizzata). Usalo a tuo rischio.

# Super Mario Galaxy — Multiplayer online

Pacchetto completo: server, client, strumenti di prova e sorgenti.

Il mod è [SMGNetworkMultiplayer](https://github.com/Headpenguin/SMGNetworkMultiplayer)
di Headpenguin; il server è il suo
[SMGMultiplayerServer](https://github.com/Headpenguin/SMGMultiplayerServer),
qui con correzioni di sicurezza e stabilità. Le modifiche sono tutte in
`sorgenti/`, come patch applicabili all'originale.

---

## Cosa funziona davvero, oggi

Vale la pena essere precisi, perché non tutto quello che c'è nei sorgenti
è anche dentro il gioco.

| | Stato |
|---|---|
| Vedere gli altri giocatori muoversi, saltargli in testa, colpirli | **funziona in gioco** |
| Animazione di camminata dell'altro giocatore | **funziona in gioco** (patch binaria) |
| Friendly fire: schianto = 1 di danno, piroetta = caduta senza danno | **patch binaria**, verificata in emulazione — da provare in gioco |
| Server: correzioni di sicurezza, porta configurabile, servizio systemd | **funziona** |
| Logo del titolo personalizzato | **funziona in gioco** |
| Monete condivise, morte dei nemici condivisa | **solo nei sorgenti** |

L'ultima riga merita una spiegazione. La parte server è finita e verificata:
`SMGServer` riconosce e rilancia i pacchetti `COIN_COLLECTED` e
`ACTOR_KILLED`, e c'è una prova automatica che lo dimostra
(`sorgenti/` → `source/tests/relaytest.cpp`). La parte *client* è scritta
(`sorgenti/mod/source/CoinSync.cpp` e `ActorSync.cpp`) ma **non è compilata
dentro il `CustomCode_USA.bin` che trovi qui**, che resta la build originale
dell'autore.

La camminata è un caso a parte, e l'unica cosa del mod cambiata senza
ricompilare. Nel gioco camminare è un gruppo di animazioni che fonde quattro
tracce con dei pesi ricalcolati a ogni fotogramma. Il pacchetto originale
trasportava solo il gruppo, non i pesi, e l'altro giocatore scivolava invece
di camminare. Il `CustomCode_USA.bin` qui dentro è quello dell'autore con una
patch scritta a mano in assembly PowerPC, che aggiunge i 4 pesi in coda al
pacchetto (da 56 a 72 byte). È compatibile in entrambe le direzioni: una
versione vecchia accetta i pacchetti nuovi e viceversa, ma **la camminata si
vede solo se entrambi avete il file nuovo**. L'originale è in
`sorgenti/patch-pesi/`.

Il motivo per cui il resto non è compilato è che il mod si compila solo con CodeWarrior per PowerPC con
licenza Gekko: le vtable e la decorazione dei nomi devono coincidere con
quelle dell'eseguibile Wii, e GCC produce un ABI incompatibile. L'edizione
gratuita di NXP si installa ma rifiuta `-proc gekko`. Finché non c'è quel
compilatore, monete e nemici condivisi restano codice pronto ma spento.

---

## Da dove partire

### Ospitare dal proprio PC Windows

Vai in `server-windows/` e fai doppio clic su **`Avvia server.bat`**.
Le istruzioni complete (firewall, Radmin VPN, port forwarding, CGNAT) sono
in `server-windows/LEGGIMI-WINDOWS.md`.

### Giocare

Vai in `client/` e fai doppio clic su **`Connetti al server.bat`**.
Il pannello installa i file del mod nelle cartelle di Dolphin, scrive
l'indirizzo del server e prova la connessione. Istruzioni passo passo in
`client/LEGGIMI-CLIENT.txt`.

---

## Le cartelle

| Cartella | Cosa c'è |
|---|---|
| `server-windows/` | `SMGServer.exe` nativo e statico, il pannello di controllo, i giocatori di prova |
| `client/` | pannello di connessione, file del mod da mettere in Dolphin, logo personalizzato |
| `strumenti/` | `Follow.exe` (un Mario che ti ripete), `Observer.exe` (spia il traffico), `Sniff.exe` (chi manda i pesi della camminata) |
| `sorgenti/` | i due patch, il sorgente del mod, il PNG del logo |

---

## Note che risparmiano tempo

**Solo copia USA del gioco** (`RMGE01`). Le patch Riivolution sono indirizzi
di memoria assoluti calcolati sull'eseguibile americano: su una copia
europea (`RMGP01`) non ottieni il multiplayer, ottieni un crash.

**Massimo 8 giocatori.** Il limite è fissato a tempo di compilazione da
entrambi i lati: cambiarlo su uno solo rompe il protocollo.

**Nessuna autenticazione.** Chi conosce indirizzo e porta entra.

**Riavviare il server non obbliga più a riavviare i giochi.** Il mod si
presenta al server una volta sola, all'avvio del gioco: prima, dopo un
riavvio del server i suoi pacchetti venivano scartati («invalid packet
received (5)», una riga a pacchetto) e bisognava riavviare il gioco. Ora il
server lo **riadotta** nel suo vecchio slot al primo pacchetto posizione. Se
nel frattempo quello slot l'ha preso un altro, lo scrive una volta sola nel
registro, e solo in quel caso bisogna riavviare il gioco.

**Gli save state di Dolphin non salvano il socket.** Se ripristini uno stato
il mod continua a credersi connesso ma non lo è: riavvia il gioco da zero.
