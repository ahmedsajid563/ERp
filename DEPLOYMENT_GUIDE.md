# 🚀 Media Mosiac ERP — Hostinger Deployment Guide
## Poora Step-by-Step

---

## 📁 Folder Structure (ZIP ke andar)

```
mediamosiac-erp/
├── frontend/
│   ├── src/
│   │   ├── App.jsx          ← Poora frontend (React)
│   │   └── main.jsx         ← Entry point
│   ├── index.html
│   ├── vite.config.js
│   ├── package.json
│   └── .htaccess            ← Apache config (public_html mein jaayega)
│
├── backend/
│   ├── routes/
│   │   ├── auth.js          ← Login/Register
│   │   ├── clients.js
│   │   ├── invoices.js
│   │   ├── payments.js      ← Client Payments + TDS
│   │   ├── expenses.js
│   │   ├── employees.js     ← Employees + Payroll
│   │   ├── vault.js         ← Password Vault (AES-256)
│   │   └── dashboard.js     ← Stats + Company Settings
│   ├── middleware/
│   │   └── auth.js          ← JWT verification
│   ├── db/
│   │   ├── connection.js    ← MySQL pool
│   │   └── schema.sql       ← Poora database structure
│   ├── index.js             ← Express server
│   ├── package.json
│   └── .env.example         ← Rename to .env and fill
│
└── DEPLOYMENT_GUIDE.md      ← Ye file
```

---

## STEP 1 — Hostinger Plan

1. **hostinger.in** → **Business Web Hosting** (~₹299/month)
2. Domain bhi lo agar nahi hai
3. Plan activate hone ke baad **hpanel.hostinger.com** login karo

---

## STEP 2 — MySQL Database Banao

1. hPanel → left menu → **Databases** → **MySQL Databases**
2. **Create New Database**:
   ```
   Database Name : mediamosiac_erp
   Username      : mediamosiac_user
   Password      : (strong password — note karlo!)
   ```
3. **Create** click karo
4. Jo full credentials milein woh note karo:
   ```
   Host     : localhost
   User     : u123456789_mediamosiac_user   ← Hostinger prefix add karta hai
   Password : tumhara_password
   DB Name  : u123456789_mediamosiac_erp
   ```

---

## STEP 3 — Database Schema Import Karo

1. hPanel → **Databases** → **phpMyAdmin**
2. Left side apna database select karo
3. Top mein **Import** tab
4. **Choose File** → `backend/db/schema.sql` upload karo
5. **Go** click karo
6. ✅ "Import successfully finished" aayega

   Ye tables ban jaayenge:
   - users, company, clients, invoices, invoice_items
   - client_payments, expenses, employees, payroll, vault

---

## STEP 4 — Backend Files Upload

### Option A: File Manager (Recommended)

1. hPanel → **File Manager**
2. Root `/` pe jao (public_html ke upar wala level)
3. **New Folder** banao naam: `backend`
4. `backend/` folder mein jao
5. **Upload** → ZIP karo pehle `mediamosiac-erp/backend/` folder, phir upload → Extract

### Option B: FTP (FileZilla)
```
Host     : ftp.yourdomain.com
Username : tumhara_ftp_username
Password : tumhara_ftp_password
Port     : 21
```
- Local: `mediamosiac-erp/backend/`
- Server: `/home/u123456789/backend/`

---

## STEP 5 — .env File Banao

1. File Manager → `/backend/` → `.env.example` file ko copy karo
2. Naam change karo: `.env`
3. Right click → **Edit**:

```env
PORT=5000
NODE_ENV=production

DB_HOST=localhost
DB_PORT=3306
DB_USER=u123456789_mediamosiac_user
DB_PASSWORD=tumhara_db_password
DB_NAME=u123456789_mediamosiac_erp

JWT_SECRET=MediaMosiac2026_LikhoBhiKuch_MinimumLength32Chars!!
VAULT_KEY=VaultEncryption2026_YeBhiChangeKaro_32Chars1234

FRONTEND_URL=https://yourdomain.com
```

