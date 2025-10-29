# Configurazione Ambiente Docker per Fork n8n

Questa guida descrive le variabili d'ambiente e i secrets necessari per il workflow Docker del fork n8n.

## 🔐 Secrets Automatici

Il workflow utilizza principalmente secrets automatici di GitHub:

### `GITHUB_TOKEN`
- **Descrizione**: Token automatico fornito da GitHub Actions
- **Utilizzo**: Autenticazione con GitHub Container Registry (ghcr.io)
- **Permessi**: Read/Write su packages, contents
- **Configurazione**: Automatica, nessuna azione richiesta

## 🌍 Variabili d'Ambiente del Workflow

### Variabili Predefinite

```yaml
env:
  NODE_OPTIONS: '--max-old-space-size=7168'  # Aumenta memoria per build
  REGISTRY: ghcr.io                           # GitHub Container Registry
  IMAGE_NAME: ${{ github.repository }}       # Nome immagine automatico
```

### Variabili di Build Args

Queste vengono passate al Dockerfile durante il build:

```yaml
build-args: |
  NODE_VERSION=22                              # Versione Node.js
  N8N_VERSION=${{ version_tag }}               # Versione n8n (dinamica)
  N8N_RELEASE_TYPE=fork                        # Tipo release per fork
  GITHUB_REPOSITORY=${{ github.repository }}   # Repository corrente
```

## ⚙️ Configurazione Repository

### 1. Permessi Workflow

**Percorso**: Settings → Actions → General → Workflow permissions

```
✅ Read and write permissions
✅ Allow GitHub Actions to create and approve pull requests
```

### 2. Abilitare Packages

**Percorso**: Settings → General → Features

```
✅ Packages
```

### 3. Visibilità Package

**Percorso**: Packages → {package-name} → Package settings

```
Visibility: Public (consigliato per fork open source)
oppure
Visibility: Private (per fork privati)
```

## 🔧 Variabili d'Ambiente Runtime

Queste variabili possono essere configurate quando si esegue il container:

### Autenticazione

```bash
# Autenticazione basic
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=secure_password

# JWT per autenticazione avanzata
N8N_JWT_AUTH_ACTIVE=true
N8N_JWT_AUTH_HEADER=authorization
N8N_JWT_AUTH_HEADER_VALUE_PREFIX="Bearer "
```

### Database

```bash
# SQLite (default)
DB_TYPE=sqlite
DB_SQLITE_DATABASE=/home/node/.n8n/database.sqlite

# PostgreSQL
DB_TYPE=postgresdb
DB_POSTGRESDB_HOST=localhost
DB_POSTGRESDB_PORT=5432
DB_POSTGRESDB_DATABASE=n8n
DB_POSTGRESDB_USER=n8n
DB_POSTGRESDB_PASSWORD=password

# MySQL
DB_TYPE=mysqldb
DB_MYSQLDB_HOST=localhost
DB_MYSQLDB_PORT=3306
DB_MYSQLDB_DATABASE=n8n
DB_MYSQLDB_USER=n8n
DB_MYSQLDB_PASSWORD=password
```

### Configurazione Server

```bash
# Host e porta
N8N_HOST=0.0.0.0
N8N_PORT=5678
N8N_PROTOCOL=http

# URL pubblico
WEBHOOK_URL=https://your-domain.com

# Timezone
GENERIC_TIMEZONE=Europe/Rome
```

### Sicurezza

```bash
# Encryption key per credenziali
N8N_ENCRYPTION_KEY=your-32-character-encryption-key

# CORS
N8N_CORS_ORIGIN=https://your-frontend-domain.com

# Disable node types (sicurezza)
NODES_EXCLUDE=["n8n-nodes-base.executeCommand"]
```

### Logging

```bash
# Livello log
N8N_LOG_LEVEL=info  # debug, info, warn, error

# Output log
N8N_LOG_OUTPUT=console  # console, file

# File log (se N8N_LOG_OUTPUT=file)
N8N_LOG_FILE=/home/node/.n8n/logs/n8n.log
```

## 🐳 Esempi Docker Compose

### Configurazione Base

```yaml
version: '3.8'

services:
  n8n:
    image: ghcr.io/{your-username}/{your-repo}:latest
    container_name: n8n-fork
    ports:
      - "5678:5678"
    environment:
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=admin
      - N8N_BASIC_AUTH_PASSWORD=${N8N_PASSWORD}
      - N8N_HOST=0.0.0.0
      - N8N_PORT=5678
      - N8N_PROTOCOL=http
      - GENERIC_TIMEZONE=Europe/Rome
    volumes:
      - n8n_data:/home/node/.n8n
    restart: unless-stopped

volumes:
  n8n_data:
```

### Configurazione con PostgreSQL

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15
    container_name: n8n-postgres
    environment:
      - POSTGRES_DB=n8n
      - POSTGRES_USER=n8n
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
    volumes:
      - postgres_data:/var/lib/postgresql/data
    restart: unless-stopped

  n8n:
    image: ghcr.io/{your-username}/{your-repo}:latest
    container_name: n8n-fork
    ports:
      - "5678:5678"
    environment:
      - DB_TYPE=postgresdb
      - DB_POSTGRESDB_HOST=postgres
      - DB_POSTGRESDB_PORT=5432
      - DB_POSTGRESDB_DATABASE=n8n
      - DB_POSTGRESDB_USER=n8n
      - DB_POSTGRESDB_PASSWORD=${POSTGRES_PASSWORD}
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=admin
      - N8N_BASIC_AUTH_PASSWORD=${N8N_PASSWORD}
      - N8N_ENCRYPTION_KEY=${N8N_ENCRYPTION_KEY}
    volumes:
      - n8n_data:/home/node/.n8n
    depends_on:
      - postgres
    restart: unless-stopped

volumes:
  postgres_data:
  n8n_data:
```

### File .env di Esempio

```bash
# .env file
N8N_PASSWORD=your_secure_password_here
POSTGRES_PASSWORD=your_postgres_password_here
N8N_ENCRYPTION_KEY=your-32-character-encryption-key-here
```

## 🔍 Debug e Troubleshooting

### Variabili di Debug

```bash
# Debug completo
N8N_LOG_LEVEL=debug
DEBUG=n8n*

# Debug specifico
DEBUG=n8n:webhook
DEBUG=n8n:database
DEBUG=n8n:credentials
```

### Controllo Configurazione

```bash
# Visualizza configurazione attiva
docker exec n8n-fork n8n config

# Test connessione database
docker exec n8n-fork n8n db:migrate

# Lista nodi disponibili
docker exec n8n-fork n8n list:workflow
```

## 🛡️ Sicurezza Best Practices

1. **Usa sempre HTTPS in produzione**
2. **Genera chiavi di encryption uniche**
3. **Limita CORS alle origini necessarie**
4. **Disabilita nodi pericolosi se non necessari**
5. **Usa database esterni per produzione**
6. **Configura backup regolari**
7. **Monitora i log per attività sospette**

## 📚 Riferimenti

- [Documentazione n8n Environment Variables](https://docs.n8n.io/hosting/environment-variables/)
- [GitHub Packages Documentation](https://docs.github.com/en/packages)
- [Docker Compose Reference](https://docs.docker.com/compose/)
- [GitHub Actions Secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets)