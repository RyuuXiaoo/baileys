# <div align='center'>🌸 LIN HUA XIU 🌸</div>

 <p align="center">
<em>Framework otomasi pesan berbasis Baileys yang dirancang untuk efisiensi dan kompatibilitas tinggi.</em>
</p>

## ✨ Fitur Utama

● **✨ Modern Architecture** – Dibangun menggunakan TypeScript & Node.js 24 untuk performa yang lebih cepat dan kode yang lebih bersih.

● **🔧 Advanced JID Fix** – Sistem cerdas untuk menangani transisi @lid ke @pn, memastikan pengiriman pesan di grup tetap akurat tanpa error.

● **🖥 Multi-Device Support** – Kompatibilitas penuh dengan fitur Multi-Device WhatsApp terbaru, stabil untuk penggunaan jangka panjang.

● **🔐 Enterprise Security** – Keamanan tingkat tinggi dengan dukungan enkripsi End-to-End (E2EE) asli dari protokol WhatsApp.

● **📨 Rich Media Handling** – Mendukung segala jenis interaksi: teks, media (foto/video/file), polling, hingga interactive product messages.

● **⚡ Optimized Connection** – Menggunakan identitas Safari 26.4 untuk meminimalisir risiko looping connection dan meningkatkan stabilitas panel.


**⚙️ Gambaran Teknis**

Script ini berfungsi sebagai **base/mentahan**. Fokus utama pengembangannya adalah pada stabilitas koneksi dan penanganan metadata pesan. Pengguna bebas mengimplementasikan logika tambahan seperti database, manajemen limit, atau fitur bisnis lainnya secara mandiri.

**⚠️ Peringatan & Ketentuan**

**PENTING:** Proyek ini tidak berafiliasi, terkait, diotorisasi, didukung, atau terhubung secara resmi dengan WhatsApp atau anak perusahaannya. Situs resmi WhatsApp dapat diakses di whatsapp.com.

1. **Tanggung Jawab Pengguna:** Pengelola tidak mendukung penggunaan script ini untuk melanggar Ketentuan Layanan WhatsApp. Pengguna bertanggung jawab penuh atas segala aktivitas yang dilakukan.

2. **Penggunaan Bijak:** Sangat disarankan untuk menghindari aktivitas spam atau otomatisasi yang berlebihan guna menjaga keamanan akun masing-masing.

3. **Status Proyek:** Kode ini disediakan sebagai referensi teknis bagi pengembang yang ingin mendalami library Baileys.


## 📦 Instalasi

### Instalasi via Package.json

Agar proyek kamu selalu menggunakan versi stabil dari Lin Hua Xiu, tambahkan baris berikut secara manual ke dalam file package.json milikmu:

**Opsi 1: Menggunakan Nama Repositori**

Gunakan ini jika kamu ingin menggunakan identitas paket langsung dari sumbernya.

```json
"dependencies": {
    "linhuaxiu/baileys": "github:linhuaxiu/baileys-pro"
}
```

**Opsi 2: Menggunakan Nama Alias (Direkomendasikan)**

Gunakan ini agar kamu tetap bisa melakukan import ... from 'baileys' tanpa harus mengubah kode yang sudah ada.

```json
"dependencies": {
    "baileys": "github:linhuaxiu/baileys-pro"
}
```

Setelah menambahkan baris di atas, jalankan perintah berikut di terminal:

```bash
npm install
```

> [!TIP]
> Menggunakan metode ini sangat membantu dalam menjaga konsistensi versi antar tim pengembang Lin Hua Xiu dan memudahkan proses deployment pada server.

### 📦 Instalasi via NPM

Gunakan salah satu perintah di bawah ini untuk memasang library ke proyek kamu menggunakan NPM:

```bash
# Versi Stabil
npm install linhuaxiu/baileys-pro

# Atau menggunakan shortcut GitHub
npm install github:linhuaxiu/baileys-pro

# Atau spesifik menggunakan nama paket
npm install baileys@github:linhuaxiu/baileys-pro

```

### 📦 Instalasi via Yarn

Jika kamu menggunakan Yarn sebagai manajer paket, gunakan perintah berikut:

```bash
# Menggunakan shortcut GitHub
yarn add github:linhuaxiu/baileys-pro

# Atau spesifik menggunakan nama paket
yarn add baileys@github:linhuaxiu/baileys-pro

```

> [!TIP]
> Menggunakan format baileys@github:linhuaxiu/baileys sangat disarankan jika kamu ingin memastikan dependensi di package.json tetap menggunakan nama baileys agar tidak perlu mengubah import statement di kode bot kamu.


### Impor di Kode

Setelah proses instalasi selesai, kamu bisa langsung mengimpor library ini ke dalam proyek JavaScript atau TypeScript milikmu:

**CommonJS (require)**

Gunakan format ini jika proyek kamu menggunakan standar Node.js klasik:

```javascript
const { default: makeWASocket, useMultiFileAuthState } = require("linhuaxiu/baileys");
```

**ESM / TypeScript (import)**

Gunakan format ini untuk proyek modern atau jika kamu menggunakan TypeScript:

```javascript
import makeWASocket, { useMultiFileAuthState } from "linhuaxiu/baileys";
```


## 🚀 Mulai Cepat

Berikut adalah contoh implementasi dasar untuk memulai koneksi menggunakan Lin Hua Xiu. Contoh ini mencakup penanganan sesi, pemindaian QR code, dan fitur balas pesan otomatis sederhana.

###Contoh Dasar (CommonJS)

```javascript
const { default: makeWASocket, DisconnectReason, useMultiFileAuthState } = require('linhuaxiu/baileys')
const { Boom } = require('@hapi/boom')

async function connectToWhatsApp() {
    // Inisialisasi penyimpanan sesi (auth)
    const { state, saveCreds } = await useMultiFileAuthState('auth_info_linhuaxiu')
    
    const sock = makeWASocket({
        auth: state,
        printQRInTerminal: true,
        // Nama browser yang akan muncul di daftar perangkat tertaut
        browser: ['Lin Hua Xiu', 'Desktop', '3.0']
    })

    // Menangani update koneksi (QR, Terhubung, atau Terputus)
    sock.ev.on('connection.update', (update) => {
        const { connection, lastDisconnect } = update
        if(connection === 'close') {
            const shouldReconnect = (lastDisconnect?.error)?.output?.statusCode !== DisconnectReason.loggedOut
            console.log('Koneksi terputus. Mencoba menghubungkan kembali: ', shouldReconnect)
            if(shouldReconnect) {
                connectToWhatsApp()
            }
        } else if(connection === 'open') {
            console.log('✅ Berhasil terhubung ke WhatsApp!')
        }
    })

    // Menangani pesan masuk
    sock.ev.on('messages.upsert', async ({ messages }) => {
        for (const m of messages) {
            if (!m.message) continue
            
            console.log('📱 Pesan baru dari:', m.key.remoteJid)
            
            // Contoh balas pesan otomatis
            // Hapus komentar di bawah ini jika ingin mencoba fitur auto-reply
            /*
            await sock.sendMessage(m.key.remoteJid, { 
                text: 'Halo! Ini adalah pesan otomatis dari sistem Lin Hua Xiu 🌸' 
            })
            */
        }
    })

    // Simpan kredensial setiap kali ada perubahan (penting agar tidak perlu scan ulang)
    sock.ev.on('creds.update', saveCreds)
}

connectToWhatsApp()
```

## 📋 Daftar Isi

> [!IMPORTANT]
> **Disclaimer:** Dokumentasi ini masih dalam tahap Beta. Beberapa bagian mungkin masih dalam pengembangan atau mengalami penyesuaian di masa mendatang.

## 🔌 Koneksi Akun

WhatsApp menggunakan API Multi-Device yang memungkinkan Lin Hua Xiu terautentikasi sebagai klien WhatsApp sekunder. Kamu bisa menghubungkannya melalui pemindaian QR Code atau menggunakan Pairing Code.

### Konfigurasi Browser Kustom (Ubuntu Safari)

Kamu bisa menentukan identitas browser yang muncul di perangkat tertaut WhatsApp. Ini sangat berguna untuk membedakan sesi bot dengan sesi WhatsApp Web pribadi kamu.

```javascript
const { default: makeWASocket } = require("linhuaxiu/baileys")

const sock = makeWASocket({
    // Menampilkan sesi sebagai Safari di Ubuntu pada perangkat tertaut
    browser: ["Ubuntu", "Safari", "26.4"] //saat ini versi terbaru 26.4
})
```

Mengapa Mengatur Browser?

● **Identifikasi Sesi:** Memudahkan kamu memantau sesi mana yang aktif melalui pengaturan "Perangkat Tertaut" di aplikasi WhatsApp HP.

● **Keamanan:** Konsistensi user-agent (seperti menggunakan Ubuntu Safari secara tetap) dapat membantu mengurangi risiko deteksi aktivitas mencurigakan oleh sistem keamanan WhatsApp.

● **Personalisasi:** Memberikan identitas unik pada koneksi bot milikmu.

### Hubungkan dengan QR Code

Cara ini adalah yang paling umum digunakan. Kamu bisa menyesuaikan identitas perangkat yang muncul di WhatsApp melalui konstanta Browsers.

> [!TIP]
> Menggunakan identitas browser yang spesifik (seperti Ubuntu atau MacOS) dapat membantu kamu mengenali koneksi bot di daftar "Perangkat Tertaut" pada aplikasi WhatsApp.

```javascript
const { default: makeWASocket, Browsers } = require("linhuaxiu/baileys")

const sock = makeWASocket({
    browser: Browsers.ubuntu('My App'), 
    printQRInTerminal: true
})
```

**Langkah-langkah:**

● Jalankan script di terminal kamu.

● Tunggu hingga QR Code muncul.

● Buka WhatsApp di ponsel > Perangkat Tertaut > Tautkan Perangkat.

● Pindai QR Code yang muncul di terminal.

### Hubungkan dengan Pairing Code

Metode ini memungkinkan kamu menghubungkan akun tanpa perlu memindai QR Code. Sangat berguna jika kamu menjalankan bot di lingkungan terminal tanpa akses layar tambahan.

> [!IMPORTANT]
> Catatan Penting: Pairing code dirancang untuk koneksi satu perangkat sebagai klien WhatsApp Web. Pastikan nomor yang digunakan mengikuti format internasional (kode negara + nomor telepon) tanpa tanda +, spasi, atau karakter lainnya.

**1. Pairing Normal**
Gunakan metode ini untuk mendapatkan kode acak yang dihasilkan secara otomatis oleh sistem WhatsApp.

```javascript
const { default: makeWASocket } = require("linhuaxiu/baileys")

const sock = makeWASocket({
    printQRInTerminal: false // Matikan QR karena kita menggunakan pairing code
})

if (!sock.authState.creds.registered) {
    const number = '628123456789' // Sesuaikan dengan nomor kamu
    const code = await sock.requestPairingCode(number)
    console.log('🔑 Kode Pairing Anda:', code)
}
```

**2. Pairing Kustom**
Lin Hua Xiu juga mendukung permintaan kode dengan identitas kustom (maksimal 8 karakter). Ini membuat proses autentikasi terasa lebih personal.

```javascript
const { default: makeWASocket } = require("linhuaxiu/baileys")

const sock = makeWASocket({
    printQRInTerminal: false
})

if (!sock.authState.creds.registered) {
    const pair = "LINHUA59" // Maksimal 8 karakter kustom
    const number = '6289123456789'
    const code = await sock.requestPairingCode(number, pair)
    console.log('🔑 Kode Pairing Kustom:', code)
}
```

**💡 Cara Menggunakan Kode Pairing:**

1. Jalankan script hingga muncul 8 digit kode di terminal.
2. Buka WhatsApp di ponsel kamu.
3. Klik Titik Tiga (Menu) > Perangkat Tertaut > Tautkan Perangkat.
4. Pilih opsi "Tautkan dengan nomor telepon saja" di bagian bawah.
5. Masukkan 8 digit kode yang muncul di terminal tadi.

### Terima Riwayat Lengkap

Secara standar, koneksi baru hanya akan menerima pesan-pesan terbaru. Jika kamu membutuhkan bot untuk menarik riwayat pesan lama (seperti pesan di grup atau chat pribadi yang sudah ada), kamu bisa mengaktifkan fitur sinkronisasi penuh.

1. Setel properti `syncFullHistory` ke nilai `true`.
2. Gunakan konfigurasi browser tipe Desktop (seperti macOS atau Windows) untuk memicu WhatsApp mengirimkan lebih banyak data riwayat dibandingkan profil Mobile/Chrome biasa.

