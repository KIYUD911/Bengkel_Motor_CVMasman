# Product Requirement Document (PRD)
## Sistem Manajemen Bengkel Motor CVMasman

**Versi:** 1.0  
**Tanggal:** 20 Mei 2026  
**Status:** Draft  
**Author:** Development Team  

---

## 1. EXECUTIVE SUMMARY

Sistem Manajemen Bengkel Motor CVMasman adalah aplikasi web terintegrasi yang dirancang untuk mengotomatisasi dan mengoptimalkan semua proses operasional bengkel motor. Aplikasi ini mencakup manajemen pelanggan, inventori sparepart, transaksi servis, dan pelaporan komprehensif untuk meningkatkan efisiensi operasional dan kepuasan pelanggan.

---

## 2. OBJECTIVE (TUJUAN)

### 2.1 Tujuan Bisnis
- Meningkatkan efisiensi operasional bengkel motor hingga 40%
- Mengurangi waktu pemrosesan servis dan transaksi
- Meningkatkan kepuasan pelanggan melalui layanan yang lebih responsif
- Mengoptimalkan manajemen inventori sparepart
- Menghasilkan laporan bisnis yang akurat dan real-time

### 2.2 Tujuan Teknis
- Mengembangkan aplikasi web yang scalable dan maintainable
- Mengimplementasikan sistem keamanan yang robust
- Menyediakan antarmuka pengguna yang user-friendly
- Mengintegrasikan laporan dan export data otomatis
- Membuat audit trail untuk compliance dan transparansi

---

## 3. SCOPE (RUANG LINGKUP)

### 3.1 Dalam Scope
- Sistem autentikasi multi-role (Admin, Manager, Staff)
- Manajemen data pelanggan (CRM)
- Manajemen data kendaraan pelanggan
- Sistem transaksi servis (intake, diagnosis, eksekusi, pembayaran)
- Manajemen inventori sparepart dengan tracking stock
- Laporan penjualan, servis, dan inventori
- Sistem audit trail untuk semua transaksi
- Export data ke format Excel dan PDF
- Dashboard analytics dan insights
- Manajemen user dan permission

### 3.2 Luar Scope
- Integrasi dengan sistem pembayaran online (fase 2)
- Aplikasi mobile (fase 2)
- Integrasi dengan sistem asuransi otomotif
- Sistem booking online pelanggan (fase 2)
- Multi-branch management (versi 2.0)

---

## 4. TECHNOLOGY STACK

| Komponen | Teknologi | Versi |
|----------|-----------|-------|
| Backend Framework | Laravel | 12.x |
| Database | MySQL | 8.0+ |
| Frontend CSS | TailwindCSS | 3.x |
| UI Framework | Bootstrap | 5.x |
| PDF Generation | DomPDF | 2.x |
| Excel Export | Laravel Excel (Maatwebsite) | 3.x |
| Server | Apache/Nginx | - |
| PHP Version | PHP | 8.3+ |
| Version Control | Git | - |

---

## 5. BUSINESS RULES (ATURAN BISNIS)

### 5.1 Manajemen Pelanggan
- Setiap pelanggan harus memiliki nomor identitas (KTP/SIM) yang valid
- Pelanggan dapat memiliki multiple kendaraan
- Kontak pelanggan dapat diupdate kapan saja
- Pelanggan yang tidak memiliki transaksi 1 tahun dapat di-archive

### 5.2 Manajemen Kendaraan
- Satu kendaraan hanya dapat dimiliki oleh satu pelanggan
- Nomer polisi harus unik dalam sistem
- Wajib mencatat tipe/merk/tahun kendaraan
- Riwayat servis kendaraan harus tercatat

### 5.3 Transaksi Servis
- Servis hanya dapat dibuat oleh Staff atau Manager
- Estimasi biaya harus disetujui pelanggan sebelum dikerjakan
- Servis harus melalui workflow: Intake → Diagnosis → Eksekusi → QC → Pembayaran
- Servis yang belum selesai tidak boleh ditandai sebagai selesai
- Diskon hanya dapat diberikan oleh Manager atau Admin

### 5.4 Inventori Sparepart
- Stock minimum harus ditetapkan untuk setiap sparepart
- Pengurangan stock otomatis saat sparepart digunakan
- Alert dikirim saat stock mencapai minimum level
- Pembelian sparepart baru harus dicatat dengan detail supplier
- Sparepart yang sudah discontinued tidak boleh digunakan untuk servis baru

### 5.5 Pembayaran & Keuangan
- Pembayaran dapat dilakukan sebelum servis selesai (DP) atau sesudah
- Metode pembayaran: Tunai, Debit, Credit Card, Transfer Bank
- Bukti pembayaran harus tercetak/tersimpan digital
- Pengembalian uang hanya dapat dilakukan oleh Manager/Admin
- Laporan keuangan harus seimbang dengan bukti transaksi

