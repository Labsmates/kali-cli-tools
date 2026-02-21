# 🚀 Masscan - Ultra-Fast Port Scanner

## Description

Masscan est le scanner de ports **le plus rapide au monde** :
- **6 minutes** pour scanner tout Internet (IPv4)
- **10 millions de paquets/seconde** possible
- Syntaxe compatible **Nmap**
- Utilise son propre **stack TCP/IP** (pas l'OS)

**Use case :** Scanner rapidement de très larges plages d'IPs.

---

## Installation

✅ **Déjà installé dans Kali Headless**

```bash
# Vérifier l'installation
masscan --version
```

---

## Exemples pratiques

### Exemple 1 : Scan basique

```bash
# Scanner port 80 sur un subnet
masscan 192.168.1.0/24 -p80

# Scanner plusieurs ports
masscan 192.168.1.0/24 -p22,80,443,8080

# Scanner plage de ports
masscan 192.168.1.0/24 -p1-1000
```

---

### Exemple 2 : Scan ultra-rapide

```bash
# Vitesse maximale (10M paquets/sec)
masscan 10.0.0.0/8 -p80,443 --rate 10000000

# Vitesse modérée (éviter saturation réseau)
masscan 192.168.0.0/16 -p80 --rate 100000
```

---

### Exemple 3 : Scan de tout Internet

```bash
# Scanner port 22 (SSH) sur tout Internet IPv4 ⚠️
masscan 0.0.0.0/0 -p22 --rate 1000000 --exclude 255.255.255.255

# Exclure plages privées
masscan 0.0.0.0/0 -p80 --rate 1000000 \
  --exclude 10.0.0.0/8 \
  --exclude 192.168.0.0/16 \
  --exclude 172.16.0.0/12
```

---

### Exemple 4 : Output formats

```bash
# Format liste
masscan 192.168.1.0/24 -p80 -oL results.txt

# Format XML (importable dans Nmap/Metasploit)
masscan 192.168.1.0/24 -p80 -oX results.xml

# Format JSON
masscan 192.168.1.0/24 -p80 -oJ results.json

# Format grepable
masscan 192.168.1.0/24 -p80 -oG results.grep
```

---

### Exemple 5 : Banner grabbing

```bash
# Capturer bannières (version services)
masscan 192.168.1.0/24 -p80,443 --banners

# Résultat :
# Discovered open port 80/tcp on 192.168.1.100
# Banner on port 80/tcp: HTTP/1.1 200 OK\r\nServer: nginx/1.18.0
```

---

### Exemple 6 : Configuration file

```bash
# Créer fichier config
cat > masscan.conf <<EOF
rate = 100000
ports = 80,443,8080
output-format = list
output-filename = scan.txt
EOF

# Utiliser config
masscan 192.168.1.0/24 -c masscan.conf
```

---

## Options importantes

```bash
# Vitesse
--rate 10000        # Paquets/seconde (défaut: 100)
--max-rate 10000    # Limite max

# Ports
-p 80,443           # Ports spécifiques
-p 1-65535          # Tous les ports
--top-ports 100     # Top 100 ports

# Exclusions
--exclude 192.168.1.1           # Exclure IP
--excludefile exclude.txt        # Fichier d'exclusion

# Interface réseau
-e eth0             # Interface spécifique
--adapter-ip 192.168.1.50  # IP source
--adapter-mac 00:11:22:33:44:55  # MAC source

# Bannières
--banners           # Capturer bannières
--http-user-agent "Mozilla/5.0"  # User-Agent custom

# Randomisation
--seed 1234         # Seed pour randomisation (reproductible)
```

---

## Cas d'usage réels

### Scénario 1 : Découverte rapide de serveurs web

```bash
# Scanner tout le /16 pour HTTP/HTTPS
masscan 10.0.0.0/16 -p80,443,8080,8443 --rate 50000 -oJ web_servers.json

# Parser résultats
cat web_servers.json | jq -r '.[] | "\(.ip):\(.ports[0].port)"'
```

---

### Scénario 2 : Scan Internet pour vulnérabilité

```bash
# Trouver serveurs Elasticsearch exposés
masscan 0.0.0.0/0 -p9200 --rate 1000000 \
  --exclude 10.0.0.0/8 \
  --exclude 192.168.0.0/16 \
  -oL elasticsearch_public.txt

# Vérifier vulnérabilité
while read ip; do
    curl -s http://$ip:9200/_cat/indices
done < elasticsearch_public.txt
```

---

## Combiner avec Nmap

```bash
# 1. Masscan pour découverte rapide
masscan 10.0.0.0/16 -p1-65535 --rate 100000 -oL masscan_results.txt

# 2. Extraire IPs avec ports ouverts
grep "open" masscan_results.txt | awk '{print $4}' | sort -u > live_hosts.txt

# 3. Nmap pour scan détaillé
nmap -sV -A -iL live_hosts.txt -oA nmap_detailed
```

---

## Astuces

### Éviter les bans

```bash
# Réduire rate
masscan 192.168.1.0/24 -p80 --rate 1000

# Randomiser ordre de scan
masscan 192.168.1.0/24 -p80 --randomize-hosts

# Source IP spoofing (⚠️ attention)
masscan 192.168.1.0/24 -p80 --adapter-ip 1.2.3.4
```

### Reprendre scan interrompu

```bash
# Sauvegarder état
masscan 0.0.0.0/0 -p80 --rate 1000000 --resume paused.conf

# Reprendre
masscan --resume paused.conf
```

---

## Ressources

- [Masscan GitHub](https://github.com/robertdavidgraham/masscan)
- [Masscan Documentation](https://github.com/robertdavidgraham/masscan/blob/master/doc/masscan.8.markdown)

---

## ⚠️ AVERTISSEMENT CRITIQUE

**Masscan peut :**
- Saturer votre réseau (DoS involontaire)
- Déclencher des IDS/IPS
- Être considéré comme une attaque

**Scanner Internet = ILLÉGAL sans autorisation**

**Usage légitime uniquement :**
- Votre propre réseau
- Pentests autorisés
- Recherche académique avec permission

**Ne JAMAIS scanner :**
- Internet public (illégal)
- Réseaux tiers
- Sans autorisation écrite