```javascript
const { default: makeWASocket, Browsers } = require("linhuaxiu/baileys")

const sock = makeWASocket({
    // Menggunakan profil Desktop agar riwayat pesan yang ditarik lebih maksimal
    browser: Browsers.macOS('Desktop'),
    syncFullHistory: true
})
```

> [!NOTE]
> Proses sinkronisasi riwayat penuh mungkin memakan waktu lebih lama saat pertama kali terhubung, tergantung pada seberapa banyak data chat yang ada pada akun WhatsApp tersebut.


## ⚙️ Catatan Penting Konfigurasi Socket

### Caching Metadata Grup (Direkomendasikan)

Untuk meningkatkan kecepatan respon dan mengurangi beban request ke server WhatsApp, sangat disarankan untuk mengimplementasikan Caching. Fitur ini akan menyimpan informasi grup (seperti nama, deskripsi, dan daftar anggota) sementara di memori.

**Keuntungan:**

● Mengurangi pemrosesan data yang berulang.

● Menghindari rate limit dari WhatsApp saat menangani banyak pesan grup secara bersamaan.

```javascript
const { default: makeWASocket } = require("linhuaxiu/baileys")
const NodeCache = require('node-cache')

// Inisialisasi Cache dengan waktu simpan (TTL) 5 menit
const groupCache = new NodeCache({ stdTTL: 5 * 60, useClones: false })

const sock = makeWASocket({
    // Meminta library untuk mengambil metadata dari cache terlebih dahulu
    cachedGroupMetadata: async (jid) => groupCache.get(jid)
})

// Memperbarui cache saat ada perubahan informasi grup (Nama, Deskripsi, dll)
sock.ev.on('groups.update', async ([event]) => {
    const metadata = await sock.groupMetadata(event.id)
    groupCache.set(event.id, metadata)
})

// Memperbarui cache saat ada perubahan anggota (Join, Kick, Promote, Demote)
sock.ev.on('group-participants.update', async (event) => {
    const metadata = await sock.groupMetadata(event.id)
    groupCache.set(event.id, metadata)
})
```

### Perbaiki Sistem Retry & Dekripsi Vote Poll

Untuk memastikan pesan yang gagal terkirim dapat dicoba kembali (retry) dan hasil pilihan pada fitur Polling dapat didekripsi dengan benar, kamu perlu mengintegrasikan fungsi getMessage dari penyimpanan pesan (store) milikmu.

```javascript
const sock = makeWASocket({
    // Mengambil pesan dari database/store lokal untuk proses dekripsi/retry
    getMessage: async (key) => {
        if (store) {
            const msg = await store.loadMessage(key.remoteJid, key.id)
            return msg?.message || undefined
        }
        return undefined
    }
})
```

### 🔔 Tetap Terima Notifikasi di Aplikasi WhatsApp

Secara default, saat bot terhubung, WhatsApp mungkin menganggap akun sedang aktif di desktop sehingga notifikasi di ponsel tidak muncul. Kamu bisa menonaktifkan status online otomatis untuk tetap menerima notifikasi di aplikasi WhatsApp utama.

```javascript
const sock = makeWASocket({
    // Bot tidak akan otomatis terlihat 'Online' saat terhubung
    markOnlineOnConnect: false
})
```

## 💾 Simpan Info Auth

Gunakan useMultiFileAuthState untuk menyimpan kredensial agar kamu tidak perlu melakukan pemindaian QR Code setiap kali script dijalankan ulang.

```javascript
const { default: makeWASocket, useMultiFileAuthState } = require("linhuaxiu/baileys")

async function connect() {
    // Folder 'session_data' akan menyimpan file JSON kredensial
    const { state, saveCreds } = await useMultiFileAuthState('session_data')
    
    const sock = makeWASocket({ 
        auth: state 
    })

    // Event wajib untuk memperbarui file sesi saat ada perubahan
    sock.ev.on('creds.update', saveCreds)
}

connect()
```

> [!IMPORTANT]
> Keamanan Sesi: `useMultiFileAuthState` menyimpan data sensitif di dalam folder lokal. Untuk penggunaan skala besar (produksi), sangat disarankan untuk mengelola folder tersebut dengan aman atau menggunakan database (SQL/No-SQL) untuk menyimpan state autentikasi.

### 📥 Menangani Pesan Masuk

Contoh sederhana untuk memantau setiap pesan yang masuk ke dalam sistem:

```javascript
sock.ev.on('messages.upsert', ({ messages, type }) => {
    if (type === 'notify') {
        console.log('📩 Pesan baru diterima:', messages)
    }
})
```

### 🚀 Contoh Implementasi Lengkap

Gunakan contoh kode di bawah ini sebagai titik awal untuk membangun bot kamu sendiri. Script ini sudah mencakup penanganan sesi otomatis dan logika pemulihan koneksi.

```javascript
const { default: makeWASocket, DisconnectReason, useMultiFileAuthState } = require("linhuaxiu/baileys")
const { Boom } = require('@hapi/boom')

async function connectToWhatsApp() {
    // Inisialisasi penyimpanan sesi di folder 'auth_info_linhuaxiu'
    const { state, saveCreds } = await useMultiFileAuthState('auth_info_linhuaxiu')
    
    const sock = makeWASocket({
        auth: state,
        printQRInTerminal: true, // Menampilkan QR di terminal
        browser: ['Lin Hua Xiu', 'Desktop', '3.0']
    })

    // Menangani perubahan status koneksi
    sock.ev.on('connection.update', (update) => {
        const { connection, lastDisconnect } = update
        
        if(connection === 'close') {
            // Logika untuk menghubungkan kembali secara otomatis jika tidak logout manual
            const shouldReconnect = (lastDisconnect.error)?.output?.statusCode !== DisconnectReason.loggedOut
            console.log('Koneksi terputus karena: ', lastDisconnect.error, '. Mencoba hubungkan ulang: ', shouldReconnect)
            
            if(shouldReconnect) {
                connectToWhatsApp()
            }
        } else if(connection === 'open') {
            console.log('✅ Koneksi berhasil dibuka!')
        }
    })

    // Menangani setiap pesan masuk
    sock.ev.on('messages.upsert', async ({ messages }) => {
        for (const m of messages) {
            // Menghindari error jika konten pesan kosong
            if (!m.message) continue 
            
            console.log('📩 Menerima pesan dari:', m.key.remoteJid)
            console.log('Isi Pesan:', JSON.stringify(m, undefined, 2))
            
            // Contoh membalas pesan secara otomatis
            await sock.sendMessage(m.key.remoteJid, { text: 'Halo! Bot Lin Hua Xiu telah aktif 🌸' })
        }
    })

    // Menyimpan perubahan kredensial secara otomatis
    sock.ev.on('creds.update', saveCreds)
}

// Menjalankan bot
connectToWhatsApp()

```

### Dekripsi Vote Poll

Secara default, data pilihan (vote) pada fitur Polling dikirimkan dalam bentuk terenkripsi. Untuk membacanya, kamu perlu menangani event messages.update dan melakukan agregasi menggunakan bantuan data dari penyimpanan (store) kamu.

**Alur Kerja:**

1. Pantau perubahan pada pesan melalui event messages.update.
2. Gunakan fungsi getMessage untuk mengambil data asli saat Poll dibuat.
3. Gunakan utility getAggregateVotesInPollMessage untuk menghitung hasil suara terbaru.

```javascript
const { getAggregateVotesInPollMessage } = require("linhuaxiu/baileys")

sock.ev.on('messages.update', async (event) => {
    for(const { key, update } of event) {
        // Cek apakah update pesan berisi pembaruan polling
        if(update.pollUpdates) {
            // Ambil data pembuatan poll asli dari store/database kamu
            const pollCreation = await getMessage(key)
            
            if(pollCreation) {
                const totalVotes = getAggregateVotesInPollMessage({
                    message: pollCreation,
                    pollUpdates: update.pollUpdates,
                })

                console.log('📊 Update Poll Diterima! Hasil agregasi suara saat ini:', totalVotes)
            }
        }
    }
})
```

> [!NOTE]
> Fungsi `getMessage(key)` di atas adalah implementasi sisi pengguna. Kamu harus menyediakan fungsi yang bisa menarik data pesan lama berdasarkan key dari database atau memory store yang kamu gunakan.

### Ringkasan Siklus Koneksi Pertama

Saat bot dihubungkan untuk pertama kalinya (setelah scan QR atau Pairing), sistem akan melewati urutan event berikut secara otomatis:

1. Triger Restart: Event connection.update akan dipicu untuk meminta restart pada instance socket. Hal ini diperlukan agar kredensial yang baru saja terverifikasi dapat dimuat dengan sempurna ke dalam sistem.
2. Sinkronisasi Riwayat: Setelah berhasil terhubung kembali, pesan-pesan riwayat (chat lama) akan mulai masuk dan diterima melalui event messaging.history-set.

> [!TIP]
> Pastikan kamu sudah menyiapkan handler untuk event messaging.history-set jika ingin mengolah atau menyimpan riwayat pesan lama ke dalam database saat pertama kali bot dijalankan.


## 🗄️ Implementasi Data Store

Secara bawaan, Lin Hua Xiu tidak menyertakan penyimpanan permanen untuk obrolan, kontak, atau pesan. Namun, tersedia implementasi in-memory store sederhana yang bisa digunakan untuk menjaga data tetap terkini selama bot berjalan.

> [!IMPORTANT]
> Rekomendasi Performa: Penyimpanan in-memory menyimpan seluruh data di RAM. Untuk penggunaan skala besar dengan ribuan chat, sangat disarankan untuk membangun Data Store sendiri menggunakan database (seperti PostgreSQL atau MongoDB) agar tidak boros penggunaan memori (RAM).

**Contoh Penggunaan In-Memory Store**

```javascript
const { default: makeWASocket, makeInMemoryStore } = require("linhuaxiu/baileys")

// Inisialisasi store
const store = makeInMemoryStore({ })

// Membaca data cadangan dari file lokal (jika ada)
store.readFromFile('./linhuaxiu_store.json')

// Menyimpan data ke file setiap 10 detik secara otomatis
setInterval(() => {
    store.writeToFile('./linhuaxiu_store.json')
}, 10_000)

const sock = makeWASocket({ /* konfigurasi Anda */ })

// Menghubungkan event socket ke store agar data tersinkronisasi otomatis
store.bind(sock.ev)

sock.ev.on('chats.upsert', () => {
    // Menampilkan daftar obrolan yang tersimpan di memori
    console.log('Daftar Obrolan:', store.chats.all())
})

sock.ev.on('contacts.upsert', () => {
    // Menampilkan daftar kontak yang tersimpan
    console.log('Daftar Kontak:', Object.values(store.contacts))
})
```

**Manfaat Menggunakan Store:**

● **Pengambilan Pesan Cepat:** Menyediakan fungsi seperti `loadMessages` untuk memanggil kembali pesan lama tanpa harus menunggu request ke server.

● **Manajemen Kontak:** Bot bisa mengenali nama kontak meskipun nomor tersebut tidak tersimpan di buku telepon server.

● **Integritas Data:** Membantu fitur lain seperti Dekripsi Vote Poll karena memiliki referensi pesan asli yang dibutuhkan.


## 🆔 Penjelasan ID WhatsApp

Dalam ekosistem WhatsApp, setiap tujuan pesan (baik itu individu, grup, maupun siaran) diidentifikasi menggunakan sebuah ID unik yang disebut sebagai JID. Memahami format JID sangat penting agar bot dapat mengirimkan pesan ke target yang tepat.

**Format Identitas**

**Format:** `[kode negara][nomor telepon]@s.whatsapp.net`  
**Contoh:** `+628123456789@s.whatsapp.net`.  

**Group:** `123456789-123345@g.us`.  

**Daftar siaran:** `[timestamp pembuatan]@broadcast`.  

**Status (Story):** `status@broadcast`.

**Catatan Penting:**

● **Nomor Telepon:* Saat menggunakan JID individu, pastikan nomor telepon ditulis dalam format internasional tanpa tanda plus `(+)`, spasi, atau tanda strip `(-)`.

● **Grup JID:** ID grup biasanya dihasilkan secara otomatis oleh server WhatsApp saat grup dibuat. Kamu bisa mendapatkan ID ini melalui event groups.upsert atau dengan mengecek metadata grup.

● **Status:** JID `status@broadcast` digunakan jika kamu ingin membaca update status orang lain atau mengirimkan status melalui bot.


## 🔧 Fungsi Utilitas

Lin Hua Xiu menyertakan beberapa fungsi utilitas untuk mempermudah pemrosesan data pesan dan optimasi performa:

- `getContentType(message)` Membantu kamu mengidentifikasi tipe konten di dalam pesan secara akurat (apakah itu pesan teks, gambar, video, atau tipe lainnya) tanpa perlu melakukan pengecekan manual yang rumit.
- `getDevice(id)` Digunakan untuk mengetahui jenis perangkat yang digunakan oleh pengirim pesan berdasarkan ID pesan tersebut.
- `makeCacheableSignalKeyStore(store, log)` Fungsi krusial untuk mempercepat proses Signal Key Store. Utilitas ini sangat direkomendasikan untuk meningkatkan kecepatan autentikasi dan efisiensi penyimpanan sesi.
- `downloadContentFromMessage(message, type)` Fungsi utama untuk mengunduh konten media (seperti foto, video, atau pesan suara) langsung dari server WhatsApp ke dalam bentuk Buffer.


## 📤 Kirim Pesan

Lin Hua Xiu menyediakan fungsi tunggal yang powerful untuk menangani segala jenis pengiriman pesan. Mulai dari teks biasa, media, hingga pesan interaktif lainnya.

**Struktur Fungsi**

```javascript
const jid = '628xxx@s.whatsapp.net' // ID tujuan
const content = { text: 'Halo!' }    // Konten pesan
const options = { quoted: m }        // Opsi tambahan (misal: membalas pesan)

await sock.sendMessage(jid, content, options)

```

### Pesan Non-Media

#### Pesan Teks

Berikut adalah beberapa variasi pengiriman pesan berbasis teks dan interaksi sosial di dalam chat.

1. Pesan Teks Sederhana
Cara paling dasar untuk mengirim pesan teks ke individu maupun grup.

```javascript
await sock.sendMessage(jid, { text: 'halo' })
```

#### Pesan Kutipan (bekerja dengan semua tipe)

Gunakan opsi quoted untuk membalas pesan tertentu. Fitur ini sangat berguna untuk menjaga konteks percakapan di dalam grup yang ramai. Fitur ini bekerja untuk semua tipe pesan (teks maupun media).

```javascript
await sock.sendMessage(jid, { text: 'halo' }, { quoted: message })
```

#### Sebut Pengguna (bekerja dengan sebagian besar tipe)

Untuk menandai atau menyebut seseorang di dalam grup agar mereka mendapatkan notifikasi khusus. Pastikan kamu menyertakan array JID di properti mentions.

> [!TIP]
> Penulisan `@number` di dalam teks hanya bersifat visual. Agar pengguna benar-benar tertag, ID mereka wajib dimasukkan ke dalam daftar mentions. 

```javascript
await sock.sendMessage(
    jid,
    {
        text: 'Halo @628123456789, ada yang bisa saya bantu?',
        // Daftar JID yang akan mendapatkan notifikasi mention
        mentions: ['628123456789@s.whatsapp.net']
    }
)
```

#### Teruskan Pesan

Fitur ini memungkinkan kamu untuk mengirim ulang pesan yang sudah ada ke tujuan lain. Kamu memerlukan objek pesan lengkap yang bisa kamu ambil dari store atau dari event pesan masuk.

```javascript
const msg = getMessageFromStore() // implementasikan sendiri
await sock.sendMessage(jid, { forward: msg })
```

> [!NOTE]
> Saat meneruskan pesan menggunakan metode ini, pesan akan muncul di penerima dengan label "Diteruskan" (Forwarded) sesuai dengan standar WhatsApp.

#### Pesan Lokasi

Gunakan fitur ini untuk mengirimkan titik koordinat peta. Sangat berguna untuk bot yang memberikan informasi alamat atau lokasi fisik.

```javascript
await sock.sendMessage(
    jid, 
    {
        location: {
            degreesLatitude: -6.234567, // Garis Lintang
            degreesLongitude: 106.123456 // Garis Bujur
        }
    }
)
```

#### Pesan Kontak

Kamu bisa mengirimkan informasi kontak dalam format vCard agar pengguna bisa langsung menyimpan nomor tersebut ke buku telepon mereka hanya dengan satu kali klik.

```javascript
const vcard = 'BEGIN:VCARD\n' 
            + 'VERSION:3.0\n' 
            + 'FN:Lin Hua Xiu\n' // Nama Lengkap
            + 'ORG:Ika Store;\n' // Nama Organisasi
            + 'TEL;type=CELL;type=VOICE;waid=628123456789:+62 812-3456-789\n' // Nomor WhatsApp
            + 'END:VCARD'

await sock.sendMessage(
    jid,
    { 
        contacts: { 
            displayName: 'Lin Hua Xiu', 
            contacts: [{ vcard }] 
        }
    }
)

)
```

> [!TIP]
> Pastikan bagian waid di dalam vCard diisi dengan nomor telepon tanpa tanda + agar tombol "Chat" langsung muncul di aplikasi WhatsApp penerima.

#### Pesan Reaksi

Gunakan fitur ini untuk memberikan emoji pada pesan tertentu. Kamu memerlukan key dari pesan yang ingin diberikan reaksi.

```javascript
await sock.sendMessage(
    jid,
    {
        react: {
            text: '💖',    // Emoji reaksi (gunakan string kosong '' untuk menghapus reaksi)
            key: m.key    // Kunci pesan yang ingin diberi reaksi
        }
    }
)
```

> [!TIP]
> Jika kamu ingin menghapus reaksi yang sudah diberikan sebelumnya, cukup kirimkan objek react yang sama namun dengan properti text berupa string kosong ('').

#### Pin Pesan

Gunakan fitur ini untuk menyematkan pesan penting di bagian atas obrolan. Kamu bisa mengatur durasi penyematan mulai dari 24 jam hingga 30 hari.

**Opsi Durasi Penyematan:**

Waktu:

| Waktu | Detik      |
|-------|------------|
| 24j   | 86.400     |
| 7h    | 604.800    |
| 30h   | 2.592.000  |

```javascript
await sock.sendMessage(
    jid,
    {
        pin: {
            key: m.key,   // Kunci pesan yang ingin disematkan
            type: 1,      // 1 untuk Sematkan, 0 untuk Lepas Semat (Unpin)
            time: 86400   // Durasi dalam detik (contoh: 24 jam)
        }
    }
)
```

> [!TIP]
> Untuk melepas sematan (Unpin) sebelum waktunya habis, kamu cukup mengirimkan objek yang sama dengan mengubah nilai type menjadi 0.

#### Pesan Poll

Fitur polling memungkinkan kamu membuat daftar pilihan yang bisa diklik oleh anggota grup atau individu. Hasil suaranya dapat dipantau dan didekripsi melalui event messages.update

```javascript
await sock.sendMessage(
    jid,
    {
        poll: {
            name: 'Pilih Hero MLBB Favoritmu!', // Pertanyaan polling
            values: ['Kagura', 'Gusion', 'Chou', 'Fanny'], // Daftar opsi
            selectableCount: 1, // Berapa banyak opsi yang boleh dipilih (1 = Single choice)
            toAnnouncementGroup: false // Setel true jika dikirim ke grup pengumuman (admin only)
        }
    }
)
```

> [!TIP]
> Jika kamu ingin mengizinkan pengguna memilih lebih dari satu jawaban (Multiple Choice), cukup ubah nilai selectableCount sesuai dengan jumlah maksimal pilihan yang diperbolehkan.

### Kirim dengan Pratinjau Tautan

Secara bawaan, pengiriman pesan melalui API tidak otomatis menghasilkan pratinjau tautan seperti pada aplikasi WhatsApp resmi. Library Lin Hua Xiu mendukung pembuatan pratinjau tautan secara manual untuk meningkatkan tampilan pesan kamu.

**Persyaratan**

Kamu perlu menginstal library tambahan untuk mengambil metadata dari situs web:

```bash
npm install link-preview-js
```

**Cara Penggunaan**

Cukup kirimkan pesan teks yang berisi URL yang valid. Sistem akan mencoba memproses pratinjau jika fitur tersebut diaktifkan atau didukung oleh handler pesan kamu.

```javascript
await sock.sendMessage(
    jid,
    {
        // Pastikan URL diawali dengan http:// atau https://
        text: 'Halo, bot ini dikembangkan menggunakan https://github.com/linhuaxiu/baileys'
    }
)
```

> [!TIP]
> Untuk hasil terbaik, pastikan situs web yang kamu bagikan memiliki **Open Graph Tags** (seperti og:title dan og:image) agar pratinjau yang muncul terlihat lengkap dengan gambar dan deskripsi yang menarik.

### Pesan Media 

**📷 Pesan Media (Gambar, Video, Stiker, & Audio)**

Pengiriman media di Lin Hua Xiu dirancang untuk efisiensi maksimal. Library ini tidak memuat seluruh data media ke dalam memori (RAM), melainkan melakukan enkripsi secara bertahap menggunakan sistem Stream.

**💡 Tips Hemat Memori**

Gunakan format `{ url: '...' }` atau `{ stream: Stream }` daripada menggunakan Buffer langsung untuk mencegah lonjakan penggunaan RAM saat mengirim file berukuran besar.

#### Pesan Gif

Perlu diingat bahwa WhatsApp tidak mendukung format file .gif secara asli untuk diputar secara otomatis di dalam chat. Untuk mencapai efek GIF, kamu harus mengirimkan file berformat .mp4 dengan menambahkan flag `gifPlayback`.

```javascript
await sock.sendMessage(
    jid, 
    { 
        // Gunakan file video berformat mp4
        video: fs.readFileSync('Media/animasi.mp4'),
        caption: 'Halo dunia!',
        gifPlayback: true // Pesan akan muncul dan berputar otomatis seperti GIF
    }
)
```

> [!TIP]
> Pastikan video yang digunakan memiliki durasi yang singkat dan tanpa suara (muted) agar pengalaman pengguna benar-benar terasa seperti menggunakan GIF asli.

#### Pesan Video

Kamu bisa mengirimkan video biasa atau pesan video instan berbentuk bulat (Video Note).

```javascript
await sock.sendMessage(
    jid, 
    { 
        video: { url: './Media/video.mp4' },
        caption: 'Halo dunia!',
        // Setel ke 'true' untuk mengirim sebagai Video Note (bulat)
        ptv: false 
    }
)
```

> [!NOTE]
> Video Note (PTV): Jika ptv disetel ke true, video akan muncul sebagai pesan video bulat yang langsung terputar secara otomatis di chat. Pastikan rasio video adalah 1:1 (persegi) untuk hasil terbaik.

#### Pesan Audio

Untuk memastikan audio dapat diputar dengan sempurna di semua perangkat, sangat disarankan untuk melakukan konversi menggunakan ffmpeg dengan parameter khusus agar sesuai dengan standar codec Opus WhatsApp.

**Perintah Konversi yang Disarankan:**

```bash
ffmpeg -i input.mp3 -c:a libopus -ac 1 -avoid_negative_ts make_zero output.ogg
```

**Format Audio**

Implementasi Kode:

```javascript
await sock.sendMessage(
    jid, 
    {
        audio: {
            url: './Media/audio.mp3'
        },
        mimetype: 'audio/mp4'
    }
)
```

**Format Audio Ke Pesan Suara**

Implementasi Kode:

```javascript
await sock.sendMessage(
    jid, 
    {
        audio: { url: './Media/audio.mp3' },
        mimetype: 'audio/mp4', // Gunakan audio/mp4 untuk file audio biasa
        ptt: true              // Setel ke 'true' untuk mengirim sebagai Voice Note (VN)
    }
)
```

> [!TIP]
> Jika kamu ingin mengirimkan rekaman suara yang tampil dengan waveform biru (seperti direkam langsung), pastikan properti `ptt` bernilai `true` dan file sudah dikonversi ke format `.ogg` atau `.opus`.

#### Pesan Gambar

Kamu bisa mengirim gambar baik dari penyimpanan lokal maupun melalui URL publik. Format yang didukung meliputi .jpg, .png, dan .jpeg.

```javascript
await sock.sendMessage(
    jid, 
    { 
        image: {
            // Bisa berupa Path lokal, URL, atau Buffer
            url: './Media/gambar.png' 
        },
        caption: 'Halo, ini deskripsi gambar.' // Opsional
    }
)
```

> [!TIP]
> Jika kamu ingin mengirim gambar tanpa kehilangan kualitas (HD), pastikan resolusi gambar asli sudah tinggi. WhatsApp akan melakukan kompresi otomatis, namun memulai dengan file berkualitas tinggi akan memberikan hasil yang lebih baik.

#### Pesan View Once

Fitur privasi ini memungkinkan kamu mengirim media (gambar, video, atau audio) yang akan langsung terhapus setelah dibuka oleh penerima. Kamu cukup menambahkan properti `viewOnce: true` pada objek pesan media apa pun.

```javascript
await sock.sendMessage(
    jid, 
    { 
        image: { url: './Media/rahasia.png' },
        caption: 'Pesan ini hanya bisa dilihat sekali!',
        // Aktifkan fitur View Once
        viewOnce: true 
    }
)
```

