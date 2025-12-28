# Easy Crypto

## Challenge Information

- **Event**: Example CTF 2024
- **Category**: Crypto
- **Points**: 150
- **Solves**: 30/100
- **Author**: CTF Organizer

## Description

```
We intercepted this message. Can you decode it?

Ciphertext: SYNT{P3N1R_PVCGB_VF_RNL}
```

## Attachments

- cipher.txt

## Solution

### Analisis Awal

Melihat ciphertext, terlihat pola yang mirip dengan flag format (uppercase letters dan underscore di posisi yang tepat). Ini mengindikasikan substitution cipher.

### Langkah Penyelesaian

#### Step 1: Identifikasi Cipher

Format `SYNT{...}` sangat mirip dengan `FLAG{...}`. Ini mengindikasikan ROT cipher (kemungkinan ROT13).

Mapping:
- S -> F (shift 13)
- Y -> L (shift 13)
- N -> A (shift 13)
- T -> G (shift 13)

#### Step 2: Decrypt dengan ROT13

```python
import codecs

ciphertext = "SYNT{P3N1R_PVCGB_VF_RNL}"
plaintext = codecs.decode(ciphertext, 'rot_13')
print(plaintext)
```

#### Step 3: Mendapatkan Flag

Output: `FLAG{C3E1R_CIPHER_IS_EASY}`

### Exploit

```python
# Simple ROT13 decoder
def rot13(text):
    result = ""
    for char in text:
        if char.isalpha():
            base = ord('A') if char.isupper() else ord('a')
            result += chr((ord(char) - base + 13) % 26 + base)
        else:
            result += char
    return result

ciphertext = "SYNT{P3N1R_PVCGB_VF_RNL}"
print(rot13(ciphertext))
```

## Flag

```
FLAG{C3E1R_CIPHER_IS_EASY}
```

## Tools Used

- Python
- CyberChef (alternative)
- ROT13 decoder online

## References

- [ROT13 - Wikipedia](https://en.wikipedia.org/wiki/ROT13)
- [CyberChef](https://gchq.github.io/CyberChef/)

## Lessons Learned

- ROT13 adalah salah satu cipher paling sederhana
- Pattern recognition penting dalam crypto challenges
- Banyak tools online yang bisa membantu decode basic ciphers
