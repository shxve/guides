
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

Andiamo a impostare la sessione persistente con un nome personalizzato, verranno creati diversi file quindi consiglio di creare una cartella per la sessione che si vuole salvare

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

Iniziamo assicurandoci che ZAP sia in modalita standard e non ATTACK o altre, dato che andrebbero a mettere troppo (o troppo poco) a rischio il target

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
 
Dopo aver finalmente finito di configurare la nostra sessione, possiamo iniziare a utilizzare la nostra applicazione mobile intercettando le richieste tramite ZAP.

> **NB**: cerca di navigare quanto piu possibile la applicazione da testare e di toccare tutti i flussi prima di iniziare ad analizzare le richieste con ZAP

Per prima cosa facciamo qualche richiesta tramite la nostra applicazione in modo da rivelare gli host `in scope` > Quando comprariranno nella schermata principale sotto la voce `Siti` > Fare `tasto destro sul dominio` > `Aggiungi al contesto`, mentre con il resto dei domini inutili > Fare `tasto destro sul domionio` > `Escludere da` > `Proxy`
Dopo aver aggiunto tutti gli host in scope e aver tolto quelli che non ci servono, non resta alto che navigare i flussi il piu possibile, per arrichire al meglio la site map di ZAP e ottenere i migliori risultati possibili

Una volta terminata la fase di navigazione (**veramente**) fare `tasto destro sul contesto` > `Scansione attiva` > Godersi lo spettacolo