> [!IMPORTANT]
> Fitur viewOnce juga berlaku untuk pesan **Video** dan **Audio (VN)**. Pastikan kamu menggunakan library versi terbaru agar metadata viewOnce terbaca dengan benar oleh server WhatsApp.


#### Mengirim Pesan Tombol (Button Message)

Gunakan fitur ini untuk membuat menu interaktif yang memudahkan pengguna dalam memilih opsi tanpa harus mengetik teks.

```javascript
const buttons = [
    { 
        buttonId: 'id1', 
        buttonText: { 
            displayText: 'Button 1' 
        }
    },
    { 
        buttonId: 'id2', 
        buttonText: { 
            displayText: 'Button 2' 
        }
    }
]

const buttonMessage = {
    text: "Hi it's button message",
    footer: 'Hello World',
    buttons
}

await sock.sendMessage(jid, buttonMessage)
```

**Komponen Utama:**

● **text:**Pesan utama atau isi instruksi.

● **footer:** Teks kecil yang muncul di bagian bawah pesan (biasanya untuk keterangan tambahan).

● **buttons:** Array yang berisi objek tombol. Setiap tombol membutuhkan buttonId sebagai identitas unik dan displayText untuk label tombol.

> [!CAUTION]
> Fitur Button Message terkadang tidak muncul pada semua versi WhatsApp Desktop atau beberapa aplikasi modifikasi. Pastikan untuk menyediakan perintah teks alternatif jika pelanggan tidak melihat tombolnya.

#### Mengirim Pesan Tombol dengan Gambar (Image Button Message)

Gunakan format ini untuk menampilkan visual produk sekaligus memberikan tombol aksi cepat.

```javascript
const buttons = [
    {
        buttonId: 'id1', 
        buttonText: { 
            displayText: 'Button 1'
        }
    },
    {
        buttonId: 'id2', 
        buttonText: { 
            displayText: 'Button 2' 
        }
    }
]

const buttonMessage = {
    image: { url: "https://example.com/abcd.jpg" }, // image: buffer or path
    caption: "Hi it's button message with image",
    footer: 'Hello World',
    buttons
}

await sock.sendMessage(jid, buttonMessage)
```

**Detail Penggunaan:**

● **image:** Bisa berupa URL gambar, path file lokal, atau Buffer.

● **caption:** Teks penjelasan yang muncul tepat di bawah gambar.

● **footer:** Teks kecil yang muncul di bagian bawah pesan (biasanya untuk keterangan tambahan).

● **buttons:** Array yang berisi objek tombol. Setiap tombol membutuhkan buttonId sebagai identitas unik dan displayText untuk label tombol.

> [!TIP]
> Gunakan gambar dengan rasio 1:1 atau 16:9 agar tombol tetap terlihat jelas dan tidak tertutup saat gambar ditampilkan di layar ponsel pelanggan.


#### Mengirim Pesan Tombol dengan Video (Video Button Message)

Format ini menggabungkan konten video dengan tombol interaktif. Sangat cocok untuk konten demonstrasi produk atau testimoni.

```javascript
const buttons = [
    { 
        buttonId: 'id1', 
        buttonText: { 
            displayText: 'Button 1' 
        }
    },
    {
        buttonId: 'id2', 
        buttonText: { 
            displayText: 'Button 2' 
        }
    }
]

const buttonMessage = {
    video: { url: "https://example.com/abcd.mp4" }, // video: buffer or path
    caption: "Hi it's button message with video",
    footer: 'Hello World',
    buttons
}

await sock.sendMessage(jid, buttonMessage)
```

**Detail Penggunaan:**

● **video:** Mendukung URL, Buffer, atau path file lokal.

● **caption:** Teks penjelasan yang muncul tepat di bawah video.

● **footer:** Teks kecil yang muncul di bagian bawah pesan (biasanya untuk keterangan tambahan).

● **buttons:** Array yang berisi objek tombol. Setiap tombol membutuhkan buttonId sebagai identitas unik dan displayText untuk label tombol.

> [!IMPORTANT]
> Mengirim video memerlukan bandwidth lebih besar. Pastikan server bot kamu memiliki koneksi yang stabil saat mengunggah aset video berukuran besar.


#### Mengirim Pesan Daftar (List Message)

Gunakan format ini untuk menampilkan banyak pilihan kategori atau produk dalam satu menu terstruktur. Sangat cocok untuk daftar harga top-up atau menu layanan pelanggan.

**Contoh Pertama:**

```javascript
const sections = [
    {
        title: "keterangan"
        rows: [
            {
                title: "Pilihan 1",
                description: "Teks Deskripsi",
                rowId: ".pilihan1"
            },
            {
                title: "Pilihan 2",
                description: "Teks Deskripsi",
                rowId: ".pilihan2
            }
        ]
    }
]

const ListMessage = {
    title: "Teks Header",
    text: "Pesan Teks",
    footer: "Pesan Footer",
    buttonText: "Klik Disini",
    sections
}

await sock.sendMessage(jid, ListMessage) 
```

**Contoh Kedua:**

```javascript
const sections = [
    {
        title: "Keterangan",
        rows: [
            {
                title: "Pilihan 1",
                description: "Teks Deskripsi",
                rowId: ".pilihan1"
            },
            {
                title: "Pilihan 2",
                description: "Teks Deskripsi",
                rowId: ".pilihan1"
            }
        ]
    },
    {
        title: "Keterangan 2",
        rows: [
            {
                title: "Pilihan 3",
                description: "Teks Deskripsi",
                rowId: ".pilihan3"
            }
        ]
    }
]

const listMessage = {
    title: "Pesan Header",
    text: "Pesan teks",
    footer: "Pesan Footer",
    buttonText: "Klik Disini",
    sections
}

await sock.sendMessage(jid, listMessage) 

```

**Struktur Komponen:**

● **sections:** Array bagian menu. Kamu bisa membagi menu menjadi beberapa kategori (misalnya: Diamond ML, Diamond FF, Jasa Joki)
.
● **rows:** Isi dari setiap bagian. Setiap baris memiliki title (nama produk), description (penjelasan singkat), dan rowId (perintah yang akan dikirim bot saat diklik).

● **buttonText:** Teks pada tombol utama yang membuka daftar menu.

> [!TIP]
> Pastikan rowId yang kamu gunakan sudah sesuai dengan logika command handler pada bot kamu agar pesanan bisa diproses secara otomatis saat pelanggan memilih baris tersebut.


## ✏️ Ubah Pesan

### Menghapus Pesan (Delete for Everyone)

Kamu bisa menghapus pesan yang telah dikirim agar hilang dari obrolan penerima maupun pengirim. Kamu memerlukan objek key dari pesan yang ingin dihapus.

```javascript
// Kirim pesan terlebih dahulu
const msg = await sock.sendMessage(jid, { text: 'Pesan ini akan dihapus' })

// Hapus pesan tersebut untuk semua orang
await sock.sendMessage(jid, { delete: msg.key })
```

> [!NOTE]
> Fitur ini hanya bekerja untuk pesan yang Anda kirim sendiri atau jika bot Anda adalah admin di sebuah grup (untuk menghapus pesan anggota lain).

Untuk fitur **Hapus untuk Diri Sendiri** (Delete for Me), kamu harus menggunakan metode chatModify.

### Edit Pesan

Kamu bisa memperbarui isi pesan teks yang sudah terkirim tanpa harus menghapusnya terlebih dahulu. Ini sangat berguna untuk memperbaiki typo atau memperbarui status proses pada bot secara real-time.

```javascript
// Kirim pesan awal
const sentMsg = await sock.sendMessage(jid, { text: 'Sedang memproses...' })

// Perbarui pesan tersebut dengan teks baru
await sock.sendMessage(jid, {
    text: '✅ Proses selesai!',
    edit: sentMsg.key,
})
```

> [!IMPORTANT]
> Ketentuan Edit:

● Fitur ini hanya dapat digunakan untuk pesan bertipe teks.

● Sama seperti di aplikasi resmi, terdapat batas waktu tertentu sejak pesan dikirim di mana pesan tersebut masih bisa diedit.

● Pesan yang telah diedit akan memiliki label "Diedit" (Edited) di perangkat penerima.

## 🖼️ Manipulasi Pesan Media

### Thumbnail di Pesan Media

Lin Hua Xiu dapat menghasilkan thumbnail secara otomatis untuk pesan gambar dan stiker agar tampilan pesan lebih menarik sebelum dibuka.

● **Untuk Gambar & Stiker:** Pastikan kamu menginstal salah satu dari library berikut:

```bash
npm install jimp
# atau
npm install sharp
```
● **Untuk Video:** Pastikan **FFmpeg** sudah terinstal di sistem operasi kamu (Ubuntu/Windows/Termux).

### Unduh Pesan Media

Gunakan fungsi downloadMediaMessage untuk mengambil media yang dikirim oleh pengguna. Sangat disarankan menggunakan tipe stream untuk menjaga penggunaan memori tetap rendah.

```javascript
import { createWriteStream } from 'fs'
import { downloadMediaMessage, getContentType } from 'linhuaxiu/baileys'

sock.ev.on('messages.upsert', async ({ messages: [m] }) => {
    if (!m.message) return
    
    const messageType = getContentType(m.message)

    // Contoh: Unduh jika pesan berupa gambar
    if (messageType === 'imageMessage') {
        const stream = await downloadMediaMessage(
            m,
            'stream', // Opsi: 'stream' atau 'buffer'
            { },
            { 
                logger,
                // Diperlukan untuk mengunduh media yang sudah kadaluwarsa (lama)
                reuploadRequest: sock.updateMediaMessage 
            }
        )
        
        // Simpan file ke penyimpanan lokal
        const writeStream = createWriteStream('./media-unduhan.jpeg')
        stream.pipe(writeStream)
        
        writeStream.on('finish', () => {
            console.log('✅ Gambar berhasil disimpan!')
        })
    }
})
```

> [!TIP]
> Gunakan `getContentType` untuk mendeteksi tipe pesan secara akurat (apakah itu gambar, video, audio, atau dokumen) sebelum menjalankan proses pengunduhan.

### Unggah Ulang Pesan Media ke WhatsApp

WhatsApp secara berkala menghapus media lama dari server mereka. Jika bot kamu mencoba mengunduh atau meneruskan pesan media yang sudah lama, proses tersebut mungkin gagal. Gunakan fungsi ini untuk memperbarui metadata media agar bisa diakses kembali.

```javascript
// 'msg' adalah objek pesan media lama yang ingin diperbarui
await sock.updateMediaMessage(msg)

```

> [!TIP]
> Fungsi ini sangat berguna jika digabungkan dengan downloadMediaMessage. Dengan menyertakan reuploadRequest: sock.updateMediaMessage pada opsi pengunduhan, library akan secara otomatis mencoba mengunggah ulang media jika file aslinya sudah tidak ditemukan di server.

## 📞 Tolak Panggilan

Gunakan fitur ini untuk menjaga stabilitas bot dari gangguan panggilan suara atau video. Kamu bisa mendengarkan event call dan langsung menolaknya menggunakan callId dan callFrom.

```javascript
sock.ev.on('call', async (call) => {
    const { id, from, status } = call[0]
    
    // Hanya proses jika status panggilan adalah 'offer' (panggilan masuk)
    if (status === 'offer') {
        console.log(`🚫 Menolak panggilan dari: ${from}`)
        
        await sock.rejectCall(id, from)
        
        // Opsional: Kirim pesan otomatis setelah menolak
        await sock.sendMessage(from, { 
            text: 'Maaf, bot ini tidak dapat menerima panggilan telepon. Silakan kirim pesan teks saja.' 
        })
    }
})
```

> [!TIP]
> Fitur ini sangat disarankan untuk bot yang berjalan di server (VPS) agar sesi tetap terjaga dan tidak terputus akibat adanya incoming call yang tidak tertangani.

## 💬 Status Kirim di Obrolan

### Baca Pesan

WhatsApp mengharuskan kamu untuk menandai pesan secara eksplisit agar muncul sebagai "Sudah Dibaca" (centang biru) di sisi pengirim. Kamu bisa menandai satu atau banyak pesan sekaligus menggunakan array kunci pesan (key).

```javascript
// 'm' adalah objek pesan yang diterima
const key = m.key 

// Menandai satu pesan sebagai dibaca
await sock.readMessages([key])

// Menandai banyak pesan sekaligus sebagai dibaca
await sock.readMessages([key1, key2, key3])
```

> [!TIP]
> ID pesan adalah pengenal unik yang bisa kamu akses melalui `m.key.id`. Menyimpan ID ini sangat berguna jika kamu ingin melakukan pelacakan status pesan di database toko digitalmu.

### Update Kehadiran

