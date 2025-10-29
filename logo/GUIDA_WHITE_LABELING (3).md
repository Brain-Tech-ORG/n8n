# Guida al White Labeling di n8n

Questa guida descrive come personalizzare l'interfaccia di n8n per applicare il proprio branding (white labeling).

## Modifiche Principali Effettuate

### 1. Configurazione del Brand Name

**File:** `packages/frontend/editor-ui/src/plugins/i18n/locales/en.json`

```json
{
  "generic": {
    "n8n": "La Traccia"
  }
}
```

**Descrizione:** Cambia il nome del brand da "n8n" a "La Traccia" in tutta l'interfaccia utente.

### 2. Personalizzazione del Colore Primario

**File:** `packages/frontend/design-system/src/css/tokens.css`

Cercare la sezione `:root` e modificare:

```css
:root {
  --color-primary: #1F70AF;
  --color-primary-shade-1: #1a5f96;
  --color-primary-shade-2: #154e7d;
  --color-primary-tint-1: #3280bf;
  --color-primary-tint-2: #4590cf;
}
```

**Descrizione:** Cambia il colore primario dell'applicazione dal rosa originale (#EA4B71) al blu aziendale (#1F70AF).

### 3. Modifica del Titolo della Finestra

**File:** `packages/frontend/editor-ui/index.html`

```html
<title>La Traccia - Workflow Automation</title>
```

**Descrizione:** Cambia il titolo che appare nella barra del browser.

### 4. Personalizzazione dei Loghi

**Directory:** `packages/frontend/editor-ui/src/components/Logo/`

**File da modificare:**
- `logo-icon.svg` - Icona del logo
- `logo-text.svg` - Testo del logo

**Posizioni dove appare il logo:**
- Header principale dell'applicazione
- Pagine di autenticazione (login/registrazione)
- Sidebar principale
- Favicon (generato dinamicamente)

### 5. Altri Asset Grafici

**Directory:** `packages/frontend/editor-ui/public/`

**File da considerare:**
- `favicon.ico` - Favicon del sito
- `static/n8n-logo.png` - Logo PNG per vari utilizzi
- `static/og_image.png` - Immagine per social media sharing

## Processo di Applicazione delle Modifiche



### 2. Modifica dei File
Applicare le modifiche ai file sopra elencati seguendo le specifiche del proprio brand.




## Note Importanti

- Le modifiche ai file CSS e di localizzazione richiedono un riavvio dell'applicazione di sviluppo
- Per la produzione, è necessario eseguire il build completo
- I loghi SVG possono essere modificati direttamente o sostituiti con nuovi file
- Il colore primario viene applicato automaticamente a tutti gli elementi dell'interfaccia
- Le modifiche alla localizzazione influenzano tutti i testi che fanno riferimento al brand name

## Struttura dei File Principali

```
packages/frontend/
├── design-system/src/css/tokens.css          # Colori e variabili CSS
├── editor-ui/
│   ├── index.html                            # Titolo finestra
│   ├── public/                               # Asset statici
│   │   ├── favicon.ico
│   │   └── static/
│   │       ├── n8n-logo.png
│   │       └── og_image.png
│   └── src/
│       ├── components/Logo/                  # Componenti logo
│       │   ├── Logo.vue
│       │   ├── logo-icon.svg
│       │   └── logo-text.svg
│       └── plugins/i18n/locales/en.json     # Localizzazione
```

Questa guida fornisce una base completa per personalizzare l'aspetto di n8n secondo le proprie esigenze di branding.