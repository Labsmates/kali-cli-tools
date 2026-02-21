# 🔍 WPScan - WordPress Security Scanner

## Description

WPScan est LE scanner de sécurité spécialisé pour WordPress :
- Détection de version WordPress
- Énumération de thèmes/plugins vulnérables
- Énumération d'utilisateurs
- Brute-force de mots de passe
- Détection de vulnérabilités (CVE)
- Test de fichiers sensibles

---

## Installation

✅ **Déjà installé dans Kali Headless**

```bash
# Vérifier l'installation
wpscan --version

# Mettre à jour la base de données
wpscan --update
```

---

## Configuration API Token (recommandé)

Pour obtenir des infos de vulnérabilités détaillées :

```bash
# 1. Créer un compte gratuit sur https://wpscan.com/
# 2. Obtenir votre API token

# 3. Configurer le token
wpscan --api-token YOUR_API_TOKEN_HERE

# Limite gratuite : 25 requêtes/jour
```

---

## Syntaxe de base

```bash
wpscan --url https://example.com [options]
```

---

## Exemples pratiques

### Exemple 1 : Scan basique

```bash
# Scan simple
wpscan --url http://example.com

# Scan avec API token (plus d'infos)
wpscan --url http://example.com --api-token YOUR_TOKEN
```

**Résultat typique :**
```
[+] URL: http://example.com/
[+] WordPress version 6.1.1 identified
[!] 3 vulnerabilities identified:
    - CVE-2023-1234: XSS vulnerability
    - CVE-2023-5678: SQL Injection
```

---

### Exemple 2 : Énumération d'utilisateurs

```bash
# Énumérer tous les utilisateurs
wpscan --url http://example.com --enumerate u

# Énumérer plage d'IDs spécifique
wpscan --url http://example.com --enumerate u1-100

# Méthodes alternatives d'énumération
wpscan --url http://example.com --enumerate u \
  --user-detection-mode mixed

# Modes disponibles :
# - mixed (défaut, combine plusieurs techniques)
# - aggressive (plus de requêtes)
# - passive (détection discrète)
```

**Résultat :**
```
[i] User(s) Identified:
[+] admin
 | Found By: Author Archive (Passive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] john
 | Found By: Author Id Brute Forcing (Aggressive Detection)
```

---

### Exemple 3 : Énumération de plugins

```bash
# Énumérer plugins vulnérables uniquement
wpscan --url http://example.com --enumerate vp

# Tous les plugins (lent)
wpscan --url http://example.com --enumerate ap

# Plugins populaires
wpscan --url http://example.com --enumerate p

# Avec détection agressive
wpscan --url http://example.com --enumerate vp \
  --plugins-detection aggressive
```

**Modes de détection :**
- `passive` : Uniquement versions dans HTML
- `aggressive` : Teste existence de fichiers (readme.txt, etc.)
- `mixed` : Combinaison (défaut)

---

### Exemple 4 : Énumération de thèmes

```bash
# Thèmes vulnérables
wpscan --url http://example.com --enumerate vt

# Tous les thèmes
wpscan --url http://example.com --enumerate at

# Thèmes populaires
wpscan --url http://example.com --enumerate t
```

---

### Exemple 5 : Scan complet (énumération complète)

```bash
# Énumérer : users + plugins vulnérables + thèmes vulnérables + timthumbs
wpscan --url http://example.com \
  --enumerate u,vp,vt,tt \
  --api-token YOUR_TOKEN

# Codes d'énumération :
# u  = Users
# vp = Vulnerable plugins
# ap = All plugins
# p  = Popular plugins
# vt = Vulnerable themes
# at = All themes
# t  = Popular themes
# tt = Timthumbs
# cb = Config backups
# dbe = Db exports
# m  = Media IDs
```

---

### Exemple 6 : Brute-force de mots de passe

```bash
# Brute-force avec wordlist
wpscan --url http://example.com \
  --passwords /usr/share/wordlists/rockyou.txt \
  --usernames admin

# Brute-force multi-users
wpscan --url http://example.com \
  --passwords passwords.txt \
  --usernames admin,john,sarah

# Brute-force depuis fichier users
wpscan --url http://example.com \
  --passwords passwords.txt \
  --usernames users.txt

# Avec threads (plus rapide)
wpscan --url http://example.com \
  --passwords passwords.txt \
  --usernames admin \
  --max-threads 50
```

**Résultat si succès :**
```
[SUCCESS] - admin / password123
```

---

### Exemple 7 : Test de fichiers sensibles

```bash
# Détecter fichiers exposés (backups, exports DB, etc.)
wpscan --url http://example.com --enumerate cb,dbe

# Test de fichiers personnalisés
wpscan --url http://example.com --enumerate cb \
  --config-backups file1.txt,file2.sql
```

---

### Exemple 8 : Bypass WAF / Stealth scan

```bash
# User-Agent aléatoire
wpscan --url http://example.com --random-user-agent

# User-Agent personnalisé
wpscan --url http://example.com \
  --user-agent "Mozilla/5.0 (Windows NT 10.0; Win64; x64)"

# Délai entre requêtes (éviter ban)
wpscan --url http://example.com --throttle 500
# --throttle 500 = 500ms entre requêtes

# Via proxy
wpscan --url http://example.com --proxy http://127.0.0.1:8080

# Via proxy SOCKS
wpscan --url http://example.com --proxy socks5://127.0.0.1:9050

# Désactiver vérification SSL
wpscan --url https://example.com --disable-tls-checks
```

---

## Formats de sortie

