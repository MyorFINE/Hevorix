# Anonim Chat by Hevorix

Telegram uslubidagi zamonaviy chat platforma: umumiy guruh chat, shaxsiy yozishmalar (lichka), profillar va a'zolar ro'yxati.
Foydalanuvchilar **hech qanday buyruq yozmaydi** — shunchaki yozishadi. Moderatsiya (mute/ban/admin) tugmalar orqali.
**FastAPI + SQLite + WebSocket**, frontend: vanilla HTML/CSS/JS (qat'iy CSP, inline skript/uslub yo'q, SRI).


## Frontend manbasi va yig'ish (v3.5.0)

`app/static/js/app.js` va `app/static/css/style.css` — **yig'ilgan fayllar**. Manba: `frontend/js/app/*.js` (24 modul) va `frontend/css/*.css` (12 bo'lak). Tahrirdan keyin: `python tools/build_frontend.py` (tekshirish: `--check`). Qolgan JS fayllar (`richtext.js`, `chessbot.js`, `e2ee.js` …) alohida modullar. Xabarlar ro'yxatida uzoq qatorlar `js/virtual.js` bilan muzlatiladi.


## Yangiliklar: dizaynni foydalanuvchining o'zi sozlaydi

- **Sozlamalar → Profil dizayni** hammaga ochiq: nickname, username, bio, profil sahifasi, reply va havola uslubi, profil foni va avatar ramkasini har kim **o'zi** tanlaydi (`POST /api/profile/design`, faqat o'z akkaunti; 30/daqiqa).
- **Admin foydalanuvchiga dizayn bermaydi.** Admin/ega faqat uchta narsa beradi (`POST /api/admin/user/profile`, `/api/admin/badge`): **emoji nishon / galochka**, **unvon** va **ishonch belgisi**. «Ishonchli» deb belgilansa «⚠ Yangi foydalanuvchi» yozuvi yo'qoladi; «Yangi» — eski akkauntda ham ko'rsatiladi; «Avtomatik» — akkaunt yoshi bo'yicha. Server boshqaga dizayn berishni rad etadi (403).
- Oddiy foydalanuvchi nishon, unvon, ishonch belgisi va profil rangini o'zi qo'ya olmaydi. Adminlar o'z dizaynini (rangi bilan) o'zlari sozlaydi.
- **«Xabarlar» effektlari (Sakrash, Sirpanish, Ko'tarilish, Aylanish, Tushish, Aniqlashish, Oqma hoshiya) butunlay olib tashlandi** — server (`fxstyle.FX`), interfeys va CSS'dan. Bazadagi eski `msg` qiymatlari ko'rsatilmaydi va keyingi saqlashda tozalanadi.

## Yangiliklar (v3.8.0): batareya/animatsiyalar, avtomatik o'chirish, yopiq lichka, ega @Asura

- **Sozlamalar → Batareya va animatsiyalar** (har bir qurilmada alohida saqlanadi): *Animatsion stikerlar*, *Animatsion emojilar*, *Interfeys animatsiyalari* — har biri yoqish/o'chirish. O'chirilganda GIF/WEBP stiker va emojilar birinchi kadr (oddiy rasm) bo'lib qoladi, CSS animatsiyalar to'xtaydi; interfeys animatsiyalari o'chirilsa oynalar, menyular, xabar va tugma effektlari bir zumda almashadi. Manba: `app/static/js/theme.js` (`AnimPrefs`).
- **Maxfiylik va xavfsizlik → Xabarlarimni avtomatik o'chirish.** Yoqilgandan keyin yozilgan xabarlar (umumiy chat va lichka; kanal postlari kirmaydi) 24 soatdan keyin o'chadi. Yoqishdan oldingi xabarlarga tegilmaydi. Yoqish/o'chirish vaqtlari `auto_del_windows` jadvalida saqlanadi: muddatidan oldin o'chirilsa ham, yoqilgan va o'chirilgan vaqt orasida yozilgan xabarlar o'z 24 soatidan keyin baribir o'chadi. Tekshiruv har 60 soniyada (`app/services/autodel.py`), o'chirish haqidagi xabar jonli yuboriladi.
- **Maxfiylik va xavfsizlik → Menga yozishni cheklash.** Yoqilsa, foydalanuvchiga lichkada faqat admin/ega yoza oladi. O'zi ilgari yozgan odam unga javob yoza oladi (A → B yozsa, B ham A'ga yoza oladi; `dm_allow` jadvali, mavjud yozishmalar migratsiyada kiritiladi). Umumiy chatga ta'sir qilmaydi.
- **Ega endi `@Asura`** (`OWNER_USERNAME`). Eski `@Hevorix` akkaunti o'chirilmaydi — ishga tushganda egalikdan admin darajasiga tushiriladi (xabarlari va kalitlari saqlanadi; username qayta nomlanmaydi, chunki parol va E2EE kalitlari username'dan olinadi). `@Asura` bo'lmasa `TERMCHAT_ADMIN_PASSWORD` (yoki log'dagi parol) bilan yaratiladi. Ilova nomi («Anonim Chat by Hevorix») o'zgarmagan.
- Testlar: `python -m unittest tests.test_privacy_features`, `node tests/anim_prefs_test.mjs`.

## Yangiliklar (v11 / 3.6.0): element bo'yicha animatsiyali dizayn (neon olib tashlandi)

Admin har bir foydalanuvchiga nickname, username, bio, profil sahifasi, xabar, reply va havola uchun alohida tayyor uslub beradi
(**Dizayn** tugmasi yoki profildagi **Dizayn berish**; tepada jonli namuna va «▶ Qayta ko'rsatish»). Server faqat tayyor nomlarni qabul qiladi
(`app/services/fxstyle.py`). Xabar effekti faqat yangi xabarda bir marta ishlaydi. «Animatsiyani kamaytirish» yoqilgan qurilmada hamma effekt o'chadi.
Zaxira dizayn maydonlarini ham saqlaydi.

## Yangiliklar (v3.3.0): xavfsizlikni mustahkamlash va to'liq zaxira

Mavjud funksiyalar, xabar/fayl formatlari va E2EE konvertlari **o'zgarmagan** (eski xabarlar ochiladi, eski parollar ishlaydi).

- **Parol hash'i kuchaytirildi.** scrypt N=2¹⁴ → N=2¹⁵; parametrlar hash ichida saqlanadi (`s2$15$8$1$<tuz>$<hash>`). Eski hash'lar tekshiriladi va foydalanuvchi keyingi kirishida **avtomatik yangilanadi** (sezmaydi). Zaxiradan tiklash (.acdb) ikkala shaklni ham qabul qiladi. Mavjud bo'lmagan username uchun ham bir xil vaqt sarflanadi (vaqt orqali username aniqlanmaydi).
- **Kirishga urinishlar:** (IP, username) bo'yicha mavjud cheklovga qo'shimcha — bitta IP'dan turli username'larni ketma-ket sinash (password spraying) ham cheklanadi.
- **AES-GCM nonce takrorlanmasligi kafolatlandi.** Server → mijoz yo'nalishida nonce endi `<4 bayt oqim raqami>|<8 bayt hisoblagich>` (HTTP javoblari — oqim 0, har bir WebSocket — o'z oqimi). Oldin tasodifiy edi (amalda xavfsiz, lekin kafolat yo'q edi). Mijoz kodi o'zgarmadi.
- **WebSocket:** kutilmagan xato ulanishni uzmaydi (foydalanuvchi "Server xatosi" xabarini oladi); kadr hajmi cheklangan (`ws_max_size` 1 MB, ilova darajasida ham tekshiriladi, 1009 bilan uziladi); bir akkauntdan ≤10 ulanish (ortig'i 1013); kadrlar tezligi (150/10 s) va `cmd` buyruqlari (20/10 s) cheklangan.
- **Xotira himoyasi (DoS):** shifrlangan kanal tanasi butunlay RAMga o'qiladi (AES-GCM yaxlit teg), shuning uchun bir vaqtda xotirada turuvchi katta so'rovlar jami hajmi va bir vaqtdagi fayl yuklab olishlar (≤3) cheklandi — ortig'i 503 oladi va mijoz keyinroq urinadi. Animatsiyali GIF/WEBP uchun barcha kadrlar piksellari yig'indisi cheklandi (avval 300 kadr × katta o'lcham xotirani to'ldirishi mumkin edi); katta JPEG kichraytirib dekodlanadi.
- **`'²'.isdigit()` xatosi:** raqamga o'xshash yunikod belgilari (`²`, `١`) `int()` da xato berib, WebSocket ulanishini uzar yoki 500 qaytarar edi (`/mute ²`, `/api/e2ee/keys?ids=²`, ...). Barcha joyda faqat ASCII raqam qabul qilinadi.
- **Qurilmalar (sessiyalar):** `GET /api/sessions` (ro'yxat: vaqt, "kirib qolish", hozirgisi), `DELETE /api/sessions/{id}` (faqat o'zingiznikini), `POST /api/sessions/revoke-others`. Tokenning o'zi ko'rsatilmaydi (id = hash boshi). Bekor qilingan sessiyaning WebSocket'i keyingi kadrdayoq uziladi. *(Interfeysda tugma hali yo'q — faqat API.)*
- **Davriy tozalash** (`app/services/maintenance.py`, har 30 daqiqada): yozish chala qolgan `*.tmp` fayllar, biriktirilmagan shifrlangan fayllar (+ bazadagi yetim yozuvlar), muddati o'tgan sessiyalar. Xabarlar va biriktirilgan fayllarga tegilmaydi.
- **SQLite:** `synchronous=NORMAL` (WAL bilan xavfsiz), `busy_timeout`, `Database.snapshot()` (backup API) va `integrity_check()`.

### To'liq ma'lumotlar zaxirasi (baza + shifrlangan fayllar)

Brauzerdagi `.acdb` zaxira faqat **akkauntlarni** saqlaydi. Xabarlar, kanallar, reaksiyalar, moderatsiya, sozlamalar, E2EE ommaviy kalitlari va shifrlangan media uchun serverda:

```
python tools/datasnap.py backup  --data data --out zaxira.tar.gz      # (--db-only: faqat baza)
python tools/datasnap.py verify  zaxira.tar.gz                         # SHA-256, baza butunligi, xavfsiz yo'llar
python tools/datasnap.py restore zaxira.tar.gz --data data             # serverni to'xtatib; mavjud ma'lumot bo'lsa rad etadi
python tools/datasnap.py restore zaxira.tar.gz --data data --force     # eski nusxa data/pre-restore-<vaqt>/ ga ko'chiriladi
```

- Baza SQLite backup API bilan olinadi (server ishlab turganda ham izchil). Arxiv `0600` ruxsat bilan yoziladi; avval `.part` fayliga, keyin atomik almashtiriladi.
- **`master.key` / `vapid.key` / `TERMCHAT_MASTER_KEY` arxivga KIRMAYDI.** Ularsiz `enc1:` qiymatlar va `*.enc` fayllar ochilmaydi: kalitni zaxiradan **alohida** saqlang. E2EE ma'lumotlari (`e2e1:`, `*.e2e`) server kalitiga bog'liq emas.
- Arxivda parol hash'lari bor: uni xuddi `.acdb` kabi ehtiyot qiling (shifrlanmagan; faqat serverda/o'zingizda saqlang).
- Tiklash buzuq arxivda hech narsaga tegmaydi; yo'l-o'tish (`../`) va oddiy fayl bo'lmagan elementlar rad etiladi.
- **Avtomatik nusxa:** `TERMCHAT_AUTO_BACKUP_HOURS=24` — har kuni `data/backups/auto-*.tar.gz` (faqat baza, yozilgach tekshiriladi, oxirgi `TERMCHAT_AUTO_BACKUP_KEEP` ta qoladi). Standart: o'chiq.
- Diqqat: doimiy diskli hostingda `data/` ni saqlang; vaqtinchalik diskda (masalan, bepul tarif) zaxirani boshqa joyga ko'chirib turish kerak.

