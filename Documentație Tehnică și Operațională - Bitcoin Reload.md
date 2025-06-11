# Documentație Tehnică și Operațională - Bitcoin Reload

## Cuprins
1. [Introducere](#introducere)
2. [Arhitectura Sistemului](#arhitectura-sistemului)
3. [Componente](#componente)
4. [Cerințe de Sistem](#cerințe-de-sistem)
5. [Instalare și Configurare](#instalare-și-configurare)
6. [Operare și Administrare](#operare-și-administrare)
7. [Monitorizare și Logging](#monitorizare-și-logging)
8. [Backup și Recuperare](#backup-și-recuperare)
9. [Securitate](#securitate)
10. [Troubleshooting](#troubleshooting)
11. [FAQ](#faq)

## Introducere

Bitcoin Reload este un sistem blockchain descentralizat, conceput ca o alternativă îmbunătățită la Bitcoin, oferind viteze de tranzacționare mai mari și comisioane mai mici, păstrând în același timp principiile de descentralizare și securitate.

Această documentație oferă informații complete despre instalarea, configurarea, operarea și mentenanța sistemului Bitcoin Reload în mediul de producție.

### Caracteristici Principale

- **Mecanism de consens hibrid PoS+PoH** - Oferă viteză și eficiență energetică superioară
- **Viteză de tranzacționare ridicată** - Peste 65 TPS (tranzacții pe secundă)
- **Comisioane reduse** - Aproximativ 0.1% din valoarea tranzacției
- **Arhitectură modulară** - Permite scalabilitate și extensibilitate
- **Securitate avansată** - Protecție împotriva atacurilor comune și management securizat al cheilor
- **Interfețe complete** - API RESTful și interfață web pentru utilizatori și administratori

## Arhitectura Sistemului

Bitcoin Reload utilizează o arhitectură modulară, bazată pe microservicii, pentru a asigura scalabilitatea și flexibilitatea sistemului. Componentele principale sunt:

### Diagrama Arhitecturală

```
+------------------+      +------------------+      +------------------+
|                  |      |                  |      |                  |
|  Web Frontend    |<---->|   API Service    |<---->|  Blockchain Core |
|  (React)         |      |   (Flask)        |      |  (Rust)          |
|                  |      |                  |      |                  |
+------------------+      +------------------+      +------------------+
                                   ^                        ^
                                   |                        |
                                   v                        v
                          +------------------+     +------------------+
                          |                  |     |                  |
                          |  Database        |     |  P2P Network     |
                          |  (MySQL)         |     |  (Noduri)        |
                          |                  |     |                  |
                          +------------------+     +------------------+
                                   ^
                                   |
                                   v
                          +------------------+
                          |                  |
                          |  Cache & Queue   |
                          |  (Redis)         |
                          |                  |
                          +------------------+
```

### Flux de Date

1. Utilizatorii interacționează cu sistemul prin interfața web (React)
2. Interfața web comunică cu API Service (Flask) prin cereri HTTP
3. API Service interacționează cu Blockchain Core prin RPC
4. Blockchain Core menține starea blockchain-ului și comunică cu alte noduri prin rețeaua P2P
5. Datele utilizatorilor și metadatele sunt stocate în baza de date MySQL
6. Redis este utilizat pentru caching, rate limiting și gestionarea sesiunilor

## Componente

### Blockchain Core

Implementat în Rust, Blockchain Core este nucleul sistemului, responsabil pentru:
- Menținerea blockchain-ului și a stării acestuia
- Implementarea mecanismului de consens hibrid PoS+PoH
- Validarea și procesarea tranzacțiilor
- Comunicarea P2P cu alte noduri
- Expunerea unei interfețe RPC pentru interacțiunea cu alte componente

### API Service

Implementat în Flask (Python), API Service oferă:
- Endpoint-uri RESTful pentru interacțiunea cu blockchain-ul
- Autentificare și autorizare utilizatori
- Gestionarea portofelelor și a cheilor private
- Procesarea tranzacțiilor
- Interogări și rapoarte

### Web Frontend

Implementat în React, Web Frontend oferă:
- Interfață utilizator intuitivă
- Dashboard pentru utilizatori și administratori
- Gestionarea portofelelor
- Vizualizarea tranzacțiilor și a soldurilor
- Funcționalități administrative

### Baza de Date

MySQL este utilizat pentru stocarea:
- Datelor utilizatorilor
- Metadatelor portofelelor
- Istoricului tranzacțiilor
- Configurațiilor și setărilor

### Redis

Redis este utilizat pentru:
- Caching pentru îmbunătățirea performanței
- Rate limiting pentru prevenirea atacurilor DoS
- Gestionarea sesiunilor și a token-urilor JWT
- Comunicare asincronă între componente

## Cerințe de Sistem

### Hardware Recomandat

#### Nod Validator
- CPU: 8+ cores, 3.0+ GHz
- RAM: 16+ GB
- Storage: 500+ GB SSD
- Rețea: 100+ Mbps, conexiune stabilă

#### Nod Non-Validator
- CPU: 4+ cores, 2.5+ GHz
- RAM: 8+ GB
- Storage: 250+ GB SSD
- Rețea: 50+ Mbps

#### Server API și Web
- CPU: 4+ cores, 2.5+ GHz
- RAM: 8+ GB
- Storage: 100+ GB SSD
- Rețea: 100+ Mbps

### Software Necesar

- Docker și Docker Compose (versiunea 20.10+ / 2.0+)
- Sistem de operare: Ubuntu 20.04+ / Debian 11+ / CentOS 8+
- Nginx sau alt reverse proxy pentru producție
- Certificat SSL pentru HTTPS

## Instalare și Configurare

### Pregătirea Mediului

1. Instalați Docker și Docker Compose:
```bash
# Instalare Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Instalare Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/v2.5.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

2. Clonați repository-ul Bitcoin Reload:
```bash
git clone https://github.com/bitcoin-reload/bitcoin-reload.git
cd bitcoin-reload
```

3. Creați fișierul de configurare pentru variabilele de mediu:
```bash
cp .env.example .env
```

4. Editați fișierul `.env` cu valorile corespunzătoare:
```bash
# Configurare blockchain
RPC_USER=admin
RPC_PASSWORD=your_secure_password_here

# Configurare bază de date
DB_USER=btcr_user
DB_PASSWORD=your_secure_db_password_here
DB_NAME=btcr_db

# Configurare Redis
REDIS_PASSWORD=your_secure_redis_password_here

# Configurare securitate
JWT_SECRET=your_secure_jwt_secret_here
ENCRYPTION_KEY=your_secure_encryption_key_here

# Configurare monitorizare
GRAFANA_USER=admin
GRAFANA_PASSWORD=your_secure_grafana_password_here
```

### Instalare cu Docker Compose

1. Construiți și porniți containerele:
```bash
docker-compose up -d
```

2. Verificați starea containerelor:
```bash
docker-compose ps
```

3. Inițializați baza de date (la prima rulare):
```bash
docker-compose exec api flask db upgrade
```

4. Creați un utilizator admin:
```bash
docker-compose exec api flask create-admin
```

### Configurare Reverse Proxy (Nginx)

Pentru a expune serviciul în producție, configurați un reverse proxy:

1. Instalați Nginx:
```bash
sudo apt update
sudo apt install -y nginx
```

2. Creați configurația pentru Bitcoin Reload:
```bash
sudo nano /etc/nginx/sites-available/bitcoin-reload
```

3. Adăugați următoarea configurație:
```nginx
server {
    listen 80;
    server_name your-domain.com;
    
    location / {
        return 301 https://$host$request_uri;
    }
}

server {
    listen 443 ssl;
    server_name your-domain.com;
    
    ssl_certificate /path/to/your/certificate.crt;
    ssl_certificate_key /path/to/your/private.key;
    
    # Configurare SSL securizată
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers ECDHE-RSA-AES256-GCM-SHA512:DHE-RSA-AES256-GCM-SHA512:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-SHA384;
    ssl_session_timeout 10m;
    ssl_session_cache shared:SSL:10m;
    ssl_session_tickets off;
    ssl_stapling on;
    ssl_stapling_verify on;
    
    # Headere de securitate
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    add_header X-Content-Type-Options nosniff;
    add_header X-Frame-Options DENY;
    add_header X-XSS-Protection "1; mode=block";
    add_header Content-Security-Policy "default-src 'self'; script-src 'self'; img-src 'self' data:; style-src 'self' 'unsafe-inline'; font-src 'self'; connect-src 'self'";
    
    # Frontend web
    location / {
        proxy_pass http://localhost:80;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
    
    # API
    location /api {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
    
    # Grafana
    location /monitoring {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

4. Activați configurația și reporniți Nginx:
```bash
sudo ln -s /etc/nginx/sites-available/bitcoin-reload /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

## Operare și Administrare

### Gestionarea Nodurilor Blockchain

#### Pornirea și Oprirea Nodurilor

```bash
# Pornire
docker-compose up -d blockchain

# Oprire
docker-compose stop blockchain

# Restart
docker-compose restart blockchain
```

#### Verificarea Stării Nodului

```bash
# Verificare logs
docker-compose logs -f blockchain

# Verificare status
docker-compose exec blockchain ./btcr-cli getinfo
```

#### Adăugarea unui Nod Validator

1. Generați o cheie de validator:
```bash
docker-compose exec blockchain ./btcr-cli generate-validator-key
```

2. Înregistrați cheia în rețea:
```bash
docker-compose exec blockchain ./btcr-cli register-validator <public_key>
```

3. Activați modul validator:
```bash
docker-compose exec blockchain ./btcr-cli start-validator <private_key>
```

### Administrarea API Service

#### Gestionarea Utilizatorilor

```bash
# Creare admin
docker-compose exec api flask create-admin

# Listare utilizatori
docker-compose exec api flask list-users

# Dezactivare utilizator
docker-compose exec api flask deactivate-user <username>
```

#### Configurarea Rate Limiting

Editați fișierul `.env` pentru a configura rate limiting:

```
RATE_LIMIT_DEFAULT=100
RATE_LIMIT_AUTH=5
RATE_LIMIT_SENSITIVE=3
```

### Administrarea Web Frontend

#### Actualizarea Frontend-ului

```bash
# Reconstruire frontend
docker-compose build web

# Restart frontend
docker-compose up -d web
```

## Monitorizare și Logging

### Accesarea Dashboard-urilor

- **Grafana**: http://your-domain.com/monitoring (sau http://localhost:3000)
- **Prometheus**: http://localhost:9090

### Configurarea Alertelor

1. Accesați Grafana și autentificați-vă
2. Navigați la "Alerting" > "Alert Rules"
3. Creați alerte pentru:
   - Utilizare CPU/RAM ridicată
   - Spațiu disk redus
   - Latență API ridicată
   - Erori de validare blockchain

### Vizualizarea Logurilor

```bash
# Toate logurile
docker-compose logs -f

# Loguri specifice
docker-compose logs -f blockchain
docker-compose logs -f api
docker-compose logs -f web
```

## Backup și Recuperare

### Backup Blockchain

```bash
# Export blockchain
docker-compose exec blockchain ./btcr-cli export-blockchain /app/data/backup/blockchain_$(date +%Y%m%d).dat

# Copiere locală
docker cp btcr-blockchain:/app/data/backup/blockchain_$(date +%Y%m%d).dat ./backups/
```

### Backup Bază de Date

```bash
# Backup MySQL
docker-compose exec db mysqldump -u root -p${DB_PASSWORD} ${DB_NAME} > ./backups/db_backup_$(date +%Y%m%d).sql
```

### Recuperare din Backup

```bash
# Restaurare blockchain
docker cp ./backups/blockchain_20250605.dat btcr-blockchain:/app/data/backup/
docker-compose exec blockchain ./btcr-cli import-blockchain /app/data/backup/blockchain_20250605.dat

# Restaurare bază de date
cat ./backups/db_backup_20250605.sql | docker-compose exec -T db mysql -u root -p${DB_PASSWORD} ${DB_NAME}
```

## Securitate

### Cele Mai Bune Practici

1. **Actualizări Regulate**
   - Actualizați toate componentele sistemului în mod regulat
   - Urmăriți buletinele de securitate pentru dependențe

2. **Gestionarea Cheilor**
   - Utilizați KMS pentru gestionarea cheilor de criptare
   - Rotiți cheile la intervale regulate (30 zile recomandat)
   - Stocați cheile master în mod securizat (de ex. HashiCorp Vault)

3. **Firewall și Rețea**
   - Limitați accesul la porturile necesare
   - Utilizați grupuri de securitate pentru a izola componentele
   - Implementați WAF pentru protecția API și web

4. **Autentificare și Autorizare**
   - Utilizați autentificare multi-factor pentru administratori
   - Implementați politici de parole puternice
   - Revizuiți periodic permisiunile utilizatorilor

### Auditare și Conformitate

1. **Logging de Securitate**
   - Activați logging-ul extins pentru evenimente de securitate
   - Centralizați logurile pentru analiză
   - Implementați detectarea anomaliilor

2. **Scanări de Vulnerabilități**
   - Efectuați scanări regulate de vulnerabilități
   - Testați penetrarea sistemului anual
   - Remediați vulnerabilitățile critice imediat

## Troubleshooting

### Probleme Comune și Soluții

#### Blockchain nu sincronizează

**Simptome**: Nodul blockchain rămâne în urmă, nu se sincronizează cu rețeaua.

**Soluții**:
1. Verificați conectivitatea rețelei:
```bash
docker-compose exec blockchain ./btcr-cli getnetworkinfo
```

2. Verificați nodurile peer:
```bash
docker-compose exec blockchain ./btcr-cli getpeerinfo
```

3. Restart nod:
```bash
docker-compose restart blockchain
```

#### API Service returnează erori

**Simptome**: API returnează erori 500 sau nu răspunde.

**Soluții**:
1. Verificați logurile:
```bash
docker-compose logs -f api
```

2. Verificați conexiunea la blockchain:
```bash
docker-compose exec api flask check-blockchain
```

3. Restart API:
```bash
docker-compose restart api
```

#### Probleme de Performanță

**Simptome**: Latență ridicată, timpi de răspuns lenți.

**Soluții**:
1. Verificați utilizarea resurselor:
```bash
docker stats
```

2. Verificați cache Redis:
```bash
docker-compose exec redis redis-cli -a ${REDIS_PASSWORD} info stats
```

3. Optimizați configurația:
```bash
docker-compose exec blockchain ./btcr-cli setconfig max_connections 50
```

## FAQ

### Întrebări Generale

**Î: Câte noduri sunt necesare pentru o rețea funcțională?**

R: Minim 3 noduri validator sunt recomandate pentru o rețea funcțională și descentralizată. Pentru testare, un singur nod este suficient.

**Î: Care este timpul mediu de confirmare a tranzacțiilor?**

R: Timpul mediu de confirmare este de aproximativ 15 secunde, semnificativ mai rapid decât Bitcoin original.

**Î: Cum pot verifica dacă nodul meu este sincronizat?**

R: Utilizați comanda:
```bash
docker-compose exec blockchain ./btcr-cli getblockchaininfo
```
Comparați `blocks` cu `headers` - dacă sunt egale, nodul este sincronizat.

### Întrebări Tehnice

**Î: Cum pot modifica parametrii de consens?**

R: Parametrii de consens pot fi modificați prin propuneri de guvernanță on-chain. Administratorii pot propune modificări care sunt apoi votate de validatori.

**Î: Cum pot adăuga noi noduri seed în rețea?**

R: Editați fișierul de configurare blockchain:
```bash
docker-compose exec blockchain nano /app/config/seed_nodes.json
```
Adăugați noile noduri și reporniți serviciul.

**Î: Cum pot face backup la cheile private ale portofelelor?**

R: Cheile private sunt stocate criptat în baza de date. Pentru backup:
```bash
docker-compose exec api flask export-wallets --output=/app/data/backup/wallets_backup.enc
docker cp btcr-api:/app/data/backup/wallets_backup.enc ./backups/
```

### Suport și Resurse Adiționale

Pentru asistență suplimentară:
- Documentație online: https://docs.bitcoin-reload.com
- Forum comunitate: https://community.bitcoin-reload.com
- Email suport: support@bitcoin-reload.com
- GitHub: https://github.com/bitcoin-reload/bitcoin-reload
