# 🔍 TheHarvester - OSINT Email & Domain Reconnaissance

## Description

TheHarvester est un outil OSINT (Open Source Intelligence) pour collecter :
- **Emails** (adresses email publiques)
- **Noms** (personnes associées au domaine)
- **Sous-domaines** (subdomains)
- **IPs** (adresses IP)
- **URLs** (pages indexées)
- **Hosts** (serveurs)

**Sources :** Google, Bing, Shodan, Hunter.io, LinkedIn, Twitter, etc.

---

## Installation

✅ **Déjà installé dans Kali Headless**

```bash
# Vérifier l'installation
theHarvester --help
```

---

## Syntaxe de base

```bash
theHarvester -d DOMAIN -b SOURCE
```

**Paramètres :**
- `-d` : domaine cible
- `-b` : source de données (google, bing, linkedin, etc.)

---

## Exemples pratiques

### Exemple 1 : Recherche basique (Google)

```bash
# Rechercher emails + sous-domaines via Google
theHarvester -d example.com -b google

# Limiter les résultats
theHarvester -d example.com -b google -l 100
# -l 100 = limiter à 100 résultats
```

**Résultat typique :**
```
[*] Target: example.com

[*] Searching Google:
        Searching 0 results.
        Searching 100 results.

[*] Emails found: 5
----------------------------------------
john.doe@example.com
sales@example.com
support@example.com
admin@example.com
contact@example.com

[*] Hosts found: 3
----------------------------------------
www.example.com
mail.example.com
ftp.example.com
```

---

### Exemple 2 : Recherche multi-sources

```bash
# Google + Bing + Baidu
theHarvester -d example.com -b google,bing,baidu

# Toutes les sources (peut être long)
theHarvester -d example.com -b all

# Sources spécifiques OSINT
theHarvester -d example.com -b hunter,linkedin,twitter
```

---

### Exemple 3 : Shodan (IP + services)

```bash
# Rechercher via Shodan (nécessite API key)
theHarvester -d example.com -b shodan

# Configurer API key Shodan
# Éditer ~/.theHarvester/api-keys.yaml
# shodan_key: YOUR_SHODAN_API_KEY
```

**Résultat Shodan :**
```
[*] IPs found: 2
----------------------------------------
203.0.113.10
203.0.113.20

[*] Ports found:
203.0.113.10:80 (HTTP)
203.0.113.10:443 (HTTPS)
203.0.113.10:22 (SSH)
```

---

### Exemple 4 : Hunter.io (emails professionnels)

```bash
# Rechercher emails via Hunter.io (nécessite API key gratuite)
theHarvester -d example.com -b hunter

# Obtenir API key gratuite : https://hunter.io/api
# Limite gratuite : 25 requêtes/mois
```

---

### Exemple 5 : Sous-domaines uniquement

```bash
# Rechercher sous-domaines via multiple sources
theHarvester -d example.com -b sublist3r,crtsh,virustotal

# Sources pour sous-domaines :
# - crtsh         : Certificate Transparency logs
# - virustotal    : VirusTotal
# - threatcrowd   : ThreatCrowd
# - sublist3r     : Sublist3r (agrégateur)
```

---

### Exemple 6 : LinkedIn (employés)

```bash
# Rechercher employés sur LinkedIn
theHarvester -d example.com -b linkedin -l 200

# Note : LinkedIn bloque souvent les scrapers
# Utilisez avec modération
```

---

### Exemple 7 : DNS Brute-force

```bash
# Brute-force DNS avec wordlist
theHarvester -d example.com -b dnsdumpster -n

# -n : DNS bruteforce (utilise wordlist intégrée)
```

---

## Sources disponibles