### 5.6 Audit & Keamanan
- Semua perubahan data harus tercatat dalam audit trail
- User yang menghapus/mengubah data harus terekam
- Backup otomatis dilakukan setiap hari
- Password minimal 8 karakter dengan kombinasi uppercase, lowercase, number, special char
- Session timeout setelah 30 menit inaktivitas

---

## 6. USER ROLES & PERMISSIONS

### 6.1 Admin
**Deskripsi:** Full control terhadap sistem

| Feature | Permission |
|---------|-----------|
| User Management | Create, Read, Update, Delete |
| Role & Permission | Create, Read, Update, Delete |
| Settings | Create, Read, Update, Delete |
| All Module | Full Access |
| Audit Trail | Read |
| Reports | Access All |
| Backup & Restore | Execute |

### 6.2 Manager
**Deskripsi:** Mengelola operasional harian dan approval

| Feature | Permission |
|---------|-----------|
| Customer | Create, Read, Update |
| Vehicle | Create, Read, Update |
| Service Transaction | Create, Read, Update, Approve |
| Parts Inventory | Create, Read, Update, Manage Stock |
| Payment | Create, Read, Refund |
| Staff | Create, Read, Update (View Only) |
| Reports | Access Department Reports |
| Diskon/Promo | Approve |

### 6.3 Staff
**Deskripsi:** Menjalankan operasional servis harian

| Feature | Permission |
|---------|-----------|
| Customer | Create, Read, Update (Limited) |
| Vehicle | Read |
| Service Transaction | Create, Read, Update (Own Only) |
| Parts Inventory | Read, View Stock |
| Payment | Create, Read |
| Reports | Access Own Reports |
| Dashboard | View Only |

---

## 7. USER STORIES

### 7.1 Authentication Module
```
US-AUTH-001: Login User
Sebagai: Staff/Manager/Admin
Saya ingin: Login dengan username dan password
Agar: Saya dapat mengakses sistem

Kriteria Penerimaan:
- Login form tersedia di halaman utama
- Validasi username dan password
- Redirect ke dashboard setelah login berhasil
- Tampilkan error message jika login gagal
- Session timeout 30 menit
```

```
US-AUTH-002: Logout User
Sebagai: Staff/Manager/Admin
Saya ingin: Logout dari sistem
Agar: Akun saya aman

Kriteria Penerimaan:
- Tombol logout tersedia di menu utama
- Session dihapus setelah logout
- Redirect ke halaman login
```

```
US-AUTH-003: Change Password
Sebagai: Staff/Manager/Admin
Saya ingin: Mengubah password saya
Agar: Menjaga keamanan akun saya

Kriteria Penerimaan:
- User dapat mengubah password sendiri
- Validasi password lama harus benar
- Password baru min 8 karakter, kombinasi uppercase, lowercase, number, special char
- Konfirmasi password harus cocok
```

### 7.2 CRM Pelanggan
```
US-CRM-001: Tambah Pelanggan Baru
Sebagai: Staff/Manager
Saya ingin: Menambah data pelanggan baru
Agar: Data pelanggan tersimpan dalam sistem

Kriteria Penerimaan:
- Form input tersedia untuk nama, alamat, kontak, no identitas
- Validasi data required field
- No identitas harus unik
- Nomor pelanggan auto-generated
- Success notification setelah data disimpan
```

```
US-CRM-002: Lihat Daftar Pelanggan
Sebagai: Staff/Manager
Saya ingin: Melihat daftar semua pelanggan
Agar: Saya dapat mencari dan memilih pelanggan

Kriteria Penerimaan:
- Tampilkan tabel dengan kolom: No Pelanggan, Nama, Kontak, Alamat
- Implementasi pagination (default 10 per halaman)
- Sorting berdasarkan nama, no pelanggan
- Search functionality berdasarkan nama, kontak, alamat
- Able to filter berdasarkan status (aktif/inactive)
```

```
US-CRM-003: Edit Data Pelanggan
Sebagai: Manager/Admin
Saya ingin: Mengedit data pelanggan yang sudah ada
Agar: Data pelanggan tetap akurat dan update

Kriteria Penerimaan:
- Buka form edit dengan data existing
- Validasi semua field sesuai US-CRM-001
- Simpan perubahan dengan confirmation
- Audit trail mencatat perubahan data
```

```
US-CRM-004: Lihat Detail Pelanggan
Sebagai: Staff/Manager
Saya ingin: Melihat detail lengkap pelanggan
Agar: Saya tahu riwayat transaksi dan info lengkap

Kriteria Penerimaan:
- Tampilkan data pribadi pelanggan
- Tampilkan list kendaraan milik pelanggan
- Tampilkan riwayat servis 10 transaksi terakhir
- Tampilkan total pengeluaran pelanggan
- Tombol edit dan hapus
```

### 7.3 Manajemen Kendaraan
```
US-VEH-001: Tambah Kendaraan Pelanggan
Sebagai: Staff/Manager
Saya ingin: Menambah data kendaraan milik pelanggan
Agar: Riwayat servis kendaraan dapat ditrack

Kriteria Penerimaan:
- Form input untuk: Nomer Polisi, Merk, Tipe, Tahun, Warna, VIN
- Nomer polisi harus unik
- Validasi tahun kendaraan
- Link ke profil pelanggan
- Auto-generate registration number
```

