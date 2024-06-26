# Zed Attack Proxy Guide

> OWASP ZAP is a penetration testing tool that helps developers and security professionals detect and find vulnerabilities in web applications

## Features

- Passively scanning web requests
- Using dictionary lists to search for files and folders on web servers
- Using crawlers to identify a site's structure and retrieve all links and URLs
- Intercepting, displaying, modifying, and forwarding web requests between browsers and web applications

OWASP ZAP can identify vulnerabilities in web applications including compromised authentication, exposure of sensitive data, security misconfigurations, SQL injection, cross-site scripting (XSS), insecure deserialization, and components with known vulnerabilities

## Guida

La prima cosa che viene visualizzata all'avvio di ZAP è la scelta della persistenza della sessione, dove si hanno 3 scelte:

- Rendere la sessione persistente con un nome basato su orario e data attuali
- Rendere la sessione persistente con un nome personalizzato
- Non rendere la sessione persistente

Si consiglia di creare una cartella specifica per ogni progetto, in sui salvare la sessione di ZAP, impostando la sessione persistente con un nome personalizzato.

### Impostazione del proxy su ZAP

Per andare ad impostare correttamente il proxy su ZAP, i passaggi da seguire sono i seguenti:

1. Da ZAP andare su `Strumenti` / `Tools` dalla navbar in alto
2. Selezionare `Opzioni` / `Options` > `Network` > `Local Servers/Proxies`
3. Impostare come `IP` quello locale nella rete tramite menu a cascata e come `porta` una libera a scelta

### Esportazione e installazione ZAP CA Certificate

Per andare a proxare il traffico di rete di un dispositivo mobile simulato, ad esempio tramite Genymotion, i passaggi da seguire sono i seguenti:

1. Da ZAP andare su `Strumenti` / `Tools` dalla navbar in alto
2. Selezionare `Opzioni` / `Options` > `Network` > `Server Certificates`
3. Selezionare `Salva` / `Save`
4. Importare il certificato nel telefono tramite `drag-n-drop` o tramite il seguente comando:
```sh
adb push certificate.cer /sdcard/Download/
```
5. Dal dispositivo andare in `Impostazioni` > `Sicurezza` > `Credenziali` > `Installa un certificato` > `CA Certificate` > `Install anyway` > Selezionare il certificate `.cer` importato precedentemente
6. Su alcune versioni di Android, potrebbe essere necessario abilitare il certificato come attendibile per reti Wi-Fi, questo si puo fare andando ad installare il medesimo certificato dalla scheda Wi-Fi delle impostazioni o dal `Sicurezza` > `Credenziali` e installare il certificato come certificato Wi-Fi
7. Controllare che il certificato sia stato accettato e installato tramite `Impostazioni` > `Sicurezza` > `Credenziali` > `Trusted credentials`  > `User` e controllare la presenza di `ZAP Root CA` 
8. Spegnere il telefono chiudendo la finestra di Genymotion
9. Impostare nelle impostazioni di Genymotion il proxy HTTP con indirizzo `IP` e `porta` impostati su ZAP
10. Avviare il telefono
11. Da telefono acceso, dare questo comando da terminale per attivare il proxy e concludere il procedimento
 ```sh
adb shell settings put global http_proxy IP:PORTA
```

### Configurazioni
#### Contesto
I contesti raggruppano un insieme di URL correlati, solitamente corrispondenti a un'applicazione web. Si consiglia di definire un nuovo contesto per ogni applicazione web testata.
Puoi configurare i contesti tramite:
-   Cliccando con il tasto destro sugli Elementi nelle tab Sites o History
-   Le finestre di dialogo dei Contesti della Sessione.

#### Modalità
Iniziamo selezionando la modalità **Standard** nella parte sinistra della barra degli strumenti.
Si possono impostare le seguenti modalità:
-   **Sicura (Safe)** - nessuna operazione potenzialmente pericolosa è permessa.
-   **Protetta (Protected)** - puoi eseguire azioni (potenzialmente) pericolose solo su URL all'interno del contesto.
-   **Standard** - non ci sono restrizioni.
-   **ATTACK** - nuovi nodi all'interno del perimetro vengono attivamente scansionati non appena scoperti.