```bash
# Lister toutes les sources
theHarvester -b all --list-sources

# Sources principales :
anubis           # Anubis DB
baidu            # Baidu (China)
bing             # Bing
binaryedge       # BinaryEdge (requires API key)
bufferoverun     # BufferOver
certspotter      # CertSpotter
crtsh            # Certificate Transparency
dnsdumpster      # DNSDumpster
duckduckgo       # DuckDuckGo
fullhunt         # FullHunt (requires API key)
github-code      # GitHub Code
google           # Google
hackertarget     # HackerTarget
hunter           # Hunter.io (requires API key)
intelx           # Intelligence X (requires API key)
linkedin         # LinkedIn
netlas           # Netlas (requires API key)
otx              # AlienVault OTX
pentesttools     # PentestTools
projectdiscovery # ProjectDiscovery Chaos
rapiddns         # RapidDNS
rocketreach      # RocketReach (requires API key)
securityTrails   # SecurityTrails (requires API key)
shodan           # Shodan (requires API key)
sublist3r        # Sublist3r
threatcrowd      # ThreatCrowd
threatminer      # ThreatMiner
twitter          # Twitter
virustotal       # VirusTotal (requires API key)
yahoo            # Yahoo
zoomeye          # ZoomEye (requires API key)
```

---

## Options avancées

```bash
# Sauvegarder résultats en HTML
theHarvester -d example.com -b google -f results.html

# Sauvegarder en JSON
theHarvester -d example.com -b google -f results.json

# Sauvegarder en XML
theHarvester -d example.com -b google -f results.xml

# Verbose mode
theHarvester -d example.com -b google -v

# Screenshot (capture Selenium)
theHarvester -d example.com -b google -s
# Nécessite Selenium + chromedriver

# Proxy
theHarvester -d example.com -b google --proxy http://127.0.0.1:8080

# Délai entre requêtes (éviter ban)
theHarvester -d example.com -b google --delay 5
# --delay 5 = 5 secondes entre requêtes

# DNS lookup (résoudre les hosts trouvés)
theHarvester -d example.com -b google -c
# -c = DNS lookup
```

---

## Cas d'usage réels

### Scénario 1 : Reconnaissance pré-pentest

**Contexte :** Phase de reconnaissance passive avant un pentest.

```bash
# 1. Recherche complète
theHarvester -d target-company.com -b all -l 500 -f recon_results.html

# 2. Analyser les emails trouvés (format naming convention)
# john.doe@target.com → convention: first.last@domain

# 3. Générer liste d'utilisateurs probables
# john.doe, jane.smith, admin, etc.

# 4. Utiliser pour password spraying ou phishing
```

---

### Scénario 2 : Cartographie de sous-domaines

**Contexte :** Découvrir tous les sous-domaines d'une cible.

```bash
# Rechercher sous-domaines via sources multiples
theHarvester -d example.com -b crtsh,virustotal,threatcrowd,dnsdumpster -f subdomains.json

# Extraire uniquement les sous-domaines du JSON
cat subdomains.json | jq '.hosts[]' > subdomains.txt

# Scanner les sous-domaines avec Nmap
while read subdomain; do
    nmap -sV $subdomain -oA nmap_$subdomain
done < subdomains.txt
```

---

### Scénario 3 : OSINT pour phishing

**Contexte :** Créer une liste d'emails pour campagne de sensibilisation.

```bash
# 1. Récolter emails
theHarvester -d target.com -b hunter,google,linkedin -l 1000 -f emails.json

# 2. Extraire emails
cat emails.json | jq '.emails[]' | sort -u > email_list.txt

# 3. Vérifier validité des emails (avec tool externe)
# emailverify.sh email_list.txt

# 4. Utiliser pour phishing simulation (avec autorisation!)
# gophish, king-phisher, etc.
```

---

### Scénario 4 : Recherche d'employés (LinkedIn)

**Contexte :** Identifier les employés clés pour social engineering.

```bash
# Rechercher employés sur LinkedIn
theHarvester -d company.com -b linkedin -l 500 -f employees.html

# Analyser les titres de postes :
# - C-level (CEO, CTO, CISO) → phishing ciblé
# - IT Support → pretexting technique
# - HR → potential data access
```

