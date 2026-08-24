# Moliyaviy Erkinlik Daftari

Bodo Shefer metodikasiga asoslangan shaxsiy moliyaviy reja va kirim-chiqim
kuzatuv ilovasi. Bu papkadagi fayllarni GitHubga yuklab, bir necha daqiqada
telefoningizga o'rnatiladigan ilovaga aylantirishingiz mumkin.

## Fayllar
- `index.html` — asosiy ilova
- `manifest.json` — ilova nomi, ikonkasi va o'rnatish sozlamalari
- `sw.js` — offline (internetsiz) ishlash uchun service worker
- `icons/` — ilova ikonkalari

## GitHub Pages orqali chiqarish (bepul, oson)

1. github.com'da yangi repository yarating (masalan: `moliyaviy-erkinlik`).
2. Ushbu papkadagi barcha fayllarni (index.html, manifest.json, sw.js, icons/)
   o'sha repositoryga yuklang ("Add file" → "Upload files").
3. Repository sozlamalariga o'ting: **Settings → Pages**.
4. "Branch" qismida `main` ni tanlang, papka — `/ (root)`, so'ng **Save**.
5. Bir necha daqiqadan so'ng sahifa manzili tayyor bo'ladi:
   `https://<username>.github.io/moliyaviy-erkinlik/`
6. O'sha manzilni telefoningizda Chrome'da oching → brauzer menyusidan
   **"Bosh ekranga qo'shish" / "Add to Home screen"** ni tanlang — ilova
   xuddi oddiy dastur kabi telefoningizga o'rnatiladi.

## APK (Android o'rnatiladigan fayl) yasash — GitHub Actions orqali

Ilova avval GitHub Pages'da jonli turishi kerak (yuqoridagi bosqichlarni bajaring).
Shundan keyin GitHub Actions **Bubblewrap** vositasi orqali haqiqiy `.apk` faylni
avtomatik yig'ib beradi.

### 1) `twa-manifest.json` faylini to'ldiring
Faylda 3 joyda `REPLACE_WITH_USERNAME` yozuvi bor — uni o'z GitHub
foydalanuvchi nomingizga almashtiring (masalan, agar username `aziz123`
bo'lsa, `REPLACE_WITH_USERNAME.github.io` → `aziz123.github.io`).
Agar repository nomingiz `moliyaviy-erkinlik` dan farq qilsa, `startUrl` va
boshqa manzillardagi shu qismni ham repository nomingizga moslang.

### 2) Imzo kaliti (keystore) yarating — kompyuteringizda, bir marta
Terminalda (Java o'rnatilgan bo'lishi kerak):
```
keytool -genkey -v -keystore android.keystore -alias androidkey -keyalg RSA -keysize 2048 -validity 10000
```
So'ralgan parollarni eslab qoling (2 marta parol so'raydi: keystore paroli
va key paroli — bir xil qilib qo'ysangiz ham bo'ladi).

So'ng faylni base64 formatga o'tkazing:
```
base64 -w0 android.keystore > android.keystore.b64
```
(Windows'da: `certutil -encode android.keystore android.keystore.b64`)

### 3) GitHub repository'ga sirlarni (secrets) qo'shing
Repository → **Settings → Secrets and variables → Actions → New repository
secret** orqali quyidagi 3 tasini qo'shing:
- `ANDROID_KEYSTORE_BASE64` — `android.keystore.b64` faylining ichidagi matn
- `KEYSTORE_PASSWORD` — keystore paroli
- `KEY_PASSWORD` — key paroli

### 4) Actions'ni ishga tushiring
Repository'ning **Actions** bo'limiga o'ting → "Build Android APK
(Bubblewrap)" workflow'ni tanlang → **Run workflow**. Bir necha daqiqadan
so'ng tugagach, o'sha run sahifasining pastida **Artifacts** qismidan
`.apk` faylni yuklab olasiz — shu faylni telefoningizga o'tkazib, o'rnatishingiz mumkin
("noma'lum manbalardan o'rnatish" ruxsatini yoqish kerak bo'lishi mumkin).

> Eslatma: bu usul shaxsiy foydalanish uchun o'rnatiladigan APK yaratadi.
> Agar keyinchalik Google Play'ga chiqarmoqchi bo'lsangiz, qo'shimcha
> Play Store talablari (upload key, listing va h.k.) bajarilishi kerak bo'ladi.


## Muhim: `.well-known/assetlinks.json` — APK'ni to'liq ishlatish uchun shart

APK o'rnatilgach, agar bu faylni to'g'rilamasangiz, ilova ochilganda
tepasida brauzer manzil satri ko'rinib turadi (chala ishlaydi). Buni
tuzatish uchun:

1. Keystore yaratganingizda (yuqoridagi 2-bosqich) SHA256 barmoq izini
   oling:
   ```
   keytool -list -v -keystore android.keystore -alias androidkey
   ```
   Chiqqan natijada `SHA256:` bilan boshlanadigan qatorni toping
   (masalan: `AA:BB:CC:...`).

2. `.well-known/assetlinks.json` faylini oching, `REPLACE_WITH_YOUR_SHA256_FINGERPRINT`
   o'rniga shu qiymatni (ikki nuqta bilan, katta harflarda) qo'ying.

3. Bu faylni GitHub repository'ga **aynan shu joylashuvda** — ya'ni
   `.well-known/assetlinks.json` — yuklang. `.nojekyll` deb nomlangan bo'sh
   fayl ham repo tub papkasida bo'lishi shart (bu GitHub Pages'ga
   "." bilan boshlanuvchi papkalarni yashirmasdan ko'rsat" deb aytadi —
   fayl allaqachon shu zip ichida bor).

4. Pages qayta joylashgach, tekshirish uchun brauzerda oching:
   `https://<username>.github.io/moliyaviy-erkinlik/.well-known/assetlinks.json`
   — JSON matni to'g'ridan-to'g'ri ko'rinishi kerak (xato chiqmasligi kerak).

5. Google'ning tekshiruv vositasida tasdiqlang:
   `https://developers.google.com/digital-asset-links/tools/generator`
   — domenni va paket nomini (`uz.moliyaviyerkinlik.daftari`) kiritib,
   "muvaffaqiyatli" degan javob olguningizcha tekshiring.



## Muhim eslatma — ma'lumotlar saqlanishi

Ilova barcha ma'lumotlarni (rejalar, kirim-chiqim, qarzlar) telefon/brauzer
xotirasida (`localStorage`) saqlaydi. Bu shuni anglatadi:
- Ma'lumotlar faqat shu qurilma va shu brauzerda saqlanadi (boshqa
  qurilmaga avtomatik ko'chmaydi).
- Brauzer keshi/ma'lumotlarini qo'lda tozalasangiz, yozuvlar ham o'chib
  ketishi mumkin — shuning uchun katta o'zgarishlardan oldin muhim
  ma'lumotlarni boshqa joyga (masalan, screenshot yoki alohida yozib)
  zaxira qilib qo'yish tavsiya etiladi.
- Har doim bitta brauzer/qurilmada foydalanishga harakat qiling.
