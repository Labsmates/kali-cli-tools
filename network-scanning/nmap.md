# 🌐 Nmap - Network Scanner

## Description

Nmap (Network Mapper) est LE scanner réseau de référence pour :
- Découverte d'hôtes (ping scan)
- Scan de ports (TCP/UDP)
- Détection d'OS et services
- Détection de vulnérabilités (NSE scripts)
- Cartographie de réseaux

---

## Installation

✅ **Déjà installé dans Kali Headless**

```bash
# Vérifier l'installation
nmap --version
```

---

## Syntaxe de base

```bash
nmap [Scan Type] [Options] {target}
```

---

## Exemples pratiques

### Exemple 1 : Scan de base

```bash
# Scan simple (1000 ports les plus courants)
nmap 192.168.1.100

# Scan d'un subnet complet
nmap 192.168.1.0/24

# Scan de plusieurs hôtes
nmap 192.168.1.1 192.168.1.10 192.168.1.50

# Scan d'une plage
nmap 192.168.1.1-50

# Scan depuis un fichier de cibles
nmap -iL targets.txt
```

**Résultat typique :**
```
Starting Nmap 7.94 ( https://nmap.org )
Nmap scan report for 192.168.1.100
Host is up (0.0010s latency).
Not shown: 997 closed ports
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
443/tcp open  https

Nmap done: 1 IP address (1 host up) scanned in 0.15 seconds
```

---

### Exemple 2 : Types de scan

```bash
# TCP SYN scan (par défaut, rapide, furtif)
nmap -sS 192.168.1.100

# TCP Connect scan (pas de privilèges root requis)
nmap -sT 192.168.1.100

# UDP scan (lent mais important)
nmap -sU 192.168.1.100

# Scan tous les ports TCP (0-65535)
nmap -p- 192.168.1.100

# Scan ports spécifiques
nmap -p 22,80,443,3306,8080 192.168.1.100

# Top 100 ports les plus courants
nmap --top-ports 100 192.168.1.100
```

---

### Exemple 3 : Détection de services et OS

```bash
# Détection de version des services
nmap -sV 192.168.1.100

# Détection de l'OS
nmap -O 192.168.1.100

# Combinaison agressive (OS + services + scripts + traceroute)
nmap -A 192.168.1.100

# Scan agressif avec tous les ports
nmap -A -p- 192.168.1.100
```

**Résultat avec -sV :**
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
```

---

### Exemple 4 : NSE Scripts (Nmap Scripting Engine)

```bash
# Scripts par défaut
nmap --script=default 192.168.1.100

# Script de vulnérabilités
nmap --script=vuln 192.168.1.100

# Script spécifique (SMB)
nmap --script=smb-os-discovery 192.168.1.100

# Tous les scripts SMB
nmap --script=smb-* 192.168.1.100

# Multiples catégories
nmap --script=vuln,exploit 192.168.1.100

# Lister tous les scripts disponibles
ls /usr/share/nmap/scripts/
nmap --script-help=vuln
```

**Scripts utiles :**
- `http-enum` : Énumération de répertoires web
- `ssl-cert` : Informations certificat SSL
- `smb-vuln-*` : Vulnérabilités SMB (EternalBlue, etc.)
- `ftp-anon` : Test FTP anonymous
- `mysql-empty-password` : Test MySQL sans mot de passe

---

### Exemple 5 : Scan furtif (Stealth)

```bash
# SYN scan (furtif, pas de connexion complète)
nmap -sS 192.168.1.100

# Fragment les paquets (éviter IDS/IPS)
nmap -f 192.168.1.100

# Utiliser des leurres (decoy)
nmap -D RND:10 192.168.1.100
# Génère 10 IPs aléatoires pour masquer la vraie source

# Spoof MAC address
nmap --spoof-mac Apple 192.168.1.100

# Randomize order des hôtes
nmap --randomize-hosts 192.168.1.0/24

# Délai entre les paquets (slow scan)
nmap --scan-delay 1s 192.168.1.100
nmap -T0 192.168.1.100  # Paranoid (très lent)
```

---

### Exemple 6 : Découverte d'hôtes (Ping Scan)

```bash
# Ping scan uniquement (pas de scan de ports)
nmap -sn 192.168.1.0/24

# Désactiver le ping (scan même si hôte semble down)
nmap -Pn 192.168.1.100

# Différents types de ping
nmap -PE 192.168.1.0/24  # ICMP Echo
nmap -PP 192.168.1.0/24  # ICMP Timestamp
nmap -PM 192.168.1.0/24  # ICMP Netmask
```

---

## Timing et Performance

```bash
# Templates de timing (-T0 à -T5)
nmap -T0 192.168.1.100  # Paranoid (IDS evasion)
nmap -T1 192.168.1.100  # Sneaky
nmap -T2 192.168.1.100  # Polite
nmap -T3 192.168.1.100  # Normal (défaut)
nmap -T4 192.168.1.100  # Aggressive (recommandé)
nmap -T5 192.168.1.100  # Insane (très rapide, peut perdre résultats)