Kamu bisa mengatur status kehadiran bot di dalam obrolan tertentu (Grup maupun Pribadi). Status ini akan bertahan selama kurang lebih 10 detik.

**Pilihan Status (presence):**

● **available:** Menampilkan status Online.

● **composing:** Menampilkan status "Sedang mengetik...".

● **recording:** Menampilkan status "Sedang merekam audio...".

● **paused:** Berhenti mengetik/merekam.

● **unavailable:** Menampilkan status Offline.

```javascript
// Memberitahu JID tertentu bahwa bot sedang mengetik
await sock.sendPresenceUpdate('composing', jid) 
```

> [!NOTE]
> Jika kamu sedang membuka akun WhatsApp yang sama di klien desktop (WA Web/Desktop) yang aktif, WhatsApp mungkin tidak akan mengirimkan notifikasi push ke HP kamu. Agar tetap bisa menerima notifikasi push dengan lancar, pastikan bot disetel ke status offline menggunakan sock.sendPresenceUpdate('unavailable').

## 📁 Ubah Obrolan

WhatsApp menggunakan komunikasi yang sangat terenkripsi untuk menyinkronkan status obrolan dan pengaturan aplikasi antar perangkat. Library Lin Hua Xiu mendukung modifikasi ini melalui metode chatModify.

> [!IMPORTANT]
> Gunakan fitur ini dengan hati-hati. Kesalahan dalam struktur data saat melakukan pembaruan (update) dapat menyebabkan ketidaksinkronan sesi yang mengakibatkan WhatsApp mengeluarkan (logout) akun kamu dari seluruh perangkat yang terhubung.

### Arsipkan Obrolan

Untuk mengarsipkan obrolan dengan benar agar tersinkronisasi di perangkat lain (seperti WhatsApp Web atau Mobile), kamu harus menyertakan referensi pesan terakhir dari obrolan tersebut.

```javascript
// Kamu perlu mengambil pesan terakhir dari database/store kamu
const lastMsgInChat = await getLastMessageInChat(jid) 

await sock.chatModify(
    { 
        archive: true, 
        lastMessages: [lastMsgInChat] 
    }, 
    jid
)
```

> [!TIP]
> Jika kamu menggunakan in-memory store dari library ini, kamu bisa dengan mudah mengambil pesan terakhir dari riwayat pesan yang tersimpan di memori sebelum memanggil fungsi chatModify.

### Bisukan/Buka Bisukan Obrolan

Kamu bisa mengatur agar bot tidak memberikan notifikasi pada obrolan tertentu untuk jangka waktu yang spesifik.

**Referensi Waktu Mute**

| Waktu | Milidetik   |
|-------|-------------|
| Hapus | null        |
| 8j    | 86.400.000  |
| 7h    | 604.800.000 |

```javascript
// Membisukan obrolan selama 8 jam
await sock.chatModify(
    { mute: 8 * 60 * 60 * 1000 }, 
    jid
)

// Membuka kembali bisukan (Unmute)
await sock.chatModify(
    { mute: null }, 
    jid
)
```

> [!NOTE]
> Sebagai catatan kecil, untuk 8 jam nilai milidetik yang tepat adalah 28.800.000. Pastikan perhitungan variabel kamu di kode sudah sesuai agar durasi bisu tidak meleset.

### Tandai Obrolan Dibaca/Tidak Dibaca

Kamu bisa mengubah status indikator pesan pada sebuah obrolan. Ini akan memengaruhi kemunculan angka notifikasi (badge) pada aplikasi WhatsApp.

```javascript
// Ambil pesan terakhir dari database/store
const lastMsgInChat = await getLastMessageInChat(jid)

// Menandai obrolan sebagai "Belum Dibaca" (Mark as Unread)
await sock.chatModify(
    { 
        markRead: false, 
        lastMessages: [lastMsgInChat] 
    }, 
    jid
)

// Menandai obrolan sebagai "Sudah Dibaca" (Mark as Read)
await sock.chatModify(
    { 
        markRead: true, 
        lastMessages: [lastMsgInChat] 
    }, 
    jid
)
```

> [!IMPORTANT]
> Menggunakan lastMessages sangat krusial di sini. Tanpa pesan terakhir sebagai referensi, perubahan status mungkin tidak akan muncul di perangkat lain yang sedang login (seperti WhatsApp Web).

### Hapus Pesan untuk Saya (Clear Message for Me)

Gunakan fitur ini jika kamu ingin menghapus pesan tertentu hanya dari riwayat percakapan bot, tanpa memengaruhi pesan di perangkat penerima. Fitur ini memerlukan detail spesifik dari pesan tersebut agar sinkronisasi antar perangkat tetap terjaga.

```javascript
await sock.chatModify(
    {
        clear: {
            messages: [
                {
                    id: 'ATWYHDNNWU81732J', // ID unik pesan
                    fromMe: true,           // 'true' jika pesan dikirim oleh bot
                    timestamp: '1654823909' // Waktu pengiriman (unix timestamp)
                }
            ]
        }
    }, 
    jid
)
```

> [!TIP]
> Data timestamp biasanya didapat dari objek pesan asli (m.messageTimestamp). Pastikan data ini akurat agar permintaan penghapusan diproses dengan benar oleh server WhatsApp.

### Menghapus Seluruh Obrolan (Delete Chat)

Gunakan fitur ini untuk menghapus seluruh riwayat percakapan dengan JID tertentu secara permanen. Seperti fitur manajemen obrolan lainnya, WhatsApp memerlukan metadata dari pesan terakhir sebagai validasi keamanan.

```javascript
// Ambil data pesan terakhir dari riwayat/database
const lastMsgInChat = await getLastMessageInChat(jid)

await sock.chatModify(
    {
        delete: true,
        lastMessages: [
            {
                key: lastMsgInChat.key,
                messageTimestamp: lastMsgInChat.messageTimestamp
            }
        ]
    },
    jid
)
```

> [!WARNING]
> Tindakan ini bersifat permanen. Seluruh riwayat pesan, media, dan interaksi di dalam obrolan tersebut akan dihapus dari penyimpanan server dan perangkat. Gunakan dengan sangat bijak, terutama pada akun bisnis atau bot layanan pelanggan.

### Menyematkan/Melepas Sematan Obrolan (Pin/Unpin Chat)

Kamu bisa menyematkan obrolan agar selalu berada di posisi paling atas pada daftar percakapan, atau melepasnya jika sudah tidak diperlukan.

```javascript
// Menyematkan obrolan (Pin)
await sock.chatModify(
    { pin: true }, 
    jid
)

// Melepas sematan (Unpin)
await sock.chatModify(
    { pin: false }, 
    jid
)
```

> [!NOTE]
> Sesuai dengan batasan resmi WhatsApp, kamu biasanya hanya dapat menyematkan maksimal **3 obrolan**. Jika kamu mencoba menyematkan obrolan ke-4, permintaan tersebut mungkin akan diabaikan oleh server atau menyebabkan error pada sinkronisasi.

### Membintangi/Menghapus Bintang Pesan (Star/Unstar)

Fitur ini memungkinkan kamu menandai pesan tertentu sebagai pesan favorit. Pesan yang dibintangi akan muncul di kategori khusus pada aplikasi WhatsApp pengguna. 

```javascript
await sock.chatModify({
        star: {
            messages: [
                {
                    id: 'messageID', // ID unik pesan yang ingin ditandai
                    fromMe: true     // 'true' jika pesan dikirim oleh bot
                }
            ],
            // 'true' untuk membintangi, 'false' untuk menghapus bintang
            star: true 
        }
    },
    jid
)
```

> [!TIP]
> Kamu bisa membintangi banyak pesan sekaligus dengan menambahkan objek pesan tambahan ke dalam array messages. Fitur ini sangat efektif untuk mengorganisir informasi penting tanpa perlu menyematkan (pin) seluruh obrolannya.

### Pesan Menghilang (Ephemeral Messages)

Kamu bisa mengatur agar pesan dalam obrolan otomatis terhapus setelah jangka waktu tertentu.

**Referensi Durasi Ephemeral**

| Waktu | Detik       |
|-------|-------------|
| Hapus | 0           |
| 24j   | 86.400      |
| 7h    | 604.800     |
| 90h   | 7.776.000   |

**Mengaktifkan untuk Seluruh Obrolan:**

```javascript
import { WA_DEFAULT_EPHEMERAL } from 'linhuaxiu/baileys'

// Mengatur agar semua pesan di JID ini menghilang otomatis (Default 7 hari)
await sock.sendMessage(
    jid, 
    { disappearingMessagesInChat: WA_DEFAULT_EPHEMERAL }
)
```

**Mengirim Pesan Spesifik sebagai Ephemeral:**

```javascript
// Mengirim pesan yang akan menghilang sesuai durasi tertentu
await sock.sendMessage(
    jid, 
    { text: 'Pesan ini akan menghilang otomatis.' }, 
    { ephemeralExpiration: WA_DEFAULT_EPHEMERAL }
)
```

**Mematikan Fitur:**

```javascript
await sock.sendMessage(
    jid, 
    { disappearingMessagesInChat: false }
)
```

> [!IMPORTANT]
> Mengubah pengaturan pesan menghilang di dalam grup memerlukan izin admin jika setelan grup dibatasi. Pastikan bot memiliki otoritas yang cukup sebelum memanggil fungsi ini di grup.


## 👤 Query & Validasi Pengguna

### Periksa Nomor di WhatsApp (Check Availability)

Sebelum mengirim pesan ke nomor baru, kamu bisa memvalidasi apakah nomor tersebut terdaftar di WhatsApp. Ini sangat berguna untuk menghindari pengiriman pesan ke nomor yang tidak valid.

```javascript
// Memeriksa status keberadaan nomor
const [result] = await sock.onWhatsApp(jid)

if (result?.exists) {
    console.log(`✅ ${jid} terdaftar di WhatsApp.`)
    console.log(`ID Resmi: ${result.jid}`) // Mengembalikan JID resmi (termasuk konversi nomor lama)
} else {
    console.log(`❌ Nomor tidak ditemukan di server WhatsApp.`)
}
```

> [!TIP]
> Fungsi onWhatsApp akan mengembalikan JID yang sudah diformat dengan benar. Terkadang pengguna memasukkan nomor dengan format yang salah, dan fungsi ini akan membantu bot kamu mendapatkan JID tujuan yang valid secara otomatis.

### Cek Status/Bio Pengguna

Kamu juga bisa mengambil teks status (Bio) dari profil pengguna tertentu.

```javascript
const status = await sock.fetchStatus(jid)
console.log(`Bio ${jid}: ${status.status}`)

```

### Mengambil Riwayat Obrolan (Fetch Chat History)

Kamu bisa mengambil pesan-pesan lama dari obrolan pribadi maupun grup. Fitur ini memerlukan referensi pesan tertua yang kamu miliki saat ini agar WhatsApp tahu dari titik mana riwayat harus ditarik ke belakang.

```javascript
// Kamu perlu mengambil pesan tertua yang tersimpan di database/store kamu
const msg = await getOldestMessageInChat(jid) 

await sock.fetchMessageHistory(
    50, // Jumlah pesan yang ditarik (Maksimal 50 per permintaan)
    msg.key,
    msg.messageTimestamp
)
```

> [!IMPORTANT]
> Hasil dari permintaan riwayat ini tidak langsung dikembalikan oleh fungsi tersebut. Hasilnya akan dikirimkan melalui event listener khusus yaitu messaging.history-set.

**Cara Menangani Hasil Riwayat:**

```javascript
// Mengambil informasi bio/status dari JID tertentu
const status = await sock.fetchStatus(jid)

if (status) {
    console.log(`Status/Bio: ${status.status}`)
    console.log(`Terakhir diperbarui: ${status.setAt}`) // Menampilkan waktu update status
} else {
    console.log('Gagal mengambil status atau status tidak tersedia.')
}
```

> [!TIP]
> Perlu diingat bahwa keberhasilan pengambilan status bergantung pada pengaturan privasi pengguna tersebut. Jika mereka menyetel status hanya untuk "Kontak Saya" dan nomor bot tidak disimpan, maka data ini mungkin tidak akan muncul.

### Mengambil Bio/Status Profil

Kamu bisa mengambil teks status (Bio) yang dipasang pengguna di profil mereka. Perlu diingat bahwa hasil ini bergantung pada pengaturan privasi masing-masing pengguna.

```javascript
// Mengambil data bio dari JID tertentu
const status = await sock.fetchStatus(jid)

if (status) {
    console.log(`Status/Bio: ${status.status}`)
    console.log(`Diperbarui pada: ${status.setAt}`) // Tanggal pembaruan status
} else {
    console.log('Status tidak ditemukan atau disembunyikan oleh privasi pengguna.')
}
```