```
US-VEH-002: Lihat Riwayat Servis Kendaraan
Sebagai: Staff/Manager
Saya ingin: Melihat semua servis yang pernah dilakukan pada kendaraan
Agar: Saya dapat membuat diagnosis yang tepat

Kriteria Penerimaan:
- Tampilkan timeline servis kendaraan
- Informasi: tanggal, jenis servis, parts yang digunakan, biaya
- Dapat filter berdasarkan range tanggal
- Export ke PDF
```

### 7.4 Transaksi Servis
```
US-SRV-001: Buat Transaksi Servis Baru
Sebagai: Staff/Manager
Saya ingin: Membuat transaksi servis baru
Agar: Servis pelanggan dapat dicatat

Kriteria Penerimaan:
- Form untuk memilih pelanggan dan kendaraan
- Form untuk deskripsi keluhan/diagnosa
- Form untuk memilih jenis servis dan parts yang digunakan
- Auto-calculate estimasi biaya berdasarkan parts dan labor
- Generate invoice/WO number
- Status: Intake
```

```
US-SRV-002: Lihat Daftar Servis
Sebagai: Staff/Manager
Saya ingin: Melihat semua servis yang sedang berjalan dan selesai
Agar: Saya dapat track progress servis

Kriteria Penerimaan:
- Tampilkan tabel dengan kolom: WO#, Pelanggan, Kendaraan, Status, Tgl Masuk, Estimasi Selesai
- Filter berdasarkan status (All, Intake, Diagnosis, Proses, QC, Selesai)
- Filter berdasarkan tanggal
- Sorting berdasarkan kolom
- Quick action: View, Edit, Update Status
```

```
US-SRV-003: Update Status Servis
Sebagai: Staff/Manager
Saya ingin: Mengupdate status servis
Agar: Progress servis dapat dimonitor

Kriteria Penerimaan:
- Workflow: Intake → Diagnosis → Eksekusi → QC → Selesai
- Hanya dapat update ke status berikutnya
- Bisa add notes/comments untuk setiap status update
- Capture foto sebelum/sesudah pekerjaan (optional)
- Notifikasi ke pelanggan saat status update
```

```
US-SRV-004: Lihat Detail Servis
Sebagai: Staff/Manager
Saya ingin: Melihat detail lengkap servis
Agar: Saya tahu apa yang sudah dikerjakan

Kriteria Penerimaan:
- Tampilkan semua info: WO, pelanggan, kendaraan, keluhan
- Tampilkan parts yang digunakan dengan harga
- Tampilkan labor cost
- Tampilkan total estimasi vs actual cost
- Timeline status updates
- Invoice/nota pembayaran
```

```
US-SRV-005: Approve Estimasi Biaya
Sebagai: Manager/Admin
Saya ingin: Approve atau reject estimasi biaya dari staff
Agar: Servis baru dapat dimulai dengan biaya yang sudah disepakati

Kriteria Penerimaan:
- Manager dapat melihat list servis pending approval
- Manager dapat approve dengan opsi add discount
- Manager dapat reject dan request revision
- Notifikasi ke pelanggan setelah approval
- Servis tidak bisa dilanjutkan sampai approved
```

### 7.5 Inventori Sparepart
```
US-INV-001: Tambah Sparepart Baru
Sebagai: Manager
Saya ingin: Menambah sparepart baru ke inventori
Agar: Stock parts dapat dikelola

Kriteria Penerimaan:
- Form untuk: Nama parts, Merk, Harga beli, Harga jual, Supplier, Stock minimum
- Part number auto-generated
- Validasi harga dan stock minimum
- Kategori parts (engine, electrical, brake, dll)
- Status active/inactive
```

```
US-INV-002: Lihat Daftar Sparepart
Sebagai: Staff/Manager
Saya ingin: Melihat semua sparepart yang tersedia
Agar: Saya dapat memilih parts untuk servis

Kriteria Penerimaan:
- Tampilkan tabel: Part#, Nama, Merk, Stock, Harga Jual, Kategori
- Filter berdasarkan kategori
- Filter berdasarkan stock (Low Stock, Available)
- Search berdasarkan nama, merk, part number
- Sorting berdasarkan kolom
- Highlight parts yang stock < minimum
```

```
US-INV-003: Update Stock Sparepart
Sebagai: Manager
Saya ingin: Update stock sparepart (masuk/keluar)
Agar: Stock selalu akurat

Kriteria Penerimaan:
- Tipe transaksi: IN (pembelian), OUT (digunakan), ADJUST (koreksi)
- Form untuk: Part, Jumlah, Tipe, Catatan, Supplier/Sumber
- Auto-decrease stock saat parts digunakan di servis
- Audit trail untuk semua stock movement
- Notifikasi jika stock < minimum
```

