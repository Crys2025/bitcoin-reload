# Ghid de Instalare Rapidă - Bitcoin Reload

Acest ghid oferă instrucțiuni pas cu pas pentru instalarea și configurarea sistemului Bitcoin Reload în mediul de producție.

## Cerințe Preliminare

- Server Linux (Ubuntu 20.04+ recomandat)
- Docker și Docker Compose instalate
- Minim 8GB RAM, 4 core CPU, 250GB SSD
- Conexiune la internet stabilă

## Pași de Instalare

### 1. Pregătirea Mediului

```bash
# Instalare Docker (dacă nu este deja instalat)
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Instalare Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/v2.5.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

### 2. Descărcarea și Configurarea Bitcoin Reload

```bash
# Creați directorul pentru proiect
mkdir -p ~/bitcoin-reload
cd ~/bitcoin-reload

# Extrageți arhiva livrată
tar -xzf bitcoin_reload_project.tar.gz

# Copiați fișierul de configurare exemplu
cp .env.example .env

# Editați fișierul .env cu valorile dorite
nano .env
```

### 3. Pornirea Sistemului

```bash
# Construiți și porniți containerele
docker-compose up -d

# Verificați starea containerelor
docker-compose ps
```

### 4. Inițializarea Sistemului

```bash
# Inițializați baza de date
docker-compose exec api flask db upgrade

# Creați un utilizator admin
docker-compose exec api flask create-admin
```

### 5. Accesarea Sistemului

- **Interfața Web**: http://your-server-ip
- **API**: http://your-server-ip:5000/api
- **Monitorizare**: http://your-server-ip:3000

## Verificarea Funcționalității

```bash
# Verificați starea blockchain-ului
docker-compose exec blockchain ./btcr-cli getinfo

# Verificați starea API
curl http://localhost:5000/api/status
```

## Următorii Pași

Pentru configurare avansată și operare, consultați documentația completă în directorul `docs/`:

- [Documentație Tehnică și Operațională](docs/technical_operational_guide.md)
- [Ghid de Administrare](docs/admin_guide.md)
- [Ghid de Securitate](docs/security_guide.md)

## Suport

Pentru asistență suplimentară:
- Email: support@bitcoin-reload.com
- Documentație online: https://docs.bitcoin-reload.com
