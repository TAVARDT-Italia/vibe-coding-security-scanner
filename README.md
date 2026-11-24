# Auditor di Sicurezza Vibe Coding e Architettura IA

[![Live Demo](https://img.shields.io/badge/Live%20Demo-GitHub%20Pages-5b3cf5.svg)](https://tavardt-italia.github.io/vibe-coding-security-scanner/)
[![Python](https://img.shields.io/badge/Python-3.8%2B-blue.svg)](https://www.python.org/)
[![Security](https://img.shields.io/badge/Security-AI%20Architecture-blue.svg)](https://tavardt-italia.github.io/vibe-coding-security-scanner/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

Guida aziendale di architettura difensiva, mitigazione delle perdite di credenziali e strumento CLI deterministico per l audit di sicurezza delle applicazioni sviluppate con strumenti di IA (Vibe Coding).

## Superficie di Rischio nelle Applicazioni Generate da IA

Lo sviluppo assistito da IA (Cursor, Lovable, v0, Bolt) introduce tre vulnerabilità architetturali ricorrenti:

1. **Perdita di Sourcemap (.js.map):** Le compilazioni di produzione che conservano i file di mappatura consentono la decompilazione integrale della logica di business e dei modelli di backend originali in TypeScript.
2. **Esposizione di Chiavi di IA nei Bundle Client:** Chiavi OpenAI, Anthropic o token `service_role` di Supabase con prefisso `NEXT_PUBLIC_` o `VITE_` incorporati direttamente nel codice statico scaricato dal browser.
3. **Assenza di Row Level Security (RLS):** Database relazionali interrogati direttamente tramite endpoint REST/GraphQL senza RLS attivo consentono l estrazione non autorizzata di dati multi-utente.

***

## Strumento Ufficiale di Audit CLI

Per aggirare i vincoli di sandbox dei browser e le policy CORS, TAVARDT mette a disposizione un utilità in Python per l ispezione diretta via terminale o pipeline CI/CD:

```bash
# 1. Scarica lo script ufficiale dal repository
curl -sO https://raw.githubusercontent.com/TAVARDT-Italia/vibe-coding-security-scanner/main/audit_vibe_security.py

# 2. Esegui l audit diretto contro l applicazione
python audit_vibe_security.py https://tua-applicazione.com
```

### Funzionalità Principali di Ispezione

* **Audit dei Sourcemap:** Scansione automatica delle directory di build standard per rilevare file `.js.map` accessibili pubblicamente.
* **Ispezione degli Header HTTP:** Validazione rigorosa delle policy `Content-Security-Policy`, `Strict-Transport-Security`, `X-Content-Type-Options` e `X-Frame-Options`.
* **Rilevamento di Segreti Compilati:** Analisi regex ad alta precisione per identificare token di provider di IA e credenziali amministrative annidate nei bundle JavaScript statici.

***

## Blueprint di Mitigazione Difensiva

Questo repository include configurazioni certificate per blindare l architettura applicativa:

* **Vite (`vite.config.ts`):** Disattivazione rigorosa dei sourcemap in produzione e bonifica dei nomi dei moduli.
* **Next.js (`next.config.js`):** Disattivazione di `productionBrowserSourceMaps` e rafforzamento degli header difensivi.
* **Supabase SQL (`supabase-rls.sql`):** Attivazione obbligatoria di `ROW LEVEL SECURITY` sugli schemi pubblici e revoca dei permessi indiscriminati del ruolo `anon`.
* **Cloudflare WAF:** Regole perimetrali per bloccare istantaneamente le richieste verso `.js.map`, `.env` e `.git`.

***

## Standard e Normative di Conformità

Architettura difensiva e controlli di cybersicurezza conformi a GDPR, Linee Guida ACN / AgID, CIS Benchmarks e standard Zero Trust.

***

## Collegamenti Ufficiali & Risorse

* **Pagina Ufficiale:** [https://tavardt-italia.github.io/vibe-coding-security-scanner/](https://tavardt-italia.github.io/vibe-coding-security-scanner/)
* **Portale TAVARDT:** [Portale Istituzionale](https://ag.tavardt.com/it/)
* **Strumenti:** [Centro Strumenti](https://tools.tavardt.com/it/)

***

© 2026 TAVARDT. Tutti i diritti riservati.