```
US-INV-004: Laporan Inventori
Sebagai: Manager/Admin
Saya ingin: Melihat laporan inventori sparepart
Agar: Saya dapat membuat keputusan pembelian

Kriteria Penerimaan:
- Laporan current stock vs minimum stock
- Laporan usage per bulan
- Laporan value of inventory (qty x harga beli)
- Laporan slow-moving items
- Export ke Excel dan PDF
```

### 7.6 Audit Trail
```
US-AUD-001: Lihat Audit Log
Sebagai: Admin
Saya ingin: Melihat semua aktivitas dalam sistem
Agar: Saya dapat monitor dan track perubahan data

Kriteria Penerimaan:
- Tampilkan log dengan kolom: Waktu, User, Action, Module, Data Before/After
- Filter berdasarkan module, user, action type
- Filter berdasarkan date range
- Search berdasarkan keyword
- Export ke Excel
- Log retention minimal 1 tahun
```

### 7.7 Reporting & Laporan
```
US-REP-001: Laporan Transaksi Servis
Sebagai: Manager/Admin
Saya ingin: Membuat laporan transaksi servis
Agar: Saya dapat analisa performa bisnis

Kriteria Penerimaan:
- Filter berdasarkan date range, staff, status
- Metrics: Jumlah servis, revenue, average cost, completion rate
- Detail: WO#, Pelanggan, Kendaraan, Biaya, Tanggal
- Export ke PDF dan Excel
- Visualisasi chart
```

```
US-REP-002: Laporan Inventori & Cost
Sebagai: Manager/Admin
Saya ingin: Laporan inventori dan biaya parts
Agar: Saya bisa manage cost dan procurement

Kriteria Penerimaan:
- Current inventory value
- Inventory movement report
- Most used parts
- Supplier performance
- Forecast untuk pembelian
- Export ke PDF dan Excel
```

```
US-REP-003: Dashboard Analytics
Sebagai: Manager/Admin
Saya ingin: Melihat dashboard dengan KPI utama
Agar: Saya dapat quick overview bisnis

Kriteria Penerimaan:
- KPI Cards: Total Revenue, Total Servis, Active Customers, Low Stock Items
- Chart: Revenue trend, Servis completion, Top customers, Parts usage
- Filter berdasarkan date range
- Responsive untuk mobile dan tablet
```

---

## 8. FUNCTIONAL REQUIREMENTS

### 8.1 Authentication & Authorization
| ID | Requirement | Priority |
|----|-------------|----------|
| FR-AUTH-001 | Sistem login dengan username dan password | HIGH |
| FR-AUTH-002 | Password hashing dan encryption | HIGH |
| FR-AUTH-003 | Role-based access control (RBAC) | HIGH |
| FR-AUTH-004 | Permission management per role | HIGH |
| FR-AUTH-005 | Logout dan session termination | HIGH |
| FR-AUTH-006 | Password reset functionality | MEDIUM |
| FR-AUTH-007 | Change password user | MEDIUM |
| FR-AUTH-008 | Session timeout dan automatic logout | HIGH |

### 8.2 Customer Management (CRM)
| ID | Requirement | Priority |
|----|-------------|----------|
| FR-CRM-001 | Create new customer dengan validation | HIGH |
| FR-CRM-002 | Read/View customer data dan history | HIGH |
| FR-CRM-003 | Update customer information | HIGH |
| FR-CRM-004 | Search customer by name, phone, address | HIGH |
| FR-CRM-005 | Customer categorization (regular, VIP, etc) | MEDIUM |
| FR-CRM-006 | Customer contact history tracking | MEDIUM |
| FR-CRM-007 | Archive inactive customers | LOW |
| FR-CRM-008 | Bulk import customer dari CSV | LOW |

### 8.3 Vehicle Management
| ID | Requirement | Priority |
|----|-------------|----------|
| FR-VEH-001 | Add vehicle untuk customer | HIGH |
| FR-VEH-002 | View vehicle list dan details | HIGH |
| FR-VEH-003 | Update vehicle information | MEDIUM |
| FR-VEH-004 | Service history tracking per vehicle | HIGH |
| FR-VEH-005 | Maintenance schedule reminder | MEDIUM |
| FR-VEH-006 | Vehicle document management (STNK, asuransi) | LOW |

### 8.4 Service Transaction
| ID | Requirement | Priority |
|----|-------------|----------|
| FR-SRV-001 | Create service order (WO) dengan workflow | HIGH |
| FR-SRV-002 | Service status workflow management | HIGH |
| FR-SRV-003 | Parts selection dan auto-calculation | HIGH |
| FR-SRV-004 | Labor cost management | HIGH |
| FR-SRV-005 | Discount management dan approval | MEDIUM |
| FR-SRV-006 | Service estimation dan approval | HIGH |
| FR-SRV-007 | Service completion dan QC | HIGH |
| FR-SRV-008 | Work notes dan photo attachment | MEDIUM |
| FR-SRV-009 | Invoice generation dan printing | HIGH |
| FR-SRV-010 | Payment processing dan tracking | HIGH |

