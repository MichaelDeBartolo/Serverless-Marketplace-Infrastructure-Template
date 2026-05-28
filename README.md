# 🚀 Serverless Marketplace Infrastructure Template

Questo repository contiene l'architettura infrastrutturale backend e serverless per un marketplace generico ad alte prestazioni, scalabile ed "Edge-first". Il progetto è nativamente focalizzato sulla sicurezza granulare del dato e sulla resilienza dei flussi logitici automatizzati di terze parti.

---

## 🛠️ Stack Tecnologico & Architettura
L'applicazione è ingegnerizzata per azzerare i costi di gestione infrastrutturale fissa e massimizzare la velocità di risposta globale:

* **Frontend:** Next.js / React (Ospitato su **Vercel Edge Network**).
* **Database & Cloud BaaS:** **Supabase** (PostgreSQL relazionale con estensioni attive).
* **Serverless Compute:** **Supabase Edge Functions** (TypeScript strutturato su runtime **Deno** ad altissima velocità e bassa latenza).
* **Integrazioni API:** Stripe API (Core dei pagamenti) & REST Shipping API Wrapper (Automazione logistica).

---

## 🔄 Flusso dei Dati ed Event-Driven Design

L'architettura sfrutta i Webhook e le funzioni serverless per garantire transazioni atomiche e asincrone:

[Client / Vercel] ──(Crea sessione)──> [Edge Function: create-checkout] ──> [Stripe Hosted UI]
│
(Invia Webhook)
▼
[Paccofacile API] <──(Genera Etichetta)── [Edge Function: stripe-webhook] <────────┘
│
(Se errore 400/404)
▼
[Edge Function: retry-shipping] <──(Forzatura Admin con x-retry-secret)

1. **Checkout:** L'utente avvia l'acquisto; la funzione `create-checkout` genera una sessione Stripe protetta.
2. **Saldatura Ordine:** Al pagamento completato, Stripe notifica la Edge Function `stripe-webhook`.
3. **Trigger Logistica:** Il webhook convalida la transazione sul database PostgreSQL e contatta istantaneamente le API del corriere per generare la lettera di vettura.
4. **Resilienza (Fallback):** Se l'API del corriere fallisce, l'ordine viene tracciato con stato di errore logistico e agganciato all'endpoint isolato `retry-shipping` per il recupero asincrono guidato dall'amministratore.

---

## 🔒 Funzionalità di Cybersecurity & Hardening

* **Row Level Security (RLS) & Isolamento Clienti:** Configurazione di policy PostgreSQL granulari e rigide. Gli utenti (`buyers`/`sellers`) possono accedere e modificare esclusivamente le righe di loro pertinenza (basate su `auth.uid()`), impedendo attacchi di tipo *IDOR (Insecure Direct Object Reference)*.
* **Validazione della Firma dei Webhook:** La funzione `stripe-webhook` valida l'autenticità di ogni richiesta tramite la verifica crittografica dell'header `stripe-signature` utilizzando la chiave segreta di firma di Stripe (`STRIPE_WEBHOOK_SECRET`).
* **Asymmetric JWT Verification:** Protezione nativa degli endpoint tramite il gateway Kong di Supabase con verifica dei JSON Web Tokens asimmetrici per l'autenticazione delle sessioni.
* **Secret Statici di Emergenza (`x-retry-secret`):** L'endpoint di manutenzione bypassa il gateway JWT standard (evitando blocchi dovuti a token di sessione scaduti durante i processi batch) ma blinda l'accesso tramite un header custom statico ad alta entropia memorizzato nei Vault di Supabase.

---

## 📂 Struttura del Repository

```text
├── .supabase/                 # Configurazioni locali del framework Supabase
├── supabase/
│   ├── functions/             # Core Backend (Supabase Edge Functions / Deno)
│   │   ├── create-checkout/   # Inizializzazione transazioni Stripe
│   │   ├── stripe-webhook/    # Gestione eventi di pagamento e trigger logistica
│   │   ├── get-shipping-label/# Download e storage dei PDF delle etichette
│   │   └── retry-shipping/    # Gateway di ripristino ed emulazione flussi API
│   ├── migrations/            # Script SQL di migrazione del Database
│   └── config.toml            # Configurazione sistemistica delle funzioni
├── src/                       # Frontend generico (UI / Components / Contexts)
└── .env.example               # Template per le variabili d'ambiente

⚙️ Setup delle Variabili d'Ambiente (Configurazione Sicura)
Il progetto non contiene chiavi hardcoded. Per replicare l'infrastruttura, configurare i segreti nel pannello Supabase (supabase secrets set) e nel file locale .env seguendo lo schema delle macro-aree:

Snippet di codice
# --- STRIPE CONFIGURATION ---
STRIPE_SECRET_KEY=sk_live_...
VITE_STRIPE_PUBLISHABLE_KEY=pk_live_...
STRIPE_WEBHOOK_SECRET=whsec_...

# --- LOGISTICS API (PACCOFACILE / GENERIC) ---
SHIPPING_API_KEY=your_api_key_here
SHIPPING_API_TOKEN=your_token_here

# --- SYSTEM & EMERGENCY RECOVERY ---
RETRY_SHIPPING_SECRET=un_segreto_molto_lungo_e_complesso_di_sicurezza
📄 Licenza
Questo progetto è distribuito sotto licenza MIT. Consulta il file LICENSE per ulteriori dettagli.


---

---

## 🎯 Considerazioni Finali & AI-Driven Engineering

Questo repository rappresenta una solida dimostrazione di **ingegneria del software moderna ed "Edge-First"**. L'intero ciclo di progettazione, refactoring e hardening della sicurezza è stato condotto applicando metodologie avanzate di **AI-Driven Development** (attraverso l'uso strategico di prompt ingegneristici e l'orchestrazione dell'ambiente di sviluppo tramite modelli di IA).

### 💡 Nota sulla Proprietà Intellettuale & Logica di Business
* **Astrazione del Codice:** Per motivi di riservatezza commerciale e protezione del posizionamento di mercato, questo repository **non contiene** le logiche di business proprietarie, i moduli specifici di messaggistica asimmetrica per le trattative private, i database di categorizzazione degli asset reali o l'interfaccia utente finale del brand.
* **Scopo del Progetto:** Il codice qui presente ha scopi puramente dimostrativi ed espositivi per illustrare le mie competenze verticali in ambito **Full-Stack Development (Supabase/Vercel)**, **Sistemistica Cloud/DevOps (Serverless Infrastructure)** e **Cybersecurity (Hardening di API e policy RLS PostgreSQL)**. 

Se desideri approfondire le metriche prestazionali, i flussi di business completi o discutere di una potenziale collaborazione, non esitare a contattarmi direttamente tramite i canali di riferimento sul mio profilo.