# Parallélisation
nmap --min-parallelism 100 192.168.1.0/24

# Timeout personnalisé
nmap --host-timeout 10m 192.168.1.0/24
```

---

## Formats de sortie

```bash
# Output normal (écran)
nmap 192.168.1.100

# Sauvegarder résultats
nmap -oN scan_results.txt 192.168.1.100         # Normal
nmap -oX scan_results.xml 192.168.1.100         # XML
nmap -oG scan_results.grep 192.168.1.100        # Grepable
nmap -oA scan_results 192.168.1.100             # Tous les formats

# Affichage verbeux
nmap -v 192.168.1.100     # Verbose
nmap -vv 192.168.1.100    # Plus verbose
nmap -d 192.168.1.100     # Debug
```

---

## Cas d'usage réels

### Scénario 1 : Audit réseau d'entreprise

**Contexte :** Découvrir tous les hôtes et services d'un réseau.

```bash
# 1. Découverte d'hôtes actifs
nmap -sn 10.0.0.0/24 -oA discovery

# 2. Extraire les IPs actives
grep "Up" discovery.gnmap | cut -d" " -f2 > live_hosts.txt

# 3. Scan complet des hôtes actifs
nmap -A -p- -iL live_hosts.txt -oA full_scan

# 4. Scan des vulnérabilités
nmap --script=vuln -iL live_hosts.txt -oA vuln_scan
```

---

### Scénario 2 : Test de sécurité web server

**Contexte :** Auditer un serveur web.

```bash
# Scan web complet
nmap -p 80,443,8080,8443 \
  --script=http-enum,http-headers,http-methods,http-title,ssl-cert,ssl-enum-ciphers \
  192.168.1.100 -oA web_audit

# Vérifier CVE connus
nmap -p 80,443 --script=vulners 192.168.1.100
```

---

### Scénario 3 : Détecter EternalBlue (MS17-010)

**Contexte :** Vérifier vulnérabilité SMB sur le réseau.

```bash
# Scan SMB avec détection EternalBlue
nmap -p 445 --script=smb-vuln-ms17-010 192.168.1.0/24 -oA eternalblue_scan

# Résultat si vulnérable :
# | smb-vuln-ms17-010: 
# |   VULNERABLE:
# |   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
```

---

## Commandes utiles

```bash
# Afficher les interfaces réseau
nmap --iflist

# Test de connexion à une cible
nmap --packet-trace 192.168.1.100

# Utiliser un script personnalisé
nmap --script=/path/to/custom_script.nse 192.168.1.100

# Mettre à jour la base NSE
nmap --script-updatedb

# Scan depuis une interface spécifique
nmap -e eth0 192.168.1.100

# IPv6 scan
nmap -6 fe80::1
```

---

## NSE Scripts par catégorie

```bash
# Auth : Tests d'authentification
nmap --script=auth 192.168.1.100

# Broadcast : Découverte par broadcast
nmap --script=broadcast

# Brute : Brute-force
nmap --script=brute 192.168.1.100

# Default : Scripts par défaut
nmap --script=default 192.168.1.100

# Discovery : Découverte avancée
nmap --script=discovery 192.168.1.100

# Dos : Tests de déni de service
nmap --script=dos 192.168.1.100

# Exploit : Exploits actifs (ATTENTION!)
nmap --script=exploit 192.168.1.100

# Fuzzer : Fuzzing
nmap --script=fuzzer 192.168.1.100

# Intrusive : Scripts intrusifs
nmap --script=intrusive 192.168.1.100

# Safe : Scripts sûrs (pas d'impact)
nmap --script=safe 192.168.1.100

# Vuln : Détection de vulnérabilités
nmap --script=vuln 192.168.1.100
```

---

## Astuces

### Combiner avec grep

```bash
# Trouver tous les serveurs web
nmap -p 80,443 192.168.1.0/24 -oG - | grep "open"

# Compter les hôtes actifs
nmap -sn 192.168.1.0/24 -oG - | grep "Up" | wc -l
```

### Utiliser avec Metasploit

```bash
# Importer résultats Nmap dans Metasploit
msfconsole
msf6 > db_import scan_results.xml
msf6 > hosts
msf6 > services
```

### Scan via Tor (anonymat)

```bash
# Installer proxychains
apt install proxychains4

# Configurer Tor
service tor start

# Scanner via Tor
proxychains nmap -sT -Pn 192.168.1.100
```

---

## Ressources

- [Documentation Nmap officielle](https://nmap.org/book/man.html)
- [NSE Scripts Library](https://nmap.org/nsedoc/)
- [Nmap Cheat Sheet](https://www.stationx.net/nmap-cheat-sheet/)

---

## ⚠️ Avertissement légal

**Scan réseau = potentiellement illégal sans autorisation**

**Autorisé :**
- Votre propre réseau
- Avec autorisation écrite du propriétaire
- Environnements de lab/CTF

**Interdit :**
- Réseaux publics/tiers sans permission
- Internet scan massif
- Scan de cibles non autorisées

**Conséquences :** Poursuites judiciaires + amendes + prison