### 8.5 Spare Parts Inventory
| ID | Requirement | Priority |
|----|-------------|----------|
| FR-INV-001 | Add spare parts dengan categorization | HIGH |
| FR-INV-002 | Stock management (IN/OUT/ADJUST) | HIGH |
| FR-INV-003 | Minimum stock alert system | HIGH |
| FR-INV-004 | Supplier management dan tracking | MEDIUM |
| FR-INV-005 | Parts history dan usage tracking | MEDIUM |
| FR-INV-006 | Stock opname functionality | MEDIUM |
| FR-INV-007 | Automatic stock reduction saat servis | HIGH |
| FR-INV-008 | Inventory valuation report | MEDIUM |

### 8.6 Reporting & Analytics
| ID | Requirement | Priority |
|----|-------------|----------|
| FR-REP-001 | Service transaction report | HIGH |
| FR-REP-002 | Revenue report | HIGH |
| FR-REP-003 | Inventory report | HIGH |
| FR-REP-004 | Customer report | MEDIUM |
| FR-REP-005 | Staff performance report | MEDIUM |
| FR-REP-006 | Dashboard analytics dengan chart | MEDIUM |
| FR-REP-007 | Export report ke PDF dan Excel | HIGH |
| FR-REP-008 | Scheduled report generation | LOW |

### 8.7 Audit & Security
| ID | Requirement | Priority |
|----|-------------|----------|
| FR-AUD-001 | Audit trail logging semua transaksi | HIGH |
| FR-AUD-002 | User action tracking | HIGH |
| FR-AUD-003 | Data change history | HIGH |
| FR-AUD-004 | System backup functionality | HIGH |
| FR-AUD-005 | User activity monitoring | MEDIUM |

### 8.8 User & System Management
| ID | Requirement | Priority |
|----|-------------|----------|
| FR-USR-001 | Create user account dengan role | HIGH |
| FR-USR-002 | Edit user data dan permissions | HIGH |
| FR-USR-003 | Deactivate/delete user | MEDIUM |
| FR-USR-004 | Role management | HIGH |
| FR-USR-005 | System settings management | MEDIUM |
| FR-USR-006 | Database backup dan restore | HIGH |

---

## 9. NON-FUNCTIONAL REQUIREMENTS

### 9.1 Performance
| ID | Requirement | Target |
|----|-------------|--------|
| NFR-PER-001 | Page load time | < 3 detik |
| NFR-PER-002 | Response time API | < 500 ms |
| NFR-PER-003 | Database query optimization | < 200 ms |
| NFR-PER-004 | Support concurrent users | Minimum 50 users |
| NFR-PER-005 | Report generation time | < 10 detik |

### 9.2 Scalability
| ID | Requirement | Target |
|----|-------------|--------|
| NFR-SCA-001 | Database size support | Up to 100GB |
| NFR-SCA-002 | Annual data growth | 20% per tahun |
| NFR-SCA-003 | Storage capacity | Scalable cloud storage |
| NFR-SCA-004 | Transaction volume | Minimum 1000 transaksi/hari |

### 9.3 Availability & Reliability
| ID | Requirement | Target |
|----|-------------|--------|
| NFR-REL-001 | System uptime | 99.5% per tahun |
| NFR-REL-002 | Backup frequency | Daily at 2 AM |
| NFR-REL-003 | Recovery Time Objective (RTO) | 4 jam |
| NFR-REL-004 | Recovery Point Objective (RPO) | 1 hari |
| NFR-REL-005 | Data integrity | 100% consistency |

### 9.4 Security
| ID | Requirement | Detail |
|----|-------------|--------|
| NFR-SEC-001 | Data encryption | SSL/TLS untuk data in transit |
| NFR-SEC-002 | Password policy | Min 8 char, uppercase, lowercase, number, special |
| NFR-SEC-003 | Session security | Timeout 30 menit, secure cookies |
| NFR-SEC-004 | Input validation | All user inputs validated & sanitized |
| NFR-SEC-005 | SQL injection prevention | Prepared statements & ORM |
| NFR-SEC-006 | XSS prevention | HTML escaping & CSP headers |
| NFR-SEC-007 | CSRF protection | Token-based CSRF protection |
| NFR-SEC-008 | Access control | Role-based permission enforcement |
| NFR-SEC-009 | Audit logging | All actions logged untuk compliance |
| NFR-SEC-010 | Data backup encryption | Encrypted backups |

### 9.5 Usability
| ID | Requirement | Detail |
|----|-------------|--------|
| NFR-USA-001 | User interface | Clean, intuitive, professional design |
| NFR-USA-002 | Navigation | Logical menu structure & breadcrumbs |
| NFR-USA-003 | Responsiveness | Mobile-friendly design (Bootstrap) |
| NFR-USA-004 | Accessibility | WCAG 2.1 Level AA compliant |
| NFR-USA-005 | Error messages | Clear, actionable error messages |
| NFR-USA-006 | Help & documentation | In-app help & user guide |

