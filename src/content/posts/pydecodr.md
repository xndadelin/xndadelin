---
title: 'pydecodr'
pubDate: '2025-10-31'
---

[pydecodr](https://github.com/xndadelin/pydecodr) is a modular CTF/crypto toolkit i built for encodings, classic ciphers, and autodetection. it has a python API and a CLI. you can install it with `pip install pydecodr`.

i made this project during [moonshot](https://moonshot.hackclub.com/), a 4-day hackathon in florida visiting kennedy space center and universal studios!

---

## what it does

it lets you **encode, decode, encrypt, and decrypt** text using a bunch of cipher families — all from python or the terminal:

- **classical** — caesar, atbash, affine, rot13, substitution, hill, polybius, four-square, bacon
- **polyalphabetic** — vigenère, autokey vigenère, beaufort, playfair, gronsfeld
- **fractionation** — bifid, trifid, ADFGX, ADFGVX
- **transposition** — railfence, columnar, double transposition, myszkowski, route
- **stream** — xor, repeating xor, RC4
- **rotor** — enigma
- **modern** — AES, RSA, hash utilities
- **encodings** — base32, base64, base85, hex, URL-safe, morse
- **detection** — autodetect cipher type, file magic identification

---

## quick start

### python API

```python
from pydecodr.ciphers.classical import caesar
from pydecodr.ciphers.polyalphabetic import vigenere

caesar.encrypt("HELLO", 3)       # -> KHOOR
vigenere.encrypt("HELLO", "KEY") # -> RIJVS
```

you can also load any module dynamically through the global registry:

```python
from pydecodr import load_module

mod = load_module("adfgx")
ct = mod.encrypt("DEFEND THE EAST WALL", "FORTIFICATION", "CIPHER")
mod.decrypt(ct, "FORTIFICATION", "CIPHER")
```

### CLI

every module works with `python3 -m`:

```bash
python3 -m pydecodr.ciphers.classical.caesar encrypt "HELLO" 3
# -> KHOOR

python3 -m pydecodr.ciphers.classical.caesar decrypt "KHOOR" 3
# -> HELLO
```

---

## cipher reference

below is every supported cipher with its CLI usage and python API. the pattern is always the same — `encrypt`/`decrypt` (or `encode`/`decode` for encodings).

### classical ciphers

**caesar** — shift cipher with optional brute-force cracking

```python
from pydecodr.ciphers.classical.caesar import encrypt, decrypt, crack
encrypt("HELLO", shift=3)  # KHOOR
decrypt("KHOOR", shift=3)  # HELLO
crack("KHOOR")             # tries all 26 shifts
```
```bash
python3 -m pydecodr.ciphers.classical.caesar encrypt "HELLO" 3
python3 -m pydecodr.ciphers.classical.caesar crack "KHOOR"
```

**affine** — multiplicative + additive cipher with cracking support

```python
from pydecodr.ciphers.classical.affine import encrypt, decrypt, crack
encrypt("HELLO", a=5, b=8)
decrypt(cipher, a=5, b=8)
crack(cipher)
```
```bash
python3 -m pydecodr.ciphers.classical.affine encrypt "HELLO" 5 8
python3 -m pydecodr.ciphers.classical.affine crack "RCLLA"
```

**atbash** — mirror alphabet substitution

```python
from pydecodr.ciphers.classical.atbash import encode, decode
encode("HELLO")  # SVOOL
```

**rot13** — special case of caesar with shift=13

```python
from pydecodr.ciphers.classical.rot13 import encode, decode
encode("HELLO")  # URYYB
```

**substitution** — random key monoalphabetic substitution

```python
from pydecodr.ciphers.classical.substitution import generate_key, encrypt, decrypt
key = generate_key()
encrypt("HELLO", key)
```

---

### polyalphabetic ciphers

**vigenère** — repeating-key polyalphabetic cipher

```python
from pydecodr.ciphers.polyalphabetic.vigenere import encrypt, decrypt
encrypt("HELLO", "KEY")
```

**autokey vigenère** — vigenère variant where the plaintext extends the key

```python
from pydecodr.ciphers.polyalphabetic.autokey_vigenere import encrypt, decrypt
encrypt("HELLO", "KEY")
```

**beaufort** — reciprocal cipher (encrypt = decrypt)

```python
from pydecodr.ciphers.polyalphabetic.beaufort import encrypt, decrypt
encrypt("HELLO", "KEY")
```

**playfair** — digraph substitution cipher using a 5×5 matrix

```python
from pydecodr.ciphers.polyalphabetic.playfair import encrypt, decrypt
encrypt("HELLO", "SECRET")
```

---

### fractionation ciphers

**bifid** — combines polybius square with transposition

```python
from pydecodr.ciphers.fractionation.bifid import encrypt, decrypt
encrypt("HELLO", key="KEYWORD", period=5)
```

**ADFGX** — WWI german cipher combining substitution and columnar transposition

```python
from pydecodr.ciphers.fractionation.adfgx import encrypt, decrypt
encrypt("DEFEND THE CASTLE", "FORTIFICATION", "CIPHER", pad="X")
```

---

### transposition ciphers

**railfence** — zigzag transposition

```python
from pydecodr.ciphers.transposition.railfence import encrypt, decrypt
encrypt("HELLO WORLD", rails=3)
```

**columnar** — key-ordered column transposition

```python
from pydecodr.ciphers.transposition.columnar import encrypt, decrypt
encrypt("HELLO WORLD", "KEY", pad="X")
```

---

### stream ciphers

**xor / repeating xor** — byte-level XOR encryption

```python
from pydecodr.ciphers.stream.xor import encrypt, decrypt
encrypt("HELLO", "KEY", "hex")
```

**RC4** — stream cipher with key scheduling

```python
from pydecodr.ciphers.stream.rc4 import encrypt, decrypt
encrypt("HELLO", "KEY", encoding="hex")
```

---

### rotor cipher

**enigma** — simplified enigma machine simulation

```python
from pydecodr.ciphers.rotor.enigma import encrypt, decrypt
encrypt("HELLO")
```

---

### modern ciphers

**AES** — symmetric encryption (CBC mode, auto IV)

```python
from pydecodr.ciphers.modern.aes import encrypt, decrypt
ct = encrypt("HELLO WORLD", "mysecretkey123")
decrypt(ct, "mysecretkey123")
```

**RSA** — asymmetric encryption with key generation

```python
from pydecodr.ciphers.modern.rsa import gen_keys, encrypt, decrypt
n, e, d = gen_keys(61, 53)
ct = encrypt("HELLO", n, e)
decrypt(ct, n, d)
```

**hashes** — hashing and verification

```python
from pydecodr.ciphers.modern.hashes import hash_text, verify
digest = hash_text("HELLO", algo="sha256")
verify("HELLO", digest)  # True
```

---

### encodings

all encodings follow the same `encode`/`decode` pattern:

```python
from pydecodr.encodings.base64_mod import encode, decode
encode("HELLO")  # SEVMTE8=
decode("SEVMTE8=")  # HELLO
```

available: `base32_mod`, `base64_mod`, `hex_mod`, `url_mod`

```bash
python3 -m pydecodr.encodings.base64_mod encode "HELLO"
python3 -m pydecodr.encodings.hex_mod decode "48454c4c4f"
```

---

### detectors

**autodetect** — tries to identify what cipher was used

```python
from pydecodr.detectors.autodetect import detect
detect("SOMECIPHERTEXT")
```

**file magic** — identifies file types by their magic bytes

```python
from pydecodr.detectors.file_magic import identify
identify("mystery.bin")
```

---

## dynamic module loading

pydecodr has a global registry that maps short names to module paths, so you can load anything dynamically:

```python
from pydecodr import load_module

caesar = load_module("caesar")
caesar.encrypt("HELLO", 3)
```

this is useful for building tools on top of pydecodr where the cipher is selected at runtime — like in CTF solvers or automated pipelines.

---

check out the [source code](https://github.com/xndadelin/pydecodr) or install it with `pip install pydecodr` and play around!