---

## Configuration API Keys

Éditer : `~/.theHarvester/api-keys.yaml`

```yaml
# Hunter.io (25 free requests/month)
hunter:
  key: your_hunter_api_key

# Shodan (100 free results)
shodan:
  key: your_shodan_api_key

# SecurityTrails (50 free requests/month)
securitytrails:
  key: your_securitytrails_api_key

# VirusTotal
virustotal:
  key: your_virustotal_api_key

# BinaryEdge
binaryedge:
  key: your_binaryedge_api_key

# FullHunt
fullhunt:
  key: your_fullhunt_api_key

# GitHub
github:
  key: your_github_token
```

**Obtenir API keys gratuites :**
- Hunter.io : https://hunter.io/api
- Shodan : https://account.shodan.io/
- SecurityTrails : https://securitytrails.com/
- VirusTotal : https://www.virustotal.com/gui/join-us

---

## Combiner avec d'autres outils

### Avec Amass (sous-domaines)

```bash
# 1. TheHarvester pour emails + sous-domaines
theHarvester -d example.com -b all -f harvester.json

# 2. Amass pour énumération avancée de sous-domaines
amass enum -d example.com -o amass_subdomains.txt

# 3. Combiner résultats
cat harvester.json amass_subdomains.txt | sort -u > all_subdomains.txt
```

### Avec Maltego (visualisation)

```bash
# 1. Exporter résultats TheHarvester en XML
theHarvester -d example.com -b all -f results.xml

# 2. Importer dans Maltego pour visualisation graphique
# (Maltego commercial ou Maltego CE)
```

---

## Commandes utiles

```bash
# Mettre à jour TheHarvester
pip3 install --upgrade theHarvester

# Lister plugins installés
theHarvester --list-plugins

# Tester une seule source rapidement
theHarvester -d example.com -b google -l 10

# Mode silencieux (pas d'output verbose)
theHarvester -d example.com -b google -q
```

---

## Astuces

### Automatiser recherche quotidienne

```bash
#!/bin/bash
# daily_osint.sh

DOMAIN="target-company.com"
DATE=$(date +%Y-%m-%d)

theHarvester -d $DOMAIN -b all -f /var/osint/${DOMAIN}_${DATE}.json

# Comparer avec hier
diff /var/osint/${DOMAIN}_$(date -d yesterday +%Y-%m-%d).json \
     /var/osint/${DOMAIN}_${DATE}.json > changes.txt

# Envoyer email si changements
if [ -s changes.txt ]; then
    mail -s "OSINT Changes for $DOMAIN" admin@example.com < changes.txt
fi
```

### Combiner résultats de plusieurs domaines

```bash
# domains.txt
target1.com
target2.com
target3.com

# Scanner tous
while read domain; do
    theHarvester -d $domain -b all -f ${domain}.json
done < domains.txt

# Merger tous les emails
cat *.json | jq '.emails[]' | sort -u > all_emails.txt
```

---

## Ressources

- [TheHarvester GitHub](https://github.com/laramies/theHarvester)
- [OSINT Framework](https://osintframework.com/)
- [Intelligence X](https://intelx.io/)
- [Hunter.io](https://hunter.io/)

---

## ⚠️ Avertissement légal

**TheHarvester collecte des données PUBLIQUES, MAIS :**

**Légal :**
- Recherche sur vos propres domaines
- OSINT pour pentests autorisés
- Recherche académique/sécurité

**Attention :**
- Respecter les ToS des sources (Google, LinkedIn, etc.)
- Ne pas abuser des API (rate limiting)
- Ne pas utiliser pour spam/phishing malveillant

**Conséquences possibles :**
- Ban IP par les moteurs de recherche
- Blocage API
- LinkedIn peut bloquer votre compte

**Conseil :** Utilisez un VPN et respectez les délais entre requêtes (`--delay`).