4. Save karo

---

## STEP 6 — Node.js Setup (Hostinger)

1. hPanel → **Advanced** → **Node.js**
2. **Create Application**:
   ```
   Node.js version        : 18.x (ya latest LTS)
   Application mode       : Production
   Application root       : backend
   Application URL        : yourdomain.com
   Application startup file: index.js
   ```
3. **Create** click karo
4. **NPM Install** button click karo (wait karo — 1-2 min)
5. **Start Application** click karo
6. ✅ Status "Running" dikhna chahiye

**Test karo:**
```
https://yourdomain.com/api/health
```
Response aana chahiye: `{"status":"ok","time":"...","version":"1.0.0"}`

---

## STEP 7 — Frontend Build Karo (Apne Computer Pe)

**Node.js install nahi hai?** → nodejs.org se download karo (LTS)

```bash
# 1. Frontend folder mein jao
cd mediamosiac-erp/frontend

# 2. Dependencies install karo
npm install

# 3. Production URL set karo (apna domain likho)
# Windows:
echo VITE_API_URL=https://yourdomain.com/api > .env

# Mac/Linux:
echo "VITE_API_URL=https://yourdomain.com/api" > .env

# 4. Build banao
npm run build
```

Isse `frontend/dist/` folder banega — iske andar:
- `index.html`
- `assets/` folder

---

## STEP 8 — Frontend Upload Karo

1. hPanel → **File Manager** → `public_html` folder mein jao
2. `public_html` ka poora content **delete karo** (ya backup lo)
3. `mediamosiac-erp/frontend/dist/` ke **andar ka content** upload karo:
   - `index.html` → directly `public_html/` mein
   - `assets/` folder → directly `public_html/` mein
4. **IMPORTANT**: `.htaccess` file bhi upload karo
   - `mediamosiac-erp/frontend/.htaccess` → `public_html/.htaccess`

---

## STEP 9 — SSL Certificate

1. hPanel → **SSL** → **Free SSL (Let's Encrypt)**
2. Domain select karo
3. **Install** click karo
4. ✅ 5-10 min mein HTTPS active ho jaayega

---

## STEP 10 — Final Test

Browser mein kholo:
```
https://yourdomain.com/api/health   → {"status":"ok"} ✅
https://yourdomain.com              → Login page ✅
```

**Default Login:**
```
Email    : admin@mediamosiac.com
Password : Admin@1234
```

---

## ⚠️ PEHLA KAAM — Password Change Karo

Login hone ke baad:
1. Top right → profile icon
2. Settings → Change Password
3. **Admin@1234 se alag strong password set karo**

---

## 🔄 Future Update Kaise Karo

### Backend change:
1. File Manager → `/backend/` → file edit karo
2. hPanel → Node.js → **Restart Application**

### Frontend change:
1. Apne computer pe: `npm run build`
2. `dist/` ka content `public_html/` mein dobara upload karo

---

## ❓ Common Problems

| Problem | Solution |
|---------|---------|
| `/api/health` nahi khulta | hPanel → Node.js → Application Running hai? |
| "Cannot connect to DB" | `.env` mein DB credentials check karo |
| Page refresh pe 404 | `.htaccess` file `public_html/` mein hai? |
| "CORS error" browser mein | `.env` mein `FRONTEND_URL` sahi domain set kiya? |
| "npm install" fail | Node.js version 18+ select karo |
| Login page nahi dikhta | `public_html/index.html` exist karta hai? |

---

## 💰 Total Kharch

| Item | Cost |
|------|------|
| Hostinger Business Hosting | ~₹299/month |
| Domain (.com) | ~₹800/year |
| SSL Certificate | FREE |
| **Total** | **~₹400/month** |

---

## 📞 Support

- Hostinger Live Chat: support.hostinger.com (24/7)
- Ye guide follow karo — koi problem ho toh Claude se poochho!
