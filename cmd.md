# PDF Signer – Install requirements (commands)

## 1. Poppler (pdftotext, pdftoppm)

**Debian/Ubuntu:**
```bash
sudo apt update
sudo apt install -y poppler-utils
```

**RHEL/CentOS:**
```bash
sudo yum install -y poppler-utils
```

**Fedora:**
```bash
sudo dnf install -y poppler-utils
```

**Verify:**
```bash
which pdftotext
which pdftoppm
```

---

## 2. PHP extensions

**Debian/Ubuntu:**
```bash
sudo apt install -y php-gd php-mbstring php-zip php-xml php-json
```

**RHEL/CentOS:**
```bash
sudo yum install -y php-gd php-mbstring php-zip php-xml php-json
```

**Verify:**
```bash
php -m | grep -E 'gd|mbstring|zip|dom|json'
```

---

## 3. proc_open (must not be disabled)

**Check:**
```bash
php -r "echo in_array('proc_open', array_map('trim', explode(',', ini_get('disable_functions')))) ? 'DISABLED' : 'OK';"
```

Remove `proc_open` from `disable_functions` in php.ini if needed.

---

## 4. Composer

**Install:**
```bash
cd /path/to/project
composer install --no-dev --optimize-autoloader
```

**Packages used by PDF Sign:** smalot/pdfparser, setasign/fpdi, setasign/fpdf, intervention/image

---

## 5. .env

```env
PDFTOTEXT_PATH='/usr/bin/pdftotext'
PDFTOPPM_PATH='/usr/bin/pdftoppm'
```

---

## 6. All-in-one (Debian/Ubuntu)

```bash
sudo apt update
sudo apt install -y poppler-utils php-gd php-mbstring php-zip php-xml php-json
cd /path/to/project
composer install --no-dev --optimize-autoloader
which pdftotext
php -m | grep gd
```