```bash
# Output JSON
wpscan --url http://example.com -o scan_results.json --format json

# Output CLI-no-colour (pour logs)
wpscan --url http://example.com -o scan_results.txt --format cli-no-colour

# Formats disponibles :
# - cli (défaut, couleurs)
# - cli-no-colour (texte brut)
# - json (structuré, pour scripts)
```

---

## Cas d'usage réels

### Scénario 1 : Audit de sécurité WordPress complet

**Contexte :** Audit d'un site WordPress client.

```bash
# 1. Scan complet avec API token
wpscan --url https://client-site.com \
  --enumerate u,vp,vt,cb,dbe \
  --api-token YOUR_TOKEN \
  --random-user-agent \
  -o full_audit.json --format json

# 2. Analyser les résultats
cat full_audit.json | jq '.vulnerabilities'

# 3. Générer rapport (filtrer vulns critiques)
cat full_audit.json | jq '.vulnerabilities[] | select(.severity == "high")'
```

---

### Scénario 2 : Test d'intrusion WordPress

**Contexte :** Pentest avec phase d'énumération + brute-force.

```bash
# Phase 1 : Énumération
wpscan --url http://target.com \
  --enumerate u,vp,vt \
  --api-token YOUR_TOKEN \
  -o enum_results.txt

# Phase 2 : Extraire usernames
grep "Found By:" enum_results.txt | grep -oP '(?<=\[+] )[a-zA-Z0-9_-]+'

# Phase 3 : Brute-force
wpscan --url http://target.com \
  --passwords /usr/share/wordlists/rockyou.txt \
  --usernames admin,john \
  --max-threads 20 \
  --throttle 100 \
  -o bruteforce_results.txt
```

---

### Scénario 3 : Détection de backdoor / webshell

```bash
# Scanner plugins non-standard
wpscan --url http://example.com \
  --enumerate ap \
  --plugins-detection aggressive

# Chercher fichiers suspects
wpscan --url http://example.com --enumerate cb

# Vérifier uploads (webshells possibles)
curl http://example.com/wp-content/uploads/ | grep -E "\.php|\.phtml"
```

---

## Options avancées

```bash
# Ignorer certificat SSL invalide
wpscan --url https://example.com --disable-tls-checks

# Timeout personnalisé
wpscan --url http://example.com --request-timeout 60

# Cookie personnalisé (contourner auth)
wpscan --url http://example.com --cookie "session=abc123"

# HTTP Basic Auth
wpscan --url http://example.com --http-auth user:password

# Vhost custom (si multi-sites)
wpscan --url http://192.168.1.100 --vhost example.com

# Suivre redirections
wpscan --url http://example.com --max-redirects 5
```

---

## Commandes utiles

```bash
# Mettre à jour WPScan
gem update wpscan

# Lister toutes les options
wpscan --help

# Version de WPScan
wpscan --version

# Afficher banner
wpscan --banner

# Mode verbose
wpscan --url http://example.com --verbose
```

---

## Intégration avec d'autres outils

### Avec Metasploit

```bash
# 1. Scanner avec WPScan
wpscan --url http://example.com --enumerate vp -o vuln_plugins.txt

# 2. Exploiter avec Metasploit
msfconsole
msf6 > search wordpress plugin
msf6 > use exploit/unix/webapp/wp_admin_shell_upload
msf6 > set RHOST example.com
msf6 > exploit
```

### Avec Nikto (scan web général)

```bash
# 1. WPScan pour WordPress
wpscan --url http://example.com -o wpscan.txt

# 2. Nikto pour scan web complet
nikto -h http://example.com -o nikto.txt
```

---

## Wordlists recommandées

```bash
# Pour brute-force WordPress
/usr/share/wordlists/rockyou.txt              # 14M passwords
/usr/share/seclists/Passwords/Common-Credentials/10-million-password-list-top-1000000.txt

# WordPress-specific
wget https://raw.githubusercontent.com/danielmiessler/SecLists/master/Passwords/Common-Credentials/WordPress.txt
```

---

## Astuces

### Automatiser scan quotidien

```bash
#!/bin/bash
# wp_daily_scan.sh

DATE=$(date +%Y-%m-%d)
wpscan --url https://mysite.com \
  --enumerate u,vp,vt \
  --api-token YOUR_TOKEN \
  -o /var/log/wpscan_$DATE.json --format json

# Envoyer email si vulnérabilités trouvées
if grep -q "vulnerabilities" /var/log/wpscan_$DATE.json; then
    mail -s "WPScan Alert" admin@example.com < /var/log/wpscan_$DATE.json
fi
```

### Scan multi-sites

```bash
# sites.txt
http://site1.com
http://site2.com
http://site3.com

# Scanner tous les sites
while read site; do
    wpscan --url $site --enumerate vp -o "${site//\//_}.txt"
done < sites.txt
```

---

## Ressources

- [WPScan Official](https://wpscan.com/)
- [WPScan Documentation](https://github.com/wpscanteam/wpscan/wiki)
- [WPVulnDB](https://wpscan.com/wordpresses) (base de données vulns)
- [WordPress Security Guide](https://wordpress.org/support/article/hardening-wordpress/)

---

## ⚠️ Avertissement légal

**WPScan est un outil de sécurité légitime, MAIS :**

**Autorisé :**
- Vos propres sites WordPress
- Avec autorisation écrite du propriétaire
- Environnements de test/staging

**Interdit :**
- Sites tiers sans permission
- Brute-force sur sites publics
- Utilisation malveillante

**Conséquences :**
- Ban IP
- Alertes de sécurité chez l'hébergeur
- Poursuites légales

**Note :** Le brute-force déclenche souvent des alertes. Utilisez avec précaution.