> [!TIP]
> Kamu bisa memanfaatkan informasi ini untuk memverifikasi apakah akun tersebut adalah akun asli atau akun bisnis sebelum memproses transaksi yang bernilai besar.

### Mengambil Foto Profil (Avatar)

Kamu bisa mendapatkan URL foto profil dari pengguna maupun grup. Library ini mendukung pengambilan foto dalam resolusi rendah (preview) maupun resolusi tinggi (asli).

```javascript
// Mengambil foto profil resolusi rendah (thumbnail)
const ppUrl = await sock.profilePictureUrl(jid)
console.log('URL Foto (Kecil):', ppUrl)

// Mengambil foto profil resolusi tinggi (HD)
// Tambahkan parameter 'image' untuk mendapatkan kualitas asli
const ppUrlHigh = await sock.profilePictureUrl(jid, 'image')
console.log('URL Foto (HD):', ppUrlHigh)
```

> [!NOTE]
> Jika pengguna tidak memasang foto profil atau menyembunyikannya melalui pengaturan privasi, fungsi ini akan mengembalikan nilai kosong atau error. Pastikan untuk selalu menggunakan blok try...catch saat memanggil fungsi ini.

### Mengambil Profil Bisnis (Business Metadata)

Jika JID yang dituju adalah akun WhatsApp Business, kamu bisa mengambil informasi tambahan seperti deskripsi perusahaan, kategori bisnis, hingga alamat email atau situs web yang terdaftar.

```javascript
// Mengambil detail profil bisnis
const profile = await sock.getBusinessProfile(jid)

if (profile) {
    console.log(`🏢 Kategori: ${profile.category}`)
    console.log(`📝 Deskripsi: ${profile.description}`)
    
    // Informasi tambahan lainnya jika tersedia
    if (profile.email) console.log(`📧 Email: ${profile.email}`)
    if (profile.website) console.log(`🌐 Web: ${profile.website[0]}`)
}
```

> [!TIP]
> Fitur ini sangat efektif digunakan bersama onWhatsApp untuk mendeteksi apakah pelanggan kamu menggunakan akun personal atau akun bisnis, sehingga bot bisa menyesuaikan gaya bahasanya.

### Memantau Kehadiran Pengguna (Presence Update)

Kamu bisa memantau aktivitas pengguna secara real-time, seperti status saat mereka sedang online, typing (mengetik), atau recording audio. Untuk mendapatkan pembaruan ini, kamu harus berlangganan (subscribe) pada JID target.

```javascript
// 1. Dengarkan event pembaruan kehadiran
sock.ev.on('presence.update', (json) => {
    console.log(`Update Kehadiran dari ${json.id}:`, json.presences)
})

// 2. Berlangganan ke JID tertentu untuk mulai menerima pembaruan
await sock.presenceSubscribe(jid) 
```

Status yang Didukung:

**composing:** Pengguna sedang mengetik pesan teks.

**recording:** Pengguna sedang merekam pesan suara (VN).

**available:** Pengguna sedang aktif (online).

**unavailable:** Pengguna sedang tidak aktif (offline).

> [!NOTE]
> Status available (online) sering kali hanya muncul jika pengguna tersebut juga menyimpan nomor bot kamu atau ada interaksi obrolan yang aktif, tergantung pada pengaturan privasi masing-masing individu.

## 🔄 Manajemen Profil (Update Profile)

### Ubah Status/Bio Profil

Memperbarui teks bio yang muncul di bawah nama profil bot.

```javascript
await sock.updateProfileStatus('Siap melayani 24 jam')
```

### Ubah Nama Profil

Memperbarui nama tampilan (Display Name) akun bot.

```javascript
await sock.updateProfileName('Lin Hua Xiu')
```

### Ubah Foto Profil (User & Grup)

Metode ini mendukung berbagai input seperti Buffer, Stream, atau URL. Gunakan parameter jid dengan ID grup untuk mengubah foto grup.

```javascript
// Menggunakan URL atau Path File
await sock.updateProfilePicture(jid, { url: './assets/new-logo.jpeg' })

// Menggunakan Buffer (Misal hasil download atau olah gambar)
await sock.updateProfilePicture(jid, imageBuffer)
```

> [!NOTE]
> Sama seperti pengiriman media, kamu bisa menggunakan format `{ stream: Stream }`, `{ url: 'http://...' }`, atau langsung memberikan Buffer. Lihat detailnya di bagian Pesan Media.

### Hapus Foto Profil (User & Grup)

Menghapus foto profil saat ini dan mengembalikannya ke foto default (kosong).

```javascript
await sock.removeProfilePicture(jid)
```

## 👥 Manajemen Grup

> [!IMPORTANT]
> Untuk mengubah properti grup (seperti nama, deskripsi, atau pengaturan), akun bot harus menjadi Admin di grup tersebut.

### Membuat Grup Baru (Create Group)

Fungsi ini digunakan untuk membuat grup baru dengan menentukan subjek (nama grup) dan daftar peserta awal.

```javascript
// Membuat grup baru dengan nama dan daftar anggota awal
const group = await sock.groupCreate(
    'Grup Pelanggan Lin Hua Xiu', 
    ['628987654321@s.whatsapp.net', '628123456789@s.whatsapp.net']
)

console.log('Grup berhasil dibuat dengan ID: ' + group.id)

// Mengirimkan pesan sambutan ke grup yang baru dibuat
await sock.sendMessage(group.id, { text: 'Selamat datang di grup resmi!' })
```

> [!TIP]
> ID grup yang dihasilkan (group.id) akan selalu berakhiran @g.us. Pastikan kamu menyimpan ID ini jika ingin melakukan modifikasi lebih lanjut seperti mengubah foto profil grup atau menambah deskripsi segera setelah grup dibuat.

### Mengelola Peserta (Add, Kick, Promote, Demote)

Gunakan fungsi tunggal ini untuk melakukan berbagai aksi moderasi terhadap anggota grup. Kamu bisa memproses banyak anggota sekaligus dengan memasukkan daftar JID ke dalam array.

```javascript
await sock.groupParticipantsUpdate(
    jid, 
    [
        '628123456789@s.whatsapp.net', 
        '628987654321@s.whatsapp.net'
    ],
    'add' // Opsi: 'add', 'remove', 'promote', atau 'demote'
)
```

**Penjelasan Parameter Aksi:** 

● **add:** Menambahkan peserta baru ke dalam grup.

● **remove:** Mengeluarkan (kick) peserta dari grup.

● **promote:** Menaikkan jabatan peserta menjadi Admin.

● **demote:** Menurunkan jabatan admin menjadi Anggota Biasa.

> [!WARNING]
> Pastikan bot sudah menjadi admin sebelum menjalankan fungsi ini. Khusus untuk aksi add, jika pengaturan privasi target tidak mengizinkan ditambahkan secara langsung, server mungkin akan mengembalikan error atau mengirimkan undangan grup secara pribadi sebagai gantinya.

### Informasi & Metadata Grup (Group Info)

Fitur ini memungkinkan kamu untuk mengambil detail lengkap sebuah grup, mulai dari nama, deskripsi, daftar seluruh peserta, hingga status admin mereka.

```javascript
// Ambil metadata untuk satu grup spesifik
const metadata = await sock.groupMetadata(jid) 

console.log(`ID Grup: ${metadata.id}`)
console.log(`Nama Grup: ${metadata.subject}`)
console.log(`Deskripsi: ${metadata.desc}`)
console.log(`Jumlah Anggota: ${metadata.participants.length}`)

// Contoh: Mencari tahu siapa pembuat grup (Creator)
console.log(`Pembuat Grup: ${metadata.owner}`)

// Mengambil daftar semua grup yang saat ini diikuti oleh bot
const allGroups = await sock.groupFetchAllParticipating()
console.log('Daftar Grup:', allGroups)
```

> [!TIP]
> Data metadata.participants berisi array objek. Kamu bisa memfilternya untuk mencari daftar admin saja dengan mengecek properti admin (nilainya bisa 'admin' atau 'superadmin') pada tiap objek peserta.

### Mengubah Deskripsi Grup

Kamu bisa memperbarui teks deskripsi grup untuk memberikan informasi tambahan atau peraturan kepada anggota. Pastikan bot memiliki hak akses admin sebelum menjalankan fungsi ini.

```javascript
// Memperbarui deskripsi grup
await sock.groupUpdateDescription(
    jid, 
    'Selamat datang'
)
```

> [!TIP]
> Jika kamu ingin menghapus deskripsi grup secara total, kamu bisa mengirimkan string kosong '' sebagai argumen deskripsi.

### Pengaturan Privasi & Izin Grup

Kontrol siapa yang bisa mengirim pesan atau mengubah info grup. 

```javascript
// Hanya admin kirim pesan
await sock.groupSettingUpdate(jid, 'announcement')
// Semua bisa kirim
await sock.groupSettingUpdate(jid, 'not_announcement')
// Semua bisa ubah pengaturan grup
await sock.groupSettingUpdate(jid, 'unlocked')
// Hanya admin ubah pengaturan
await sock.groupSettingUpdate(jid, 'locked')
```

### Keluar dari Grup (Leave Group)

Gunakan fungsi ini jika bot perlu meninggalkan grup tertentu. Setelah keluar, bot tidak akan lagi menerima pesan dari grup tersebut kecuali diundang kembali atau menggunakan tautan undangan.

```javascript
await sock.groupLeave(jid)
```

> [!CAUTION]
> Pastikan kamu memiliki logika yang tepat sebelum memanggil fungsi ini. Jika bot keluar dari grup yang dikelola sendiri tanpa ada admin lain, grup tersebut akan tetap ada namun tanpa pengawas, yang bisa menyulitkan pengelolaan manual nantinya.

### Tautan Undangan Grup (Group Invite Link)

Gunakan fitur ini untuk mendapatkan kode unik grup yang bisa dibagikan sebagai tautan undangan. Siapa pun yang memiliki tautan ini dapat bergabung ke dalam grup (tergantung pengaturan persetujuan admin).

```javascript
// Ambil Kode Undangan dari server
const code = await sock.groupInviteCode(jid)

// Format menjadi tautan lengkap
const inviteLink = `https://chat.whatsapp.com/${code}`

console.log('Tautan Undangan Grup:', inviteLink)
```

> [!TIP]
> Jika kamu merasa grup sudah terlalu penuh atau ingin menghentikan akses melalui tautan lama, kamu bisa menggunakan fungsi groupRevokeInvite(jid) untuk membatalkan kode tersebut dan menghasilkan kode baru.

### Mencabut Kode Undangan (Revoke Invite Link)

Gunakan fungsi ini untuk membatalkan tautan undangan yang sedang aktif. Setelah dijalankan, siapa pun yang menggunakan tautan lama tidak akan bisa bergabung, dan bot akan mendapatkan kode undangan baru sebagai penggantinya.

```javascript
// Membatalkan tautan undangan lama dan menghasilkan yang baru
const newCode = await sock.groupRevokeInvite(jid)

console.log('Tautan lama telah kedaluwarsa.')
console.log(`Tautan baru: https://chat.whatsapp.com/${newCode}`)
```

> [!WARNING]
> Tindakan ini bersifat permanen untuk kode yang lama. Pastikan untuk memperbarui tautan di deskripsi toko atau pesan otomatis kamu setelah melakukan revoke.

### Bergabung via Kode Undangan

Bot bisa bergabung ke grup mana pun menggunakan kode undangan (akhiran dari tautan WhatsApp). Pastikan hanya memasukkan kodenya saja, tanpa menyertakan domain chat.whatsapp.com.

```javascript
// Contoh: Untuk tautan https://chat.whatsapp.com/LInHuAXiu123
const code = 'LInHuAXiu123' 

const response = await sock.groupAcceptInvite(code)
console.log('Berhasil bergabung ke grup dengan ID: ' + response)
```

> [!TIP]
> Jika grup tersebut mengaktifkan fitur Persetujuan Admin, pemanggilan fungsi ini tidak akan langsung memasukkan bot ke dalam grup, melainkan akan mengirimkan permintaan bergabung yang harus disetujui oleh admin grup tersebut terlebih dahulu.

### Mengambil Info Grup dari Kode Undangan

Kamu bisa melihat detail sebuah grup hanya dengan kode undangannya tanpa harus bergabung terlebih dahulu. Ini sangat berguna untuk melakukan validasi grup.

```javascript
const response = await sock.groupGetInviteInfo(code)
console.log('info grup: ' + response)
```

> [!TIP]
> Gunakan fitur ini untuk memfilter grup sebelum bot bergabung otomatis. Misalnya, kamu bisa mengatur bot agar hanya bergabung jika jumlah pesertanya lebih dari 10 orang untuk memastikan grup tersebut cukup aktif.

### Query Metadata Grup (Detail Lengkap)

Gunakan fungsi ini untuk menarik informasi mendalam mengenai sebuah grup. Data yang dikembalikan mencakup segalanya mulai dari ID, nama, deskripsi, hingga daftar seluruh peserta beserta peran mereka (admin atau anggota).

```javascript
// Mengambil metadata lengkap grup
const metadata = await sock.groupMetadata(jid) 

