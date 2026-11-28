[Uploading README.md…]()

# Anonim Chat — Hevorix

> **TermChat / Anonim Chat** — FastAPI va JavaScript asosida qurilgan, real vaqt rejimida ishlaydigan, brauzerda foydalaniladigan anonim chat platformasi. Loyiha shaxsiy yozishmalar uchun E2EE, WebSocket, PWA, kanallar, media almashish, moderatsiya, OSINT/havola tekshiruvi va mini-o‘yinlarni birlashtiradi.

<p align="center">
  <strong>Python · FastAPI · WebSocket · JavaScript · SQLite · WebCrypto · PWA</strong>
</p>

---

## Mundarija

- [Loyiha haqida](#-loyiha-haqida)
- [Asosiy imkoniyatlar](#-asosiy-imkoniyatlar)
- [Xavfsizlik va maxfiylik](#-xavfsizlik-va-maxfiylik)
- [Texnologiyalar](#-texnologiyalar)
- [Loyiha tuzilishi](#-loyiha-tuzilishi)
- [Talablar](#-talablar)
- [Mahalliy ishga tushirish](#-mahalliy-ishga-tushirish)
- [Docker orqali ishga tushirish](#-docker-orqali-ishga-tushirish)
- [Sozlamalar va muhit o‘zgaruvchilari](#-sozlamalar-va-muhit-ozgaruvchilari)
- [Cloudflare R2 va Render](#-cloudflare-r2-va-render)
- [Production serverga joylash](#-production-serverga-joylash)
- [Testlar](#-testlar)
- [Muhim xavfsizlik eslatmalari](#-muhim-xavfsizlik-eslatmalari)
- [Cheklovlar](#-cheklovlar)
- [Hissa qo‘shish](#-hissa-qoshish)
- [Litsenziya](#-litsenziya)

---

## 💬 Loyiha haqida

Anonim Chat foydalanuvchilarga akkaunt yaratish, umumiy chatda qatnashish, shaxsiy yozishmalar olib borish va media yuborish imkonini beradigan web-ilovadir. Interfeys desktop va mobil brauzerlarga moslashtirilgan; PWA imkoniyatlari orqali qurilma bosh ekraniga o‘rnatilishi mumkin.

Loyiha server va brauzer tomonidagi vazifalarni ajratadi:

- **Backend** — autentifikatsiya, sessiyalar, ruxsatlar, ma’lumotlar bazasi, WebSocket ulanishlari, fayllar, kanallar, moderatsiya va xavfsizlik tekshiruvlari.
- **Frontend** — chat interfeysi, xabarlarni shifrlash/ochish, media bilan ishlash, PWA, bildirishnomalar va o‘yinlar.
- **Ma’lumotlar** — SQLite bazasi hamda alohida saqlanadigan avatar, emoji, musiqa, yuklangan media va zaxira fayllari.

Ilova FastAPI orqali ishlaydi. Realtime aloqa WebSocket yordamida amalga oshiriladi. Brauzerda Web Crypto API kriptografik amallar uchun ishlatiladi.

## ✨ Asosiy imkoniyatlar

### Chat va real vaqt aloqasi

- Umumiy chat va foydalanuvchilar o‘rtasida shaxsiy yozishmalar.
- WebSocket orqali xabarlar va holatlarning real vaqtda yangilanishi.
- Onlayn/oflayn holati, sahifa faol emasligi va qayta ulanish mexanizmlari.
- Xabarlar tarixini bosqichma-bosqich yuklash.
- Javob berish, tahrirlash, o‘chirish, nusxalash, saqlash va boshqa suhbatga forward qilish.
- Xabarlarga emoji-reaksiyalar.
- Foydalanuvchi profili, nickname, avatar, bio va dizayn sozlamalari.
- PIN qilingan xabarlar va maxfiylik sozlamalari.
- Yangi akkauntlar uchun ogohlantiruvchi belgi.
- Faolsizlikdan keyin avtomatik sessiya yakunlanishi.

### End-to-end encryption (E2EE)

Loyihada xabar mazmunini serverdan yashirish uchun brauzer tomonida E2EE mexanizmi mavjud:

- Xabarlar brauzerda AES-256-GCM bilan shifrlanadi.
- ECDH P-256 kalit almashinuvi shaxsiy suhbat kalitlarini hosil qilishda ishlatiladi.
- ECDSA P-256 yordamida xabar konvertlari imzolanadi.
- Umumiy chat uchun guruh kaliti va epoch/kalit avlodlari mexanizmi ishlatiladi.
- Fayllar alohida kalit bilan shifrlanib yuboriladi.
- Shifrlangan xabar konvertlari serverda saqlanadi va uzatiladi.
- Kalit barmoq izlarini tekshirish (TOFU) va kalit o‘zgarganda ogohlantirish mavjud.
- Server tomonida E2EE majburiyligini sozlash mumkin (`TERMCHAT_REQUIRE_E2EE`).

**Muhim:** E2EE server ma’lumotlar bazasida xabar matnining ochiq ko‘rinishda saqlanishini oldini oladi, lekin barcha metama’lumotlarni yashirmaydi. Quyidagi [xavfsizlik eslatmalari](#-muhim-xavfsizlik-eslatmalari) bilan tanishing.

### Media va fayllar

- Rasm, video, hujjat va boshqa fayllarni yuborish.
- Ovozli xabar yozish va tinglash.
- Dumaloq video xabarlar.
- Faylni qurilmaga saqlash va xabarni boshqa suhbatga qayta shifrlab yuborish.
- Rasm yuborishdan oldin qayta kodlanishi va metadata/EXIF ma’lumotlari olib tashlanishi.
- Profil avatarlari va maxsus emoji fayllari.
- Media hajmi va ayrim fayl turlari uchun server cheklovlari.

Standart maksimal media hajmi 50 MB. Bu qiymat `TERMCHAT_MAX_MEDIA_BYTES` orqali o‘zgartiriladi.

### Kanallar

- Kanal yaratish va unga qo‘shilish.
- Kanal postlarini asosiy chat interfeysida ko‘rish.
- Kanal adminlari tomonidan post joylash.
- Postlarga izohlar, ko‘rishlar va so‘rovnomalar.
- Rasm, fayl va boshqa qo‘llab-quvvatlanadigan media turlarini postlarda ishlatish.
- Kanal postlari va izohlarini E2EE bilan himoyalash.

Kanal a’zoligi va kirish huquqlarini server boshqaradi. Shaxsiy kanal uchun serverdagi ruxsat nazorati E2EE guruh kalitidan alohida qatlam hisoblanadi.

### Emoji va profil bezaklari

- Standart emoji katalogi.
- Maxsus emoji yuklash va yaratish.
- Emoji to‘plamlari (packs).
- Emoji animatsiyalari va ko‘rinish sozlamalari.
- Adminlar uchun foydalanuvchilarga emoji ishlatish/yaratish ruxsatlarini boshqarish.
- Profil uchun maxsus badge/nishonlar va tasdiqlangan belgi.

### Havola tekshiruvi va OSINT

Loyihada havolalarni tekshirish va domen/IP bo‘yicha qo‘shimcha ma’lumot yig‘ish vositalari mavjud:

- URL va redirect zanjirini tekshirish.
- WHOIS/RDAP, DNS va SSL/TLS ma’lumotlarini olish.
- Xavfli domenlar bo‘yicha evristik tekshiruvlar.
- Mavjud bo‘lsa Google Safe Browsing, VirusTotal va URLhaus integratsiyalari.
- Kalitsiz threat-intelligence feed manbalari.
- OSINT ko‘rinishida domen/host bo‘yicha texnik ma’lumotlarni jamlash.
- Ixtiyoriy URL skrinshot xizmati.

Tashqi API kalitlari ixtiyoriy: kalitsiz rejimda ham ayrim DNS, RDAP, SSL va evristik tekshiruvlar ishlashi mumkin. Natijalar mutlaq xavfsizlik kafolati emas.

### Mini-o‘yinlar

Chat ichida quyidagi o‘yinlar mavjud:

- Shaxmat — onlayn va oflayn rejim elementlari, hisob/reyting.
- Rus shashkasi.
- Durak karta o‘yini.
- O‘yin natijalari va scoreboard.
- Ayrim o‘yinlarda bot/AI raqib va reaksiyalar.

### Missiyalar va dizayn

- Missiyalar va mukofotlar.
- Inventar va jihozlanadigan bezaklar.
- Dizayn paketlari va interfeys ko‘rinishini sozlash.
- Animatsiya va unumdorlik rejimlari.

### PWA va bildirishnomalar

- Web App Manifest va Service Worker.
- Bosh ekranga o‘rnatish imkoniyati.
- Kesh va offline uchun frontend resurslarini saqlash.
- Web Push bildirishnomalari (VAPID).
- Brauzer yopiq bo‘lgan holatlarda push xabarnomalarini yetkazish — brauzer/OS va push xizmatining qo‘llab-quvvatlashiga bog‘liq.

### Admin va moderatsiya

- Foydalanuvchilar va rollarni boshqarish.
- Mute, ban, IP bloklash va blokdan chiqarish.
- Shikoyatlarni ko‘rish va yopish.
- Faollik va server holati statistikasi.
- Media va xabarlarni boshqarish.
- E2EE holati va eski ochiq matnli xabarlarni tozalash.
- Emoji, musiqa, badge, kanal va boshqa sozlamalarni boshqarish.
- Zaxira yaratish va tiklash.

---

## 🔐 Xavfsizlik va maxfiylik

Loyihada bir nechta himoya qatlamlari mavjud:

| Qatlam | Amalga oshirilgan himoya |
|---|---|
| Xabar mazmuni | Brauzer tomonidagi E2EE |
| Xabar imzosi | ECDSA P-256 |
| Kalit almashinuvi | ECDH P-256 |
| Simmetrik shifrlash | AES-256-GCM |
| Paroldan kalit hosil qilish | PBKDF2-SHA256 |
| Serverdagi maxfiy ma’lumotlar | Master key bilan shifrlash |
| HTTP xavfsizligi | CSP, HSTS (HTTPS bo‘lsa), nosniff, Referrer-Policy |
| Brauzer izolyatsiyasi | COOP/CORP va Permissions-Policy |
| So‘rovlar | Rate limit va autentifikatsiya |
| WebSocket | Origin tekshiruvi va shifrlangan transport konverti |
| DOM | Xabar matnini xavfsiz chiqarish; kod/terminal matni bajarilmaydi |

### E2EE qanday ishlaydi?

1. Foydalanuvchi paroli brauzerda PBKDF2-SHA256 orqali hosila kalitlarga aylantiriladi.
2. Foydalanuvchining ECDH va ECDSA kalit juftliklari yaratiladi.
3. Shaxsiy kalitlar parolga bog‘langan kalit bilan o‘ralgan holda zaxiralanadi.
4. Shaxsiy suhbatda ECDH orqali umumiy sir hosil qilinadi va undan AES kaliti olinadi.
5. Umumiy chatda guruh kaliti ishlatiladi; yangi a’zolarga kalit o‘ralgan holda uzatiladi.
6. Xabar AES-GCM bilan shifrlanadi va ECDSA bilan imzolanadi.
7. Server shifrlangan konvertni saqlaydi/uzatadi, mijoz esa uni o‘z qurilmasida ochadi.

### Maxfiylik bo‘yicha ochiq cheklovlar

E2EE quyidagilarni avtomatik yashirmaydi:

- Kim kim bilan yozishgani, vaqt, xabar hajmi va IP kabi metama’lumotlar.
- Profil, avatar va akkaunt ma’lumotlari.
- Umumiy chat yoki kanalga a’zo bo‘lgan foydalanuvchilarning o‘z qurilmalaridagi xabar mazmuni.
- Qurilma yoki brauzer buzilgan bo‘lsa, foydalanuvchi ekranida ochilgan xabarlar.

Parol yoki kalit zaxirasi yo‘qolsa, eski shifrlangan yozishmalarni tiklash imkonsiz bo‘lishi mumkin. Kalit barmoq izlarini tekshirish tavsiya etiladi.

---

## 🧰 Texnologiyalar

**Backend**
- Python 3.12
- FastAPI
- Uvicorn
- SQLite
- WebSocket
- `cryptography`
- Pillow
- boto3 (Cloudflare R2/S3 bilan ishlash uchun)

**Frontend**
- HTML5
- CSS3
- Vanilla JavaScript
- Web Crypto API
- IndexedDB
- Service Worker
- Web Push API

**Deploy**
- Docker / Docker Compose
- Nginx
- Render Blueprint
- Cloudflare R2

---

## 📁 Loyiha tuzilishi

```text
termchat/
├── app/
│   ├── api/                 # HTTP va WebSocket API routerlari
│   ├── repositories/        # Ma'lumotlar bazasi bilan ishlash qatlamlari
│   ├── services/            # Chat, E2EE yordamchilari, OSINT, backup va boshqalar
│   ├── static/
│   │   ├── css/             # Interfeys stillari
│   │   ├── js/              # Frontend JavaScript modullari
│   │   ├── emoji/           # O'rnatilgan emoji rasmlari
│   │   ├── icons/           # PWA ikonkalari
│   │   ├── index.html       # Asosiy frontend
│   │   ├── manifest.json    # PWA manifest
│   │   └── sw.js            # Service Worker
│   ├── config.py            # Muhit sozlamalari
│   ├── db.py                # SQLite bazasi
│   ├── e2ee.py              # E2EE konvertlarini tekshirish
│   ├── security.py          # Xavfsizlik yordamchilari
│   └── main.py              # FastAPI ilovasini yig'ish
├── deploy/                  # systemd va Nginx namunalari
├── tests/                   # Python va JavaScript testlari
├── tools/                   # Yordamchi va build vositalari
├── .env.example             # Muhit o'zgaruvchilari namunasi
├── Dockerfile
├── docker-compose.yml
├── render.yaml
├── requirements.txt
└── run.py
```

`data/` katalogi ishga tushganda yaratiladi (yoki `TERMCHAT_DATA_DIR` bilan belgilanadi). Unda baza, kalitlar, avatarlar, emoji, media, musiqa va zaxira fayllari saqlanishi mumkin. Uni ommaviy Git repozitoriyga yuklamang.

---

## 📋 Talablar

- Python **3.12** tavsiya qilinadi.
- `pip`.
- Zamonaviy brauzer (Web Crypto, WebSocket va kerakli Web API'larni qo‘llashi kerak).
- Production muhitida HTTPS.
- Ixtiyoriy: Docker va Docker Compose.
- Ixtiyoriy: Cloudflare R2 akkaunti va S3 API kalitlari.

Mikrofon, WebCrypto va Push API kabi funksiyalar uchun HTTPS yoki localhost talab qilinishi mumkin.

## 🚀 Mahalliy ishga tushirish

### 1. Repozitoriyani yuklab olish

```bash
git clone https://github.com/USERNAME/REPOSITORY.git
cd REPOSITORY
```

`USERNAME/REPOSITORY` o‘rniga o‘z GitHub manzilingizni yozing.

### 2. Virtual muhit yaratish

**Linux / macOS:**

```bash
python3.12 -m venv .venv
source .venv/bin/activate
```

**Windows PowerShell:**

```powershell
py -3.12 -m venv .venv
.venv\Scripts\Activate.ps1
```

### 3. Kutubxonalarni o‘rnatish

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 4. Muhit faylini yaratish

**Linux / macOS:**

```bash
cp .env.example .env
```

**Windows PowerShell:**

```powershell
Copy-Item .env.example .env
```

`.env` faylini ochib, kamida quyidagilarni sozlang:

```dotenv
TERMCHAT_HOST=0.0.0.0
TERMCHAT_PORT=8000
TERMCHAT_DATA_DIR=./data
TERMCHAT_TZ_OFFSET=5

# Production uchun tasodifiy, uzun va maxfiy qiymat qo'ying.
TERMCHAT_MASTER_KEY=

# Birinchi ishga tushirishdagi platforma egasi paroli.
TERMCHAT_ADMIN_PASSWORD=

# Production uchun E2EE va shifrlangan transportni yoqilgan holda qoldiring.
TERMCHAT_REQUIRE_E2EE=1
TERMCHAT_REQUIRE_TUNNEL=1
```

`TERMCHAT_MASTER_KEY` bo‘sh bo‘lsa, ilova `data/master.key` yaratishi mumkin. Production muhitida master key'ni alohida secret sifatida boshqarish va xavfsiz joyda zaxiralash ma’qul. Master key yo‘qolsa, unga bog‘liq shifrlangan ma’lumotlarni ochib bo‘lmaydi.

### 5. Serverni ishga tushirish

```bash
python run.py
```

Yoki:

```bash
uvicorn app.main:app --host 0.0.0.0 --port 8000
```

Brauzerda:

```text
http://127.0.0.1:8000
```

Health-check:

```text
http://127.0.0.1:8000/healthz
```

Birinchi ishga tushirishda `TERMCHAT_ADMIN_PASSWORD` berilmagan bo‘lsa, platforma egasi uchun yaratilgan tasodifiy parol server logida ko‘rsatilishi mumkin. Loglarni tekshiring va parolni xavfsiz saqlang.

> `TERMCHAT_REQUIRE_TUNNEL=1` standart holatda yoqilgan. Bu sozlama API va upload so‘rovlarining ilova ichidagi shifrlangan kanal orqali kelishini talab qiladi. Uni faqat lokal diagnostika/test uchun o‘chirishni ko‘rib chiqing; ochiq internetdagi production serverda o‘chirib qo‘ymang.

---

## 🐳 Docker orqali ishga tushirish

Docker image yaratish va ishga tushirish:

```bash
docker compose up -d --build
```

Loglarni ko‘rish:

```bash
docker compose logs -f termchat
```

To‘xtatish:

```bash
docker compose down
```

`docker-compose.yml` ma’lumotlarni `termchat-data` nomli persistent volume'da saqlaydi. Standart konfiguratsiyada ilova `127.0.0.1:8000` manziliga bog‘lanadi; tashqi trafikni Nginx yoki boshqa HTTPS reverse proxy orqali uzatish kerak.

**Eslatma:** loyiha bitta Uvicorn worker bilan ishlashga mo‘ljallangan: ayrim realtime ulanishlar, presence va limit holatlari jarayon xotirasida yuritiladi. Bir nechta worker qo‘shishdan oldin bu holatlarni umumiy backendga ko‘chirish zarur.

---

## ⚙️ Sozlamalar va muhit o‘zgaruvchilari

Barcha asosiy sozlamalar `.env` yoki hosting environment variables orqali boshqariladi. O‘zgaruvchilar `TERMCHAT_` prefiksi bilan ishlatiladi.

| O‘zgaruvchi | Standart | Tavsif |
|---|---:|---|
| `TERMCHAT_HOST` | `0.0.0.0` | Server bind manzili |
| `TERMCHAT_PORT` | `8000` | Server porti (`PORT` ham qo‘llanadi) |
| `TERMCHAT_DATA_DIR` | `./data` | Doimiy ma’lumotlar katalogi |
| `TERMCHAT_TZ_OFFSET` | `5` | Vaqt zonasi UTC offseti |
| `TERMCHAT_MAX_MESSAGE_LENGTH` | `3000` | Xabar matnining maksimal uzunligi |
| `TERMCHAT_INITIAL_HISTORY` | `10` | Dastlab yuklanadigan xabarlar soni |
| `TERMCHAT_HISTORY_LIMIT` | `30` | Tarixni keyingi yuklash limiti |
| `TERMCHAT_IDLE_LOGOUT` | `300` | Faolsizlikdan keyingi logout, soniya |
| `TERMCHAT_WS_IDLE_TIMEOUT` | `45` | WebSocket idle timeout, soniya |
| `TERMCHAT_MAX_MEDIA_BYTES` | `52428800` | Maksimal media hajmi, bayt |
| `TERMCHAT_MAX_AVATARS` | `3` | Bir foydalanuvchi uchun avatar limiti |
| `TERMCHAT_MSG_RATE_COUNT` | `5` | Xabar rate limit soni |
| `TERMCHAT_MSG_RATE_WINDOW` | `5` | Rate limit oynasi, soniya |
| `TERMCHAT_LOGIN_ATTEMPTS` | `8` | Login urinishlari limiti |
| `TERMCHAT_LOGIN_WINDOW` | `300` | Login limit oynasi, soniya |
| `TERMCHAT_REQUIRE_E2EE` | `1` | E2EE talab qilinsinmi |
| `TERMCHAT_REQUIRE_TUNNEL` | `1` | Ichki shifrlangan transport talab qilinsinmi |
| `TERMCHAT_TRUST_PROXY` | `1` | Proxy IP sarlavhasiga ishonish |
| `TERMCHAT_AUTO_BACKUP_HOURS` | `0` | Avto-backup oralig‘i; `0` — o‘chiq |
| `TERMCHAT_AUTO_BACKUP_KEEP` | `3` | Saqlanadigan avtomatik nusxalar soni |
| `TERMCHAT_DB_SYNC_SECONDS` | `60` | R2 bazasiga sinxronlash oralig‘i |
| `TERMCHAT_ALLOW_FRESH_DB` | `0` | R2 bazasi topilmasa bo‘sh baza bilan boshlashga ruxsat |
| `TERMCHAT_FEEDS` | `1` | Threat-intelligence feed'larini yoqish |

Qo‘shimcha o‘zgaruvchilar `.env.example` faylida berilgan. U yerda Web Push, Cloudflare R2, URL skaneri va tashqi xavfsizlik API kalitlari ham hujjatlashtirilgan.

### Muhim kalitlar

- `TERMCHAT_MASTER_KEY` — serverdagi shifrlangan ma’lumotlar uchun. Uni yo‘qotmang va repozitoriyga commit qilmang.
- `TERMCHAT_VAPID_PRIVATE_KEY` — Web Push obunalari uchun. O‘zgartirilsa, mavjud obunalarni qayta yaratish talab qilinishi mumkin.
- `TERMCHAT_ADMIN_PASSWORD` — birinchi egani yaratish/sozlash uchun.
- `R2_*` — Cloudflare R2 bilan sinxronlash uchun.
- `TERMCHAT_GSB_KEY`, `TERMCHAT_VT_KEY`, `TERMCHAT_URLHAUS_KEY` — tegishli tashqi tekshiruv xizmatlari uchun ixtiyoriy API kalitlari.

---

## ☁️ Cloudflare R2 va Render

Repozitoriyda `render.yaml` Blueprint konfiguratsiyasi mavjud.

### Render orqali

1. Repozitoriyni GitHub'ga push qiling.
2. Render boshqaruv panelida **New → Blueprint** tanlang.
3. GitHub repozitoriysini ulang.
4. `render.yaml` asosida yaratiladigan servisni tekshiring.
5. Quyidagi secret qiymatlarni Render Environment bo‘limiga kiriting:
   - `TERMCHAT_MASTER_KEY`
   - `TERMCHAT_VAPID_PRIVATE_KEY`
   - `TERMCHAT_ADMIN_PASSWORD`
   - R2 account/access/secret/bucket/endpoint qiymatlari.
6. Deploy loglarini tekshiring va `/healthz` health-check muvaffaqiyatli ekanini tasdiqlang.

### R2 nima uchun kerak?

Render'ning vaqtinchalik diskida saqlangan ma’lumotlar qayta deploy yoki restartdan keyin yo‘qolishi mumkin. R2 integratsiyasi quyidagilarni doimiy saqlash/sinxronlash uchun mo‘ljallangan:

- Shifrlangan SQLite baza nusxasi.
- Avatarlar.
- Maxsus emoji fayllari.
- Musiqa fayllari.
- Qo‘llab-quvvatlanadigan media obyektlari.

R2 ishlatilganda `TERMCHAT_MASTER_KEY` doimiy va bir xil bo‘lishi shart. Kalit o‘zgarsa, avvalgi shifrlangan baza ochilmasligi mumkin. `TERMCHAT_ALLOW_FRESH_DB=0` holatini saqlash tavsiya etiladi: R2 bazasi o‘qilmasa, ilova tasodifan bo‘sh baza bilan ishga tushib, eski ma’lumot ustiga yozib yubormasligi kerak.

R2 bucket va API kalitlarini Cloudflare boshqaruv panelida yarating. Secret qiymatlarni GitHub'ga joylamang.

---

## 🖥️ Production serverga joylash

Loyihada `deploy/termchat.service` va `deploy/nginx.conf` namunaviy konfiguratsiyalari mavjud.

Umumiy yo‘l:

1. Ubuntu serverga Python 3.12 va kerakli tizim paketlarini o‘rnating.
2. Repozitoriyni serverga joylashtiring.
3. Virtual muhit yarating va `requirements.txt` ni o‘rnating.
4. `.env` faylida production secret'larini sozlang.
5. Ilovani systemd orqali bitta worker bilan ishga tushiring.
6. Nginx reverse proxy sozlang.
7. HTTPS sertifikat o‘rnating.
8. `/healthz`, WebSocket ulanishi, media upload va backup jarayonlarini sinab ko‘ring.
9. `data/` katalogi uchun cheklangan fayl ruxsatlarini belgilang va muntazam backup oling.

`TERMCHAT_TRUST_PROXY=1` faqat ishonchli reverse proxy mijoz yuborgan soxta IP sarlavhalarini olib tashlab, o‘zi haqiqiy IP sarlavhasini qo‘yganda ishlatilishi kerak. Ilova bevosita internetga ochiq bo‘lsa, proxy sozlamasini qayta ko‘rib chiqing.

---

## 🧪 Testlar

Testlar `tests/` katalogida joylashgan. Python testlari uchun:

```bash
pip install -r requirements-dev.txt
pytest
```

Frontend JavaScript testlarining bir qismi Node.js orqali bajariladi:

```bash
node tests/chess_engine_test.mjs
node tests/checkers_engine_test.mjs
node tests/durak_engine_test.mjs
node tests/richtext_test.mjs
node tests/reactions_sync_test.mjs
```

Muayyan test faylini alohida ishga tushirish ham mumkin:

```bash
pytest tests/test_e2ee.py
pytest tests/test_urlscan.py
pytest tests/test_webpush.py
```

Testlar to‘plami E2EE, autentifikatsiya, realtime, kanallar, backup, media, havola skaneri, o‘yinlar va frontend yordamchi modullarining turli qismlarini qamrab oladi. Har bir deploy oldidan to‘liq test to‘plamini ishga tushirish tavsiya etiladi.

---

## 🛡️ Muhim xavfsizlik eslatmalari

1. **HTTPS shart.** Production'da ilovani HTTP orqali ochiq internetga chiqarmang. Web Crypto va Push kabi brauzer API'lari secure context talab qiladi.
2. **Master key'ni alohida saqlang.** `.env`, `data/master.key`, VAPID private key va R2 secret'larini GitHub'ga yuklamang.
3. **Backupni sinab ko‘ring.** Backup mavjudligi uning tiklanishini kafolatlamaydi; alohida muhitda restore testini bajaring.
4. **E2EE serverdan himoya qiladi, suhbatdoshdan emas.** Qabul qiluvchi xabarni nusxalashi yoki skrinshot qilishi mumkin.
5. **Umumiy chat a’zolari ishonch chegarasidir.** Guruh kalitini olgan a’zo xabarlarni o‘z qurilmasida o‘qiy oladi.
6. **Frontend kodiga ishonch muhim.** Brauzer bajaradigan kod serverdan yetkaziladi. O‘zingiz boshqarmaydigan server zararli frontend yuborishi mumkin.
7. **Metama’lumotlar mavjud.** IP, vaqt, ishtirokchilar va trafik hajmi kabi ma’lumotlar E2EE bilan yashirilmaydi.
8. **URL skaneri mutlaq kafolat bermaydi.** Yangi yoki yashirin zararli manzillar aniqlanmay qolishi mumkin. Hech qachon shubhali fayl yoki havolani faqat skaner natijasiga qarab ishonchli deb hisoblamang.
9. **Admin endpointlarini himoyalang.** Kuchli parollar, cheklangan server kirishi va muntazam yangilanishlardan foydalaning.
10. **Proxy sozlamasini to‘g‘ri tanlang.** Noto‘g‘ri `TRUST_PROXY` konfiguratsiyasi IP asosidagi limit va bloklash mexanizmlarini zaiflashtirishi mumkin.

Loyihani ommaviy internetga joylashdan oldin mustaqil xavfsizlik auditi o‘tkazish tavsiya etiladi.

## ⚠️ Cheklovlar

- Realtime holatlar va ayrim rate limit/presence ma’lumotlari jarayon xotirasida saqlanadi; shu sababli bitta worker tavsiya etiladi.
- Push bildirishnomalari brauzer, operatsion tizim va push provayderiga bog‘liq.
- E2EE kalitlari yo‘qolsa, shifrlangan eski ma’lumotni tiklash imkonsiz bo‘lishi mumkin.
- Threat-intelligence xizmatlarining ayrimlari API kalit, kvota yoki foydalanish litsenziyasini talab qiladi.
- Oflayn PWA imkoniyatlari frontend resurslari va avval yuklangan ma’lumotlar doirasida; serverga bog‘liq chat amallari internet talab qiladi.
- Ushbu README repozitoriydagi kod va konfiguratsiya fayllariga asoslangan. Ishlab chiqarish muhitidagi imkoniyatlar deployment sozlamalariga qarab farq qilishi mumkin.

## 🤝 Hissa qo‘shish

Hissa qo‘shish, xatolik haqida xabar berish yoki yangi imkoniyat taklif qilishdan oldin:

1. Mavjud issue'larni tekshiring.
2. O‘zgarishni alohida branch'da bajaring.
3. Tegishli Python va JavaScript testlarini ishga tushiring.
4. Xavfsizlikka taalluqli o‘zgarishlarda tahdid modeli va maxfiylik oqibatlarini ham yozing.
5. Pull request'da o‘zgarish sababini, ta’sir qiladigan modullarni va test natijalarini ko‘rsating.

## 📄 Litsenziya

Ushbu repozitoriy uchun litsenziya fayli mavjud bo‘lmasa, kodni qayta tarqatish yoki tijoriy foydalanish huquqini avtomatik ravishda taxmin qilmang. Muallif loyiha uchun tanlagan litsenziya (`LICENSE`) faylini repozitoriyga qo‘shishi kerak.

---

<p align="center">
  <strong>Anonim Chat — Hevorix</strong><br>
  <sub>Realtime · Privacy-focused · E2EE · PWA</sub>
</p>