### 9.6 Maintainability
| ID | Requirement | Detail |
|----|-------------|--------|
| NFR-MAINT-001 | Code documentation | Comprehensive inline comments |
| NFR-MAINT-002 | API documentation | Swagger/OpenAPI documentation |
| NFR-MAINT-003 | Version control | Git dengan branch strategy |
| NFR-MAINT-004 | Code quality | Unit tests, minimum 70% code coverage |
| NFR-MAINT-005 | Deployment | Automated deployment pipeline |
| NFR-MAINT-006 | Logging | Structured logging untuk debugging |

### 9.7 Compatibility
| ID | Requirement | Detail |
|----|-------------|--------|
| NFR-COMP-001 | Browser support | Chrome, Firefox, Safari, Edge (latest 2 versions) |
| NFR-COMP-002 | Operating system | Windows, MacOS, Linux |
| NFR-COMP-003 | Database | MySQL 8.0+ |
| NFR-COMP-004 | PHP version | PHP 8.3+ |

---

## 10. ACCEPTANCE CRITERIA

### 10.1 General Acceptance Criteria
- [ ] Semua functional requirements ter-implementasi
- [ ] Semua non-functional requirements terpenuhi
- [ ] Minimum 70% code coverage dengan unit tests
- [ ] Semua user stories memiliki test cases yang pass
- [ ] Security audit selesai dengan no critical vulnerabilities
- [ ] Performance testing menunjukkan semua metrics terpenuhi
- [ ] User acceptance testing (UAT) passed
- [ ] Dokumentasi lengkap dan update
- [ ] Deployment guide dan runbook ready
- [ ] User training selesai

### 10.2 Feature-Specific Acceptance Criteria

#### Authentication Module
- [ ] Login form validated dan secure
- [ ] Password strength enforcement
- [ ] Session management working correctly
- [ ] Role-based access control enforced
- [ ] Audit log mencatat semua login/logout

#### CRM Module
- [ ] Customer data validation working
- [ ] Search dan filter functionality
- [ ] Customer history tracking
- [ ] Bulk import dari CSV
- [ ] Data duplication prevention

#### Vehicle Module
- [ ] Vehicle registration valid
- [ ] Service history accurately tracked
- [ ] Multiple vehicles per customer supported
- [ ] Export vehicle data to PDF

#### Service Transaction
- [ ] Workflow progression working
- [ ] Cost calculation accurate
- [ ] Invoice generation correct
- [ ] Status notification to customer
- [ ] Service completion validation

#### Inventory Management
- [ ] Stock tracking accurate
- [ ] Automatic stock reduction
- [ ] Minimum stock alerts
- [ ] Supplier data management
- [ ] Inventory valuation correct

#### Reporting
- [ ] All reports generated correctly
- [ ] Export to Excel dan PDF working
- [ ] Dashboard performance acceptable
- [ ] Chart visualization correct
- [ ] Report scheduling working

#### Audit Trail
- [ ] All actions logged
- [ ] Timestamps accurate
- [ ] User identification correct
- [ ] Data change tracking complete
- [ ] Log retention policy enforced

---

## 11. WORKFLOW BISNIS (BUSINESS PROCESS)

### 11.1 Service Workflow
```
┌─────────────────────────────────────────────────────────────────┐
│                      SERVICE INTAKE PROCESS                      │
└─────────────────────────────────────────────────────────────────┘

1. INTAKE (Penerimaan Kendaraan)
   ├─ Staff mencatat pelanggan dan kendaraan
   ├─ Staff melakukan inspeksi awal
   ├─ Staff mencatat keluhan pelanggan
   ├─ Generate WO (Work Order) number
   ├─ Status: INTAKE
   └─ Create service record

2. DIAGNOSIS (Diagnosis & Estimasi)
   ├─ Mekanik melakukan diagnosa
   ├─ Identifikasi parts yang diperlukan
   ├─ Calculate labor hours
   ├─ Generate cost estimation
   ├─ Manager review & approve
   ├─ Status: DIAGNOSIS
   └─ Notification ke pelanggan untuk approval

3. APPROVAL PELANGGAN
   ├─ Pelanggan dihubungi untuk approval
   ├─ Jika disetujui, proceed ke execution
   ├─ Jika ditolak, service dihentikan
   └─ Create invoice estimation

4. EXECUTION (Pengerjaan Servis)
   ├─ Mekanik mulai mengerjakan
   ├─ System auto-reduce parts stock
   ├─ Record labour time
   ├─ Update progress notes
   ├─ Status: EXECUTION
   └─ Take before/after photos

5. QUALITY CONTROL (QC)
   ├─ Supervisor check quality
   ├─ Verify all works completed
   ├─ Test service functionality
   ├─ Status: QC
   └─ Sign off QC checklist

6. FINALIZATION (Finalisasi)
   ├─ Generate final invoice
   ├─ Calculate actual vs estimated cost
   ├─ Status: SELESAI
   ├─ Notify customer untuk pickup
   └─ Create payment record

7. PAYMENT (Pembayaran)
   ├─ Staff menerima pembayaran
   ├─ Record payment method & date
   ├─ Generate receipt
   ├─ Status: PAID
   └─ Customer pickup kendaraan
```