console.log(`ID Grup: ${metadata.id}`)
console.log(`Judul: ${metadata.subject}`)
console.log(`Deskripsi: ${metadata.desc}`)
console.log(`Total Anggota: ${metadata.participants.length}`)

// Contoh: Mendapatkan daftar admin saja
const admins = metadata.participants.filter(p => p.admin !== null)
console.log('Daftar Admin:', admins.map(a => a.id))
```

> [!NOTE]
> Metadata ini sangat berguna untuk memvalidasi perintah. Misalnya, kamu bisa membuat perintah /hapus yang hanya bisa dijalankan jika pengirim pesan ada di dalam daftar admin yang didapat dari metadata.participants.

### Bergabung via Pesan Undangan (Invite V4)

Gunakan fungsi ini jika bot mendapatkan pesan undangan grup secara personal. Berbeda dengan kode undangan publik, metode ini memerlukan JID grup dan objek pesan undangan yang dikirimkan oleh pengguna.

```javascript
// Bergabung menggunakan pesan undangan (V4)
// Biasanya didapat dari pesan bertipe 'groupInviteMessage'
const response = await sock.groupAcceptInviteV4(jid, groupInviteMessage)

console.log('Berhasil bergabung ke grup:', response)
```

> [!NOTE]
> Objek groupInviteMessage biasanya berisi informasi seperti inviteCode, inviteExpiration, dan groupName. Fitur ini sangat berguna untuk memastikan bot bisa masuk ke grup eksklusif yang memerlukan undangan langsung dari admin atau anggota resmi.

### Mengambil Daftar Permintaan Gabung (Pending Requests)

Jika grup mengaktifkan fitur `Setujui Peserta Baru` (Admin Approval), gunakan fungsi ini untuk melihat daftar pengguna yang sedang menunggu persetujuan dari admin untuk bergabung ke dalam grup.

```javascript
// Mengambil daftar calon anggota yang menunggu persetujuan
const pending = await sock.groupRequestParticipantsList(jid)

console.log(`Ada ${pending.length} permintaan gabung yang tertunda.`)

// Contoh melihat detail pendaftar
pending.forEach(user => {
    console.log(`User: ${user.jid}, Waktu Request: ${user.requestTime}`)
})
```

> [!TIP]
> Kamu bisa menggabungkan fitur ini dengan logika otomatisasi. Misalnya, bot bisa mengirimkan pesan ke admin setiap ada permintaan baru, atau mencocokkan JID pendaftar dengan database pelanggan sebelum memberikan persetujuan.

### Setujui atau Tolak Permintaan Gabung

Setelah mendapatkan daftar permintaan tertunda, kamu bisa menggunakan fungsi ini untuk memberikan akses masuk atau menolak calon anggota tersebut. Kamu bisa memproses banyak pengguna sekaligus dalam satu perintah.

```javascript
// Menyetujui atau menolak calon anggota
const response = await sock.groupRequestParticipantsUpdate(
    jid,
    ['628987654321@s.whatsapp.net', '628123456789@s.whatsapp.net'],
    'approve' // Gunakan 'approve' untuk menerima atau 'reject' untuk menolak
)

console.log('Status pembaruan permintaan:', response)
```

### Mengambil Semua Metadata Grup yang Diikuti

Gunakan fungsi ini untuk menarik daftar lengkap dari seluruh grup di mana bot bergabung sebagai anggota. Respons yang dikembalikan berupa objek yang berisi metadata lengkap untuk setiap grup.

```javascript
// Mengambil semua grup yang diikuti oleh bot
const allGroups = await sock.groupFetchAllParticipating()

// Contoh: Menampilkan nama semua grup
Object.values(allGroups).forEach(group => {
    console.log(`Nama Grup: ${group.subject} | ID: ${group.id}`)
})
```

> [!NOTE]
> Fungsi ini mengembalikan banyak data sekaligus. Ini sangat berguna jika kamu ingin membuat fitur sinkronisasi database agar bot selalu mengetahui status adminnya di semua grup tanpa harus mengeceknya satu per satu secara manual.

### Pengaturan Pesan Menghilang (Ephemeral)

Fitur ini memungkinkan pesan di dalam grup otomatis terhapus setelah jangka waktu tertentu. Kamu bisa mengatur durasinya menggunakan satuan detik.

**Tabel Referensi Waktu:**

| Waktu | Detik       |
|-------|-------------|
| Hapus | 0           |
| 24j   | 86.400      |
| 7h    | 604.800     |
| 90h   | 7.776.000   |

```javascript
// Mengaktifkan pesan menghilang selama 24 jam
await sock.groupToggleEphemeral(jid, 86400)

// Mematikan fitur pesan menghilang
await sock.groupToggleEphemeral(jid, 0)
```

> [!IMPORTANT]
> Sama seperti pengaturan grup lainnya, bot harus menjadi Admin untuk bisa mengubah status ephemeral di dalam grup tersebut.

### Mengatur Izin Tambah Anggota (Member Add Mode)

Fitur ini digunakan untuk menentukan siapa saja yang diperbolehkan menambahkan peserta baru ke dalam grup. Ini sangat berguna untuk mencegah spam di grup resmi.

```javascript
// Izinkan semua anggota menambahkan peserta baru
await sock.groupMemberAddMode(jid, 'all_member_add')

// Hanya admin yang bisa menambahkan peserta baru
await sock.groupMemberAddMode(jid, 'admin_add')
```

> [!TIP]
> Untuk grup profesional seperti grup transaksi atau testimoni, sangat disarankan menggunakan mode 'admin_add' agar bot kamu memiliki kendali penuh atas siapa saja yang masuk ke dalam ekosistem grup.

## 🔒 Privasi

Gunakan fitur ini untuk mengatur interaksi bot dengan pengguna lain, termasuk keamanan dari gangguan pihak ketiga.

### Blokir/Buka Blokir Pengguna

Fungsi ini memungkinkan bot untuk membatasi komunikasi dengan pengguna tertentu. Pengguna yang diblokir tidak akan bisa mengirim pesan atau melihat status bot.

```javascript
// Memblokir pengguna
await sock.updateBlockStatus('628123456789@s.whatsapp.net', 'block')

// Membuka blokir pengguna
await sock.updateBlockStatus('628123456789@s.whatsapp.net', 'unblock')
```

> [!TIP]
> Kamu bisa membuat sistem otomatis di mana bot akan memblokir pengguna secara mandiri jika mereka melakukan spam perintah lebih dari batas yang ditentukan dalam database.

### Ambil Pengaturan Privasi (Fetch Privacy Settings)

Gunakan fungsi ini untuk mengetahui konfigurasi privasi yang saat ini diterapkan pada akun bot, seperti siapa yang bisa melihat foto profil, status, hingga terakhir dilihat (last seen).

```javascript
// Mengambil pengaturan privasi saat ini
const privacySettings = await sock.fetchPrivacySettings(true)

console.log('--- Pengaturan Privasi ---')
console.log(`Terakhir Dilihat: ${privacySettings.last}`)
console.log(`Foto Profil: ${privacySettings.profile}`)
console.log(`Status/Stories: ${privacySettings.status}`)
console.log(`Laporan Dibaca (Read Receipt): ${privacySettings.readreceipts}`)
console.log(`Grup (Siapa yang bisa menambah): ${privacySettings.groupadd}`)
```

**Kemungkinan Nilai (Values):**

● **all:** Semua orang.

● **contacts:** Hanya kontak yang tersimpan.

● **contact_blacklist:** Kontak kecuali yang masuk daftar hitam.

● **none:** Tidak ada siapa pun.

> [!TIP]
> Jika kamu ingin bot terlihat lebih profesional dan terjaga privasinya di lingkungan kerja, pastikan pengaturan profile dan status diatur ke contacts atau all agar pelanggan tetap bisa melihat identitas bot.

### Mengambil Daftar Blokir (Fetch Blocklist)

Gunakan fungsi ini untuk mendapatkan daftar seluruh JID (nomor telepon) yang saat ini sedang diblokir oleh akun bot.

```javascript
// Mengambil semua nomor yang diblokir
const blocklist = await sock.fetchBlocklist()

console.log('Daftar Pengguna Terblokir:')
blocklist.forEach(jid => {
    console.log(`- ${jid}`)
})

console.log(`Total: ${blocklist.length} pengguna terblokir.`)
```

> [!TIP]
> Jika kamu mengelola bot dalam lingkungan tim, daftar ini bisa digunakan untuk membuat laporan berkala mengenai akun-akun yang melakukan pelanggaran atau spam pada sistem botmu.

### Mengubah Privasi Terakhir Dilihat (Update Last Seen)

Fungsi ini digunakan untuk mengatur siapa saja yang dapat melihat kapan terakhir kali bot aktif atau status online bot.

```javascript
// Pilihan nilai: 'all', 'contacts', 'contact_blacklist', atau 'none'
const value = 'all' 

await sock.updateLastSeenPrivacy(value)
console.log(`Privasi 'Terakhir Dilihat' berhasil diubah menjadi: ${value}`)
```

**Penjelasan Opsi:**

● **all:** Semua orang dapat melihat status online/terakhir dilihat.

● **contacts:** Hanya nomor yang tersimpan di kontak bot yang bisa melihat.

● **contact_blacklist:** Semua kontak kecuali mereka yang ada di daftar hitam.

● **none:** Tidak ada siapapun yang bisa melihat (fitur ini juga akan membuat bot tidak bisa melihat status online orang lain).

> [!TIP]
> Mengatur ke all sangat disarankan jika kamu ingin pelanggan merasa yakin bahwa bot sedang aktif dan siap melayani pesanan top-up diamond mereka.

### Mengubah Privasi Status Online (Update Online Privacy)

Fungsi ini digunakan untuk mengatur siapa yang bisa melihat saat bot sedang dalam keadaan "Online". Fitur ini bekerja sangat erat dengan pengaturan Last Seen.

```javascript
// Pilihan nilai: 'all' atau 'match_last_seen'
const value = 'all' 

await sock.updateOnlinePrivacy(value)
console.log(`Privasi status 'Online' berhasil diubah menjadi: ${value}`)
```

**Penjelasan Opsi:**

● **all:** Semua orang dapat melihat saat bot sedang online.

● **match_last_seen:** Status online bot hanya akan terlihat oleh orang yang diizinkan melihat status Last Seen (Terakhir Dilihat) kamu.

> [!NOTE]
> Jika kamu ingin privasi yang sangat ketat, gunakan `match_last_seen` dan atur `updateLastSeenPrivacy` ke `none`. Dengan begitu, bot akan terlihat offline secara permanen bagi semua orang.

### Mengubah Privasi Foto Profil (Update Profile Picture Privacy)

Gunakan fungsi ini untuk menentukan siapa saja yang diperbolehkan melihat foto profil akun bot kamu.

```javascript
// Pilihan nilai: 'all', 'contacts', 'contact_blacklist', atau 'none'
const value = 'all' 

await sock.updateProfilePicturePrivacy(value)
console.log(`Hak akses foto profil berhasil diubah menjadi: ${value}`)
```

**Penjelasan Opsi:**

● **all:** Foto profil dapat dilihat oleh semua orang, termasuk nomor yang tidak disimpan.

● **contacts:** Hanya kontak yang tersimpan di buku telepon bot yang dapat melihat foto profil.

● **contact_blacklist:** Semua kontak kecuali mereka yang ada di daftar blokir privasi.

● **none:** Foto profil tidak akan terlihat oleh siapa pun (akan muncul sebagai gambar profil default kosong).

>[!TIP]
> Untuk akun bisnis seperti bot top-up, sangat disarankan menggunakan opsi `all` agar pelanggan baru bisa langsung mengenali identitas toko kamu melalui logo yang terpasang.

### Mengubah Privasi Status (Update Status Privacy)

Gunakan fungsi ini untuk mengontrol siapa saja yang dapat melihat pembaruan status (cerita/stories) yang diunggah oleh bot.

```javascript
// Pilihan nilai: 'all', 'contacts', 'contact_blacklist', atau 'none'
const value = 'all' 