Si raccomanda di utilizzare la modalità **Protetta** per garantire ZAP che attacchi solo i siti che intendi attaccare.


#### Sessione
Come prima cosa dobbiamo andare a impostare correttamente la sessione, per farlo, andiamo su `File` in alto a sinistra > `Session Properties`

Qui ci sono diverse cose da fare:
**NB**: Sono definite solo quelle piu rilevanti
1. `Generale` > Cambiare nome della sessione
2. `Escludere dal proxy` > Escludere siti indesiderati dal proxy tramite espressione regolare (questo si puo fare anche premendo tasto destro sul `URL `> `Escludere da` nella schermata principale)
3. `Contesti` > Il contesto e tutte le sue impostazioni sono una parte fondamentale
3.1 `Nome di contesto` > Modificare il nome del contesto con uno coerente
3.2 `Includere nel contesto` > Qui possiamo inserire tramite espressione regolare gli host da includere nel contesto
3.3 `Struttura` > Qui possiamo specificare il modo in cui vengono passati i parametri nel `URL` e nel `body` delle richieste
3.4 `Autenticazione` > Qui possiamo specificare come avviene l'autenticazione nel contesto corrente > Possiamo lasciare `Auto-Detect Authentication` in modo che sia ZAP a impostare i parametri corretti, inoltre, possiamo specificare la strategia con la quale viene verificata l'autenticazione tramite il campo `Verification Strategy` > Anche qui possiamo lasciare `Auto-Detect`
3.5 `Utenti` > Qui possiamo specificare gli utenti con il quale effettuare il test
3.6 `Forzare utente` > Impostare un utente inserito precedentemente
3.7 `Gestione della sessione` > Qui possiamo specificare come viene gestita la sessione > Consiglio di lasciare anche qui `Auto-Detect` in modo da lasciare ZAP decidere

Dopo aver sistemato la sessione, è possibile impostare le politiche di scansione tramite `Analizza` (in alto) > `Aggiungi` > Da qui è possibile inserire una nuova politica andando a impostare `soglia` e `livello` per ogni `categoria` di vulnerabilità

Come ultima cosa, prima di cominciare, è buona norma sistemare le opzioni dei vari strumenti, questo lo possiamo fare tramite il menu utilizzato precedentemente `Strumenti` dalla navbar in alto > Selezionare `Opzioni` e vedrete tutti gli strumenti che ZAP mette a disposizione > Alcune considerazioni sui strumenti piu importanti da guardare:

 - `Base dati` > Impostazioni per la persistenza della sessione
 - `Casualizzatore` > Fuzzer
 - `Input vettori della scansione attiva`
 - `Regole di scansione passiva`
 - `Replacer`
 - `Scasione attiva`
 - `Sessioni HTTP`
 - `Spider`
 
Dopo aver finalmente finito di configurare la nostra sessione, possiamo iniziare a utilizzare la nostra applicazione intercettando le richieste tramite ZAP.

> **NB**: cerca di navigare quanto piu possibile la applicazione da testare e di toccare tutti i flussi prima di iniziare ad analizzare le richieste con ZAP

Per prima cosa facciamo qualche richiesta tramite la nostra applicazione in modo da rivelare gli host `in scope` > Quando comprariranno nella schermata principale sotto la voce `Siti` > Fare `tasto destro sul dominio` > `Aggiungi al contesto`, mentre con il resto dei domini inutili > Fare `tasto destro sul dominio` > `Escludere da` > `Proxy`
Dopo aver aggiunto tutti gli host in scope e aver tolto quelli che non ci servono, non resta alto che navigare i flussi il piu possibile, per arrichire al meglio la site map di ZAP e ottenere i migliori risultati possibili