### 11.2 Inventory Management Workflow
```
┌─────────────────────────────────────────────────────────────────┐
│                   INVENTORY MANAGEMENT PROCESS                   │
└─────────────────────────────────────────────────────────────────┘

A. PROCUREMENT (Pembelian Parts)
   ├─ Manager monitor stock levels
   ├─ Check minimum stock threshold
   ├─ Create PO (Purchase Order)
   ├─ Send to supplier
   ├─ Receive parts & verify
   ├─ Record stock IN dengan harga
   └─ Update inventory system

B. STOCK MOVEMENT (Pergerakan Stock)
   ├─ Mekanik gunakan parts untuk servis
   ├─ System auto-reduce stock
   ├─ Record parts in service record
   ├─ Update qty dan cost
   └─ Create stock history

C. STOCK ADJUSTMENT (Koreksi Stock)
   ├─ Manager melakukan stock opname
   ├─ Compare physical vs system
   ├─ Adjust jika ada perbedaan
   ├─ Record adjustment reason
   ├─ Create adjustment journal
   └─ Audit trail mencatat perubahan

D. STOCK ALERT (Alert System)
   ├─ Monitor stock levels
   ├─ Alert jika stock < minimum
   ├─ Notify manager untuk order
   └─ Report slow-moving items
```

### 11.3 Reporting & Analytics Workflow
```
┌─────────────────────────────────────────────────────────────────┐
│                  REPORTING & ANALYTICS PROCESS                   │
└─────────────────────────────────────────────────────────────────┘

DAILY
├─ Sales summary
├─ Service completion status
├─ Low stock alerts
└─ Critical issues report

WEEKLY
├─ Sales vs target
├─ Staff performance
├─ Top customers
└─ Parts usage analysis

MONTHLY
├─ Revenue report
├─ Inventory valuation
├─ Customer acquisition
├─ Expense vs budget
└─ KPI analysis

AS NEEDED
├─ Custom reports
├─ Ad-hoc queries
├─ Customer statements
└─ Export untuk aksounting
```

---

## 12. RISK ANALYSIS & MITIGATION

### 12.1 Technical Risks

| ID | Risk | Impact | Probability | Mitigation |
|----|------|--------|-------------|-----------|
| TR-001 | Database performance degradation | HIGH | MEDIUM | Database optimization, index management, monitoring |
| TR-002 | Server downtime | HIGH | LOW | Redundant servers, load balancing, monitoring |
| TR-003 | Security breach/data loss | CRITICAL | MEDIUM | SSL/TLS, encryption, backup, security audit |
| TR-004 | Scalability issues saat traffic tinggi | MEDIUM | MEDIUM | Load testing, caching, CDN, auto-scaling |
| TR-005 | Integration issues dengan external systems | MEDIUM | LOW | API testing, error handling, fallback mechanisms |
| TR-006 | Laravel/Dependencies vulnerability | MEDIUM | HIGH | Regular updates, security patches, monitoring |

### 12.2 Operational Risks

| ID | Risk | Impact | Probability | Mitigation |
|----|------|--------|-------------|-----------|
| OR-001 | User error in data entry | MEDIUM | HIGH | Validation, confirmation dialogs, training |
| OR-002 | Unauthorized access | HIGH | MEDIUM | RBAC, audit trail, access control |
| OR-003 | Data inconsistency | MEDIUM | MEDIUM | Transaction management, validation, backup |
| OR-004 | Staff tidak terlatih | MEDIUM | MEDIUM | Comprehensive training, documentation, support |
| OR-005 | Resistance to change | MEDIUM | MEDIUM | Change management, stakeholder engagement |

### 12.3 Business Risks

| ID | Risk | Impact | Probability | Mitigation |
|----|------|--------|-------------|-----------|
| BR-001 | Project delay | HIGH | MEDIUM | Agile methodology, risk monitoring, timeline buffer |
| BR-002 | Budget overrun | MEDIUM | MEDIUM | Budget tracking, scope management, cost control |
| BR-003 | Poor system adoption | MEDIUM | MEDIUM | User involvement, training, support |
| BR-004 | Business process disruption | HIGH | LOW | Phased rollout, parallel running, rollback plan |
| BR-005 | Vendor lock-in | MEDIUM | LOW | Open standards, vendor diversity, data portability |

### 12.4 Security Risks

| ID | Risk | Impact | Probability | Mitigation |
|----|------|--------|-------------|-----------|
| SR-001 | SQL Injection attack | CRITICAL | MEDIUM | Prepared statements, ORM, input validation |
| SR-002 | XSS attack | HIGH | MEDIUM | HTML escaping, CSP headers, sanitization |
| SR-003 | CSRF attack | HIGH | MEDIUM | CSRF token, SameSite cookies |
| SR-004 | DDoS attack | MEDIUM | LOW | Rate limiting, WAF, CDN protection |
| SR-005 | Password compromise | HIGH | MEDIUM | Strong policy, 2FA, password reset, monitoring |
| SR-006 | Insider threat | MEDIUM | LOW | Audit trail, access control, background check |

