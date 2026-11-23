# RBAC Studio — Laravel 12 + Vue 3 Starter Template

Starter template aplikasi admin modern dengan **Role-Based Access Control (RBAC)** lengkap, siap dipakai sebagai fondasi project baru. Backend Laravel 12, frontend Vue 3 SPA (Inertia-free, murni API), autentikasi session berbasis cookie dengan proteksi CSRF penuh.

[![Laravel](https://img.shields.io/badge/Laravel-12-red)](https://laravel.com)
[![Vue](https://img.shields.io/badge/Vue-3-brightgreen)](https://vuejs.org)
[![License](https://img.shields.io/badge/license-MIT-blue)](#license)

## ✨ Fitur

### Autentikasi
- 🔐 **Login/Register/Logout** — session-based, cookie HttpOnly, CSRF terproteksi
- 📧 **Email verification** (opsional, toggle via env)
- 🔑 **Forgot & reset password** — token via email, anti user-enumeration
- 👤 **Profil** — edit nama/email/telepon, ganti password, upload avatar
- 🛡️ **Two-Factor Authentication (TOTP)** — QR code SVG, challenge saat login
- ⚠️ **Force change password** — user baru dari admin wajib ganti password saat login pertama
- 🚪 Guest middleware — halaman auth tidak bisa diakses saat sudah login

### RBAC
- 👥 **Manajemen User** — CRUD, assign role, aktif/nonaktif, filter per role
- 🗑️ **Sampah (Soft Deletes)** — trash, restore, hapus permanen
- 🧩 **Role & Permission** — CRUD via UI, permission dikelompokkan per grup
- 🛡️ **Middleware `permission:`** — proteksi route per slug, superadmin bypass

### Admin Tools
- 📜 **Audit Log / Activity Log** — otomatis mencatat create/update/delete/restore/force-delete (siapa, apa, kapan, IP)
- 📊 **DataTable reusable** — search, sort server-side, pagination, slot per kolom
- 🌙 **Dark mode** — toggle + persist di localStorage
- ⚙️ **Settings page** — pengaturan aplikasi tersimpan database (cached)
- 🔔 **Toast global** + SweetAlert yang sudah di-styling rapi
- 📱 **Responsive** — sidebar drawer di mobile, collapsible di desktop

### Lainnya
- 📤 **Export/Import CSV** pengguna (UTF-8 BOM, aman Excel)
- 🔑 **API Token (Bearer)** — dual auth: session web + token untuk mobile/integrasi
- 🧪 **PHPUnit tests** — 18 test untuk auth, RBAC, dan API token
- 🚀 **`php artisan app:setup`** — wizard setup interaktif
- 🔒 Password policy: min 8 karakter + huruf besar + angka + simbol

## 🛠️ Teknologi

| Layer | Teknologi |
|---|---|
| Backend | Laravel 12, PHP 8.2+ |
| Frontend | Vue 3 (Composition API), Vue Router 5, Tailwind CSS 4 |
| Database | MySQL (default) / SQLite |
| Auth | Session + CSRF, TOTP 2FA (PragmaRX Google2FA) |
| Testing | PHPUnit 11 (DB SQLite terpisah) |
| Build | Vite 7 |

## 🚀 Instalasi

### Persyaratan
- PHP ≥ 8.2 (ekstensi: `pdo_mysql`, `mbstring`, `openssl`, `gd` — opsional untuk avatar)
- Composer ≥ 2
- Node.js ≥ 20 & npm
- MySQL ≥ 8 (atau SQLite)

### Langkah setup

```bash
# 1. Clone project
git clone https://github.com/username/laravel-vue-rbac.git
cd laravel-vue-rbac

# 2. Install dependency
composer install
npm install

# 3. Konfigurasi environment
cp .env.example .env
php artisan key:generate

# 4. Sesuaikan .env (database & email)
#    DB_DATABASE=laravel_vue_rbac
#    DB_USERNAME=root
#    DB_PASSWORD=
#    MAIL_MAILER=log          # untuk development
#    FRONTEND_URL=http://localhost:8000

# 5. Setup database + akun admin + settings (wizard interaktif)
php artisan app:setup

# 6. Jalankan
php artisan serve
npm run dev        # terminal kedua, untuk development
npm run build      # untuk production
```

Buka `http://localhost:8000/login` dan login dengan akun yang dibuat saat `app:setup`.

### Setup manual (tanpa wizard)

```bash
php artisan migrate --seed
php artisan storage:link
```

Akun default dari seeder:

| Email | Password |
|---|---|
| `superadmin@example.com` | `Superadmin123!` |

> ⚠️ **Ganti password ini sebelum deploy!**

## ⚙️ Konfigurasi (toggle via `.env`)

```env
AUTH_REGISTRATION_ENABLED=true          # aktif/nonaktifkan registrasi publik
AUTH_EMAIL_VERIFICATION_ENABLED=false   # wajib verifikasi email setelah register
AUTH_PASSWORD_RESET_ENABLED=true        # aktif/nonaktifkan reset password
FRONTEND_URL=http://localhost:8000      # untuk link verifikasi & reset di email
```

## 📁 Struktur Penting

```
app/
├── Console/Commands/AppSetup.php     # Wizard setup
├── Http/Controllers/Api/             # Auth, Profile, User, Role,
│                                     # Permission, ActivityLog, Settings,
│                                     # ApiToken, TwoFactor, Dashboard
├── Http/Middleware/                  # EnsureUserHasPermission (permission:),
│                                     # RedirectIfAuthenticated (guest),
│                                     # EnsureEmailIsVerified, AuthenticateApiToken
└── Models/
    └── Concerns/
        ├── HasRoles.php              # hasPermission(), hasRole(), permissionSlugs()
        └── RecordsActivity.php       # Audit log otomatis per event model

resources/js/
├── api.js                            # Axios + interceptor 419/401/500 global
├── auth.js                           # Auth store + guard helper
├── router.js                         # Route + beforeEach guard permission
├── components/                       # DataTable, modals, BaseInput/Button,
│                                     # ConfirmDialog, ToastStack
├── composables/                      # useToast, useTheme, useSwal
├── layouts/                          # AppLayout (sidebar+header), AuthLayout
└── pages/                            # Dashboard, Users, Roles, Permissions,
                                      # Profile, Settings, ApiTokens, ActivityLogs,
                                      # Trash, Login, Register, ForgotPassword,
                                      # ResetPassword, EmailVerify, ErrorPage
```

## 🔒 Keamanan yang Sudah Diterapkan

- **SQL Injection** — semua query via Eloquent/Query Builder (PDO prepared statements)
- **XSS** — Vue auto-escaping, tanpa `v-html` untuk konten user
- **CSRF** — token di semua request mutasi, axios interceptor otomatis
- **Brute force** — rate limit login 10/menit per IP, endpoint auth lain lebih ketat
- **Session fixation** — `session()->regenerate()` setelah login & ganti password
- **User enumeration** — pesan error generik, respons forgot-password selalu sama
- **Open redirect** — validasi query `redirect` di frontend
- **Timing attack (2FA)** — verifikasi TOTP konsisten
- **Password policy** — kompleksitas wajib + force change untuk akun buatan admin
- **API token** — disimpan hashed (SHA-256), expiry opsional

## 🧪 Testing

```bash
php vendor/bin/phpunit          # atau: php artisan test
```

Test memakai SQLite terpisah (`database/testing.sqlite`) sehingga data development aman.

## 🏗️ Menambahkan Fitur Baru (pola untuk project lanjutan)

1. Buat migration → model (+ `RecordsActivity` bila perlu audit log)
2. Buat Controller API + route di `routes/api.php` dalam grup `auth` + `permission:`
3. Tambah permission di `RbacSeeder` → jalankan `php artisan db:seed`
4. Buat halaman Vue di `resources/js/pages/` + daftar di `router.js` (`meta.permission`)
5. Tambah menu di `AppLayout.vue` (grup/caption/submenu)
6. Tulis test di `tests/Feature/`

## 📄 License

Proyek ini open-source di bawah [MIT License](LICENSE).
