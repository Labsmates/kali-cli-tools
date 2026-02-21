# 🕷️ Nikto - Web Server Scanner

## Description

Nikto est un scanner de vulnérabilités web open-source qui détecte :
- **6700+ vulnérabilités** potentielles
- Fichiers/scripts dangereux
- Mauvaises configurations serveur
- Versions de logiciels obsolètes
- Headers HTTP manquants

---

## Installation

✅ **Déjà installé dans Kali Headless**

```bash
# Vérifier l'installation
nikto -Version
```

---

## Exemples pratiques

### Exemple 1 : Scan basique

```bash
# Scanner un site web
nikto -h http://example.com

# HTTPS
nikto -h https://example.com -ssl

# Port custom
nikto -h http://example.com:8080
```

**Résultat typique :**
```
+ Server: Apache/2.4.41
+ /admin/: Admin directory found
+ /phpinfo.php: PHP info found
+ OSVDB-3092: /admin/: This might be interesting...
```

---

### Exemple 2 : Scanner plusieurs cibles

```bash
# Depuis un fichier
cat > targets.txt <<EOF
http://example.com
https://site2.com
http://10.0.0.1:8080
EOF

nikto -h targets.txt
```

---

### Exemple 3 : Tuning (types de tests)

```bash
# Tous les tests (défaut)
nikto -h http://example.com -Tuning 123456789abc

# Tests spécifiques :
# 1 = File upload
# 2 = Misconfiguration
# 3 = Information disclosure
# 4 = Injection (XSS/Script/HTML)
# 5 = Remote file retrieval
# 6 = Denial of service
# 7 = Remote file retrieval (web root)
# 8 = Command execution
# 9 = SQL injection
# a = Authentication bypass
# b = Software identification
# c = Remote source inclusion

# Exemple : Seulement injection + SQLi
nikto -h http://example.com -Tuning 49
```

---

### Exemple 4 : Output formats

```bash
# Format HTML
nikto -h http://example.com -o report.html -Format html

# Format XML
nikto -h http://example.com -o report.xml -Format xml

# Format CSV
nikto -h http://example.com -o report.csv -Format csv

# Format JSON (newer versions)
nikto -h http://example.com -o report.json -Format json
```

---

### Exemple 5 : Authentification

```bash
# HTTP Basic Auth
nikto -h http://example.com -id admin:password

# Custom headers
nikto -h http://example.com -useragent "Mozilla/5.0"

# Cookie
nikto -h http://example.com -cookie "session=abc123"
```

---

### Exemple 6 : Proxy & Stealth

```bash
# Via proxy
nikto -h http://example.com -useproxy http://127.0.0.1:8080

# Via Tor (avec proxychains)
proxychains nikto -h http://example.com

# Limiter requêtes/seconde (éviter ban)
nikto -h http://example.com -Pause 2
# -Pause 2 = attendre 2 secondes entre requêtes
```

---

## Options importantes

```bash
# Scan options
-h HOST          # Target (URL ou IP)
-p PORT          # Port (défaut: 80)
-ssl             # Forcer HTTPS
-nossl           # Désactiver HTTPS

# Tuning
-Tuning 1234     # Types de tests
-Plugins plugin  # Plugin spécifique

# Performance
-Pause N         # Pause entre requêtes (secondes)
-timeout N       # Timeout connexion (défaut: 10)
-maxtime N       # Temps max de scan (secondes)

# Output
-o FILE          # Output file
-Format html     # Format (txt/html/xml/csv/json)
-Display V       # Verbosité (1=quiet, 4=debug)

# Authentication
-id user:pass    # HTTP Basic Auth
-cookie COOKIE   # Cookie custom

# Evasion
-useragent UA    # User-Agent custom
-useproxy PROXY  # Proxy HTTP
```

---

## Cas d'usage réels

### Scénario 1 : Audit initial d'un site web

```bash
# Scan complet avec rapport HTML
nikto -h https://target.com -ssl \
  -o nikto_report.html -Format html \
  -Display V
```

---

### Scénario 2 : Scanner application web protégée

```bash
# Avec auth + cookie
nikto -h http://app.example.com \
  -id admin:password \
  -cookie "PHPSESSID=abc123; role=admin"
```

---

## Ressources

- [Nikto GitHub](https://github.com/sullo/nikto)
- [CIRT.net](https://cirt.net/Nikto2)

---

## ⚠️ Avertissement

**Nikto génère BEAUCOUP de requêtes :**
- Peut crasher serveurs faibles
- Déclenche WAF/IDS
- Logs évidents

**Usage autorisé uniquement.**
