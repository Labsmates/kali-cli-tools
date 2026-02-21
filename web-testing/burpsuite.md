# 🔥 Burp Suite - Web Application Security Testing

## Description

Burp Suite est LA référence pour les tests de sécurité web :
- **Proxy HTTP/HTTPS** interception
- **Scanner** de vulnérabilités
- **Intruder** (fuzzing/brute-force)
- **Repeater** (modification requêtes)
- **Spider** (crawling)

**Version Kali :** Burp Suite Community Edition (gratuit, fonctionnalités limitées)

---

## Installation

✅ **Déjà installé dans Kali Headless**

```bash
# Version CLI (limité)
burpsuite --help

# Version GUI (recommandé)
burpsuite &
```

---

## Configuration proxy

### 1. Configurer navigateur

**Proxy settings :**
- HTTP Proxy: `127.0.0.1`
- Port: `8080`

### 2. Installer certificat SSL

```bash
# 1. Accéder à http://burp
# 2. Télécharger CA certificate
# 3. Installer dans navigateur (Certificates → Import)
```

---

## Exemples pratiques

### Interception de requêtes

```bash
# 1. Lancer Burp Suite
# 2. Onglet Proxy → Intercept → Intercept is on
# 3. Naviguer sur un site → Requête capturée
# 4. Modifier requête → Forward
```

### Repeater (rejouer requêtes)

```bash
# 1. Capturer requête
# 2. Clic droit → Send to Repeater
# 3. Modifier paramètres
# 4. Send → Observer réponse
```

### Intruder (fuzzing)

```bash
# 1. Send to Intruder
# 2. Sélectionner payload position (§value§)
# 3. Payloads → Load wordlist
# 4. Start attack
```

### Scanner (Pro uniquement)

```bash
# Version Community : pas de scanner automatique
# Version Pro : Clic droit → Scan
```

---

## Cas d'usage

### Test d'injection SQL

```bash
# 1. Capturer requête POST
# 2. Send to Repeater
# 3. Tester : ' OR 1=1 --
# 4. Analyser réponse
```

### Bypass authentication

```bash
# 1. Capturer login request
# 2. Tester différents payloads
# 3. Observer codes de réponse
```

---

## Ressources

- [Burp Suite Documentation](https://portswigger.net/burp/documentation)
- [PortSwigger Web Security Academy](https://portswigger.net/web-security)

---

## ⚠️ Note

**Version Community = limitée (pas de scanner, speed limit).**  
**Version Pro = $449/an (scanner automatique, Intruder illimité).**