### Testlar (v3.3.0)

`python -m unittest tests.test_hardening -v` — FastAPI'siz ishlaydi, har bir test o'z vaqtinchalik papkasida. Qolgan testlar: `pip install -r requirements-dev.txt && pytest`, `node tests/*.mjs`.

## Yangiliklar: reaksiyalar va xabarni bosish (Telegramdagidek)

- **Reaksiyalar** — umumiy chat, lichka va **kanal postlari**ga. Bir foydalanuvchi bitta xabarga bitta reaksiya qo'yadi (chatdagi standart emoji tanlagichdagi hamma emojilar + yaratilgan/tayyor maxsus `:nom:` emojilar; maxsuslarni faqat emoji ishlatishga ruxsati bor foydalanuvchi qo'ya oladi, yopiq (faqat adminlar) emojilar faqat adminlarga). Xabar ostida chiplar ko'rinadi (emoji + soni; o'zingizniki rangli). Chip ustiga bosish — o'z reaksiyangizni qo'yish/olib tashlash; kompyuterda chip ustiga olib borilsa kimlar qo'ygani ko'rinadi (telefonda hozircha yo'q).
- **Ikki marta bosish = reaksiya.** Xabar ustiga ketma-ket 2 marta bosilsa, tanlangan emoji (standart ❤️) qo'yiladi va ekranda «sakrab» chiqadi. **Foydalanuvchi o'zi sozlaydi:** ☰ menyu → *Reaksiya sozlamalari* — ikki marta bosishni yoqish/o'chirish va qaysi emoji qo'yilishini tanlash (sozlama shu qurilmada saqlanadi).
- **Menyudagi reaksiyalar bitta qatorda** (7 ta tezkor + ↓ tugmasi). ↓ bosilsa hamma emojilar (standart + maxsus) ro'yxati ochiladi. Chiplar ixcham va vaqt bilan bir qatorda turadi — xabar kattalashib ketmaydi.
- **Zoom o'chirilgan:** oynalar va chat chimchilab/ikki marta bosib kattalashmaydi (faqat rasm ko'rish oynasida chimchilab kattalashtirish ishlaydi).
- **Emoji tanlagich xatosi tuzatildi:** katalog (tab) almashtirilganda panel yopilib ketardi, endi ochiq qoladi.
- **Faqat emojidan iborat xabar (1–3 ta) stiker kabi katta** ko'rinadi (maxsus emoji 96px, oddiy emoji ham katta). Reaksiya va vaqt emoji ostidagi alohida qatorda turadi.
- **Reaksiya chiplari yangi dizaynda:** oddiy xabarlarda pufak ostida (o'ngda/chapda), kanal postida post ichida (izoh tugmasi tepasida). Animatsiyalar: yangi chip "pop" bilan chiqadi, yo'qolgani kichrayib ketadi, son o'zgarganda raqam yumalab almashadi, o'zingiz qo'ysangiz emoji sakraydi, boshqa odam qo'ysa chip to'lqin beradi, ikki marta bosganda emoji sakrab atrofga mayda emojilar sochiladi va xabar yengil "bosiladi", menyudagi emojilar birin-ketin chiqadi. `prefers-reduced-motion` yoqilgan bo'lsa animatsiyalar o'chadi. Test: `node tests/reactions_sync_test.mjs`.
- **Bir marta bosish = menyu.** Xabar ustiga bir marta bosilsa (telefon ham, kompyuter ham) menyu ochiladi: tepasida emoji qatori, pastda Javob berish, Nusxalash, Oldinga yuborish, Saqlash, Tahrirlash, O'chirish va h.k. Uzoq bosish/o'ng tugma ham oldingidek ishlaydi. Ikki marta bosish yoqilgan bo'lsa, bir marta bosish menyuni ~0,3 soniyadan keyin ochadi (ikkinchi bosishni kutadi); o'chirilgan bo'lsa — darrov.
- **Profil faqat rasm va nickname bosilganda ochiladi.** Avval xabar tepasidagi ism qatorining butun kengligi bosiluvchi edi, shuning uchun xabarning yuqori qismiga tekkanda profil ochilib ketardi. Endi faqat avatar va ism matnining o'zi bosiladi; xabarning qolgan joyi — menyu/reaksiya.
- Bosilmaydigan (o'zining amali bor) joylar: rasm (kattalashtirish), fayl, ovoz/video pleyeri, javob iqtibosi, kod nusxalash tugmasi, havolalar — ularda menyu chiqmaydi. Rasm/video ustida reaksiyani menyudan (uzoq bosish yoki ⌄) tanlang.
- Texnik: `reactions` jadvali (`message_id`, `user_id`, `emoji`; xabar o'chsa avtomatik o'chadi). WebSocket: `{"type":"react","id":12,"emoji":"❤️"}` (bir xil emoji qayta — olib tashlaydi, `"emoji": null` — olib tashlash). Server hammaga `{"type":"message_reaction","id":..,"rx":{"❤️":[uid,..]}}` yuboradi; xabar ko'rinishida `rx` maydoni bor (tarix, lichka, kanal postlari). Ruxsat: lichkada faqat ikki ishtirokchi, shaxsiy kanalda faqat kirish huquqi borlar; mute holatidagilar reaksiya qo'ya olmaydi; tezlik limiti (10 s ichida 40 ta).
- **Eslatma (E2EE):** reaksiya matn emas, shuning uchun xabar konvertiga kirmaydi — server kim qaysi xabarga qaysi emoji qo'yganini ko'radi (xabar mazmunini esa ko'rmaydi).
- Testlar: `pytest tests/test_reactions.py`, `node tests/reactions_tap_test.mjs`.

## Yangiliklar: kanallar asosiy oynada (Telegramdagidek)

- **«Kanallar» tabi** chap panelda (Chatlar / A'zolar yonida). Kanal bosilsa **asosiy chat oynasida** ochiladi (alohida oyna yo'q); mobilda orqaga tugmasi ham xuddi chatdagidek ishlaydi.
- **Post yozish umumiy chatdagi bilan bir xil**: E2EE (guruh kaliti + imzo), rasm, fayl (50 MB gacha), ovozli xabar, dumaloq video, kod/terminal bloklari, emoji, tahrirlash, o'chirish, belgilab o'chirish, saqlash, oldinga yuborish. Farqi: faqat adminlar yozadi; postlar chap tomonda (muallif nomisiz), ko'rishlar soni (👁) va **izoh** tugmasi bor; javob/shikoyat yo'q.
- **Izohlar** post ostida alohida panelda; ular ham E2EE (xonasi AAD ichida: `k:<post id>`), umumiy chatga tushmaydi.
- Texnik: postlar `messages` jadvalida (`channel_id`), konvert turi `k="c"` (xonasi `c:<kanal id>`). WebSocket: `{"type":"send","e2e":...,"blob":...,"ch":<id>}`. Eski (ochiq matnli) kanal postlari ishga tushganda avtomatik ko'chiriladi (🔓 belgisi bilan).
- Eslatma: kanal posti guruh kaliti bilan shifrlanadi, shuning uchun shaxsiy kanalga kirish huquqini server nazorat qiladi (kriptografik emas).

## Yangiliklar: o'z emojilaringizni yaratish (rasm yuklab)

- **Emoji tanlagich** yuborish/mikrofon tugmasi yonida (😊). Admin va ega har doim ko'radi; oddiy foydalanuvchilarda faqat ruxsat berilsa chiqadi. Tanlagichda: standart emojilar, admin yaratgan **to'plamlar (pack)** va «Boshqa» bo'limi.
- **Emoji yaratish** (tanlagich ichidagi «＋ Emoji yaratish»): rasm yuklang → nom bering → xohlasangiz animatsiya (puls, sakrash, aylanish, ...) tanlang. Natija darrov ko'rinadi (shaffoflik katakchali fonda).
  - **PNG shaffof bo'lsa** — orqa fon umuman bo'lmaydi, shundayligicha qoladi.
  - **JPG yoki boshqa rasm** — «Orqa fonni olib tashlash» yoqilsa, fon o'chiriladi (sezgirlik slayderi bilan sozlanadi). Bu tashqi xizmatsiz, serverning o'zida ishlaydi: rasm chetidagi rang fon deb olinadi. **Bir xil rangli fonda yaxshi**, murakkab fotofonda ishlamaydi (shunda aniq xabar chiqadi).
  - **GIF / animatsiyali WEBP** — animatsiya saqlanadi (≤60 kadr, avtomatik kichraytiriladi).
  - Fayl serverda qayta kodlanadi (metadata o'chadi), SVG qabul qilinmaydi. Emoji `:nom:` ko'rinishida yoziladi va xabarda rasm bo'lib chiqadi; faqat emojidan iborat (1–3 ta) xabar katta ko'rinadi.
- **Admin paneli → «Emoji» bo'limi:** to'plam yaratish/nomini o'zgartirish/yopish/o'chirish; har bir emojini to'plamga solish, «hamma / faqat adminlar» qilish, animatsiyasini o'zgartirish, o'chirish; **oddiy foydalanuvchilar uchun** umumiy ruxsat (emoji tanlagich ochiq/yopiq) va **alohida foydalanuvchiga** ruxsat: *umumiy sozlama* / *ishlatadi* / *ishlatadi + yaratadi* (yaratganlar ≤30 ta, hamma ko'radi).
- Yaratilgan emojilar ham profil nishoni sifatida berilishi mumkin. Fayllar `data/emoji/` da — bazani zaxiralagandek, shu papkani ham saqlang.
- **Eslatma (E2EE):** xabarlar shifrlangani uchun server xabar ichidagi `:nom:` ni ko'rmaydi. «Faqat adminlar» emojilari oddiy foydalanuvchi tanlagichida yashiriladi, lekin kimdir `:nom:` ni qo'lda yozsa, serverda buni to'sib bo'lmaydi.
- API: `GET /api/emojis`, `POST /api/emoji` (+ `/api/emoji/preview`), `PATCH|DELETE /api/emoji/{id}`, `/api/emoji-packs`, `PUT /api/emoji-settings`, `PUT /api/emoji-perm/{uid}`. Testlar: `pytest tests/test_emoji_custom.py`.

## Yangiliklar (v9): kod, Linux/Kali terminali, .pcap va maxsus emoji
**Hammasi xavfsiz: hech narsa ishga tushmaydi.** Kod, buyruq va fayl mazmuni faqat MATN sifatida chiqariladi (`textContent`; `innerHTML`/`eval` yo'q, CSP o'zgarmagan). Fayllar hamon `application/octet-stream` bo'lib yuklanadi.
- **Kod filtri.** Yozilgan/yopishtirilgan kod avtomatik aniqlanadi: Python, C, C++, JavaScript, TypeScript, Java, C#, Go, Rust, PHP, SQL, HTML, CSS, JSON, Bash, **Assembly (x86/ARM)**, Lua, Ruby. Ko'rinishi alohida: tilga mos rangli qorong'i blok, qator raqamlari, sintaksis bo'yog'i, gorizontal aylantirish.
  Yopishtirish (Ctrl+V / «Qo'yish») paytida kod aniqlansa, matn avtomatik ```` ```til ```` blokiga o'raladi (faqat maydon bo'sh bo'lsa). Qo'lda ham yozish mumkin: ```` ```python ... ``` ````.
- **Nusxalash tugmasi.** Har bir kod/terminal blokining ostida, alohida kichik «Nusxalash» tugmasi (bosilganda «Nusxalandi»). Terminal blokida faqat buyruqlar nusxalanadi (prompt va chiqish qirqiladi).
- **Linux / Kali buyruqlari.** `nmap -sV 10.0.0.1`, `sudo apt update`, `$ whoami`, `┌──(kali㉿kali)-[~]` va h.k. avtomatik aniqlanadi (oddiy gaplar — «cat», «python is great», «# sarlavha» — aniqlanmaydi). Ko'rinish: neon-yashil/Kali-ko'k terminal, `user㉿kali` prompti, yangi xabarda buyruq «yozilib» chiqadi, skan chizig'i, miltillovchi kursor, sarlavhada glitch. Sarlavhani bosish — animatsiyani qayta ko'rsatadi. `prefers-reduced-motion` yoqilgan bo'lsa animatsiya o'chadi. Buyruqlar HECH QACHON bajarilmaydi.
- **Fayllar.** `.py .cpp .js .asm .sh .sql .go .rs ...` yuborilsa, fayl kartasi tilga mos rangda; «👁 Kodni ko'rish» — fayl qurilmada ochilib, xabar ichida bo'yalgan holda ko'rsatiladi (400 KB gacha, binar fayl rad etiladi) + nusxalash. `.pcap/.pcapng/.cap` — Linux uslubidagi karta; «Tahlil qilish» tugmasi `tcpdump -nn -r` ko'rinishida paketlar ro'yxatini beradi (Ethernet / Linux SLL / Raw IP; IPv4/IPv6, TCP/UDP/ICMP/ARP, protokollar statistikasi). Faylning o'zi hech qayerda ochilmaydi/bajarilmaydi.
- **Maxsus emoji nishonlar.** Admin/ega oddiy foydalanuvchiga (admin qilmasdan) rasmli nishon beradi: **Kali Linux**, **Linux (Tux)**, **Anonymous niqobi**, **Ghost (Simon Riley)** — profil → «Nishon» yoki ⋮ → Foydalanuvchilar. Mavjud 10 ta animatsiya shularga ham qo'llanadi. Nishon bazada `:kali:` ko'rinishida saqlanadi.
  - **Yangi emoji qo'shish:** rasm faylni `app/static/emoji/<nom>.svg` (yoki `.png`/`.webp`, ≤256 KB) qilib qo'ying — nom: kichik harf/raqam/`_`, 2–32 belgi. Server ~10 soniyada ro'yxatga oladi, kod o'zgartirish shart emas (chiroyli nom uchun `app/emojis.py` dagi `LABELS`). API: `GET /api/emojis`.
  - **Eslatma:** ilovadagi to'rtta ikonka — original, soddalashtirilgan chizmalar (rasmiy logotiplar emas; Kali, Tux va Ghost belgilari tegishli egalarining savdo belgilari). Rasmiy rasm kerak bo'lsa, shu nomli faylni almashtirish yetarli.
- **Xabar uzunligi** 1000 → **3000** belgi (kod sig'ishi uchun; `TERMCHAT_MAX_MESSAGE_LENGTH`), E2EE konvert chegarasi 32768.
- Testlar: `node tests/richtext_test.mjs`, `node tests/richtext_dom_test.mjs`, `pytest tests/test_emojis.py`.

## Yangiliklar (v8): Save as, Forward, galochka, shifrlangan baza zaxirasi
- **Xabar menyusi** (o'ng tugma / uzoq bosish / ⌄): Javob berish, Nusxalash, Profilni ko'rish, Shikoyat qilish — va yangi:
  - **Saqlash (Save as)** — rasm, fayl va ovozli xabar uchun. Fayl shifrlangan holda yuklanadi va FAQAT qurilmada ochiladi. Kompyuterda «Save as» oynasi (joy va nom tanlanadi),
    telefonda yuklab olishlar papkasiga saqlanadi. Fayl nomi tozalanadi (`../` kabi yo'llar o'tmaydi).
  - **Oldinga yuborish (Forward)** — matn, rasm, fayl, ovozli xabar; bir nechta suhbatni (umumiy chat va lichkalar) birdaniga tanlash mumkin. E2EE: xabar qurilmada ochiladi va har bir
    suhbat uchun QAYTA shifrlanadi (yangi konvert, o'z imzom; fayl yangi AES kaliti bilan, har nusxa alohida blob). Serverga ochiq matn/fayl nomi ketmaydi, asl xabar o'chsa ham nusxa qoladi.
    «Oldinga yuborilgan: <muallif>» belgisi shifr ichida; zanjirda asl muallif saqlanadi. Eslatma: bu belgini yuboruvchi o'zi qo'yadi (tekshirib bo'lmaydi). Eski (E2EE'siz) xabarlar oldinga yuborilmaydi.
- **Galochka admin qilmasdan.** Admin/ega ⋮ → *Foydalanuvchilar* (yoki profil → *Nishon / galochka*) orqali istalgan oddiy foydalanuvchiga **✔ tasdiqlangan galochka** yoki emoji nishon beradi (admin — faqat oddiy foydalanuvchiga, ega — adminga ham).
  Foydalanuvchi admin bo'lib qolmaydi; o'ziga nishon bera olmaydi. Galochka ism yonida va profilida «Tasdiqlangan foydalanuvchi» sifatida ko'rinadi.
- **⋮ → Foydalanuvchilar**: ro'yxat — profil rasmi, nickname, @username, rol (Ega / Admin / Oddiy), chatga qancha oldin qo'shilgani; qidiruv; har biriga «Nishon».
- **Bazani yuklab olish va tiklash (faqat ega).** `⬇ Bazani yuklab olish` — foydalanuvchilar ro'yxati **shifrlangan `.acdb` fayli** sifatida yuklanadi; `⬆ Zaxiradan tiklash` — fayl ochilib, yo'q akkauntlar tiklanadi.
  - **Shifr:** parol-ibora (≥ 8 belgi) bilan BRAUZERDA: PBKDF2-SHA256 (600 000) → AES-256-GCM, sarlavha autentifikatsiyalangan (`js/backup.js`). Parol-ibora serverga yuborilmaydi; yo'qolsa fayl ochilmaydi. Server faylni ko'rmaydi ham.
  - **Ichida:** username, nickname, rol (admin/oddiy), profil rasmi, qo'shilgan vaqt (+ bio, rang, nishon, ban) va qayta kirish uchun kirish ma'lumoti (parol hash'i, ommaviy kalitlar, parol bilan o'ralgan kalit zaxirasi). Parollar va xabarlar YO'Q.
    Tiklangan foydalanuvchi o'z paroli bilan kiradi; kalitlari o'zgarmaydi, guruh kalitini onlayn a'zolar avtomatik beradi.
  - **Tiklash qoidasi:** qo'shib tiklanadi — mavjud username'larga tegilmaydi; ega (@Asura) zaxiraga kirmaydi va ustidan yozilmaydi; har bir yozuv qat'iy tekshiriladi (username, rol faqat user/admin, hash shakli, kalitlar, rasm qayta kodlanadi).
  - **Nega faqat ega:** faylda parol hash'lari bor. Oddiy adminga berilsa, u parollarni oflayn taxmin qilib boshqalarning lichkasini ocha olardi (E2EE buziladi). Adminlar ro'yxatni ko'radi va nishon beradi.
  - **Serversiz ochish:** `python tools/decode_backup.py fayl.acdb [--summary]` (xuddi shu algoritm; `cryptography` kerak).
  - Testlar: `node tests/backup_interop.mjs` (JS↔Python), `tests/test_backup.py`, `node tests/e2e_glue_test.mjs` (endi forward/saqlash E2EE tekshiruvlari bilan).

## Yangiliklar (v7): realtime, tarix, xavfsiz kirish
- **Realtime tuzatildi.** Sabablari: (1) server xabarni foydalanuvchilarga *ketma-ket* yuborar edi — bitta sekin/qotgan mijoz hammani kutdirardi; endi yuborish *parallel* va 8 s timeout bilan (qotgan ulanish uziladi va o'zi qayta ulanadi);
  (2) mijozda bitta xato yuborish zanjirini butunlay to'xtatib qo'yardi; (3) "yurak urishi" (ping) yo'q edi — o'lik ulanish bilinmasdi; (4) tarmoq so'rovlariga timeout yo'q edi; (5) 100 ta xabarning har biri uchun kalitlar alohida so'ralardi.
- **Tezkor kirish.** Chatga kirganda dastlab **10 ta** xabar keladi. Tepaga chiqilganda (yoki «⬆ Oldingi xabarlar» tugmasi bilan) oldingilari 30 tadan yuklanadi. API: `GET /api/group/history?before=<id>`, `GET /api/dm/<uid>?before=<id>`. Qayta ulanganda yuklangan eski xabarlar yo'qolmaydi.
- **Har kirishda login.** Token faqat xotirada: sahifa yopilsa/yangilansa username va parol qayta so'raladi. Maxfiy kalitlar qurilmada saqlanmaydi (paroldan qayta hosil qilinadi).
- **Faolsizlik: 5 daqiqa.** Oyna va chat ochiq qolib 5 daqiqa tegilmasa login oynasi chiqadi (server ham sessiyani o'zi tugatadi). Video/audio ijro etilayotgan bo'lsa faol hisoblanadi. Sozlama: `TERMCHAT_IDLE_LOGOUT`.
- **Onlayn/oflayn realtime.** Mijoz har 15 s ping yuboradi; 45 s hech narsa kelmasa (qurilma o'chdi, tarmoq yo'q) server uni oflayn qiladi. Boshqa sahifa/ilovaga o'tilsa yoki ekran o'chsa — `away` yuboriladi va ~6 s dan keyin oflayn; qaytganda darhol onlayn.
- **«⚠ Yangi» belgisi.** Akkaunt yoshi 7 kundan kam bo'lgan oddiy foydalanuvchilar ismi yonida va profilida «hali ishonish qiyin» belgisi bilan ko'rinadi (admin/egaga qo'llanmaydi). Sozlama: `TERMCHAT_NEW_USER_DAYS`.
- **Admin → Media boshqaruvi tuzatildi.** Media bo'lganda ro'yxat server xatosi (500) berardi: so'rov `e2e` ustunini olmagan edi. Endi ro'yxat ochiladi, o'chirilganda shifrlangan fayl yozuvi (`e2e_blobs`) ham tozalanadi.
- **Taqiqlangan so'zlar olib tashlandi** (E2EE'da server matnni ko'rmagani uchun baribir ishlamas edi): moderatsiyada faqat sekin rejim, qulf va avtomatik himoya qoldi.

## Uchdan-uchga shifrlash (E2EE, v5)

**Maqsad:** xabar matni, fayl mazmuni va fayl nomi serverga **hech qachon** ochiq holda bormaydi. Server, hosting egasi va admin faqat shifrni ko'radi.
Foydalanuvchi chat sarlavhasidagi **🔒 tugmasi** (yoki ☰ → *Shifrlash*) orqali oynacha ochadi: u yerda xabar qanday shifrlanishi, serverga nima ketishi,
server bazasida AYNAN nima yotgani va admin logida nima ko'rinishi **haqiqiy ma'lumot bilan** ko'rsatiladi.

**Qatlamlar:**
1. **Xabar (E2EE):** brauzerda AES-256-GCM bilan shifrlanadi, ECDSA bilan imzolanadi. Server `e2e1:…` konvertini saqlaydi va uzatadi, ocha olmaydi.
2. **Kanal:** brauzer↔server o'rtasida alohida ECDH+AES-GCM kanali (Wireshark/DevTools faqat shifr ko'radi) — v3 dan o'zgarmagan. HTTPS baribir shart.
3. **Bazada:** E2EE konvertlar bazaga o'zgartirilmay yoziladi; eski ma'lumotlar server kalitida shifrlangan.

**E2EE protokoli** (kod: `app/static/js/e2ee.js`, tekshiruvchi: `app/e2ee.py`, Python nusxasi: `tests/e2e_client.py`)
- **Parol:** serverga parolning o'zi bormaydi. Brauzer `PBKDF2-SHA256(parol, SHA256("anonim-chat/e2ee/v1/"+username), 600 000)` dan 512 bit oladi:
  birinchi 256 bit — maxfiy kalitlarni o'rash kaliti (qurilmadan chiqmaydi), ikkinchi 256 bit — serverga ketadigan *kirish kaliti* (server uni yana scrypt bilan hash qiladi).
- **Shaxsiy kalitlar:** har bir foydalanuvchida ECDH P-256 (shifrlash) va ECDSA P-256 (imzo) juftligi. Maxfiy qismi parol kalitida o'ralib serverda *zaxira* sifatida turadi
  (boshqa qurilmadan kirish uchun); qurilmada IndexedDB'da eksport qilib bo'lmaydigan `CryptoKey` sifatida saqlanadi.
- **Lichka:** `HKDF(ECDH(mening maxfiy, uning ommaviy))` → AES-256-GCM kaliti.
- **Umumiy chat:** guruh kaliti (AES-256-GCM, "avlod/epoch"lar bilan). Server unda yo'q: faqat har bir a'zoning ommaviy kaliti bilan o'ralgan nusxa (`room_keys`) bor.
  Birinchi a'zo kalitni yaratadi; kaliti bor onlayn a'zo yangi a'zolar uchun uni o'rab beradi; o'ralgan kalit `check` bilan tekshiriladi (poyga/soxta o'rashga qarshi).
  **Ban/akkaunt o'chirilganda** admin mijozi guruh kalitini yangilaydi (yangi avlod, faqat joriy a'zolarga).
- **Konvert:** `e2e1:` + base64url(JSON{v,k,ep,n,c,s}); AAD = `ac/e2e1|<xona>|<yuboruvchi id>|<epoch>` (shifr boshqa xonaga/yuboruvchiga ko'chirilsa ochilmaydi);
  imzo `ECDSA(AAD‖iv‖shifr)`. Matn 64 baytga to'ldiriladi (uzunlik yashiriladi). Javob (reply), fayl nomi/turi/kaliti — hammasi shifr ichida.
- **Fayllar:** brauzerda har fayl uchun alohida AES kalit bilan shifrlanadi, server `data/media/*.e2e` ni o'zgartirmay saqlaydi. Rasmlar yuborishdan oldin qayta kodlanadi (EXIF/GPS yo'qoladi),
  kichik xira nusxa ham shifr ichida ketadi.
- **Kalit o'zgarishi:** boshqa foydalanuvchilarning barmoq izlari birinchi ko'rilganda qadab qo'yiladi (TOFU); o'zgarsa ogohlantirish chiqadi va lichka bloklanadi (🔒 oynasidan qabul qilinadi).

**Admin nimani ko'radi:** ⋮ → *Xabarlar* va `/logs` — FAQAT shifrlangan konvertlar (epoch, hajm, imzo hajmi). Tahrir/o'chirish tarixi ham faqat shifr. Harakatlar logiga xabar matni yozilmaydi.
**Shikoyat:** server matnni bilmagani uchun shikoyatchi *xohlasa* o'sha BITTA xabar matnini ochib beradi; admin buni o'z qurilmasida asl shifrlangan xabarga solishtirib tekshiradi (✅/⚠).
**Moderatsiya:** mute, ban, o'chirish, sekin rejim, qulf, spamga qarshi — ishlaydi (matn kerak emas).

**Eski akkauntlar va xabarlar:** eski akkaunt birinchi kirishda parolni qayta kiritadi — kalitlar brauzerda yaratiladi, parol hash'i almashadi (`/api/e2ee/setup`).
`@Asura` ham shunday o'tadi (shundan keyin `TERMCHAT_ADMIN_PASSWORD` o'zgarishi parolni almashtirmaydi). Eski (E2EE'dan oldingi) xabarlarni server hali o'qiy oladi: ular 🔓 belgisi bilan chiqadi;
ega ularni ⋮ → *Moderatsiya* → *Eski xabarlarni o'chirish* orqali butunlay o'chira oladi. `TERMCHAT_REQUIRE_E2EE=1` (standart) bo'lsa, server ochiq matnli xabarni umuman qabul qilmaydi.

**Muhim chegaralar (halol):**
- Umumiy chat — ochiq guruh: ro'yxatdan o'tgan har kim kalit oladi, shu jumladan o'z qurilmasida admin ham (a'zo sifatida). E2EE serverdan va log/bazadan himoya qiladi, a'zolardan emas.
- Zararli server yashirincha soxta a'zo qo'shib unga kalit o'ratishi mumkin: barmoq izlarini (🔒 → *Kalitlar*) tekshiring.
- Sahifa kodini ham server beradi: SRI tashqi o'zgarishdan himoya qiladi, lekin zararli serverdan emas. To'liq ishonch uchun kodni tekshiring yoki serverni o'zingiz yuriting.
- Metama'lumotlar (kim, qachon, kimga, hajm, IP), profil ma'lumotlari va avatarlar ochiq.
- Parol yo'qolsa, kalit zaxirasini tiklab bo'lmaydi. Lichkada oldinga maxfiylik (forward secrecy) yo'q.
- Brauzerning WebCrypto'si faqat HTTPS/localhost'da ishlaydi; ECDH kanalini faol hujumchidan TLS sertifikati himoya qiladi.

**Boshqa choralar:** SRI (`integrity="sha384-…"` avtomatik), qat'iy CSP, HSTS, `Permissions-Policy`, COOP/CORP, WebSocket `Origin` tekshiruvi, SQL parametrlangan, DOM'ga faqat `textContent`.

## Ovozli xabar (v6)
**Ishlatish:** yozuv maydoni bo'sh bo'lganda 🎤 tugmasi chiqadi. **1-bosish** — yozish boshlanadi (taymer, qizil nuqta), **2-bosish** — to'xtaydi va yuboriladi. 🗑 yoki `Esc` — bekor qilish. Android, iOS va kompyuter brauzerlarida ishlaydi (HTTPS yoki localhost shart; mikrofon ruxsati so'raladi).
Qabul qiluvchida: ▶ / ⏸ tugmasi, progress (bosib o'tish mumkin), vaqt va tezlik (1x / 1.5x / 2x).

**Ovoz o'zgartirish** (`app/static/js/voice.js`, hammasi qurilmada): ovoz **qalinlashtiriladi** (balandligi VA tembri ≈ 3–5 yarim ton pasayadi, tezligi o'zgarmaydi):
mono 16 kHz → past chastota filtri → resampling (r ∈ 0.74…0.82, **har xabarda tasodifiy**) → WSOLA bilan tezlik tiklanadi → 16-bit WAV (metama'lumotsiz). Qalinlikni `SHIFT_MIN/SHIFT_MAX` bilan sozlang.
**Xom ovoz qurilmadan chiqmaydi:** "asl ovozni yuborish" kod yo'li yo'q; o'zgargan ovoz oddiy fayl kabi E2EE bilan shifrlanadi (har xabarga alohida AES-256-GCM kalit, serverda faqat `.e2e` shifr). Server va admin na ovozni, na uning ovoz ekanini biladi.

**Xavfsizlik choralari:**
- `Permissions-Policy: microphone=(self), camera=(self)` (faqat shu sayt; boshqa ruxsatlar yopiq), CSP o'zgarmagan (skript/uslub faqat `'self'`).
- Mikrofon yozuv tugashi/bekor qilinishi bilan **darhol o'chadi** (brauzer indikatori ham). Ilova fonga o'tsa, sahifa yopilsa, chat almashsa, mikrofon uzilsa — yozuv bekor qilinadi (yashirin yozib qolish yo'q).
- Eng ko'pi 5 daqiqa (chegaraga yetsa avtomatik yuboriladi), 0,8 soniyadan qisqasi yuborilmaydi. Mute, qulflangan chat, o'chirilgan akkaunt tekshiruvlari oddiy xabardagidek; yuklash tezligi cheklovi (`/api/e2ee/blob`) amal qiladi.
- Oluvchi tomonda ovoz faqat `audio/wav` bo'lsa va faqat qurilmada ochiladi (`blob:` URL, ijro tugagach/xabar o'chganda bekor qilinadi); "ovozli xabar" belgisiga faqat shifr ichidagi imzolangan ma'lumot asosida ishoniladi.

**Halol chegaralar:** bu *niqoblash*, mutlaq anonimlik emas. Gap uslubi, so'z tanlash, til, urg'u va fon shovqini odamni baribir ayon qilishi mumkin; kuchli maxsus tahlil qiluvchi hujumchi pasaytirishni qisman qaytarishga urinishi mumkin (tasodifiy nisbat buni qiyinlashtiradi). Metama'lumotlar (kim, qachon, kimga, hajm) ochiq qoladi. Kuchliroq niqob kerak bo'lsa `SHIFT_MIN` ni kamaytiring.
Test: `node tests/voice_dsp_test.mjs` (ovoz balandligi pasayishi, davomiylik, WAV, tasodifiy nisbat).

## Yangiliklar (v4)
- **Har qanday fayl, 50 MB gacha**: 📎 tugmasi, Ctrl+V yoki sudrab tashlash. Rasm — metadata tozalanib ko'rsatiladi; video/audio — «ijro etish»; boshqalari (pdf, zip, exe...) faqat yuklab olinadi (hech qachon sahifa sifatida ochilmaydi). Limit: `TERMCHAT_MAX_MEDIA_BYTES`.
- **Fayllar diskda shifrlangan** (`data/media/*.enc`): AES-256-GCM, har bir fayl uchun alohida kalit (master kalit bilan o'ralgan); fayl nomi bazada shifrlangan. Fayl faqat tizimga kirgan foydalanuvchiga beriladi (lichka — faqat ikki ishtirokchiga). Eski ochiq fayllar birinchi ishga tushishda avtomatik shifrlanadi.
- **Media boshqaruvi** (⋮ → *Media boshqaruvi*): umumiy chatdagi fayllar tur/hajm/yuboruvchi bo'yicha filtrlanadi, tanlab o'chiriladi. Fayl nomi va mazmuni ko'rsatilmaydi (adminga ham), lichka fayllari kirmaydi; logga faqat o'chirilgan SON yoziladi.
- **Loglarni o'chirish**: Harakatlar — tanlanganlarni yoki filtrdagi hammasini; Xabarlar — «Tarixni tozalash». Eganing yozuvlarini faqat ega o'chira oladi.
- **Emoji nishon** (galochka o'rniga): ega/adminlar o'zlariga, shuningdek oddiy foydalanuvchilarga ham beradi. 10 ta CSS-animatsiya (puls, sakrash, aylanish, nur, kamalak...), 50+ emoji yoki o'zingiznikini kiriting.
- **Moderatsiya**: ogohlantirishlar (3 tadan keyin 1 soat avtomatik mute, `/warn /unwarn`), foydalanuvchi xabarlarini tozalash (`/purge`), sekin rejim, chatni qulflash, spamga avtomatik mute, foydalanuvchi shikoyatlari («Shikoyat qilish» → admin *Shikoyatlar*).

## Tezkor ishga tushirish
```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
python run.py            # http://localhost:8000
```
Docker: `docker compose up -d --build`

**Ega — doim `@Asura`.** Bu username band (Asura, ASURA, asura — hammasi band) va ishga tushganda avtomatik yaratiladi.
Paroli `TERMCHAT_ADMIN_PASSWORD` dan olinadi; bermasangiz, birinchi ishga tushishda tasodifiy parol yaratilib **logga bir marta** yoziladi (kirgach profilda almashtiring).
Eski (v1) baza bo'lsa ham bo'ladi: ishga tushganda yangi ustunlar avtomatik qo'shiladi, ma'lumotlar saqlanadi.

## Imkoniyatlar
- **Telegramga o'xshash interfeys**: chap tomonda chatlar ro'yxati (qidiruv, o'qilmagan xabarlar soni), o'ngda suhbat; telefonda to'liq ekranli, orqaga tugmali
- **Xabarlar**: o'zimniki o'ngda, boshqalarniki chapda, bir odamning ketma-ket xabarlari guruhlanadi, kun ajratgichlari, vaqt, "tahrirlangan", lichkada ✓ / ✓✓ (o'qildi)
- **Xabarga amallar** (o'ng tugma / telefonda uzoq bosish / ⌄ tugma): nusxalash, tahrirlash, o'chirish, profilni ko'rish
- **Guruh a'zolari**: chat sarlavhasini bosing — a'zolar ro'yxati (onlayn birinchi, qidiruv bilan). A'zoni bossangiz — uning profili
- **Profil**: rasm, nickname, @username, bio, onlayn holati, qo'shilgan sana, "Xabar yozish" tugmasi
- **Shaxsiy yozishmalar**: istalgan a'zoga lichkada yozish; faqat ikki ishtirokchi ko'radi (adminlar ham ko'rmaydi, `/logs` ga ham tushmaydi)
- **Premium admin dizayni**: ega va adminlarda ismi yonida galochka (tasdiqlangan belgi: egada oltin, adminda binafsha), gradient ism, nurli avatar halqasi, xabar ramkasi, profilda premium banner
- **Moderatsiya** (profil oynasidagi panel; harakatlar logiga yoziladi): mute (10 daqiqa / 1 soat / 1 kun / doimiy), ban/unban; ega esa admin tayinlaydi/oladi. Adminga faqat ega mute/ban qila oladi
- Profil sozlamalari (menyu ☰): 3 tagacha rasm (faollashtirish/o'chirish), nickname, bio, parol, tungi/kunduzgi mavzu
- **Rasm yuborish** (📎 yoki Ctrl+V): izoh (caption) bilan yoki izohsiz, umumiy chatda ham, lichkada ham; bosilsa kattalashadi. Katta fotolar yuborishdan oldin avtomatik kichraytiriladi
- **Javob berish (reply)**: xabarni o'ng tugma / uzoq bosish / ⌄ → «Javob berish»; iqtibos bosilsa asl xabarga o'tadi
- **Profil rangi**: ega va adminlar profil sozlamalarida o'z rangini tanlaydi (ism, galochka, ramka, profil banneri shu rangda)
- **Admin paneli** (faqat adminlarga ko'rinadigan ⋮ tugmasi, chap panel tepasida):
  - *IP lar* — qaysi IP'dan qaysi profil (@username) necha marta kirgan, nechta sessiya/xabar, xato urinishlar; IP yoki username bo'yicha qidirish
  - *Harakatlar* — barcha harakatlar logi (kirish/chiqish, xabar, rasm, tahrir, o'chirish, profil, parol, moderatsiya, blok), IP/username/amal bo'yicha filtr
  - *Bloklanganlar* — IP'ni bloklash/blokdan ochish (spamga qarshi). Bloklangan IP'dan sayt ochilmaydi, ulanishlar darhol uziladi
- Real vaqt (WebSocket), avtomatik qayta ulanish, onlayn/oflayn holati

## Yozish qoidalari
Xabar uzunligi ≤ 1000 belgi; Enter — yuborish, Shift+Enter — yangi qator (telefonda Enter yangi qator, yuborish tugmasi bor). Bo'sh input'da ↑ — oxirgi xabarni tahrirlash.

## WebSocket protokoli (`/ws?k=<kid>`, barcha kadrlar shifrlangan; birinchi kadr `{"a":"<token>"}`)
Quyida shifrlanishdan OLDINGI (ochiq) JSON ko'rsatilgan.
Mijoz → server:
```json
{"type":"send","text":"salom"}                 // umumiy chat
{"type":"send","text":"salom","to":12}         // lichka (user id)
{"type":"send","text":"javob","reply_to":7}      // xabarga javob (rasm: POST /api/messages/image)
{"type":"edit","id":5,"text":"yangi matn"}
{"type":"delete","id":5}
{"type":"read","with":12}                      // lichkani o'qilgan deb belgilash
{"type":"cmd","text":"/mute user 10"}          // moderatsiya (UI tugmalari ishlatadi)
```
Server → mijoz: `init`, `message`, `message_edited`, `message_deleted`, `read`, `presence`, `user_update`, `members_changed`, `system`, `banned`.

## HTTP API (`Authorization: Bearer <token>`; amalda `POST /api/x` shifrlangan konvert ichida)
| Yo'l | Vazifa |
|---|---|
| `POST /api/register` `POST /api/login` `POST /api/logout` `GET /api/me` | kirish |
| `GET /api/username/{name}` | username bo'shmi (katta-kichik harfsiz) |
| `POST /api/messages/image` | rasm + `caption`, `to?`, `reply_to?` (multipart) |
| `GET /api/admin/ips?q=` `GET /api/admin/activity?ip=&user=&action=&before=` `GET /api/admin/messages?q=&state=deleted|edited&before=` (shifrdan ochilgan xabarlar logi) | admin: IP jamlanmasi, harakatlar logi |
| `GET/POST /api/admin/blocks` `POST /api/admin/unblock` | admin: IP bloklash |
| `GET /api/members` | a'zolar + onlayn id'lar (adminga ban/mute holati ham) |
| `GET /api/dialogs` | lichka suhbatlari (oxirgi xabar, o'qilmaganlar) |
| `GET /api/dm/{user_id}` | lichka tarixi |
| `PATCH /api/profile` `{nickname?, bio?, color?}` (`color` — faqat ega/adminlar) | profilni yangilash |
| `POST /api/password` `{old, new}` | parolni almashtirish |
| `GET /api/avatars` `POST /api/avatar` `POST /api/avatars/{id}/use` `DELETE /api/avatars/{id}` `POST /api/avatar/clear` | profil rasmlari |

## Tuzilma
```
app/
  main.py            ilova fabrikasi, xavfsizlik sarlavhalari (CSP), xato ishlovchilar
  config.py          sozlamalar (TERMCHAT_* / .env)
  db.py              SQLite ulanishi, sxema va avtomatik migratsiya
  security.py        scrypt, token hash, RateLimiter
  roles.py errors.py constants.py schemas.py
  repositories/      faqat SQL (users, sessions, messages, avatars, logs)
  services/          biznes mantiq (auth, chat, directory, moderation, avatars, views)
  commands/          moderatsiya buyruqlari reyestri (UI tugmalari shularni chaqiradi)
  realtime/          ConnectionManager + events (xabarni to'g'ri auditoriyaga yetkazish)
  api/               HTTP/WS endpointlar (auth, social, avatars, ws)
  static/            index.html, css/style.css, js/theme.js, js/app.js, js/voice.js (ovozli xabar)
tests/               pytest (test_core: servis+buyruqlar, test_api: HTTP/WS)
deploy/              nginx.conf, systemd unit
```
Rasmlar `data/uploads/`, profil rasmlari `data/avatars/` da (tasodifiy 32 belgili nom bilan, `/uploads/...`).
Qatlamlar: `api/commands → services → repositories → db`. Servislar WebSocket'ni bilmaydi; real vaqt xabarlarini chaqiruvchi (api/commands) `realtime.events` orqali yuboradi: umumiy chat — hammaga, lichka — faqat ikki ishtirokchiga.

## Moderatsiya buyruqlari (ixtiyoriy)
Oddiy foydalanuvchilarga kerak emas. Admin tugmalari ichida shu buyruqlar ishlaydi: `/mute /unmute /ban /unban` (admin), `/addadmin /rmadmin` (ega), `/logs` (o'chirilgan/tahrirlangan umumiy xabarlar logi). Yangi buyruq qo'shish: `app/commands/` ichida `@command(...)`.

## Sozlamalar
`.env.example` ga qarang. Muhimlari: `TERMCHAT_DATA_DIR` (baza va rasmlar), `TERMCHAT_TZ_OFFSET` (`/logs` vaqti).

## Render.com'ga joylash
1. Loyihani GitHub'ga yuklang (`.env` va `data/` `.gitignore` da).
2. Render → **New → Blueprint** → repozitoriyni tanlang (`render.yaml` avtomatik o'qiladi).
3. `TERMCHAT_ADMIN_PASSWORD` so'ralganda **@Asura** uchun parol kiriting (kamida 6 belgi).
4. Deploy tugagach saytga kirib `Asura` + shu parol bilan kiring.

**Doimiy disk shart:** `render.yaml` da `starter` reja va `/var/data` disk bor. Bepul rejada disk yo'q — bazadagi foydalanuvchilar, xabarlar va rasmlar har qayta ishga tushishda yo'qoladi (shuningdek, bepul xizmat 15 daqiqa harakatsizlikda uxlaydi).
**IP aniqlash:** Render proxy ortida haqiqiy IP `CF-Connecting-IP` dan olinadi. Deploydan keyin admin panelida *IP lar* bo'limida o'z IP'ingiz to'g'ri ko'rinayotganini tekshiring (ichki `10.x` manzillar chiqsa, IP bloklash ishlamaydi).
**Ma'lumot:** harakatlar logi 90 kun saqlanadi (`TERMCHAT_ACTIVITY_DAYS`). Parollar va shaxsiy xabarlar matni/qabul qiluvchisi logga yozilmaydi.

## Production (o'z serveringiz)
- `deploy/nginx.conf` (WebSocket upgrade bilan) + `certbot` orqali HTTPS; `deploy/termchat.service` (systemd).
- **Faqat 1 ta worker** ishga tushiring: onlayn ulanishlar va tezlik limitlari xotirada. Ko'p worker kerak bo'lsa, Redis pub/sub qo'shiladi.
- `--forwarded-allow-ips=*` ni faqat ilova nginx ortida bo'lganda ishlating (klient IP'si limitlar uchun kerak).
- `data/` papkasini zaxiralang (`chat.db` + `avatars/`).

## Testlar
E2EE: `tests/test_e2ee.py` (server xizmatlari + brauzer kodi bilan mosligi, `node` kerak), `node tests/e2e_glue_test.mjs` (app.js E2EE mantig'i), `node tests/e2e_js_interop.mjs` (JS↔Python).
```bash
pip install -r requirements-dev.txt
pytest        # test_core, test_secure (shifrlash), test_api (HTTP/WS)
```

## Xavfsizlik
Parollar scrypt bilan; tokenlar bazada faqat SHA-256 hash; login/ro'yxatdan o'tish/xabar limitlari; qat'iy CSP (inline skript va uslub yo'q); matn DOM'ga `textContent` bilan chiqariladi; rasmlar real imzo bo'yicha tekshiriladi va tasodifiy nom bilan saqlanadi; lichka faqat ishtirokchilarga yetkaziladi va faqat yuboruvchi o'chira oladi.

## Keyingi versiyalar uchun g'oyalar
"yozmoqda…" holati, xonalar (channels), Redis bilan ko'p worker, xabar qidiruvi.

## Dumaloq video xabar (Telegram kabi)

- Mikrofon tugmasi: **bir marta bosish** — 🎤 ovoz ↔ 📹 dumaloq video rejimi almashadi; **bosib turish** — yozish, **qo'yib yuborish** — yuborish; **chapga surish** — bekor; **yuqoriga surish** — qulflash (qo'lsiz yozish, tugma = yuborish).
- Video yozuvida doirani bosish yoki 🔄 — old/orqa kamera (yozuv to'xtamaydi). Eng ko'pi 60 soniya.
- E2EE: video (ovozsiz) + o'zgartirilgan ovoz bitta paketga (`VN01`) solinib, qurilmada AES-GCM bilan shifrlanadi; server faqat shifrni ko'radi. Xom ovoz ovozli xabardagidek qurilmadan chiqmaydi (`Voice.disguise`).
- `Permissions-Policy` endi `camera=(self)` ni ham ruxsat beradi. Sinov: `node tests/vnote_pack_test.mjs`.


## Ilovani o'rnatish va bildirishnomalar

### O'rnatish tugmasi
- Saytga **birinchi marta** kirilganda oyna o'zi chiqadi: «Iltimos, 10 soniya kuting» — halqali sanagich va 4 ta tayyorlik belgisi
  (xizmat, fayllar, bildirishnomalar, o'rnatishga tayyorlik). 10 soniyadan keyin **«O'rnatish»** tugmasi yonadi.
- Android/desktop Chromium (Chrome, Edge, Samsung, Xiaomi, Honor, Vivo…): bir bosishda o'rnatiladi.
- iPhone/iPad va boshqa brauzerlar o'rnatishga ruxsat bermaydi: oyna aniq qadamma-qadam yo'riqnoma ko'rsatadi.
- Bir marta kutilgandan keyin keyingi kirishlarda oyna avtomatik chiqmaydi; kirish oynasidagi «Ilovani o'rnatish» tugmasi doim bor.

### Bildirishnomalar (brauzer butunlay yopiq bo'lsa ham)
- Kirgandan so'ng ruxsat so'raladi (yoki menyu → «Bildirishnomani yoqish»). Ruxsat bo'lsa qurilma serverda obuna bo'ladi (Web Push, VAPID).
- **Umumiy chatga** yozishsa — «Umumiy chat» bildirishnomasi; **lichkaga** yozishsa — yozuvchi nomi bilan bildirishnoma. Ketma-ket xabarlar
  bitta bildirishnomaga yig'iladi («3 ta yangi xabar»). Bosilganda aynan shu chat ochiladi.
- Onlayn bo'lsangiz xabarlar avvalgidek WebSocket orqali real vaqtda keladi; push faqat **oflayn** (brauzer yopiq, ekran o'chiq, sahifa yashirin) qabul qiluvchilarga ketadi.
- **E2EE saqlanadi:** push ichida xabar matni yo'q — faqat «kim yozdi» va «umumiy/lichka». Yuk RFC 8291 bo'yicha qurilma kaliti bilan shifrlanadi
  (Google/Apple/Mozilla uni o'qiy olmaydi). Server tashqi kutubxonasiz, faqat `cryptography` bilan yuboradi.
- Xavfsizlik: server faqat tanilgan push xizmatlariga (`*.googleapis.com`, `*.push.services.mozilla.com`, `*.notify.windows.com`,
  `*.push.apple.com`) so'rov yuboradi (SSRF himoyasi); qo'shimcha domen: `TERMCHAT_PUSH_HOSTS`.
- «Chiqish» tugmasi shu qurilma obunasini o'chiradi. Faolsizlikdan avtomatik chiqishda obuna saqlanadi (bildirishnoma kelaveradi).
- **iPhone:** bildirishnoma faqat bosh ekranga o'rnatilgan ilovada ishlaydi (iOS 16.4+). **HTTPS majburiy** (Render'da tayyor).
- VAPID kaliti `DATA_DIR/vapid.key` da saqlanadi — doimiy diskda bo'lishi shart (Render: `/var/data`). Yo'qolsa hamma qayta yoqadi.


## Kanallar

- **Admin** kanal yaratadi: nomi (nickname), username'i (`@kanal_nomi`), rasmi (profil rasmi kabi tozalanadi). Menyu: Kanallar tabi → «+».
- **Ommaviy kanal** — barcha a'zolarga ko'rinadi; hamma o'qiydi va komment yozadi, post faqat adminlar yozadi.
- **Shaxsiy kanal** — ro'yxatda KO'RINMAYDI: faqat adminlarga ko'rinadi. Parol qo'yiladi; boshqalar kanal username'i va parolini kiritib qo'shiladi
  (qulf tugmasi). Parol almashsa eski a'zolar chiqariladi; ommaviy qilinsa a'zolik tozalanadi. Parol urinishlari cheklangan; xato xabari bir xil
  (kanal borligi oshkor bo'lmaydi).
- **Post ostida:** ko'rishlar soni (har foydalanuvchi bir marta, post muallifi sanalmaydi; post ekranda 50% ko'ringanda hisoblanadi, real vaqtda
  yangilanadi) va kommentlar. Kommentlar **faqat shu post ostida** ko'rinadi, umumiy chatga tushmaydi. O'z kommentini egasi, istalganini admin o'chiradi.
- Real vaqt: yangi post, komment, ko'rishlar soni va o'chirishlar WebSocket orqali darhol keladi. Oflayn a'zolarga push bildirishnoma ketadi
  («Kanalda yangi post»; shaxsiy kanalda faqat adminlar va a'zolarga).
- **Muhim (shifrlash):** kanal matnlari umumiy chat kabi E2EE EMAS — server ularni o'qiy oladi (moderatsiya uchun), lekin bazada AES-GCM bilan
  shifrlangan holda saqlanadi. Hozircha kanalda faqat matn (rasm/fayl yo'q).

## Yangilanishlar (v3.4.1)
- **Yozish paneli yo'qolishi:** ko'rinadigan oyna har doim haqiqiy `visualViewport` bo'yicha o'rnatiladi; qo'riqchi (`Composer.guard`) panelni chat ochilganda, ilovaga qaytganda, klaviatura yopilganda va har 1,5 soniyada tekshirib, kerak bo'lsa o'zi tuzatadi.
- **Emoji:** `.png` va `.gif` (animatsiyali) yaratish; emojilar, to'plamlar, ZIP ichidagi fayllar soniga cheklov yo'q; nom 2–32 belgi; rasm 20 MB gacha, ZIP 50 MB gacha.
- **Server holati** (adminlar): Sozlamalar → «Server holati». CPU, RAM, disk, ulanishlar, so'rovlar, baza va aloqa tezligi har 2 soniyada yangilanadi. API: `GET /api/admin/server-status`.

## Oflayn rejim va Oflayn Shaxmat (v3.7.0)
- Internet yo'q bo'lsa kirish ekranida «Oflayn ko'rish» (oldin yuklangan xabarlar, faqat o'qish) chiqadi; Gaming bo'limida Online Shaxmat ichidagi «Bot bilan o'ynash» internetsiz ham ishlaydi. Shaxmat serversiz ishlaydi (`static/js/chessbot.js`).
- `frontend/js/app/` — `app.js` ning modullari (`_order.txt` — yig'ish tartibi); `frontend/css/` — `style.css` bo'laklari.

## Missiyalar, dizayn paketlari va inventar

- **Shaxmat boti (client)** o'yin boshlanganda serverdan *chipta* oladi (`POST /api/missions/start`), g'alabada yurishlar ro'yxatini yuboradi (`/api/missions/submit`).
  Server chipta egasini/bir martaligini/yoshini tekshiradi, yurishlarni `chess_engine` bilan noldan qayta o'ynaydi, foydalanuvchi matt qo'ygan bo'lishi,
  kamida 8 ta yurish va yurish boshiga ≥1 soniya o'tgan bo'lishi shart. Maslahat/«orqaga» ishlatilsa ochko berilmaydi (admin o'chira oladi).
- Standart ochkolar: 3-daraja +5, 4-daraja +10, 5-daraja +25 (1–2-daraja 0); haqiqiy o'yinchini yutish +10 (kamida 8 yurish, juftlik limiti reyting bilan bir xil). Admin → **Missiya paneli**dan o'zgartiriladi (kunlik limit ham).
- **Paket** = admin yig'adigan maxsus dizaynlar to'plami (ramka, fon, rang, nick/bio/xabar/profil-rasm animatsiyasi, nishon). Ularning nomlari `x_` bilan
  boshlanadi; oddiy «Profil dizayni»da yo'q, server ham `/profile/design` orqali qabul qilmaydi — faqat inventardan kiyiladi.
- **Missiya** = «o'yinda X ochko to'plasang → shu paket». Ochko yetganda paket inventarga ochiladi (har element alohida, bir marta).
- **Inventar**: Sozlamalar → Inventar yoki o'yin menyusi. Kiyish/olib qo'yish alohida; holat foydalanuvchining haqiqiy profilidan olinadi.
- Jadvallar: `design_packs, mission_rules, mission_points, mission_games, mission_wins, mission_rewards, mission_claims, inv_items` (avtomatik yaratiladi).
- Testlar: `python -m unittest tests.test_missions`.

## ⛀ Rus shashkasi (Gaming bo'limi)

- **Qoidalar:** rus shashkasi — shashka oldinga yuradi, oldinga va orqaga uradi; damka uzoqdan uradi; urish majburiy va boshlangan urish oxirigacha davom etadi; urish paytida damka bo'lsa shu yurishda davom etadi; "turk zarbasi"; durang — 3 marta takrorlanish yoki 15 yurish faqat damkalar bilan. Dvigatellar: `app/games/checkers_engine.py` (server) va `app/static/js/checkers.js` (brauzer + bot, Web Worker). Ikkalasi bir xil natija beradi (tasodifiy holatlarda tekshirilgan, boshlang'ich holat perft: 7, 49, 302, 1469, 7482, 37986).
- **Oflayn (bot):** qurilmada, internet shart emas. 5 daraja, tomon tanlash, 💡 maslahat, ↩ orqaga, to'xtatilgan o'yin saqlanadi (`tc_offck`). Ochko bermaydi.
- **Online:** boshqa foydalanuvchini taklif qilish (60 soniya, oflayn bo'lsa push), har biriga 10 daqiqa, server yurishlarni tekshiradi. Yutuq +10, mag'lubiyat −10 (`game_stats`, `game="checkers"`; shaxmatdagi kabi: ≥8 yarim yurish, bir juftlik bilan kuniga 5 tagacha). Xabarlar `ck_*` (`app/services/checkers.py`), shaxmat bilan bir vaqtda o'ynab bo'lmaydi.
- **Score board:** shashka menyusida 🏆 (faqat haqiqiy o'yinchilar, `GET /api/games/checkers/scoreboard`).
- **Missiyalar:** shaxmatdagi tizimga ulandi (`game="checkers"`). Admin «Missiyalar» bo'limida shashka uchun ochko qoidalarini (bot_1…bot_5, pvp_win) va mukofot paketli missiyalarni xuddi shaxmatdagidek belgilaydi. Botni internet orqali boshlangan o'yinda yutsangiz server yurishlarni qayta o'ynab tekshiradi va ochko beradi; haqiqiy o'yinchini yutish ham ochko beradi.
- **Hali qilinmagan:** reaksiyalar.
- **Muhim:** yuborilgan `app.js` / `style.css` manba (`frontend/`) dan oldinroq qo'lda tahrirlangan, shuning uchun `tools/build_frontend.py` bilan qayta yig'ish ularni buzadi. Shashka o'zgarishlari ham manbaga, ham yig'ilgan fayllarga alohida qo'shildi.
- Testlar: `python -m unittest tests.test_checkers`, `node tests/checkers_engine_test.mjs`, `node tests/checkers_ui_test.mjs`.

## 🃏 Durak (Gaming bo'limi)

- **Qoidalar:** podkidnoy durak, 36 karta, 2–4 o'yinchi, kozer, qo'shib tashlash (podkidka), «olaman» / «bito». Dvigatellar: `app/games/durak_engine.py` (server) va `app/static/js/durak.js` (brauzer + bot). Ikkalasi bir xil: tasodifiy o'yinlarda har bir qadam solishtirilgan (`tests/test_durak.py`).
- **Oflayn (botlar):** 1–3 bot, internet shart emas. Bot kozerni saqlaydi: avval kozer bo'lmagan eng past karta bilan uradi/yuradi, faqat boshqa karta bilan ura olmasa (yoki kolodada karta tugaganda) kozerni ishlatadi; yuqori kozer kerak bo'lsa, ba'zan olishni afzal ko'radi.
- **Online:** «Stol ochish» → do'stlarni taklif qilish (60 soniya, oflayn bo'lsa push) → egasi boshlaydi (2–4 kishi). Har kimga faqat o'z kartalari yuboriladi, har bir harakat serverda tekshiriladi. Yurish uchun 60 soniya; vaqtida yurmagan, chiqib ketgan yoki aloqasi uzilgan o'yinchi durak hisoblanadi. Tugagach egasi «Yana o'ynash»ni bosadi. Xabarlar `dk_*` (`app/services/durak.py`).
- **Ochko va Score board:** online o'yinda durak −10; birinchi chiqqan +10 (3 kishida 2-chiqqan +5, 4 kishida +6 va +3); kimdir vaqtida yurmasa/chiqib ketsa, qolganlarga teng (2 kishida +10, 3 kishida +5, 4 kishida +4); durangda o'zgarmaydi. Kamida 12 harakatli o'yin va bir juftlik bilan kuniga 5 tagacha o'yin hisoblanadi. `game_stats` (`game="durak"`), `GET /api/games/durak/scoreboard`. Botlar bilan o'yin ochko bermaydi.
- **Hozircha yo'q:** missiyalar, online stolga bot qo'shish.
- Testlar: `python -m unittest tests.test_durak`, `node tests/durak_engine_test.mjs`, `node tests/durak_ui_test.mjs` (soxta DOM: `tests/fake_dom.mjs`).

## Yangiliklar: ko'rinish, lichka qoidasi, kanal media, so'rovnoma

- **Oddiy foydalanuvchilar bir-birini ro'yxatda ko'rmaydi.** `/api/members` oddiy foydalanuvchiga faqat adminlarni (va o'zini) qaytaradi; admin hammani ko'radi. Umumiy guruhdagi foydalanuvchilar ro'yxati ham shunday. Umumiy chatda yozganlar xabarlari orqali ko'rinadi.
- **Lichka:** oddiy foydalanuvchi adminga, umumiy chatda yozgan odamga, avval yozishgan hamkoriga yoki `GET /api/users/search?q=username` (to'liq username, 20/daqiqa) bilan topgan odamiga yoza oladi; qolganlarga server 403 beradi (`dm_found` jadvali).
- **Kanal media/fayllari avtomatik o'chirilmaydi:** 24 soatlik tozalash (`sweep_expired`, `sweep_r2_media`) kanal postlariga tegmaydi. Faqat admin postni (yoki kanalni) o'chirsa, fayl object storage (R2)dan ham o'chadi.
- **Kanal so'rovnomalari:** admin kanalda 📊 tugmasi bilan so'rovnoma yaratadi (savol va variantlar E2EE konvert ichida; server faqat ovozlarni sanaydi), «Hammani xabardor qilish» belgisi bilan hamma onlayn foydalanuvchiga ogohlantirish (`channel_notify`) boradi. Ovoz: `POST /api/channels/{cid}/posts/{pid}/vote`, yopish: `.../poll/close`.
- Eslatma: `frontend/` papkasi `app.js`/`style.css` dan eskirgan; bu o'zgarishlar bevosita `app/static/` fayllarida. Test: `python -m unittest tests.test_visibility_polls`.

## Yangilik: Instagram video — «▶ Ko'rish» tugmasi (saqlanmaydi)

Xabarda Instagram `reel` / `p` / `tv` havolasi bo'lsa, havola yonida **▶ Ko'rish** tugmasi chiqadi. Bosilganda ekranda ko'rgich ochiladi va video Instagram'ning o'z embed sahifasidan (`https://www.instagram.com/<tur>/<kod>/embed/`) **jonli** yuklanadi; yopilganda iframe butunlay o'chiriladi.

- **Serverga hech narsa tushmaydi:** video yuklab olinmaydi, bazaga, R2 ga yoki `data/` ga yozilmaydi, media sifatida ko'rsatilmaydi. Xabarda faqat havola matni turadi (E2EE bo'lsa — shifrlangan holda, oddiy matn xabar kabi). Server trafigi sarflanmaydi.
- Havola va tugma faqat **brauzerda**, xabar ochilgandan keyin aniqlanadi (`app/static/js/richtext.js` → `R.instagramOf`); server havola ichini ko'rmaydi.
- Embed manzil foydalanuvchi havolasidan emas, tekshirilgan qismlardan (`tur` + `kod`) quriladi; faqat aniq `instagram.com` / `www.` / `m.` domenlari qabul qilinadi.
- CSP: `frame-src https://www.instagram.com` (oldin `'none'`). Boshqa hech qaysi domen frame ichida ochilmaydi. iframe `sandbox` va `referrerpolicy="no-referrer"` bilan ochiladi.
- Yopiq (private) akkaunt yoki o'chirilgan video embed'da ochilmaydi — ko'rgichdagi «Instagramda ochish» havolasi bor.
- Ko'rgich: Esc, orqaga tugmasi (Android), ✕ yoki chetga bosish bilan yopiladi.
- Test: `node tests/richtext_instagram_test.mjs`.

## Havola skaneri («Scan» tugmasi)

Xabardagi har bir havola (`habar https://... habar2`, yalang'och `example.uz`, Instagram reel/post) yonida **🛡 Scan** tugmasi chiqadi
(Instagram'da «▶ Ko'rish» bilan birga). Tugma bosilgandagina havola serverga yuboriladi (xabarlar E2EE bo'lgani uchun server ularni o'zi ko'rmaydi)
va natija havola yonida panel bo'lib ochiladi — **faqat bosgan foydalanuvchining ekranida**, boshqalarga xabar o'zgarmaydi. Havola serverda saqlanmaydi va logga yozilmaydi (faqat 10 daqiqalik xotira keshi).

Nima tekshiriladi (`app/services/urlscan.py`):

| Tekshiruv | Qanday |
|---|---|
| WHOIS / RDAP | IANA RDAP bootstrap → registry RDAP; RDAP yo'q zonalar uchun IANA ko'rsatgan WHOIS serveri (port 43). Domen yoshi, registrator, muddat, NS |
| DNS | tizim resolveri + Cloudflare DoH (A/AAAA/NS/MX, TTL) |
| SSL / TLS | haqiqiy handshake: zanjir, muddat, domen mosligi, o'zi imzolagan, protokol/shifr |
| Redirects / Final URL | qo'lda kuzatiladi (8 qadamgacha), har qadam qayta tekshiriladi; HTTPS→HTTP tushishi, boshqa domenga o'tish |
| Threat Intel | kalitsiz: Cloudflare Security DNS + Quad9; ixtiyoriy: Google Safe Browsing, VirusTotal, URLhaus (kalit env'da) |
| Sahifa | sarlavha, parol maydoni, boshqa domenga yuboruvchi forma, meta-refresh, .apk/.exe yuklash |
| URL tahlili | `login@host`, IP-host, punycode, brend taqlidi (`paypa1.com`, `instagram.com.evil.xyz`), qisqartirgichlar, xavfli zonalar |
| Skrinshot | tashqi skrinshot xizmati orqali olinadi, Pillow bilan qayta kodlanadi (JPEG ≤720px) va faqat `data:` rasm sifatida ko'rsatiladi |

**SSRF himoyasi:** faqat http/https va 80/443/8080/8443; domen IP ga o'zimiz aylantiriladi va har bir manzil global bo'lishi shart
(127.0.0.0/8, 10/8, 192.168/16, 169.254.169.254 va h.k. bloklanadi), ulanish aynan shu IP ga pin qilinadi, har redirect qayta tekshiriladi,
javob hajmi/vaqti cheklangan. Foydalanuvchi boshiga 12 so'rov/daqiqa, bir vaqtda 3 ta skan.

**Skrinshot xizmati:** `TERMCHAT_SHOT_URL` (standart `https://image.thum.io/get/width/900/crop/620/noanimate/{url}`).
Diqqat: skrinshot olish uchun havola shu tashqi xizmatga yuboriladi. Xohlasangiz o'zingizning xizmatingizni (ScreenshotOne, ApiFlash, o'z Playwright
serveringiz) shablon orqali ulang yoki bo'sh qoldirib o'chiring — qolgan tekshiruvlar ishlayveradi.

Natija «100% xavfsiz» degani emas: «Xavfli belgilar topilmadi» — faqat shu tekshiruvlarda muammo chiqmaganini bildiradi.

Testlar: `python -m pytest tests/test_urlscan.py`, `node tests/urlscan_panel_test.mjs`, `node tests/richtext_instagram_test.mjs`.