Una volta terminata la fase di navigazione (**veramente**) fare `tasto destro sul contesto` > `Scansione attiva` > Godersi lo spettacolo



### WEB APP Penetration Test Process

#### Automated Scan
Il modo più semplice per iniziare a utilizzare ZAP è tramite la scheda Quick Start.
Per eseguire una Scansione Automatica Quick Start:
-   Avvia ZAP e clicca sulla scheda Quick Start nella finestra di lavoro.
-   Clicca sul grande pulsante Automated Scan.
-   Nella casella di testo URL da attaccare, inserisci l'URL completo dell'applicazione web che vuoi attaccare.
-   Clicca su Attacca.

> **NB**: Lo spider AJAX di ZAP è più efficace per esplorare applicazioni AJAX che generano link tramite JavaScript.

ZAP procederà a esplorare l'applicazione web con il suo **spider** e a eseguire una **scansione passiva** di ogni pagina trovata. Successivamente, ZAP utilizzerà lo **scanner attivo** per attaccare tutte le pagine, le funzionalità e i parametri scoperti.
La scansione passiva non modifica le risposte in alcun modo ed è considerata sicura.
La scansione attiva, invece, tenta di trovare altre vulnerabilità utilizzando attacchi noti contro i bersagli selezionati. La scansione attiva è un vero e proprio attacco su quei bersagli e può metterli a rischio, quindi non utilizzarla contro bersagli per cui non si ha il permesso di testare.

La scansione passiva e l'attacco automatico sono utili per iniziare una valutazione delle vulnerabilità, ma presentano alcune limitazioni:
 - Le pagine protette da login non sono rilevabili senza configurazione dell'autenticazione di ZAP.
 -  Mancanza di controllo sulla sequenza di esplorazione e sui tipi di attacchi in una scansione passiva o attacco automatico.
-   Gli Spiders sono efficaci per l'esplorazione di base, ma devono essere combinati con l'esplorazione manuale per maggiore efficacia.

#### Manual Explorer
Esplora manualmente l'intera applicazione web con un browser che passa attraverso ZAP, che scansiona passivamente richieste e risposte per vulnerabilità, costruisce l'albero del sito e registra avvisi per potenziali vulnerabilità.
Puoi avviare facilmente browser preconfigurati per passare attraverso ZAP tramite la scheda Quick Start, ignorando eventuali avvisi di validazione del certificato.

Per esplorare manualmente la tua applicazione:
-   Avvia ZAP e clicca sulla scheda Quick Start nella finestra di lavoro.
-   Clicca sul pulsante Manual Explore.
-   Nella casella di testo URL da esplorare, inserisci l'URL completo dell'applicazione web che vuoi esplorare.
-   Seleziona il browser che desideri utilizzare.
-   Clicca su Launch Browser.

#### Vedere le Pagine Esplorate
Per esaminare una vista ad albero delle pagine esplorate, clicca sulla scheda Siti nella finestra ad albero. Puoi espandere i nodi per vedere gli URL individuali accessibili.

#### Interpretare i Risultati dei Test
Mentre ZAP esplora la tua applicazione web, costruisce una mappa delle pagine e delle risorse utilizzate. Registra le richieste e risposte inviate a ciascuna pagina e crea avvisi se c'è qualcosa di potenzialmente sbagliato.

La parte sinistra del Footer contiene un conteggio degli avvisi trovati durante il test, suddivisi per categorie di rischio.
Per visualizzare gli avvisi creati durante il test:
-   Clicca sulla scheda **Alerts** nella finestra Informazioni.
-   Clicca su ciascun avviso visualizzato in quella finestra per mostrare l'**URL** e la **vulnerabilità** rilevata nella parte destra della finestra Informazioni.
-   Nella finestra di lavoro, clicca sulla scheda Risposta per vedere il **contenuto** dell'header e del corpo della **risposta**. La parte della risposta che ha generato l'avviso sarà **evidenziata**.
