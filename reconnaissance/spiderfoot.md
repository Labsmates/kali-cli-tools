# 🕷️ SpiderFoot - OSINT Automation

## Description

SpiderFoot est un outil OSINT automatisé qui corrèle des données depuis 200+ sources.

**Fonctionnalités :**
- OSINT 100% automatisé
- Interface web graphique
- 200+ modules de collecte
- Graphe de corrélations

---

## Installation

✅ **Déjà installé dans Kali Headless**

```bash
spiderfoot --help
```

---

## Exemples pratiques

### CLI mode

```bash
# Scan basique
spiderfoot -s example.com -o json

# Avec modules spécifiques
spiderfoot -s example.com -m sfp_dnsresolve,sfp_hunter -o json
```

### Web UI mode

```bash
# Démarrer serveur web
spiderfoot -l 127.0.0.1:5001

# Accès : http://127.0.0.1:5001
```

---

## Ressources

- [SpiderFoot HX](https://www.spiderfoot.net/)
- [SpiderFoot GitHub](https://github.com/smicallef/spiderfoot)
