# 🔎 Amass - Network Mapping & Attack Surface Discovery

## Description

Amass est un outil OSINT avancé pour énumération de sous-domaines et cartographie réseau.

**Fonctionnalités :**
- Énumération de sous-domaines (20+ sources)
- Résolution DNS active/passive
- ASN discovery
- Reverse IP lookup
- Graphe de relations

---

## Installation

✅ **Déjà installé dans Kali Headless**

```bash
amass version
```

---

## Exemples pratiques

### Énumération basique

```bash
# Énumérer sous-domaines
amass enum -d example.com

# Avec sources passives uniquement
amass enum -d example.com -passive

# Active enumeration (DNS bruteforce)
amass enum -d example.com -active -brute
```

### Output formats

```bash
# JSON output
amass enum -d example.com -json results.json

# Lister résultats
amass enum -d example.com -o subdomains.txt
```

### Intel gathering

```bash
# Trouver ASN/CIDR
amass intel -asn 15169  # Google ASN

# Reverse WHOIS
amass intel -whois -d example.com
```

---

## Ressources

- [Amass GitHub](https://github.com/owasp-amass/amass)
- [Amass User Guide](https://github.com/owasp-amass/amass/blob/master/doc/user_guide.md)
