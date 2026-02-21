# 🔍 Gobuster - URI/DNS/VHost Fuzzer

## Description

Gobuster est un outil de brute-force rapide pour :
- **Directories/files** (brute-force web)
- **DNS** (sous-domaines)
- **VHosts** (virtual hosts)
- **S3 buckets** (AWS)

**Particularité :** Écrit en Go, ultra-rapide, parallélisé.

---

## Installation

✅ **Déjà installé dans Kali Headless**

```bash
# Vérifier installation
gobuster version
```

---

## Modes principaux

### Mode DIR - Directory/File brute-force

```bash
# Scan directories
gobuster dir -u http://example.com -w /usr/share/wordlists/dirb/common.txt

# Avec extensions
gobuster dir -u http://example.com \
  -w wordlist.txt \
  -x php,html,txt,js

# Résultat :
# /admin (Status: 200)
# /backup.zip (Status: 200)
# /config.php (Status: 403)
```

---

### Mode DNS - Subdomain enumeration

```bash
# Énumérer sous-domaines
gobuster dns -d example.com -w subdomains.txt

# Résultat :
# Found: admin.example.com
# Found: mail.example.com
# Found: dev.example.com
```

---

### Mode VHOST - Virtual host discovery

```bash
# Découvrir vhosts
gobuster vhost -u http://example.com -w vhosts.txt

# Avec auth
gobuster vhost -u http://example.com \
  -w vhosts.txt \
  -U admin -P password
```

---

### Mode S3 - AWS S3 buckets

```bash
# Trouver buckets S3 publics
gobuster s3 -w bucket_names.txt
```

---

## Exemples pratiques

### Exemple 1 : Directory brute-force basique

```bash
# Scan simple
gobuster dir -u http://example.com \
  -w /usr/share/seclists/Discovery/Web-Content/common.txt

# Ignorer codes 404
gobuster dir -u http://example.com -w wordlist.txt -b 404

# Montrer seulement codes 200
gobuster dir -u http://example.com -w wordlist.txt -s 200,301,302
```

---

### Exemple 2 : Extensions multiples

```bash
# PHP + config files
gobuster dir -u http://example.com \
  -w wordlist.txt \
  -x php,php.bak,php~,phps,php.old,inc
```

---

### Exemple 3 : Authentification

```bash
# HTTP Basic Auth
gobuster dir -u http://example.com \
  -w wordlist.txt \
  -U admin -P password

# Cookies
gobuster dir -u http://example.com \
  -w wordlist.txt \
  -c "session=abc123; role=admin"

# Headers custom
gobuster dir -u http://example.com \
  -w wordlist.txt \
  -H "Authorization: Bearer token123"
```

---

### Exemple 4 : Performance tuning

```bash
# 50 threads (plus rapide)
gobuster dir -u http://example.com \
  -w wordlist.txt \
  -t 50

# Timeout custom
gobuster dir -u http://example.com \
  -w wordlist.txt \
  --timeout 5s

# Délai entre requêtes (stealth)
gobuster dir -u http://example.com \
  -w wordlist.txt \
  --delay 1s
```

---

### Exemple 5 : DNS enumeration avancé

```bash
# Avec DNS resolver custom
gobuster dns -d example.com \
  -w subdomains.txt \
  -r 8.8.8.8

# Wildcard detection
gobuster dns -d example.com \
  -w subdomains.txt \
  --wildcard
```

---

## Options importantes

```bash
# Global
-t 10            # Threads (défaut: 10)
-q               # Quiet (pas de bannière)
-o output.txt    # Output file
--no-error       # Pas d'affichage erreurs

# DIR mode
-u URL           # Target URL
-w wordlist      # Wordlist
-x ext           # Extensions (php,html,txt)
-s codes         # Status codes (200,301,302)
-b codes         # Codes à ignorer (404,403)
-k               # Skip SSL verification
-a UA            # User-Agent
-c COOKIE        # Cookie
-U user          # Username (Basic Auth)
-P pass          # Password (Basic Auth)

# DNS mode
-d domain        # Domain
-r resolver      # DNS resolver
--wildcard       # Wildcard detection

# Performance
-t 50            # Threads
--timeout 10s    # Timeout
--delay 100ms    # Delay entre requêtes
```

---

## Wordlists recommandées

```bash
# Directories (SecLists)
/usr/share/seclists/Discovery/Web-Content/common.txt           # 4600 entrées
/usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt  # 220k entrées
/usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt

# Subdomains
/usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
/usr/share/seclists/Discovery/DNS/dns-Jhaddix.txt

# DIRB (classique)
/usr/share/wordlists/dirb/common.txt
/usr/share/wordlists/dirb/big.txt
```

---

## Cas d'usage réels

### Scénario 1 : Discovery complet d'une appli web

```bash
# 1. Directories + fichiers sensibles
gobuster dir -u http://target.com \
  -w /usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt \
  -x php,html,txt,bak,old,zip \
  -t 50 \
  -o dir_results.txt

# 2. Sous-domaines
gobuster dns -d target.com \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
  -o dns_results.txt

# 3. Analyser résultats
grep "Status: 200" dir_results.txt
```

---

## Ressources

- [Gobuster GitHub](https://github.com/OJ/gobuster)
- [SecLists Wordlists](https://github.com/danielmiessler/SecLists)

---

## ⚠️ Avertissement

**Gobuster génère BEAUCOUP de requêtes.**  
**Usage autorisé uniquement.**