### 12.5 Risk Response Strategy

**Prevention:**
- Implement comprehensive testing (unit, integration, security)
- Regular code reviews dan security audits
- Strong project management dan monitoring
- User training dan documentation

**Detection:**
- System monitoring dan alerting
- Audit trail logging
- User activity monitoring
- Regular security assessment

**Mitigation:**
- Fallback & rollback procedures
- Backup & disaster recovery
- Change management process
- Incident response plan

**Contingency:**
- Emergency support team
- Temporary manual workarounds
- Data recovery procedures
- Alternative solutions

---

## 13. PROJECT TIMELINE (ESTIMATION)

### Phase 1: Project Setup & Infrastructure (Week 1-2)
- [ ] Environment setup (Dev, Staging, Production)
- [ ] Database design & schema
- [ ] Project structure & boilerplate
- [ ] CI/CD pipeline setup
- [ ] Documentation template

### Phase 2: Core Features Development (Week 3-8)
- [ ] Authentication & Authorization system
- [ ] Customer Management (CRM)
- [ ] Vehicle Management
- [ ] Service Transaction module
- [ ] Parts Inventory module

### Phase 3: Advanced Features (Week 9-12)
- [ ] Reporting & Analytics
- [ ] Audit Trail system
- [ ] Dashboard & visualization
- [ ] PDF & Excel export
- [ ] Email notification system

### Phase 4: Testing & QA (Week 13-14)
- [ ] Unit testing
- [ ] Integration testing
- [ ] Performance testing
- [ ] Security testing
- [ ] UAT dengan client

### Phase 5: Deployment & Support (Week 15-16)
- [ ] Production deployment
- [ ] User training
- [ ] Documentation finalization
- [ ] Go-live support
- [ ] Monitoring & optimization

---

## 14. SUCCESS METRICS & KPI

### 14.1 User Adoption
- Minimum 80% user adoption within 3 months
- Average daily active users: 100%
- System usage: 4+ hours per day
- User satisfaction score: >= 4/5

### 14.2 Business Impact
- 30% reduction in service processing time
- 25% improvement in inventory accuracy
- 40% reduction in administrative time
- 20% increase in service capacity
- 95% payment accuracy

### 14.3 System Performance
- System uptime: 99.5%
- Page load time: < 3 seconds
- API response time: < 500ms
- Data backup: Daily successful
- Zero critical security incidents

### 14.4 Data Quality
- 99% data accuracy rate
- 100% audit trail completeness
- Zero untracked transactions
- 100% payment reconciliation

---

## 15. ASSUMPTIONS & CONSTRAINTS

### 15.1 Assumptions
- Client akan menyediakan requirements yang lengkap
- Client akan melakukan timely approval
- Staff akan mendapat training yang memadai
- Internet connectivity stabil
- Hosting infrastructure tersedia dan reliable
- Database backup procedures diikuti
- Users akan mematuhi security policies

### 15.2 Constraints
- Budget: [Defined by stakeholder]
- Timeline: 16 minggu development
- Team size: [Defined by organization]
- Server capacity: Scalable on demand
- Compliance: Standard IT security practices
- Browser support: Latest 2 versions
- MySQL version: 8.0 or newer
- PHP version: 8.3 or newer

---

## 16. APPENDICES

### Appendix A: Glossary
- **WO**: Work Order - Nomor identifikasi untuk setiap servis
- **SKU**: Stock Keeping Unit - Kode identifikasi untuk parts
- **RTO**: Recovery Time Objective - Target waktu recovery
- **RPO**: Recovery Point Objective - Target data loss tolerance
- **RBAC**: Role-Based Access Control
- **WCAG**: Web Content Accessibility Guidelines
- **UAT**: User Acceptance Testing
- **QC**: Quality Control

### Appendix B: Reference Documents
- Database Schema Diagram
- System Architecture Diagram
- User Flow Diagrams
- API Documentation
- Security Requirements
- Deployment Checklist

### Appendix C: Related Documents
- Technical Specification Document (TSD)
- System Architecture Design (SAD)
- Database Design Document (DDD)
- API Specification
- Test Plan & Test Cases
- User Manual
- Administrator Guide
- Security Policy

---

## DOCUMENT HISTORY

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 20-May-2026 | Dev Team | Initial PRD draft |
| | | | |

---

## SIGN-OFF

| Role | Name | Signature | Date |
|------|------|-----------|------|
| Product Owner | [PO Name] | ________ | _____ |
| Project Manager | [PM Name] | ________ | _____ |
| Technical Lead | [TL Name] | ________ | _____ |
| Stakeholder | [SH Name] | ________ | _____ |

---

**END OF DOCUMENT**

*This PRD is a living document and subject to change based on stakeholder feedback and project requirements.*
