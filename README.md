# SSH kalit tizimi qanday ishlaydi?

Public key va private key asosidagi SSH autentifikatsiyasining qadam-baqadam tushuntirilishi. Interaktiv versiyasini ko'rish uchun `index.html` faylini brauzerda oching.

---

## Qisqacha g'oya

SSH autentifikatsiyasi "qulf va kalit" metaforasiga o'xshaydi, lekin oddiy qulfdan farqli — bu yerda **ikkita turli kalit** ishlatiladi:

| Kalit | Vazifasi | Qayerda saqlanadi |
|---|---|---|
| **Public key** | qulflash (yoki tekshirish) uchun | serverda, hammaga ochiq |
| **Private key** | ochish (yoki imzolash) uchun | faqat sizning kompyuteringizda, hech kimga berilmaydi |

---

## 1-bosqich: Tayyorgarlik (kalit yaratish)

### 1.1 — Kalit juftligini yaratish

```bash
ssh-keygen -t ed25519
```

Bu buyruq bitta matematik bog'langan juft kalit yaratadi: **private key** va **public key**.

### 1.2 — Fayllarni tekshirish

```bash
ls ~/.ssh/
```
```
id_ed25519      id_ed25519.pub
```

- `id_ed25519` — **private key**. Hech kimga bermaysiz, faqat sizda qoladi.
- `id_ed25519.pub` — **public key**. Bemalol tarqatsa bo'ladi.

### 1.3 — Public key'ni serverga yuborish

```bash
ssh-copy-id user@server.com
```
```
Number of key(s) added: 1
```

Bu safar oxirgi marta parol so'raladi.

### 1.4 — Server public key'ni saqlaydi

```bash
cat ~/.ssh/authorized_keys
```
```
ssh-ed25519 AAAAC3Nza... user@laptop
```

Server public key'ni o'zining `~/.ssh/authorized_keys` fayliga yozib qo'yadi. Endi server sizni "taniydi" — lekin hali hech narsani tasdiqlamagan.

---

## 2-bosqich: Ulanish (autentifikatsiya)

### 2.1 — Ulanishni boshlash

```bash
ssh user@server.com
```

Server sizning public key'ingiz ro'yxatda borligini ko'radi va tekshiruv boshlanadi.

### 2.2 — Server challenge yaratadi

Server tasodifiy bir "xabar" (**challenge**) hosil qiladi va uni sizning public key'ingiz bilan qulflab, sizga jo'natadi.

```
Server → Sizning kompyuteringiz : [qulflangan xabar]
```

### 2.3 — Siz xabarni ochasiz

Sizning kompyuteringiz bu qulflangan xabarni faqat `id_ed25519` **private key** bilan ocha oladi — boshqa hech kim ocha olmaydi.

### 2.4 — Javobni qaytarasiz

Siz ochilgan xabarning isbotini (imzosini) serverga qaytarasiz.

```
Sizning kompyuteringiz → Server : [javob / isbot]
```

> **Muhim:** Private key'ning o'zi hech qachon tarmoqqa chiqmaydi — faqat "men buni ocha oldim" degan dalil boradi.

### 2.5 — Server tekshiradi va ruxsat beradi

Server javobni public key bilan tekshiradi: to'g'ri bo'lsa, demak siz haqiqatan ham mos private key'ga egasiz.

```
Welcome to server.com!
user@server:~$
```

Ulanish ruxsat etiladi — **parol kerak emas!**

---

## Ko'p beriladigan savol

### "Public key bilan qulflangan xabarni public key'ning o'zi bilan ochib bo'lmaydimi?"

**Javob: Yo'q.** Bu — asimmetrik kriptografiyaning asosiy qoidasi.

Oddiy qulfda bitta kalit bilan qulflaysiz, xuddi o'sha kalit (yoki nusxasi) bilan ochasiz — bu **simmetrik** tizim. Public/private key'da bunday emas, bu **asimmetrik** tizim:

> **Public key bilan qulflangan narsa faqat mos private key bilan ochiladi. Public key'ning o'zi bilan ochib bo'lmaydi.**

| Kalit | Vazifasi |
|---|---|
| Public key | faqat qulflash (shifrlash) uchun |
| Private key | faqat ochish (deshifrlash) uchun |

### Nega bu matematik jihatdan mumkin?

RSA, ECC kabi algoritmlar **"bir tomonlama funksiya"** (trapdoor function)ga asoslangan: bir yo'nalishda oson, teskarisi amalda imkonsiz — lekin "sirli qo'shimcha ma'lumot" (private key) bilsangiz, teskarisi ham oson bo'ladi.

**Soddalashtirilgan misol:** ikkita katta tub sonni ko'paytirish oson (shu ko'paytmadan public key yasaladi), lekin natijadan orqaga qaytib qaysi ikkita son ko'paytirilganini topish — maxsus "kalit" (private key) bo'lmasa, amalda deyarli imkonsiz.

### Qo'shimcha nuance: shifrlash vs raqamli imzo

Zamonaviy SSH (Ed25519 kabi)da xabar aslida "shifrlanmaydi" — buning o'rniga **raqamli imzo** ishlatiladi:

- Siz private key bilan challenge'ga **imzo chekasiz**
- Server esa public key bilan imzoning to'g'riligini **tekshiradi** (ochmaydi, balki tasdiqlaydi)

Printsip bir xil qoladi: faqat private key egasi to'g'ri imzo qo'ya oladi, lekin uni tekshirish uchun private key shart emas.

---

## Interaktiv versiya

Ushbu repo'dagi [`index.html`](./index.html) faylini brauzerda ochib, yuqoridagi barcha bosqichlarni animatsiyali tarzda, "Keyingi qadam" tugmasi bilan o'zingiz boshqarib ko'rishingiz mumkin.
