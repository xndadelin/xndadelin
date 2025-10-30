---
title: 'pydecodr'
pubDate: '2025-10-31'
---

# pydecodr
> A modular CTF/crypto library toolkit for encodings, classic ciphers, and autodetection. CLI included :3.


`pydecodr` is a Python package that lets you **encode, decode, encrypt or decrypt text** using classical, polyalphabetic, modern, and stream ciphers, all through a **Python API** and a **CLI interface**

---
## Features:
- classical ciphers: (caesar, atbash, affine, rot13, substitution)
- polyalphabetic: (vigenere, autokey_vigenere, beaufort, playfair)
- fractionation: (bifid, ADFGX)
- transpos9ition: (railfence, columnar)
- stream: (xor, repeating xor, rc4)
- modern: (aes, rsa, hash utilities)
- encodings: (b32, b64, hex, URL-safe)
- detection & utils: (file magic detection, I/O helpers)
- cli interface

---

## Installation
```bash
pip install pydecodr
```

--- 

## Usage
### CLI interface
You can use any cipher module directly with Python's `-m` flag:

```python
# caesar cipher
python3 -m pydecodr.ciphers.classical.caesar encrypt "HELLO" 3
# -> KHOOR

python3 -m pydecodr.ciphers.classical.caesar decrypt "KHOOR" 3
# -> HELLO
```

### Python API
```python
from pydecodr.ciphers.classical import caesar
from pydecodr.ciphers.polyalphabetic import vigenere

print(caesar.encrypt("HELLO", 3))
# KHOOR

print(vigenere.encrypt("HELLOWORLD", "KEY"))
# RIJVSUYVJN
```
Or load dynamically using the global registry:
```python
from pydecodr import load_module

mod = load_module("adfgx")
ciphertext = mod.encrypt("DEFEND THE EAST WALL", "FORTIFICATION", "CIPHER")
print(mod.decrypt(ciphertext, "FORTIFICATION", "CIPHER"))
```
# decodr CLI & code usage reference

This document shows how to use each cipher and module from the command line and from Python code.

---

## classical ciphers

### affine

CLI:

```
python3 -m decodr.ciphers.classical.affine encrypt <text> [a] [b]
python3 -m decodr.ciphers.classical.affine decrypt <text> [a] [b]
python3 -m decodr.ciphers.classical.affine crack <ciphertext>
```

Python:

```python
from decodr.ciphers.classical.affine import encrypt, decrypt, crack
cipher = encrypt("HELLO", a=5, b=8)
plain = decrypt(cipher, a=5, b=8)
guesses = crack(cipher)
```

---

### atbash

CLI:

```
python3 -m decodr.ciphers.classical.atbash <encode|decode> <text>
```

Python:

```python
from decodr.ciphers.classical.atbash import encode, decode
cipher = encode("HELLO")
plain = decode(cipher)
```

---

### caesar

CLI:

```
python3 -m decodr.ciphers.classical.caesar encrypt <text> [shift]
python3 -m decodr.ciphers.classical.caesar decrypt <text> [shift]
python3 -m decodr.ciphers.classical.caesar crack <text>
```

Python:

```python
from decodr.ciphers.classical.caesar import encrypt, decrypt, crack
cipher = encrypt("HELLO", shift=3)
plain = decrypt(cipher, shift=3)
possible = crack(cipher)
```

---

### rot13

CLI:

```
python3 -m decodr.ciphers.classical.rot13 <encode|decode> <text>
```

Python:

```python
from decodr.ciphers.classical.rot13 import encode, decode
cipher = encode("HELLO")
plain = decode(cipher)
```

---

### substitution

CLI:

```
python3 -m decodr.ciphers.classical.substitution generate-key
python3 -m decodr.ciphers.classical.substitution encrypt <text> <key>
python3 -m decodr.ciphers.classical.substitution decrypt <text> <key>
```

Python:

```python
from decodr.ciphers.classical.substitution import generate_key, encrypt, decrypt
key = generate_key()
cipher = encrypt("HELLO", key)
plain = decrypt(cipher, key)
```

---

## fractionation ciphers

### adfgx

CLI:

```
python3 -m decodr.ciphers.fractionation.adfgx encrypt <text> <square_key> <trans_key> [pad]
python3 -m decodr.ciphers.fractionation.adfgx decrypt <text> <square_key> <trans_key> [pad]
```

Python:

```python
from decodr.ciphers.fractionation.adfgx import encrypt, decrypt
cipher = encrypt("DEFEND THE CASTLE", "FORTIFICATION", "CIPHER", pad="X")
plain = decrypt(cipher, "FORTIFICATION", "CIPHER", pad="X")
```

---

### bifid

CLI:

```
python3 -m decodr.ciphers.fractionation.bifid encrypt <text> [key] [period]
python3 -m decodr.ciphers.fractionation.bifid decrypt <text> [key] [period]
```

Python:

```python
from decodr.ciphers.fractionation.bifid import encrypt, decrypt
cipher = encrypt("HELLO", key="KEYWORD", period=5)
plain = decrypt(cipher, key="KEYWORD", period=5)
```

---

## modern ciphers

### aes

CLI:

```
python3 -m decodr.ciphers.modern.aes encrypt <text> <key>
python3 -m decodr.ciphers.modern.aes decrypt <base64_iv_plus_ct> <key>
```

Python:

```python
from decodr.ciphers.modern.aes import encrypt, decrypt
cipher = encrypt("HELLO WORLD", "mysecretkey123")
plain = decrypt(cipher, "mysecretkey123")
```

---

### hashes

CLI:

```
python3 -m decodr.ciphers.modern.hashes hash <text> [algo]
python3 -m decodr.ciphers.modern.hashes verify <text> <digest>
```

Python:

```python
from decodr.ciphers.modern.hashes import hash_text, verify
digest = hash_text("HELLO", algo="sha256")
valid = verify("HELLO", digest)
```

---

### rsa

CLI:

```
python3 -m decodr.ciphers.modern.rsa gen <p> <q> [e]
python3 -m decodr.ciphers.modern.rsa encrypt <text> <n> <e>
python3 -m decodr.ciphers.modern.rsa decrypt <cipher> <n> <d>
```

Python:

```python
from decodr.ciphers.modern.rsa import gen_keys, encrypt, decrypt
n, e, d = gen_keys(61, 53)
cipher = encrypt("HELLO", n, e)
plain = decrypt(cipher, n, d)
```

---

## polyalphabetic ciphers

### autokey vigenere

CLI:

```
python3 -m decodr.ciphers.polyalphabetic.autokey_vigenere encrypt <text> <key>
python3 -m decodr.ciphers.polyalphabetic.autokey_vigenere decrypt <text> <key>
```

Python:

```python
from decodr.ciphers.polyalphabetic.autokey_vigenere import encrypt, decrypt
cipher = encrypt("HELLO", "KEY")
plain = decrypt(cipher, "KEY")
```

---

### beaufort

CLI:

```
python3 -m decodr.ciphers.polyalphabetic.beaufort encrypt <text> <key>
python3 -m decodr.ciphers.polyalphabetic.beaufort decrypt <text> <key>
```

Python:

```python
from decodr.ciphers.polyalphabetic.beaufort import encrypt, decrypt
cipher = encrypt("HELLO", "KEY")
plain = decrypt(cipher, "KEY")
```

---

### playfair

CLI:

```
python3 -m decodr.ciphers.polyalphabetic.playfair encrypt <text> <key>
python3 -m decodr.ciphers.polyalphabetic.playfair decrypt <text> <key>
```

Python:

```python
from decodr.ciphers.polyalphabetic.playfair import encrypt, decrypt
cipher = encrypt("HELLO", "SECRET")
plain = decrypt(cipher, "SECRET")
```

---

### vigenere

CLI:

```
python3 -m decodr.ciphers.polyalphabetic.vigenere encrypt <text> <key>
python3 -m decodr.ciphers.polyalphabetic.vigenere decrypt <text> <key>
```

Python:

```python
from decodr.ciphers.polyalphabetic.vigenere import encrypt, decrypt
cipher = encrypt("HELLO", "KEY")
plain = decrypt(cipher, "KEY")
```

---

## rotor cipher

### enigma

CLI:

```
python3 -m decodr.ciphers.rotor.enigma encrypt <text>
python3 -m decodr.ciphers.rotor.enigma decrypt <text>
```

Python:

```python
from decodr.ciphers.rotor.enigma import encrypt, decrypt
cipher = encrypt("HELLO")
plain = decrypt(cipher)
```

---

## stream ciphers

### rc4

CLI:

```
python3 -m decodr.ciphers.stream.rc4 encrypt <text> <key> <encoding>
python3 -m decodr.ciphers.stream.rc4 decrypt <hex> <key> <encoding>
```

Python:

```python
from decodr.ciphers.stream.rc4 import encrypt, decrypt
cipher = encrypt("HELLO", "KEY", encoding="hex")
plain = decrypt(cipher, "KEY", encoding="hex")
```

---

### repeating xor

CLI:

```
python3 -m decodr.ciphers.stream.repeating_xor encrypt <text> <key> <encoding>
python3 -m decodr.ciphers.stream.repeating_xor decrypt <hex> <key> <encoding>
```

Python:

```python
from decodr.ciphers.stream.repeating_xor import encrypt, decrypt
cipher = encrypt("HELLO", "KEY", "hex")
plain = decrypt(cipher, "KEY", "hex")
```

---

### xor

CLI:

```
python3 -m decodr.ciphers.stream.xor encrypt <text> <key> <encoding>
python3 -m decodr.ciphers.stream.xor decrypt <hex> <key> <encoding>
```

Python:

```python
from decodr.ciphers.stream.xor import encrypt, decrypt
cipher = encrypt("HELLO", "KEY", "hex")
plain = decrypt(cipher, "KEY", "hex")
```

---

## transposition ciphers

### columnar

CLI:

```
python3 -m decodr.ciphers.transposition.columnar encrypt <text> <key> [pad]
python3 -m decodr.ciphers.transposition.columnar decrypt <text> <key> [pad]
```

Python:

```python
from decodr.ciphers.transposition.columnar import encrypt, decrypt
cipher = encrypt("HELLO WORLD", "KEY", pad="X")
plain = decrypt(cipher, "KEY", pad="X")
```

---

### railfence

CLI:

```
python3 -m decodr.ciphers.transposition.railfence encrypt <text> [rails]
python3 -m decodr.ciphers.transposition.railfence decrypt <text> [rails]
```

Python:

```python
from decodr.ciphers.transposition.railfence import encrypt, decrypt
cipher = encrypt("HELLO", rails=3)
plain = decrypt(cipher, rails=3)
```

---

## detectors

### autodetect

CLI:

```
python3 -m decodr.detectors.autodetect <string>
```

Python:

```python
from decodr.detectors.autodetect import detect
result = detect("SOMECIPHERTEXT")
```

---

### file magic

CLI:

```
python3 -m decodr.detectors.file_magic <file>
```

Python:

```python
from decodr.detectors.file_magic import identify
filetype = identify("mystery.bin")
```

---

## encodings

### base32

CLI:

```
python3 -m decodr.encodings.base32_mod <encode|decode> <text>
```

Python:

```python
from decodr.encodings.base32_mod import encode, decode
encoded = encode("HELLO")
decoded = decode(encoded)
```

### base64

CLI:

```
python3 -m decodr.encodings.base64_mod <encode|decode> <text>
```

Python:

```python
from decodr.encodings.base64_mod import encode, decode
encoded = encode("HELLO")
decoded = decode(encoded)
```

### hex

CLI:

```
python3 -m decodr.encodings.hex_mod <encode|decode> <text>
```

Python:

```python
from decodr.encodings.hex_mod import encode, decode
encoded = encode("HELLO")
decoded = decode(encoded)
```

### url

CLI:

```
python3 -m decodr.encodings.url_mod <encode|decode> <text>
```

Python:

```python
from decodr.encodings.url_mod import encode, decode
encoded = encode("HELLO WORLD")
decoded = decode(encoded)
```

---

## module map

```python
ENCODING_MAP = {
    "base64": "pydecodr.encodings.base64_mod",
    "base32": "pydecodr.encodings.base32_mod",
    "hex": "pydecodr.encodings.hex_mod",
    "url": "pydecodr.encodings.url_mod",

    "caesar": "pydecodr.ciphers.classical.caesar",
    "atbash": "pydecodr.ciphers.classical.atbash",
    "affine": "pydecodr.ciphers.classical.affine",
    "rot13": "pydecodr.ciphers.classical.rot13",
    "substitution": "pydecodr.ciphers.classical.substitution",

    "vigenere": "pydecodr.ciphers.polyalphabetic.vigenere",
    "autokey_vigenere": "pydecodr.ciphers.polyalphabetic.autokey_vigenere",
    "beaufort": "pydecodr.ciphers.polyalphabetic.beaufort",
    "playfair": "pydecodr.ciphers.polyalphabetic.playfair",

    "bifid": "pydecodr.ciphers.fractionation.bifid",
    "adfgx": "pydecodr.ciphers.fractionation.adfgx",

    "railfence": "pydecodr.ciphers.transposition.railfence",
    "columnar": "pydecodr.ciphers.transposition.columnar",

    "xor": "pydecodr.ciphers.stream.xor",
    "repeating_xor": "pydecodr.ciphers.stream.repeating_xor",
    "rc4": "pydecodr.ciphers.stream.rc4",

    "enigma": "pydecodr.ciphers.rotor.enigma",

    "aes": "pydecodr.ciphers.modern.aes",
    "rsa": "pydecodr.ciphers.modern.rsa",
    "hashes": "pydecodr.ciphers.modern.hashes",

    "fmt": "pydecodr.utils.fmt",
    "ioutils": "pydecodr.utils.ioutils"
}
```