await sock.updateStatusPrivacy(value)
console.log(`Hak akses pembaruan status berhasil diubah menjadi: ${value}`)
```

**Penjelasan Opsi:**

● **all:** Status dapat dilihat oleh semua orang yang menyimpan nomor bot (dan nomornya disimpan balik oleh bot).

● **contacts:** Hanya kontak yang saling simpan yang dapat melihat status.

● **contact_blacklist:** Kecuali orang-orang tertentu di daftar hitam.

● **none:** Tidak ada yang bisa melihat status bot.

> [!NOTE]
> Perlu diingat bahwa di WhatsApp, status hanya akan muncul jika kedua belah pihak saling menyimpan nomor kontak masing-masing.

### Mengubah Privasi Tanda Terima Baca (Read Receipts)

Fungsi ini digunakan untuk mengatur apakah bot akan mengirimkan tanda `centang biru` saat pesan telah dibaca.

```javascript
// Pilihan nilai: 'all' (Aktif) atau 'none' (Matikan)
const value = 'all' 

await sock.updateReadReceiptsPrivacy(value)
console.log(`Pengaturan tanda terima baca berhasil diubah menjadi: ${value}`)
```

**Penjelasan Opsi:**

● **all:**bBot akan mengirimkan tanda centang biru setelah membaca pesan. Kamu juga bisa melihat centang biru pada pesan yang kamu kirim ke orang lain.

● **none:** Bot tidak akan mengirimkan tanda centang biru. Konsekuensinya, bot juga tidak akan bisa melihat apakah pesan yang dikirimkannya sudah dibaca oleh orang lain atau belum.

> [!NOTE]
> Pengaturan ini tidak berlaku di dalam grup. Di dalam grup, tanda terima baca akan selalu terkirim meskipun fitur ini dimatikan.

### Mengubah Privasi Tambah Grup (Groups Add Privacy)

Gunakan fungsi ini untuk membatasi siapa saja yang memiliki izin untuk memasukkan akun bot ke dalam sebuah grup secara langsung.

```javascript
// Pilihan nilai: 'all', 'contacts', atau 'contact_blacklist'
const value = 'contacts' 

await sock.updateGroupsAddPrivacy(value)
console.log(`Izin penambahan grup berhasil diubah menjadi: ${value}`)
```

**Penjelasan Opsi:**

● **all:** Siapa pun yang memiliki nomor bot dapat memasukkannya ke dalam grup tanpa persetujuan.

● **contacts:** Hanya nomor yang tersimpan di kontak bot yang bisa memasukkannya ke dalam grup.

● **contact_blacklist:** Semua orang kecuali mereka yang ada di daftar hitam privasi dapat memasukkan bot ke grup.

> [!TIP]
> Mengatur ke `contacts` sangat disarankan untuk bot bisnis agar akun kamu tidak dimasukkan ke dalam grup spam oleh orang asing yang tidak dikenal.

### Mengatur Mode Pesan Menghilang Default

Fungsi ini menetapkan durasi ephemeral (pesan menghilang) secara otomatis untuk semua obrolan chat baru. Ini tidak akan memengaruhi obrolan yang sudah ada sebelumnya.

**Tabel Referensi Waktu:**

| Waktu | Detik       |
|-------|-------------|
| Hapus | 0           |
| 24j   | 86.400      |
| 7h    | 604.800     |
| 90h   | 7.776.000   |

```javascript
// Mengatur agar semua chat baru otomatis menghilang dalam 24 jam
const ephemeral = 86400 

await sock.updateDefaultDisappearingMode(ephemeral)
console.log('Mode pesan menghilang default telah diperbarui.')
```

> [!NOTE]
> Fitur ini sangat berguna untuk menjaga kerahasiaan data secara otomatis sejak interaksi pertama dimulai.

## 📢 Daftar Siaran & Cerita

Gunakan fitur ini untuk menjangkau banyak audiens sekaligus, baik melalui pesan siaran pribadi maupun melalui pembaruan status. 

### Kirim Siaran & Cerita

Pesan dapat dikirim ke daftar siaran atau diunggah sebagai cerita. Kamu perlu menambahkan opsi tambahan pada fungsi sendMessage untuk mengaktifkan mode ini.

```javascript
await sock.sendMessage(
    jid, // Contoh: 'status@broadcast' untuk Story atau '12345678@broadcast' untuk Siaran
    {
        image: { url: 'https://ika-store.com/promo.jpg' },
        caption: 'Promo Top-Up Weekend di Ika Store!'
    },
    {
        backgroundColor: '#31081F', // Warna latar (khusus pesan teks di story)
        font: 1,                    // Jenis font (khusus story)
        statusJidList: ['628123xxx@s.whatsapp.net', '628987xxx@s.whatsapp.net'], // Daftar penerima
        broadcast: true             // Wajib diatur true untuk mode siaran
    }
)
```

**Detail Parameter:**

● **Body Pesan:** Bisa berupa text, image, video, atau extendedTextMessage. Lihat bagian Pesan Media.

● **broadcast: true:** Mengaktifkan mode pengiriman siaran.

● **statusJidList:** Daftar JID pengguna yang akan menerima siaran atau yang diizinkan melihat cerita kamu.

● **ID Target (jid):**
1. Gunakan status@broadcast untuk mengunggah ke **WhatsApp Status**.
2. Gunakan ID@broadcast untuk mengirim ke **Daftar Siaran** tertentu.

> [!TIP]
> Mengirimkan katalog harga terbaru via **Status** adalah cara terbaik untuk mengingatkan pelanggan tanpa harus mengirim pesan pribadi (PM) yang mungkin dianggap mengganggu.

### Query Penerima & Nama Daftar Siaran

Jika kamu memiliki ID daftar siaran yang sudah ada, gunakan fungsi ini untuk mengambil informasi detail mengenai nama daftar tersebut beserta siapa saja anggota yang terdaftar di dalamnya.

```javascript
// Mengambil informasi detail dari ID Broadcast tertentu
const bList = await sock.getBroadcastListInfo('1234567890@broadcast')

console.log(`Nama Daftar Siaran: ${bList.name}`)
console.log(`Jumlah Penerima: ${bList.recipients.length}`)

// Menampilkan semua JID penerima dalam daftar
bList.recipients.forEach(recipient => {
    console.log(`- JID: ${recipient.jid}`)
})
```

> [!NOTE]
> Fitur ini memudahkan kamu untuk melakukan segmentasi pelanggan, misalnya memisahkan daftar siaran untuk `Reseller VIP` dan `Pelanggan Umum` di **Bot kamu**.

## 🛠️ Fungsi Kustom

Fungsi Kustom
Lin Hua Xiu dirancang agar sangat fleksibel untuk kebutuhan pengembangan tingkat lanjut. Kamu dapat menambahkan ekstensi atau fungsi kustom sendiri untuk memodifikasi perilaku bot tanpa harus melakukan fork pada source code utama.

### Aktifkan Level Debug di Log

Jika kamu sedang dalam tahap pengembangan atau mengalami kendala teknis (error), kamu bisa meningkatkan detail informasi yang ditampilkan di terminal dengan mengubah level logger ke debug.

```javascript
import P from 'pino'

const sock = makeWASocket({
    // Mengaktifkan logger dengan level debug
    logger: P({ level: 'debug' }),
    auth: state,
    // ... opsi lainnya
})
```

**Tingkatan Log (Logger Levels):**

● **silent:** Tidak menampilkan log apa pun.

● **error:** Hanya menampilkan pesan error yang fatal.

● **warn:** Menampilkan peringatan dan error.

● **info:** Standar log untuk informasi umum (default).

● **debug:** Menampilkan detail teknis lengkap (sangat berguna untuk melacak bug).

● **trace:** Menampilkan seluruh aktivitas data secara mentah.

> [!WARNING]
> Menggunakan level debug atau trace akan menghasilkan banyak teks di terminal dan bisa sedikit memperlambat kinerja jika dijalankan secara terus-menerus. Gunakan level ini hanya saat melakukan perbaikan kode.

### Memahami Cara WhatsApp Berkomunikasi

Baileys beroperasi dengan menangkap dan mengirim frame data yang mengikuti protokol keamanan tingkat tinggi. Memahami struktur ini sangat berguna untuk melakukan debugging tingkat lanjut atau memantau status perangkat.

> [!TIP]
> Untuk pemahaman lebih dalam, pelajari Libsignal Protocol (untuk enkripsi end-to-end) dan Noise Protocol (untuk jabat tangan/handshake koneksi).

**Contoh: Melacak Persentase Baterai**

Jika kamu mengaktifkan log level debug atau trace, kamu akan melihat data mentah dalam format JSON. Berikut adalah contoh frame komunikasi saat WhatsApp mengirimkan informasi sistem:

```json
{
    "level": 10,
    "fromMe": false,
    "frame": {
        "tag": "ib",
        "attrs": {
            "from": "@s.whatsapp.net"
        },
        "content": [
            {
                "tag": "edge_routing",
                "attrs": {},
                "content": [
                    {
                        "tag": "routing_info",
                        "attrs": {},
                        "content": {
                            "type": "Buffer",
                            "data": [8, 2, 8, 5]
                        }
                    }
                ]
            }
        ]
    },
    "msg": "communication"
}
```

**Struktur Frame:**

● **tag:** Jenis frame atau node (misalnya ib, message, presence).

● **attrs:** Atribut atau metadata yang berisi informasi pengirim, ID pesan, atau stempel waktu.

● **content:** Data utama atau konten dari frame tersebut (bisa berupa teks, buffer, atau node anak lainnya).

### Mendaftarkan Callback untuk Event WebSocket

Untuk kontrol yang lebih granular, kamu bisa mendengarkan (listen) langsung pada node-node tertentu yang dikirim melalui WebSocket. Ini memungkinkan kamu menangkap BinaryNode berdasarkan tag, ID, atau konten tertentu.

> [!TIP]
> Kamu bisa mempelajari implementasi asli fungsi-fungsi ini dengan melihat onMessageReceived di dalam file socket.ts.

```javascript
// Mendengarkan semua node dengan tag 'edge_routing'
sock.ws.on('CB:edge_routing', (node: BinaryNode) => { 
    console.log('Node routing diterima:', node)
})

// Mendengarkan node 'edge_routing' dengan ID spesifik 'abcd'
sock.ws.on('CB:edge_routing,id:abcd', (node: BinaryNode) => { 
    console.log('Node dengan ID abcd diterima')
})

// Mendengarkan node 'edge_routing' dengan ID 'abcd' yang berisi child node 'routing_info'
sock.ws.on('CB:edge_routing,id:abcd,routing_info', (node: BinaryNode) => { 
    console.log('Data routing_info spesifik ditemukan')
})
```

Mengapa Menggunakan Callback WS?

● **Efisiensi:** Menangkap data spesifik tanpa harus memfilter semua pesan yang masuk.

● **Low-Level Control:** Mengakses informasi protokol mentah yang biasanya disembunyikan oleh abstraksi tingkat tinggi.

● **Fitur Kustom:** Berguna untuk menangani handshake tambahan atau integrasi dengan server edge WhatsApp secara langsung.

## 💡 Tips & Praktik Terbaik

Untuk memastikan bot berjalan stabil dalam lingkungan produksi dan menghindari risiko pemblokiran akun, ikuti panduan berikut:

**1. Optimalisasi Performa**
● **Gunakan Caching:** Simpan metadata grup dan profil pengguna di memori lokal atau Redis. Menarik data langsung dari WhatsApp setiap saat akan memperlambat respon bot.

● **Kelola Store:** Untuk penggunaan skala besar, jangan mengandalkan file JSON. Gunakan database relasional atau NoSQL seperti MongoDB atau PostgreSQL untuk menyimpan data chat dan kontak.

**2. Kestabilan & Keamanan**
● **Tangani Rekoneksi:** WhatsApp sering memutus koneksi secara sepihak. Selalu implementasikan logika auto-reconnect pada event connection.update agar bot segera aktif kembali.

● **Keamanan Kredensial:** Jangan pernah mengunggah folder auth atau file sesi ke repositori publik (GitHub). Gunakan Variabel Lingkungan (.env) untuk menyimpan informasi sensitif.

● **Penanganan Error:** Selalu bungkus panggilan fungsi sock dengan blok try-catch untuk mencegah bot crash saat terjadi gangguan jaringan atau data yang tidak valid.

**3. Etika & Kebijakan (Anti-Ban)**
● **Hormati Batas Rate:** Jangan mengirim terlalu banyak pesan dalam waktu singkat (spam). Berikan jeda (delay) acak antara 3-5 detik antar pesan untuk meniru perilaku manusia.

● **Jangan Spam:** Pastikan fitur Siaran/Broadcast hanya digunakan untuk pelanggan yang memang menginginkan informasi dari toko kamu.


## 📄 Lisensi

Copyright (c) 2026 Lin Hua Xiu

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.


---

**🌸 Catatan Lin Hua Xiu**
Dokumentasi ini disusun untuk mempermudah pengembangan ekosistem bot WhatsApp yang lebih stabil dan transparan. Selamat berkoding! 🚀
