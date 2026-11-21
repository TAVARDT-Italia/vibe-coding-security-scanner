# Vibe Coding Security Scanner & Auditor per Applicazioni Generate con IA | TAVARDT Italia

[![Licence MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Vibe Coding Security](https://img.shields.io/badge/Vibe%20Coding-Security%20Hardened-00d26a.svg)](README.md)
[![Language](https://img.shields.io/badge/Language-Italiano-green.svg)](README.md)
[![Engineering](https://img.shields.io/badge/TAVARDT-Cybersecurity-5b3cf5.svg)](https://ag.tavardt.com/it/sicurezza-informatica/)

Scanner open source per verificare la sicurezza di applicazioni sviluppate con strumenti vibe coding (Cursor, Lovable, Bolt, v0). Rileva chiavi API esposte nel frontend, vulnerabilità RLS e sourcemap.

Sviluppato e gestito dal team di ingegneria della sicurezza informatica di **[TAVARDT Italia](https://ag.tavardt.com/it/sicurezza-informatica/)**.

---

## 1. Panoramica dello Scanner di Sicurezza Vibe Coding

Lo sviluppo software assistito da intelligenza artificiale (*vibe coding*) consente di pubblicare rapidamente applicazioni web complete tramite **Cursor, Lovable.dev, Bolt.new, v0 e Replit**.

Tuttavia, l'assenza di controlli di sicurezza espone frequentemente chiavi di produzione, database privi di autorizzazione e codice sorgente direttamente nel browser del client.

Questo repository fornisce un tool in Python per verificare **il perimetro pubblico di qualsiasi applicazione web in linea**, evidenziando rischi concreti prima che vengano sfruttati da attaccanti.

---

## 2. Vulnerabilità e Vettori di Rischio Verificati

| Vettore di Rischio | Causa nel Vibe Coding | Impatto Tecnico |
| :--- | :--- | :--- |
| **Chiavi API nel Frontend** | Inserimento di chiavi OpenAI (`sk-...`), Stripe o AWS nel file `.env` del client o in componenti React. | Qualsiasi utente può estrarre la chiave tramite DevTools ed esaurire il credito contrattualizzato. |
| **Supabase RLS Disattivato** | Creazione di tabelle su Supabase senza abilitare il *Row Level Security* (RLS). | Con la `anon_key` pubblica chiunque può scaricare o cancellare l'intero database via REST. |
| **Sourcemap in Produzione** | Pubblicazione di build web con file `.js.map` attivi. | L'intero codice sorgente originale è disponibile per il download e il reverse engineering. |
| **File Riservati Esposti** | Configurazioni errate che lasciano percorsi come `/.env` o `/.git/HEAD` aperti via HTTP. | Fuga totale di credenziali di infrastruttura e variabili di ambiente. |
| **Assenza di Header Sicuri** | Mancanza di policy CSP, HSTS e X-Frame-Options. | Vulnerabilità a Clickjacking e iniezioni di script malevoli (XSS). |

---

## 3. Guida all'Installazione e all'Esecuzione

### Installazione Rapida

```bash
git clone https://github.com/TAVARDT-Italia/vibe-coding-security-scanner.git
cd vibe-coding-security-scanner
```

### Eseguire la Scansione su un URL

```bash
python scanner.py https://la-tua-applicazione.vercel.app
```

### Parametri da Riga di Comando

```bash
# Esportare il report strutturato in formato JSON
python scanner.py https://la-tua-applicazione.vercel.app --json

# Escludere la verifica dei sourcemap
python scanner.py https://la-tua-applicazione.vercel.app --no-sourcemaps
```

---

## 4. Protezione Professionale e Supporto Tecnico

Se la diagnosi ha evidenziato credenziali esposte, tabelle Supabase/Firebase prive di RLS o necessita di blindatura perimetrale:

* **Portale Aziendale:** [TAVARDT Italia](https://ag.tavardt.com/it/sicurezza-informatica/)
* **Canale di Contatto Tecnico:** [Contatta il Team di Ingegneria](https://ag.tavardt.com/it/contatti/)

---

## Licenza

Distribuito sotto licenza open-source MIT. Consultare il file [`LICENSE`](LICENSE) per maggiori informazioni.  
Copyright (c) 2026 TAVARDT Italia.
