# 🕵️ Recon-ng - Reconnaissance Framework

## Description

Recon-ng est un framework de reconnaissance modulaire (style Metasploit) pour OSINT.

**Fonctionnalités :**
- 80+ modules OSINT
- API integration (Shodan, Hunter.io, etc.)
- Base de données intégrée
- Reporting HTML/CSV/XML

---

## Installation

✅ **Déjà installé dans Kali Headless**

```bash
recon-ng --version
```

---

## Exemples pratiques

### Démarrage

```bash
# Lancer Recon-ng
recon-ng

# Créer workspace
[recon-ng][default] > workspaces create target_company
```

### Utiliser modules

```bash
# Lister modules
marketplace search

# Installer module
marketplace install recon/domains-hosts/hackertarget

# Charger module
modules load recon/domains-hosts/hackertarget

# Configurer
options set SOURCE example.com

# Exécuter
run
```

### Reporting

```bash
# Afficher résultats
show hosts

# Export HTML
modules load reporting/html
run
```

---

## Ressources

- [Recon-ng GitHub](https://github.com/lanmaster53/recon-ng)
