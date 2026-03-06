# PDF Signer – Mga kailangan i-install (list at commands)

Para gumana ang **PDF Sign** (search matches, lagay signature, preview): system packages, PHP extensions, Composer packages, at config.

---

## 1. System: Poppler (pdftotext, pdftoppm)

Ginagamit para sa:
- **Text search sa PDF** (bbox / exact position)
- **Preview** (pdftoppm)

### Linux (Debian / Ubuntu)

```bash
sudo apt update
sudo apt install -y poppler-utils
```

### Linux (RHEL / CentOS / Fedora)

```bash
sudo yum install -y poppler-utils
```

o (newer RHEL/Fedora):

```bash
sudo dnf install -y poppler-utils
```

### Verify na naka-install

```bash
which pdftotext
which pdftoppm
pdftotext -v
```

Dapat may path (hal. `/usr/bin/pdftotext`) at walang error.

---

## 2. PHP extensions

Kailangan ng **PDF Sign** at ng mga package nito:

| Extension   | Para sa                    |
|------------|-----------------------------|
| **gd**     | Intervention Image (e-sign) |
| **mbstring** | String handling           |
| **zip**    | Composer / packages         |
| **dom**    | Smalot PdfParser / XML      |
| **json**   | Laravel / API               |

### Linux (Debian / Ubuntu)

```bash
sudo apt install -y php-gd php-mbstring php-zip php-xml php-json
```

(Kung may version: `php8.2-gd php8.2-mbstring php8.2-zip php8.2-xml` – palitan `8.2` ng PHP version mo.)

### Linux (RHEL / CentOS – remi o default PHP)

```bash
sudo yum install -y php-gd php-mbstring php-zip php-xml php-json
```

### Verify

```bash
php -m | grep -E 'gd|mbstring|zip|dom|json'
```

Dapat lumabas ang bawat extension.

---

## 3. php.ini – walang block sa proc_open

Ang **pdftotext** ay tinatakbo gamit `proc_open()`. Dapat **hindi** naka-disable sa `disable_functions`.

### Check

```bash
php -r "echo in_array('proc_open', array_map('trim', explode(',', ini_get('disable_functions')))) ? 'proc_open DISABLED' : 'proc_open OK';"
```

Kung naka-disable, alisin ang `proc_open` sa `disable_functions` sa `php.ini` (o sa hosting control panel kung walang SSH).

---

## 4. Composer packages (PDF Sign)

Naka-declare na sa `composer.json`; kailangan lang i-install.

### Install lahat (production)

```bash
cd /path/to/your/project
composer install --no-dev --optimize-autoloader
```

### Kung may CMD/SSH at gusto i-update

```bash
composer update --no-dev
```

### Mga package na gamit ng PDF Sign

| Package            | Gamit                    |
|--------------------|--------------------------|
| **smalot/pdfparser** | Fallback text sa PDF   |
| **setasign/fpdi**  | Overlay / sign sa PDF   |
| **setasign/fpdf**  | Dependency ng FPDI       |
| **intervention/image** | E-sign image          |

Kung **walang CMD** sa server: i-copy ang buong `vendor/` mula sa local (kasama `vendor/smalot`, `vendor/setasign`, `vendor/intervention`, at `vendor/autoload.php`).

---

## 5. .env (PDF paths)

Sa server (Linux), idagdag sa `.env`:

```env
# PDF signer – path sa pdftotext (Poppler)
PDFTOTEXT_PATH='/usr/bin/pdftotext'
```

Opsyonal (para sa preview):

```env
PDFTOPPM_PATH='/usr/bin/pdftoppm'
```

Kung **walang Poppler** sa server, puwede walang linyang ito; gagamitin ang Smalot fallback (mas limitado ang “exact position”).

---

## 6. Quick checklist (may CMD/SSH)

```bash
# 1. Poppler
sudo apt install -y poppler-utils    # Debian/Ubuntu
# o: sudo yum install -y poppler-utils

# 2. PHP extensions
sudo apt install -y php-gd php-mbstring php-zip php-xml

# 3. Composer (sa project folder)
cd /path/to/epms
composer install --no-dev --optimize-autoloader

# 4. Verify
which pdftotext
php -m | grep gd
```

Pagkatapos, lagyan ng `PDFTOTEXT_PATH` ang `.env` (Section 5).

---

## 7. Walang CMD/SSH (copy-paste deploy)

- **Poppler:** Hindi mo ma-i-install; mag-request sa host na i-install ang **poppler-utils**, o gamitin ang app na **Smalot-only** (walang pdftotext).
- **PHP extensions:** Karaniwan naka-install na sa shared hosting; kung may error, mag-request sa host.
- **Composer:** I-copy ang buong `vendor/` mula sa local papuntang server.
- **.env:** I-set lang ang `PDFTOTEXT_PATH` kung naka-install na ang Poppler sa server; kung hindi, puwede walang linyang ito.
