# 🐉 Kali Linux CLI Tools - Guide Complet

Guide pratique et exemples d'utilisation des principaux outils de pentest disponibles dans Kali Linux Headless.

> ⚠️ **DISCLAIMER LÉGAL**  
> Ces outils sont destinés **uniquement à des tests autorisés** sur vos propres systèmes ou avec permission écrite explicite.  
> L'utilisation non autorisée est **illégale** et peut entraîner des poursuites judiciaires.

---

## 📚 Table des matières

1. [Exploitation](#exploitation)
2. [Password Cracking](#password-cracking)
3. [Réseau & Scanning](#réseau--scanning)
4. [Web Testing](#web-testing)
5. [Active Directory](#active-directory)
6. [Reconnaissance](#reconnaissance)
7. [Wireless](#wireless)

---

## 🎯 Exploitation

### Metasploit Framework

**Description :** Framework d'exploitation modulaire le plus utilisé au monde.

**Installation incluse :** ✅ (msfconsole, msfvenom)

**[→ Guide complet](./exploitation/metasploit.md)**

### SQLMap

**Description :** Outil automatisé de détection et exploitation d'injections SQL.

**[→ Guide complet](./exploitation/sqlmap.md)**

### Impacket

**Description :** Collection de scripts Python pour travailler avec les protocoles réseau Windows.

**[→ Guide complet](./exploitation/impacket.md)**

---

## 🔓 Password Cracking

### John the Ripper

**Description :** Cracker de mots de passe multi-format.

**[→ Guide complet](./password-cracking/john.md)**

### Hashcat

**Description :** Cracker de mots de passe par GPU ultra-rapide.

**[→ Guide complet](./password-cracking/hashcat.md)**

### Hydra

**Description :** Brute-force parallèle de services réseau (SSH, FTP, HTTP, etc.).

**[→ Guide complet](./password-cracking/hydra.md)**

---

## 🌐 Réseau & Scanning

### Nmap

**Description :** Scanner de ports et découverte réseau.

**[→ Guide complet](./network-scanning/nmap.md)**

### Masscan

**Description :** Scanner de ports ultra-rapide (scan Internet en 6 minutes).

**[→ Guide complet](./network-scanning/masscan.md)**

### Nikto

**Description :** Scanner de vulnérabilités web.

**[→ Guide complet](./network-scanning/nikto.md)**

### Gobuster

**Description :** Brute-force de répertoires/fichiers/DNS/vhosts.

**[→ Guide complet](./network-scanning/gobuster.md)**

---

## 🕸️ Web Testing

### WPScan

**Description :** Scanner de vulnérabilités WordPress.

**[→ Guide complet](./web-testing/wpscan.md)**

### Burp Suite

**Description :** Proxy d'interception HTTP pour tests web.

**[→ Guide complet](./web-testing/burpsuite.md)**

### Commix

**Description :** Détection et exploitation d'injections de commandes.

**[→ Guide complet](./web-testing/commix.md)**

### Wfuzz

**Description :** Fuzzer web pour injection de payloads.

**[→ Guide complet](./web-testing/wfuzz.md)**

---

## 🏢 Active Directory

### BloodHound.py

**Description :** Cartographie et analyse des chemins d'attaque AD.

**[→ Guide complet](./active-directory/bloodhound.md)**

### Evil-WinRM

**Description :** Shell WinRM pour exploitation Windows.

**[→ Guide complet](./active-directory/evil-winrm.md)**

### Responder

**Description :** Empoisonnement LLMNR/NBT-NS/mDNS.

**[→ Guide complet](./active-directory/responder.md)**

### Netexec

**Description :** Outil post-exploitation réseau (successeur de CrackMapExec).

**[→ Guide complet](./active-directory/netexec.md)**

---

## 🔍 Reconnaissance

### Amass

**Description :** Énumération de sous-domaines et cartographie réseau.

**[→ Guide complet](./reconnaissance/amass.md)**

### TheHarvester

**Description :** OSINT - Récolte d'emails, noms, IPs, URLs.

**[→ Guide complet](./reconnaissance/theharvester.md)**

### Recon-ng

**Description :** Framework de reconnaissance modulaire.

**[→ Guide complet](./reconnaissance/recon-ng.md)**

### SpiderFoot

**Description :** OSINT automatisé multi-sources.

**[→ Guide complet](./reconnaissance/spiderfoot.md)**

---

## 📡 Wireless

### Aircrack-ng

**Description :** Suite complète pour auditer les réseaux Wi-Fi.

**[→ Guide complet](./wireless/aircrack-ng.md)**

### Wifite

**Description :** Automatisation d'attaques Wi-Fi.

**[→ Guide complet](./wireless/wifite.md)**

---

## 🚀 Démarrage rapide

```bash
# Accès au container Kali
ssh user@10.10.10.1
docker exec -it kali-linux /bin/bash

# Vérifier un outil
which nmap
nmap --version

# Mettre à jour la base de données (recommandé)
apt update && apt upgrade -y
```

---

## 📖 Ressources

- [Documentation Kali officielle](https://www.kali.org/docs/)
- [Kali Tools](https://www.kali.org/tools/)
- [HackTheBox Academy](https://academy.hackthebox.com/)
- [TryHackMe](https://tryhackme.com/)

---

## 📝 Contribution

Les pull requests sont les bienvenues ! Chaque guide doit inclure :
- Description de l'outil
- Cas d'usage typiques
- Exemples pratiques commentés
- Avertissements légaux si nécessaire

---

## 📄 Licence

MIT License - Utilisation éducative et éthique uniquement.

**Auteur :** Wilfrid (Labsmates)  
**Date :** Février 2026
