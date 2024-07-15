# BBOT: The OSINT Automation Framework

BBOT (Black Lantern Security Bot) è uno strumento di automazione per l'OSINT (Open Source Intelligence) che permette di eseguire una varietà di task di raccolta informazioni in modo automatizzato. Questo strumento è sviluppato da Black Lantern Security e può essere trovato su GitHub all'indirizzo [BBOT GitHub Repository](https://github.com/blacklanternsecurity/bbot).

## Caratteristiche Principali
- Automazione della raccolta informazioni da fonti aperte
- Configurabile e personalizzabile
- Supporto per numerosi plugin per estendere le funzionalità
- Output strutturato e leggibile

## Installazione

Per installare BBOT, è necessario avere Python 3.7 o superiore. Segui questi passaggi per installare BBOT:

1. Clona il repository:
    ```sh
    git clone https://github.com/blacklanternsecurity/bbot.git
    cd bbot
    ```

2. Crea un ambiente virtuale (opzionale ma raccomandato):
    ```sh
    python3 -m venv venv
    source venv/bin/activate
    ```

3. Installa le dipendenze:
    ```sh
    pip install -r requirements.txt
    ```

4. Esegui BBOT:
    ```sh
    python bbot.py --help
    ```

## Utilizzo

Una volta installato, BBOT può essere utilizzato tramite la riga di comando. Ecco alcuni comandi base per iniziare:

### Scansione di Base
Per eseguire una scansione di base su un dominio:
```sh
python bbot.py -t example.com
```

### Configurazione Avanzata
BBOT permette una configurazione avanzata tramite l'utilizzo di un file di configurazione YAML. Ecco un esempio di configurazione:
```yaml
targets:
  - example.com

modules:
  - dns
  - subdomains
  - whois

output:
  - type: json
    file: results.json
```

Per eseguire BBOT con un file di configurazione:
```sh
python bbot.py -c config.yaml
```

### Output
BBOT supporta vari formati di output, inclusi JSON, CSV e Markdown. Puoi specificare il formato di output tramite il file di configurazione o tramite parametri della riga di comando.

## Plugin
BBOT supporta numerosi plugin per estendere le sue funzionalità. Puoi trovare una lista completa dei plugin disponibili nella documentazione ufficiale o nel repository GitHub.

Per installare un plugin, utilizza pip:
```sh
pip install bbot-plugin-name
```

Assicurati di aggiungere il plugin alla tua configurazione YAML:
```yaml
modules:
  - dns
  - subdomains
  - whois
  - plugin-name
```

## Documentazione

Per maggiori dettagli sull'utilizzo di BBOT, visita la [documentazione ufficiale](https://github.com/blacklanternsecurity/bbot/wiki).
