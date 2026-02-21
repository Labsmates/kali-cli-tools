# 🔀 Wfuzz - Web Application Fuzzer

## Description

Wfuzz est un fuzzer web pour injection de payloads dans requêtes HTTP.

**Use cases :**
- Brute-force parameters
- Fuzzing headers/cookies
- Directory discovery
- IDOR testing
- Auth bypass

---

## Installation

✅ **Déjà installé dans Kali Headless**

```bash
wfuzz --version
```

---

## Exemples pratiques

### Directory fuzzing

```bash
# Fuzzer directories
wfuzz -c -z file,/usr/share/wordlists/dirb/common.txt \
  http://example.com/FUZZ

# -c : couleurs
# -z file,wordlist : source de payloads
# FUZZ : position d'injection
```

### Parameter fuzzing

```bash
# Fuzzer paramètre GET
wfuzz -c -z file,wordlist.txt \
  http://example.com/page.php?id=FUZZ

# POST data
wfuzz -c -z file,wordlist.txt -d "username=admin&password=FUZZ" \
  http://example.com/login.php
```

### Filtrer résultats

```bash
# Cacher réponses 404
wfuzz -c -z file,wordlist.txt --hc 404 http://example.com/FUZZ

# Montrer seulement code 200
wfuzz -c -z file,wordlist.txt --sc 200 http://example.com/FUZZ

# Filtrer par taille de réponse
wfuzz -c -z file,wordlist.txt --hh 5000 http://example.com/FUZZ
```

### Multiple injection points

```bash
# 2 points d'injection
wfuzz -c -z file,users.txt -z file,pass.txt \
  -d "user=FUZZ&pass=FUZ2Z" \
  http://example.com/login.php
```

---

## Ressources

- [Wfuzz Documentation](https://wfuzz.readthedocs.io/)
- [Wfuzz GitHub](https://github.com/xmendez/wfuzz)
