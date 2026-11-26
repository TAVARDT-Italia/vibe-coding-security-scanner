<p align="center">
  <a href="https://ag.tavardt.com/it/">
    <img src="https://ag.tavardt.com/img/tavardt-banner.png" alt="TAVARDT" width="1024" />
  </a>
</p>

# Auditor di Sicurezza Vibe Coding e Architettura IA

[![Live Demo](https://img.shields.io/badge/Live%20Demo-GitHub%20Pages-5b3cf5.svg)](https://tavardt-italia.github.io/vibe-coding-security-scanner/)
[![Python](https://img.shields.io/badge/Python-3.8%2B-blue.svg)](https://www.python.org/)
[![Security](https://img.shields.io/badge/Security-AI%20Architecture-blue.svg)](https://tavardt-italia.github.io/vibe-coding-security-scanner/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

Guida aziendale di ingegneria difensiva, mitigazione della fuga di credenziali e utility CLI deterministica per l'audit di sicurezza di applicazioni realizzate mediante strumenti di intelligenza artificiale (Vibe Coding).

***

## Quadro Esecutivo: La Realtà di Sicurezza del Vibe Coding

La diffusione del paradigma di sviluppo assistito da intelligenza artificiale (tramite piattaforme come Cursor, Lovable, v0, Bolt, Windsurf, Replit, Claude Code e GitHub Copilot) consente a team di ingegneria e sviluppatori di rilasciare applicazioni web complete a ritmi straordinari. Tuttavia, i generatori automatici di codice privilegiano la funzionalità visiva immediata rispetto all'isolamento perimetrale, al principio del minimo privilegio e alla bonifica degli artefatti destinati alla produzione.

Quando i programmatori richiedono a modelli di intelligenza artificiale la pubblicazione rapida di funzionalità, i sistemi risultanti incorporano vulnerabilità strutturali frequenti:
* Configurazioni di sviluppo e collaudo migrano nei pacchetti finali di produzione senza controlli di compilazione.
* Chiavi amministrative di database e token di servizi IA risultano visibili all'interno di file statici scaricati dal browser.
* Tabelle di database relazionali vengono instanziate prive di regole dichiarative di autorizzazione (Row Level Security).
* Intestazioni HTTP di protezione perimetrale vengono omesse dai server web.

Questo repository definisce uno standard di architettura difensiva per contesti aziendali, offrendo modelli di configurazione pronti all'uso per i principali framework moderni e una utility deterministica in Python (`audit_vibe_security.py`) per identificare e mitigare tali criticità prima della distribuzione.

***

## Superficie Dettagliata di Rischio nelle Applicazioni Generate da IA

### 1. Esposizione di Sourcemap in Produzione (.js.map)
Gli assistenti di intelligenza artificiale impostano regolarmente i sistemi di compilazione frontend (Vite, Webpack, Turbopack, Rollup) con parametri predefiniti che pubblicano i file di mappatura del codice accanto ai file minificati. Se distribuiti su reti di distribuzione dei contenuti (CDN) pubbliche, i file `.js.map` consentono l'inversione completa del codice:
* Ricostruzione fedele del codice sorgente originario in TypeScript e JavaScript.
* Visibilità dettagliata sui modelli interni dei dati, schemi relazionali e contratti delle API.
* Individuazione di endpoint amministrativi non documentati, percorsi riservati e note inserite durante lo sviluppo.

### 2. Chiavi di Provider IA e Token BaaS Compilati nel Client
In assenza di contesto circa la separazione fisica tra l'ambiente server e il browser client, i generatori di IA inseriscono spesso parametri riservati nel codice frontend. Quando vengono utilizzate variabili con prefissi come `NEXT_PUBLIC_` o `VITE_`, i relativi valori letterali vengono incorporati direttamente nei file statici:
* Chiavi di API di OpenAI, Anthropic e Google Gemini soggette a utilizzi non autorizzati.
* Token `service_role` e JWT amministrativi di Supabase che annullano ogni controllo sulle tabelle.
* Credenziali di accesso ad ambienti cloud AWS, chiavi di Google Cloud e webhook di pagamento.

### 3. Assenza di Row Level Security (RLS) nei Database Relazionali
L'approccio del Vibe Coding incentiva l'interazione diretta tra browser e database tramite SDK dedicati. Tuttavia, le IA instanziano sistematicamente tabelle PostgreSQL senza impartire il comando `ALTER TABLE ... ENABLE ROW LEVEL SECURITY;`. In assenza di politiche RLS:
* Qualsiasi visitatore che analizzi le comunicazioni di rete può formulare interrogazioni REST o GraphQL non autorizzate.
* Archivi integrali comprendenti dati identificativi di utenti, informazioni soggette a GDPR e movimentazioni finanziarie risultano accessibili a chiamate anonime.

### 4. Omissione di Intestazioni HTTP di Sicurezza
Le configurazioni applicative create da strumenti di IA omettono frequentemente le intestazioni difensive essenziali:
* `Content-Security-Policy` (CSP): Impedisce l'iniezione e l'esecuzione non autorizzata di script esterni.
* `Strict-Transport-Security` (HSTS): Impone l'impiego esclusivo di canali cifrati HTTPS.
* `X-Frame-Options` e `X-Content-Type-Options`: Prevengono attacchi di clickjacking e l'errata interpretazione dei tipi MIME.

### 5. Percorsi Esposti di File di Configurazione (.env)
Cartelle principali con permessi non corretti consentono l'accesso pubblico diretto a file come `/.env`, `/.env.local` e `/.env.production`, facilitando l'esfiltrazione rapida di credenziali aziendali mediante una singola richiesta HTTP.

***

## Utility Deterministica di Audit CLI

Le applicazioni web in esecuzione nei browser operano all'interno di ambienti protetti (sandbox) e sottostanno a restrizioni CORS che impediscono ai linguaggi lato client di analizzare liberamente le intestazioni o verificare domini terzi. Per garantire verifiche tecniche riproducibili e prive di telemetria remota, TAVARDT fornisce una utility in Python concepita per l'esecuzione da riga di comando e all'interno di pipeline di integrazione continua (CI/CD).

### Fasi dell'Ispezione Deterministica

L'utility (`audit_vibe_security.py`) articola l'analisi attraverso cinque fasi progressive:
1. **Verifica Perimetrale Radice:** Stabilisce la connessione TLS, controlla i codici di stato HTTP e valida le intestazioni di sicurezza.
2. **Individuazione Script nel DOM:** Esamina il codice HTML per mappare dinamicamente tutti gli script importati nella pagina.
3. **Controllo dei Sourcemap:** Verifica la reperibilità pubblica dei file `.js.map` corrispondenti a ciascun bundle rilevato.
4. **Ispezione di Token nei File Compilati:** Applica espressioni regolari per individuare la presenza di chiavi attive di provider IA e credenziali cloud.
5. **Scansione Percorsi di Ambiente:** Controlla che i file di configurazione (`/.env`, `/.env.local`, `/.env.production`) siano inaccessibili.

### Istruzioni di Esecuzione

Scaricare ed eseguire l'utility direttamente da terminale contro l'indirizzo di destinazione:

```bash
# 1. Scaricare lo script ufficiale dal repository
curl -sO https://raw.githubusercontent.com/TAVARDT-Italia/vibe-coding-security-scanner/main/audit_vibe_security.py

# 2. Eseguire l audit deterministico
python audit_vibe_security.py https://vostra-applicazione.com
```

***

## Modelli di Mitigazione per Ambienti di Produzione

Il repository mette a disposizione configurazioni verificate per rafforzare l'architettura applicativa:

### Configurazione Vite (`vite.config.ts`)
Disabilita la creazione di sourcemap nei rilasci definitivi:

```typescript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  build: {
    sourcemap: false,
    chunkSizeWarningLimit: 1000,
    rollupOptions: {
      output: {
        manualChunks: undefined
      }
    }
  }
});
```

### Configurazione Next.js (`next.config.js`)
Inibisce la mappatura del codice lato client e applica intestazioni di sicurezza:

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  productionBrowserSourceMaps: false,
  async headers() {
    return [
      {
        source: '/(.*)',
        headers: [
          { key: 'X-Frame-Options', value: 'DENY' },
          { key: 'X-Content-Type-Options', value: 'nosniff' },
          { key: 'Referrer-Policy', value: 'strict-origin-when-cross-origin' },
          { key: 'Strict-Transport-Security', value: 'max-age=63072000; includeSubDomains; preload' }
        ]
      }
    ];
  }
};

module.exports = nextConfig;
```

### Rafforzamento di Row Level Security su Supabase (`supabase-rls.sql`)
Garantisce la separazione dei dati e revoca i permessi anonimi di estrazione:

```sql
/* 1. Abilitazione obbligatoria di Row Level Security nelle tabelle pubbliche */
ALTER TABLE public.profiles ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.organizations ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.transactions ENABLE ROW LEVEL SECURITY;

/* 2. Revoca dei privilegi predefiniti di esecuzione del ruolo anon */
REVOKE ALL ON ALL TABLES IN SCHEMA public FROM anon;

/* 3. Definizione di criterio restrittivo di lettura associato all identita utente */
CREATE POLICY "Gli utenti visualizzano unicamente il proprio profilo" 
ON public.profiles FOR SELECT 
TO authenticated 
USING (auth.uid() = id);
```

### Regola di Filtraggio Perimetrale su Cloudflare WAF
Regola di blocco immediato per richieste dirette a risorse riservate:

```text
(http.request.uri.path contains ".js.map") or 
(http.request.uri.path contains ".env") or 
(http.request.uri.path contains ".git")
Azione: Block (Interruzione immediata della connessione)
```

***

## Domande Frequenti di Architettura (FAQ Tecnico)

### Quali sono le cause principali di vulnerabilità nel Vibe Coding?
I modelli di intelligenza artificiale sintetizzano codice mediante corrispondenze statistiche senza eseguire un'analisi formale del rischio informatico. Di conseguenza, collocano credenziali riservate in componenti client, tralasciano l'attivazione di Row Level Security sui database e mantengono parametri di collaudo attivi in ambienti di produzione.

### Per quale motivo la pubblicazione di file sourcemap (.js.map) è pericolosa?
I sourcemap contengono la correlazione precisa tra il codice minificato e i file sorgenti originari. La loro presenza in ambienti pubblici consente a soggetti ostili di esaminare la logica interna, individuare percorsi di amministrazione e analizzare i contratti dei dati con facilità.

### In che modo le applicazioni frontend devono consumare API di intelligenza artificiale?
I componenti eseguiti nei browser non devono mai invocare direttamente i provider di IA utilizzando credenziali amministrative. Ogni operazione deve essere mediata da endpoint di backend sicuri o funzioni serverless perimetrali in grado di autenticare l'utente, applicare limiti di frequenza e custodire le chiavi in modo riservato sul server.

### L'utility deterministica in Python causa rallentamenti nel servizio ispezionato?
No. Il programma conduce unicamente diagnosi di lettura passive e non invasive. Esegue richieste HTTP standard di tipo GET e HEAD per verificare la presenza di intestazioni e risorse pubbliche, senza modificare informazioni né compromettere la disponibilità dell'infrastruttura.

***

## Normative di Riferimento e Standard Aziendali

Architettura difensiva e misure tecniche conformi al Regolamento Generale sulla Protezione dei Dati (GDPR, Regolamento UE 2016/679), ai provvedimenti del Garante per la Protezione dei Dati Personali e alle linee guida dell'Agenzia per la Cybersicurezza Nazionale (ACN).

***

## Repository & Documentazione Ufficiale

* **Pagina Ufficiale:** [https://tavardt-italia.github.io/vibe-coding-security-scanner/](https://tavardt-italia.github.io/vibe-coding-security-scanner/)
* **Portale TAVARDT:** [Portale Istituzionale](https://ag.tavardt.com/it/)
* **Strumenti:** [Centro Strumenti](https://tools.tavardt.com/it/)

***

## Link Utili

* **Portale Aziendale:** https://ag.tavardt.com/it/
* **Suite di Diagnostica:** https://tools.tavardt.com/it/
* **Contatto Tecnico:** https://ag.tavardt.com/it/contatto/

***

© 2026 TAVARDT. Tutti i diritti riservati.
