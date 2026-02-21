# 📡 Wifite - Automated WiFi Attacking Tool

## Description

Wifite automatise les attaques WiFi (successor de Wifite2) :
- **WEP/WPA/WPA2/WPS** cracking
- Automatise Aircrack-ng/Reaver/Bully
- Interface interactive simple
- Gestion des cibles multiple

---

## Installation

✅ **Déjà installé dans Kali Headless**

```bash
wifite --help
```

---

## Exemples pratiques

### Mode automatique

```bash
# Lancer Wifite (scanner + attaque automatique)
sudo wifite

# Workflow :
# 1. Scan réseaux WiFi
# 2. Sélectionner cible
# 3. Capture handshake
# 4. Crack automatique avec wordlist
```

### Options spécifiques

```bash
# Cibler seulement WPA
sudo wifite --wpa

# Avec wordlist custom
sudo wifite --dict /usr/share/wordlists/rockyou.txt

# WPS attack uniquement
sudo wifite --wps

# Limiter scan (5 cibles max)
sudo wifite --scan-limit 5
```

### Kill mode (deauth clients)

```bash
# Deauth tous les clients (forcer reconnexion)
sudo wifite --kill
```

---

## Ressources

- [Wifite GitHub](https://github.com/derv82/wifite2)

---

## ⚠️ AVERTISSEMENT

**Attaques WiFi automatisées = ILLÉGALES sans autorisation.**  
**Usage uniquement sur votre propre réseau.**
