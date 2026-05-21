# Database Design - Sistem Manajemen Bengkel Motor
## MySQL 8.0+ Architecture & Schema

**Versi:** 1.0  
**Tanggal:** 21 Mei 2026  
**Database:** MySQL 8.0+  
**Normalisasi:** 3NF (Third Normal Form)

---

## 1. ENTITY RELATIONSHIP DIAGRAM (ERD)

### 1.1 Visual ERD

```
┌─────────────────┐
│     roles       │
├─────────────────┤
│ id (PK)         │
│ name (UNIQUE)   │
│ description     │
│ created_at      │
└────────┬────────┘
         │ 1:M
         │
┌────────▼───────────┐          ┌──────────────────┐
│      users         │ 1:M      │    customers     │
├────────────────────┤────────→ ├──────────────────┤
│ id (PK)            │          │ id (PK)          │
│ uuid (UNIQUE)      │          │ uuid (UNIQUE)    │
│ name               │          │ customer_code    │
│ email (UNIQUE)     │          │ name             │
│ password           │          │ phone            │
│ role_id (FK)       │          │ email            │
│ is_active         │          │ address          │
│ created_by        │          │ created_by (FK)  │
│ created_at        │          │ created_at       │
│ updated_at        │          │ deleted_at       │
│ deleted_at        │          └────────┬─────────┘
└─────────────────────┘                  │
                                         │ 1:M
                                         │
                         ┌───────────────▼──────────────┐
                         │       vehicles               │
                         ├──────────────────────────────┤
                         │ id (PK)                      │
                         │ uuid (UNIQUE)                │
                         │ customer_id (FK)             │
                         │ license_plate (UNIQUE)       │
                         │ vehicle_brand                │
                         │ vehicle_type                 │
                         │ year                         │
                         │ color                        │
                         │ vin (UNIQUE)                 │
                         │ notes                        │
                         │ created_at                   │
                         │ deleted_at                   │
                         └───────────┬──────────────────┘
                                     │ 1:M
                                     │
                         ┌───────────▼──────────────┐
                         │   service_orders         │
                         ├──────────────────────────┤
                         │ id (PK)                  │
                         │ uuid (UNIQUE)            │
                         │ work_order_number        │
                         │ vehicle_id (FK)          │
                         │ customer_id (FK)         │
                         │ description              │
                         │ status                   │
                         │ priority                 │
                         │ estimated_cost           │
                         │ actual_cost              │
                         │ created_by (FK)          │
                         │ completed_by (FK)        │
                         │ completed_at             │
                         │ created_at               │
                         │ deleted_at               │
                         └───────────┬──────────────┘
                                     │ 1:M
                                     │
                         ┌───────────▼──────────────────┐
                         │ service_order_details        │
                         ├──────────────────────────────┤
                         │ id (PK)                      │
                         │ service_order_id (FK)        │
                         │ product_id (FK)              │
                         │ quantity                     │
                         │ unit_price                   │
                         │ subtotal                     │
                         │ notes                        │
                         │ created_at                   │
                         └──────────────────────────────┘
                                     △
                                     │
                         ┌───────────┴──────────────┐
                         │      products            │
                         ├──────────────────────────┤
                         │ id (PK)                  │
                         │ uuid (UNIQUE)            │
                         │ product_code (UNIQUE)    │
                         │ name                     │
                         │ category                 │
                         │ description              │
                         │ cost_price               │
                         │ selling_price            │
                         │ unit                     │
                         │ created_at               │
                         │ deleted_at               │
                         └──────────────────────────┘
                                     △
                                     │
┌────────────────────┐   ┌──────────┴──────────┐
│    inventory       │   │   customer_inter    │
├────────────────────┤   │    actions          │
│ id (PK)            │   ├─────────────────────┤
│ product_id (FK)    │   │ id (PK)             │
│ warehouse_location │   │ uuid (UNIQUE)       │
│ quantity_on_hand   │   │ customer_id (FK)    │
│ quantity_reserved  │   │ user_id (FK)        │
│ quantity_available │   │ interaction_type    │
│ reorder_level      │   │ description         │
│ last_updated_at    │   │ notes               │
│ updated_by (FK)    │   │ created_at          │
└────────────────────┘   │ deleted_at          │
                         └─────────────────────┘

┌────────────────────────────────────────┐
│        audit_logs                      │
├────────────────────────────────────────┤
│ id (PK)                                │
│ user_id (FK, nullable)                 │
│ table_name                             │
│ record_id                              │
│ action (CREATE/UPDATE/DELETE)          │
│ old_values (JSON)                      │
│ new_values (JSON)                      │
│ ip_address                             │
│ user_agent                             │
│ created_at                             │
└────────────────────────────────────────┘
```

---

## 2. TABEL DEFINITIONS & RELASI

### 2.1 TABEL: roles

```
Tujuan: Menyimpan role/permission groups
Relasi: 1 role : M users

Kolom:
- id (PK) → Unsigned Big Int, auto increment
- name (UNIQUE) → Varchar 50, unique, not null
- description → Text, nullable
- created_at → Timestamp
- updated_at → Timestamp

Indexing:
- Primary Key: id
- Unique Index: name
```

### 2.2 TABEL: users

```
Tujuan: Menyimpan data pengguna sistem
Relasi: 
  - M:1 ke roles (role_id)
  - 1:M ke customers (created_by)
  - 1:M ke service_orders (created_by, completed_by)

Kolom:
- id (PK) → Unsigned Big Int
- uuid (UNIQUE) → Char 36, unique, not null
- name → Varchar 100, not null
- email (UNIQUE) → Varchar 100, unique, not null
- password → Varchar 255, not null
- role_id (FK) → Unsigned Big Int, not null
- is_active → Boolean, default true
- last_login_at → Timestamp, nullable
- created_by → Unsigned Big Int, nullable (self-reference)
- created_at → Timestamp
- updated_at → Timestamp
- deleted_at → Timestamp, nullable (soft delete)

Indexing:
- Primary Key: id
- Unique Index: uuid
- Unique Index: email
- Index: role_id (Foreign Key)
- Index: created_by (Foreign Key)
- Index: (deleted_at, created_at) untuk soft delete queries
- Index: is_active untuk filter user aktif
```

### 2.3 TABEL: customers

```
Tujuan: Menyimpan data pelanggan bengkel
Relasi:
  - M:1 ke users (created_by)
  - 1:M ke vehicles (customer_id)
  - 1:M ke service_orders (customer_id)
  - 1:M ke customer_interactions (customer_id)

Kolom:
- id (PK) → Unsigned Big Int
- uuid (UNIQUE) → Char 36, unique, not null
- customer_code (UNIQUE) → Varchar 50, unique, not null
- name → Varchar 100, not null
- phone → Varchar 20, nullable
- email → Varchar 100, nullable, unique
- address → Text, nullable
- city → Varchar 50, nullable
- province → Varchar 50, nullable
- postal_code → Varchar 10, nullable
- identity_type → Enum('KTP', 'SIM', 'PASSPORT'), nullable
- identity_number → Varchar 50, nullable, unique
- customer_type → Enum('regular', 'vip', 'corporate'), default 'regular'
- preferred_contact_method → Enum('phone', 'email', 'whatsapp'), nullable
- notes → Text, nullable
- created_by (FK) → Unsigned Big Int, nullable
- created_at → Timestamp
- updated_at → Timestamp
- deleted_at → Timestamp, nullable (soft delete)

Indexing:
- Primary Key: id
- Unique Index: uuid
- Unique Index: customer_code
- Unique Index: email (nullable)
- Unique Index: identity_number (nullable)
- Index: phone
- Index: created_by (Foreign Key)
- Index: customer_type
- Index: (deleted_at, created_at)
- Full Text Index: name, phone, email untuk search
```

### 2.4 TABEL: vehicles

```
Tujuan: Menyimpan data kendaraan pelanggan
Relasi:
  - M:1 ke customers (customer_id)
  - 1:M ke service_orders (vehicle_id)

Kolom:
- id (PK) → Unsigned Big Int
- uuid (UNIQUE) → Char 36, unique, not null
- customer_id (FK) → Unsigned Big Int, not null
- license_plate (UNIQUE) → Varchar 20, unique, not null
- vehicle_brand → Varchar 50, not null
- vehicle_type → Varchar 50, not null
- model → Varchar 100, nullable
- year → Year, not null
- color → Varchar 30, nullable
- vin (UNIQUE) → Varchar 50, nullable, unique
- engine_number → Varchar 50, nullable, unique
- transmission → Enum('manual', 'automatic'), nullable
- fuel_type → Enum('bensin', 'diesel', 'electric', 'hybrid'), nullable
- last_service_date → Date, nullable
- last_service_mileage → Int, nullable
- current_mileage → Int, nullable
- registration_expiry_date → Date, nullable
- insurance_expiry_date → Date, nullable
- notes → Text, nullable
- status → Enum('active', 'inactive', 'maintenance'), default 'active'
- created_at → Timestamp
- updated_at → Timestamp
- deleted_at → Timestamp, nullable

Indexing:
- Primary Key: id
- Unique Index: uuid
- Unique Index: license_plate
- Unique Index: vin (nullable)
- Unique Index: engine_number (nullable)
- Index: customer_id (Foreign Key)
- Index: status
- Index: (deleted_at, created_at)
- Index: last_service_date untuk reminder
```

### 2.5 TABEL: products

```
Tujuan: Menyimpan data produk (spare parts & jasa)
Relasi:
  - 1:M ke inventory (product_id)
  - 1:M ke service_order_details (product_id)

Kolom:
- id (PK) → Unsigned Big Int
- uuid (UNIQUE) → Char 36, unique, not null
- product_code (UNIQUE) → Varchar 50, unique, not null
- name → Varchar 100, not null
- category → Enum('spare_part', 'service', 'labor'), not null
- brand → Varchar 50, nullable
- description → Text, nullable
- cost_price → Decimal(12, 2), not null
- selling_price → Decimal(12, 2), not null
- unit → Varchar 20, not null (pcs, liter, jam, etc)
- supplier_id → Unsigned Big Int, nullable
- is_active → Boolean, default true
- notes → Text, nullable
- created_at → Timestamp
- updated_at → Timestamp
- deleted_at → Timestamp, nullable

Indexing:
- Primary Key: id
- Unique Index: uuid
- Unique Index: product_code
- Index: category
- Index: brand
- Index: is_active
- Index: (deleted_at, created_at)
- Full Text Index: name, description untuk search
```

### 2.6 TABEL: inventory

```
Tujuan: Menyimpan informasi stock dan warehouse
Relasi:
  - M:1 ke products (product_id)
  - M:1 ke users (updated_by)

Kolom:
- id (PK) → Unsigned Big Int
- product_id (FK) → Unsigned Big Int, not null, unique
- warehouse_location → Varchar 100, not null
- quantity_on_hand → Int, not null, default 0
- quantity_reserved → Int, not null, default 0
- quantity_available → Int (generated: on_hand - reserved)
- reorder_level → Int, not null, default 0
- reorder_quantity → Int, nullable
- last_counted_at → Timestamp, nullable
- last_counted_by (FK) → Unsigned Big Int, nullable
- updated_by (FK) → Unsigned Big Int, nullable
- updated_at → Timestamp
- created_at → Timestamp

Indexing:
- Primary Key: id
- Unique Index: product_id
- Index: warehouse_location
- Index: (quantity_available, reorder_level) untuk low stock alerts
- Index: last_counted_at untuk stock opname
```

### 2.7 TABEL: service_orders

```
Tujuan: Menyimpan work order servis kendaraan
Relasi:
  - M:1 ke vehicles (vehicle_id)
  - M:1 ke customers (customer_id)
  - M:1 ke users (created_by, completed_by)
  - 1:M ke service_order_details (service_order_id)

Kolom:
- id (PK) → Unsigned Big Int
- uuid (UNIQUE) → Char 36, unique, not null
- work_order_number (UNIQUE) → Varchar 50, unique, not null
- vehicle_id (FK) → Unsigned Big Int, not null
- customer_id (FK) → Unsigned Big Int, not null
- description → Text, not null (keluhan pelanggan)
- diagnosis → Text, nullable
- status → Enum('intake', 'diagnosis', 'execution', 'qc', 'completed', 'cancelled'), default 'intake'
- priority → Enum('low', 'medium', 'high', 'urgent'), default 'medium'
- estimated_completion_date → Date, nullable
- actual_completion_date → Date, nullable
- estimated_cost → Decimal(12, 2), nullable
- actual_cost → Decimal(12, 2), nullable
- discount → Decimal(12, 2), default 0
- discount_reason → Varchar 200, nullable
- tax_amount → Decimal(12, 2), default 0
- total_cost → Decimal(12, 2), nullable
- payment_method → Enum('cash', 'card', 'transfer', 'check'), nullable
- payment_status → Enum('unpaid', 'partial', 'paid'), default 'unpaid'
- paid_amount → Decimal(12, 2), default 0
- technician_notes → Text, nullable
- quality_check_notes → Text, nullable
- created_by (FK) → Unsigned Big Int, not null
- completed_by (FK) → Unsigned Big Int, nullable
- created_at → Timestamp
- updated_at → Timestamp
- deleted_at → Timestamp, nullable

Indexing:
- Primary Key: id
- Unique Index: uuid
- Unique Index: work_order_number
- Index: vehicle_id (Foreign Key)
- Index: customer_id (Foreign Key)
- Index: status
- Index: priority
- Index: payment_status
- Index: created_by, completed_by (Foreign Keys)
- Index: (status, created_at) untuk active orders
- Index: (deleted_at, created_at)
- Index: estimated_completion_date untuk scheduling
```

### 2.8 TABEL: service_order_details

```
Tujuan: Menyimpan detail item dalam service order
Relasi:
  - M:1 ke service_orders (service_order_id)
  - M:1 ke products (product_id)

Kolom:
- id (PK) → Unsigned Big Int
- service_order_id (FK) → Unsigned Big Int, not null
- product_id (FK) → Unsigned Big Int, not null
- quantity → Decimal(10, 2), not null
- unit_price → Decimal(12, 2), not null (harga saat order)
- subtotal → Decimal(12, 2), not null (quantity * unit_price)
- warranty_period → Varchar 50, nullable (misal: 1 bulan)
- warranty_notes → Text, nullable
- notes → Text, nullable
- created_at → Timestamp
- updated_at → Timestamp

Indexing:
- Primary Key: id
- Index: service_order_id (Foreign Key)
- Index: product_id (Foreign Key)
- Index: (service_order_id, created_at) untuk order items
```

### 2.9 TABEL: customer_interactions

```
Tujuan: Menyimpan riwayat interaksi dengan pelanggan
Relasi:
  - M:1 ke customers (customer_id)
  - M:1 ke users (user_id)

Kolom:
- id (PK) → Unsigned Big Int
- uuid (UNIQUE) → Char 36, unique, not null
- customer_id (FK) → Unsigned Big Int, not null
- user_id (FK) → Unsigned Big Int, not null
- interaction_type → Enum('phone', 'email', 'whatsapp', 'in_person', 'note'), not null
- subject → Varchar 200, not null
- description → Text, nullable
- outcome → Enum('pending', 'resolved', 'follow_up_needed'), default 'pending'
- follow_up_date → Date, nullable
- priority → Enum('low', 'medium', 'high'), default 'medium'
- notes → Text, nullable
- created_at → Timestamp
- updated_at → Timestamp
- deleted_at → Timestamp, nullable

Indexing:
- Primary Key: id
- Unique Index: uuid
- Index: customer_id (Foreign Key)
- Index: user_id (Foreign Key)
- Index: interaction_type
- Index: outcome
- Index: (customer_id, created_at) untuk interaction history
- Index: follow_up_date untuk reminders
- Index: (deleted_at, created_at)
```

### 2.10 TABEL: audit_logs

```
Tujuan: Menyimpan log semua perubahan data (audit trail)
Relasi:
  - M:1 ke users (user_id, nullable)

Kolom:
- id (PK) → Unsigned Big Int
- user_id (FK) → Unsigned Big Int, nullable
- table_name → Varchar 100, not null
- record_id → Varchar 50, not null (bisa int atau uuid)
- action → Enum('CREATE', 'UPDATE', 'DELETE', 'VIEW'), not null
- old_values → JSON, nullable (snapshot before update)
- new_values → JSON, nullable (snapshot after update)
- ip_address → Varchar 45, not null (support IPv6)
- user_agent → Varchar 255, nullable
- changes_description → Text, nullable (human-readable summary)
- created_at → Timestamp
- indexed: true

Indexing:
- Primary Key: id
- Index: user_id (Foreign Key)
- Index: table_name
- Index: (table_name, record_id)
- Index: action
- Index: (created_at DESC) untuk recent logs
- Index: (table_name, created_at DESC) untuk table-specific history
- Composite Index: (table_name, record_id, action, created_at)
```

---

## 3. NORMALISASI DATABASE (3NF)

### 3.1 First Normal Form (1NF)

Semua tabel memenuhi 1NF:
- ✓ Setiap kolom berisi atomic value (tidak ada repeating groups)
- ✓ Tidak ada array atau nested data (kecuali JSON untuk audit)
- ✓ Setiap baris unik (primary key)

### 3.2 Second Normal Form (2NF)

Semua tabel memenuhi 2NF:
- ✓ Memenuhi 1NF
- ✓ Setiap non-key attribute fully dependent on primary key
- ✓ Tidak ada partial dependency

Contoh:
```
❌ SEBELUM (violates 2NF):
Table service_orders
├─ id (PK)
├─ customer_name (partially dependent on customer_id, not PK)
├─ vehicle_brand (partially dependent on vehicle_id, not PK)

✓ SESUDAH (2NF):
Table service_orders
├─ id (PK)
├─ customer_id (FK) → join dengan customers untuk nama
├─ vehicle_id (FK) → join dengan vehicles untuk brand

Table customers
├─ id (PK)
├─ name

Table vehicles
├─ id (PK)
├─ vehicle_brand
```

### 3.3 Third Normal Form (3NF)

Semua tabel memenuhi 3NF:
- ✓ Memenuhi 2NF
- ✓ Tidak ada transitive dependency

Contoh:
```
❌ SEBELUM (violates 3NF):
Table service_orders
├─ id (PK)
├─ product_id (FK)
├─ product_name (transitive: product_id → product_name)
├─ product_price (transitive: product_id → product_price)

✓ SESUDAH (3NF):
Table service_orders
├─ id (PK)
├─ service_order_detail_id (FK)

Table service_order_details
├─ id (PK)
├─ product_id (FK)
├─ unit_price (copied at order time, not transitive)

Table products
├─ id (PK)
├─ name
├─ selling_price (current price, updated over time)
```

### 3.4 Normalisasi Summary

```
Tabel          1NF  2NF  3NF  Boyce-Codd NF
─────────────────────────────────────────────
roles           ✓    ✓    ✓       ✓
users           ✓    ✓    ✓       ✓
customers       ✓    ✓    ✓       ✓
vehicles        ✓    ✓    ✓       ✓
products        ✓    ✓    ✓       ✓
inventory       ✓    ✓    ✓       ✓
service_orders  ✓    ✓    ✓       ✓
service_order_details ✓  ✓    ✓    ✓
customer_interactions ✓  ✓    ✓    ✓
audit_logs      ✓    ✓    ✓       ✓
```

---

## 4. LARAVEL MIGRATIONS

### 4.1 Migration: Create Roles Table

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up(): void
    {
        Schema::create('roles', function (Blueprint $table) {
            $table->id();
            $table->string('name', 50)->unique();
            $table->text('description')->nullable();
            $table->timestamps();

            // Indexing
            $table->index('name');
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('roles');
    }
};
```

### 4.2 Migration: Create Users Table

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up(): void
    {
        Schema::create('users', function (Blueprint $table) {
            $table->id();
            $table->uuid()->unique();
            $table->string('name', 100);
            $table->string('email', 100)->unique();
            $table->string('password');
            $table->unsignedBigInteger('role_id');
            $table->boolean('is_active')->default(true);
            $table->timestamp('last_login_at')->nullable();
            $table->unsignedBigInteger('created_by')->nullable();
            $table->timestamps();
            $table->softDeletes();

            // Foreign Keys
            $table->foreign('role_id')
                ->references('id')
                ->on('roles')
                ->onDelete('restrict')
                ->onUpdate('cascade');

            $table->foreign('created_by')
                ->references('id')
                ->on('users')
                ->nullOnDelete()
                ->onUpdate('cascade');

            // Indexing
            $table->index('role_id');
            $table->index('created_by');
            $table->index('is_active');
            $table->index(['deleted_at', 'created_at']);
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('users');
    }
};
```

### 4.3 Migration: Create Customers Table

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up(): void
    {
        Schema::create('customers', function (Blueprint $table) {
            $table->id();
            $table->uuid()->unique();
            $table->string('customer_code', 50)->unique();
            $table->string('name', 100);
            $table->string('phone', 20)->nullable();
            $table->string('email', 100)->nullable()->unique();
            $table->text('address')->nullable();
            $table->string('city', 50)->nullable();
            $table->string('province', 50)->nullable();
            $table->string('postal_code', 10)->nullable();
            $table->enum('identity_type', ['KTP', 'SIM', 'PASSPORT'])->nullable();
            $table->string('identity_number', 50)->nullable()->unique();
            $table->enum('customer_type', ['regular', 'vip', 'corporate'])->default('regular');
            $table->enum('preferred_contact_method', ['phone', 'email', 'whatsapp'])->nullable();
            $table->text('notes')->nullable();
            $table->unsignedBigInteger('created_by')->nullable();
            $table->timestamps();
            $table->softDeletes();

            // Foreign Keys
            $table->foreign('created_by')
                ->references('id')
                ->on('users')
                ->nullOnDelete()
                ->onUpdate('cascade');

            // Indexing
            $table->index('phone');
            $table->index('created_by');
            $table->index('customer_type');
            $table->index(['deleted_at', 'created_at']);
            $table->fullText(['name', 'phone', 'email']);
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('customers');
    }
};
```

### 4.4 Migration: Create Vehicles Table

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up(): void
    {
        Schema::create('vehicles', function (Blueprint $table) {
            $table->id();
            $table->uuid()->unique();
            $table->unsignedBigInteger('customer_id');
            $table->string('license_plate', 20)->unique();
            $table->string('vehicle_brand', 50);
            $table->string('vehicle_type', 50);
            $table->string('model', 100)->nullable();
            $table->year('year');
            $table->string('color', 30)->nullable();
            $table->string('vin', 50)->nullable()->unique();
            $table->string('engine_number', 50)->nullable()->unique();
            $table->enum('transmission', ['manual', 'automatic'])->nullable();
            $table->enum('fuel_type', ['bensin', 'diesel', 'electric', 'hybrid'])->nullable();
            $table->date('last_service_date')->nullable();
            $table->integer('last_service_mileage')->nullable();
            $table->integer('current_mileage')->nullable();
            $table->date('registration_expiry_date')->nullable();
            $table->date('insurance_expiry_date')->nullable();
            $table->text('notes')->nullable();
            $table->enum('status', ['active', 'inactive', 'maintenance'])->default('active');
            $table->timestamps();
            $table->softDeletes();

            // Foreign Keys
            $table->foreign('customer_id')
                ->references('id')
                ->on('customers')
                ->onDelete('cascade')
                ->onUpdate('cascade');

            // Indexing
            $table->index('customer_id');
            $table->index('status');
            $table->index(['deleted_at', 'created_at']);
            $table->index('last_service_date');
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('vehicles');
    }
};
```

### 4.5 Migration: Create Products Table

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up(): void
    {
        Schema::create('products', function (Blueprint $table) {
            $table->id();
            $table->uuid()->unique();
            $table->string('product_code', 50)->unique();
            $table->string('name', 100);
            $table->enum('category', ['spare_part', 'service', 'labor']);
            $table->string('brand', 50)->nullable();
            $table->text('description')->nullable();
            $table->decimal('cost_price', 12, 2);
            $table->decimal('selling_price', 12, 2);
            $table->string('unit', 20);
            $table->unsignedBigInteger('supplier_id')->nullable();
            $table->boolean('is_active')->default(true);
            $table->text('notes')->nullable();
            $table->timestamps();
            $table->softDeletes();

            // Indexing
            $table->index('category');
            $table->index('brand');
            $table->index('is_active');
            $table->index(['deleted_at', 'created_at']);
            $table->fullText(['name', 'description']);
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('products');
    }
};
```

### 4.6 Migration: Create Inventory Table

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up(): void
    {
        Schema::create('inventory', function (Blueprint $table) {
            $table->id();
            $table->unsignedBigInteger('product_id')->unique();
            $table->string('warehouse_location', 100);
            $table->integer('quantity_on_hand')->default(0);
            $table->integer('quantity_reserved')->default(0);
            $table->virtualAs('quantity_on_hand - quantity_reserved', 'quantity_available');
            $table->integer('reorder_level')->default(0);
            $table->integer('reorder_quantity')->nullable();
            $table->timestamp('last_counted_at')->nullable();
            $table->unsignedBigInteger('last_counted_by')->nullable();
            $table->unsignedBigInteger('updated_by')->nullable();
            $table->timestamp('updated_at')->useCurrent();
            $table->timestamp('created_at')->useCurrent();

            // Foreign Keys
            $table->foreign('product_id')
                ->references('id')
                ->on('products')
                ->onDelete('cascade')
                ->onUpdate('cascade');

            $table->foreign('last_counted_by')
                ->references('id')
                ->on('users')
                ->nullOnDelete()
                ->onUpdate('cascade');

            $table->foreign('updated_by')
                ->references('id')
                ->on('users')
                ->nullOnDelete()
                ->onUpdate('cascade');

            // Indexing
            $table->index('warehouse_location');
            $table->index(['quantity_available', 'reorder_level']);
            $table->index('last_counted_at');
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('inventory');
    }
};
```

### 4.7 Migration: Create Service Orders Table

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up(): void
    {
        Schema::create('service_orders', function (Blueprint $table) {
            $table->id();
            $table->uuid()->unique();
            $table->string('work_order_number', 50)->unique();
            $table->unsignedBigInteger('vehicle_id');
            $table->unsignedBigInteger('customer_id');
            $table->text('description');
            $table->text('diagnosis')->nullable();
            $table->enum('status', ['intake', 'diagnosis', 'execution', 'qc', 'completed', 'cancelled'])
                ->default('intake');
            $table->enum('priority', ['low', 'medium', 'high', 'urgent'])->default('medium');
            $table->date('estimated_completion_date')->nullable();
            $table->date('actual_completion_date')->nullable();
            $table->decimal('estimated_cost', 12, 2)->nullable();
            $table->decimal('actual_cost', 12, 2)->nullable();
            $table->decimal('discount', 12, 2)->default(0);
            $table->string('discount_reason', 200)->nullable();
            $table->decimal('tax_amount', 12, 2)->default(0);
            $table->decimal('total_cost', 12, 2)->nullable();
            $table->enum('payment_method', ['cash', 'card', 'transfer', 'check'])->nullable();
            $table->enum('payment_status', ['unpaid', 'partial', 'paid'])->default('unpaid');
            $table->decimal('paid_amount', 12, 2)->default(0);
            $table->text('technician_notes')->nullable();
            $table->text('quality_check_notes')->nullable();
            $table->unsignedBigInteger('created_by');
            $table->unsignedBigInteger('completed_by')->nullable();
            $table->timestamps();
            $table->softDeletes();

            // Foreign Keys
            $table->foreign('vehicle_id')
                ->references('id')
                ->on('vehicles')
                ->onDelete('restrict')
                ->onUpdate('cascade');

            $table->foreign('customer_id')
                ->references('id')
                ->on('customers')
                ->onDelete('restrict')
                ->onUpdate('cascade');

            $table->foreign('created_by')
                ->references('id')
                ->on('users')
                ->onDelete('restrict')
                ->onUpdate('cascade');

            $table->foreign('completed_by')
                ->references('id')
                ->on('users')
                ->nullOnDelete()
                ->onUpdate('cascade');

            // Indexing
            $table->index('vehicle_id');
            $table->index('customer_id');
            $table->index('status');
            $table->index('priority');
            $table->index('payment_status');
            $table->index('created_by');
            $table->index('completed_by');
            $table->index(['status', 'created_at']);
            $table->index(['deleted_at', 'created_at']);
            $table->index('estimated_completion_date');
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('service_orders');
    }
};
```

### 4.8 Migration: Create Service Order Details Table

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up(): void
    {
        Schema::create('service_order_details', function (Blueprint $table) {
            $table->id();
            $table->unsignedBigInteger('service_order_id');
            $table->unsignedBigInteger('product_id');
            $table->decimal('quantity', 10, 2);
            $table->decimal('unit_price', 12, 2);
            $table->decimal('subtotal', 12, 2);
            $table->string('warranty_period', 50)->nullable();
            $table->text('warranty_notes')->nullable();
            $table->text('notes')->nullable();
            $table->timestamps();

            // Foreign Keys
            $table->foreign('service_order_id')
                ->references('id')
                ->on('service_orders')
                ->onDelete('cascade')
                ->onUpdate('cascade');

            $table->foreign('product_id')
                ->references('id')
                ->on('products')
                ->onDelete('restrict')
                ->onUpdate('cascade');

            // Indexing
            $table->index('service_order_id');
            $table->index('product_id');
            $table->index(['service_order_id', 'created_at']);
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('service_order_details');
    }
};
```

### 4.9 Migration: Create Customer Interactions Table

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up(): void
    {
        Schema::create('customer_interactions', function (Blueprint $table) {
            $table->id();
            $table->uuid()->unique();
            $table->unsignedBigInteger('customer_id');
            $table->unsignedBigInteger('user_id');
            $table->enum('interaction_type', ['phone', 'email', 'whatsapp', 'in_person', 'note']);
            $table->string('subject', 200);
            $table->text('description')->nullable();
            $table->enum('outcome', ['pending', 'resolved', 'follow_up_needed'])->default('pending');
            $table->date('follow_up_date')->nullable();
            $table->enum('priority', ['low', 'medium', 'high'])->default('medium');
            $table->text('notes')->nullable();
            $table->timestamps();
            $table->softDeletes();

            // Foreign Keys
            $table->foreign('customer_id')
                ->references('id')
                ->on('customers')
                ->onDelete('cascade')
                ->onUpdate('cascade');

            $table->foreign('user_id')
                ->references('id')
                ->on('users')
                ->onDelete('restrict')
                ->onUpdate('cascade');

            // Indexing
            $table->index('customer_id');
            $table->index('user_id');
            $table->index('interaction_type');
            $table->index('outcome');
            $table->index(['customer_id', 'created_at']);
            $table->index('follow_up_date');
            $table->index(['deleted_at', 'created_at']);
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('customer_interactions');
    }
};
```

### 4.10 Migration: Create Audit Logs Table

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up(): void
    {
        Schema::create('audit_logs', function (Blueprint $table) {
            $table->id();
            $table->unsignedBigInteger('user_id')->nullable();
            $table->string('table_name', 100);
            $table->string('record_id', 50);
            $table->enum('action', ['CREATE', 'UPDATE', 'DELETE', 'VIEW']);
            $table->json('old_values')->nullable();
            $table->json('new_values')->nullable();
            $table->ipAddress('ip_address')->nullable();
            $table->string('user_agent', 255)->nullable();
            $table->text('changes_description')->nullable();
            $table->timestamp('created_at')->useCurrent();

            // Foreign Keys
            $table->foreign('user_id')
                ->references('id')
                ->on('users')
                ->nullOnDelete()
                ->onUpdate('cascade');

            // Indexing
            $table->index('user_id');
            $table->index('table_name');
            $table->index(['table_name', 'record_id']);
            $table->index('action');
            $table->index('created_at');
            $table->index(['table_name', 'created_at']);
            $table->index(['table_name', 'record_id', 'action', 'created_at']);
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('audit_logs');
    }
};
```

---

## 5. INDEXING STRATEGY

### 5.1 Indexing Rules

```
Rule 1: Primary Key
- Setiap tabel harus memiliki primary key
- Gunakan BIGINT UNSIGNED AUTO_INCREMENT untuk id
- Gunakan UUID untuk distributed systems

Rule 2: Foreign Keys
- Index semua foreign keys
- Foreign keys adalah join points

Rule 3: Search Columns
- Index kolom yang sering dicari (WHERE clause)
- name, phone, email, customer_code, etc

Rule 4: Soft Delete
- Composite index: (deleted_at, created_at)
- Untuk query yang exclude deleted records

Rule 5: Status & Flags
- Index kolom enum seperti status, is_active
- Filter berdasarkan status sering terjadi

Rule 6: Sorting
- Index kolom yang sering di-ORDER BY
- created_at, updated_at, date fields

Rule 7: Full Text Search
- Full text indexes untuk name, description, etc
- Natural language search
```

### 5.2 Indexing Summary

```
Tabel: customers
├─ PK: id
├─ UNIQUE: uuid, customer_code, email, identity_number
├─ SINGLE: phone, created_by, customer_type
├─ COMPOSITE: (deleted_at, created_at)
└─ FULLTEXT: (name, phone, email)

Tabel: vehicles
├─ PK: id
├─ UNIQUE: uuid, license_plate, vin, engine_number
├─ SINGLE: customer_id, status
├─ COMPOSITE: (deleted_at, created_at)
└─ SINGLE: last_service_date

Tabel: service_orders
├─ PK: id
├─ UNIQUE: uuid, work_order_number
├─ SINGLE: vehicle_id, customer_id, status, priority, payment_status
├─ SINGLE: created_by, completed_by
├─ COMPOSITE: (status, created_at), (deleted_at, created_at)
└─ SINGLE: estimated_completion_date

Tabel: products
├─ PK: id
├─ UNIQUE: uuid, product_code
├─ SINGLE: category, brand, is_active
├─ COMPOSITE: (deleted_at, created_at)
└─ FULLTEXT: (name, description)

Tabel: inventory
├─ PK: id
├─ UNIQUE: product_id
├─ SINGLE: warehouse_location, last_counted_at
└─ COMPOSITE: (quantity_available, reorder_level)

Tabel: service_order_details
├─ PK: id
├─ SINGLE: service_order_id, product_id
└─ COMPOSITE: (service_order_id, created_at)

Tabel: customer_interactions
├─ PK: id
├─ UNIQUE: uuid
├─ SINGLE: customer_id, user_id, interaction_type, outcome
├─ COMPOSITE: (customer_id, created_at), (deleted_at, created_at)
└─ SINGLE: follow_up_date

Tabel: audit_logs
├─ PK: id
├─ SINGLE: user_id, table_name, action, created_at
├─ COMPOSITE: (table_name, record_id), (table_name, created_at)
└─ COMPOSITE: (table_name, record_id, action, created_at)
```

---

## 6. FOREIGN KEY STRATEGY

### 6.1 Foreign Key Rules

```
Rule 1: Referential Integrity
- Foreign keys enforce data consistency
- Prevent orphaned records

Rule 2: Delete Strategy
- CASCADE: Hapus child ketika parent dihapus
  → Gunakan untuk: service_order_details, customer_interactions
- RESTRICT: Jangan boleh hapus jika ada child
  → Gunakan untuk: users dalam service_orders (completed_by)
- SET NULL: Set FK ke NULL ketika parent dihapus
  → Gunakan untuk: nullable foreign keys

Rule 3: Update Strategy
- CASCADE: Update child ketika parent diupdate
  → Paling aman untuk id updates
- RESTRICT: Jangan boleh update jika ada child

Rule 4: Performance
- Jangan circular references
- Avoid deep chains (A → B → C → D)
- Index all foreign keys
```

### 6.2 Foreign Key Relationships

```
users (1) ──→ (M) customers (created_by)
  │                    │
  │                    └─→ (1:M) vehicles
  │                              │
  │                              └─→ (1:M) service_orders
  │
  └─→ (1:M) roles
  └─→ (1:M) service_orders (created_by, completed_by)
  └─→ (1:M) customer_interactions (user_id)
  └─→ (1:M) audit_logs

products (1) ──→ (M) service_order_details
  │
  └─→ (1:1) inventory

service_orders (1) ──→ (M) service_order_details

vehicles (1) ──→ (M) service_orders
customers (1) ──→ (M) service_orders
```

### 6.3 Foreign Key Configuration

**Strategy untuk setiap relasi:**

```
1. users → roles (M:1)
   - Delete: RESTRICT (cannot delete role with users)
   - Update: CASCADE (update role_id on users if role changes)
   - Reason: Data integrity - must always have role

2. users → customers (1:M)
   - Delete: SET NULL (created_by can be null)
   - Update: CASCADE (user id rarely changes, but safe)
   - Reason: created_by is optional tracking info

3. customers → vehicles (1:M)
   - Delete: CASCADE (delete vehicles when customer deleted)
   - Update: CASCADE
   - Reason: Vehicles belong to customer, no point keeping orphan vehicles

4. vehicles → service_orders (1:M)
   - Delete: RESTRICT (prevent deleting vehicle with orders)
   - Update: CASCADE
   - Reason: Service history important, prevent accidental deletion

5. products → service_order_details (1:M)
   - Delete: RESTRICT (prevent deleting product if in orders)
   - Update: CASCADE
   - Reason: Product history important

6. service_orders → service_order_details (1:M)
   - Delete: CASCADE (delete details when order deleted)
   - Update: CASCADE
   - Reason: Details belong to order

7. users → service_orders (created_by)
   - Delete: RESTRICT (keep order history intact)
   - Update: CASCADE
   - Reason: Who created the order is important

8. users → service_orders (completed_by)
   - Delete: SET NULL (user can be deleted)
   - Update: CASCADE
   - Reason: Optional tracking, user might be terminated
```

---

## 7. AUDIT LOGGING SCHEMA

### 7.1 Audit Trail Workflow

```
┌──────────────┐
│ Data Change  │
│ (INSERT/     │
│  UPDATE/     │
│  DELETE)     │
└──────┬───────┘
       │
┌──────▼──────────────────┐
│ Trigger/Event Listener  │
│ - Capture old_values    │
│ - Capture new_values    │
│ - Get current user      │
│ - Get IP address        │
└──────┬──────────────────┘
       │
┌──────▼──────────────────┐
│ Create Audit Log Record │
│ - table_name            │
│ - record_id             │
│ - action (C/U/D/V)      │
│ - old_values (JSON)     │
│ - new_values (JSON)     │
│ - ip_address            │
│ - user_agent            │
│ - timestamp             │
└──────┬──────────────────┘
       │
┌──────▼──────────────────┐
│ Audit Log Entry Created │
│ Ready for reporting &   │
│ compliance              │
└───────────────────────┘
```

### 7.2 Audit Log Queries

```php
// Get all changes to a customer
SELECT * FROM audit_logs
WHERE table_name = 'customers'
  AND record_id = '550e8400-e29b-41d4-a716-446655440000'
ORDER BY created_at DESC;

// Get deletions in last 30 days
SELECT * FROM audit_logs
WHERE action = 'DELETE'
  AND created_at >= DATE_SUB(NOW(), INTERVAL 30 DAY)
ORDER BY created_at DESC;

// Get changes by specific user
SELECT * FROM audit_logs
WHERE user_id = 5
  AND created_at >= DATE_SUB(NOW(), INTERVAL 7 DAY)
ORDER BY created_at DESC;

// Get price changes
SELECT * FROM audit_logs
WHERE table_name = 'products'
  AND JSON_CONTAINS(new_values, '"selling_price"')
ORDER BY created_at DESC;

// Get payment status changes
SELECT *,
  JSON_EXTRACT(old_values, '$.payment_status') as old_status,
  JSON_EXTRACT(new_values, '$.payment_status') as new_status
FROM audit_logs
WHERE table_name = 'service_orders'
  AND action = 'UPDATE'
  AND JSON_EXTRACT(new_values, '$.payment_status') != JSON_EXTRACT(old_values, '$.payment_status')
ORDER BY created_at DESC;

// Compliance: Get all DELETE actions with responsible user
SELECT
  al.id,
  al.action,
  al.table_name,
  al.record_id,
  u.name as deleted_by,
  al.created_at,
  al.ip_address
FROM audit_logs al
LEFT JOIN users u ON al.user_id = u.id
WHERE al.action = 'DELETE'
  AND al.created_at >= '2026-01-01'
ORDER BY al.created_at DESC;
```

---

## 8. REKOMENDASI OPTIMASI QUERY

### 8.1 Query Optimization Techniques

```
1. EAGER LOADING (Prevent N+1)
   ❌ SEBELUM (1 query + N queries):
   $customers = Customer::all();
   foreach ($customers as $customer) {
       echo $customer->vehicles; // 1 + N queries
   }

   ✓ SESUDAH (2 queries):
   $customers = Customer::with('vehicles')->get();

2. SELECT SPECIFIC COLUMNS
   ❌ SEBELUM:
   SELECT * FROM customers; // All columns

   ✓ SESUDAH:
   SELECT id, uuid, name, email FROM customers;

3. PAGINATION
   ❌ SEBELUM (Load all):
   $customers = Customer::all();

   ✓ SESUDAH (Load per page):
   $customers = Customer::paginate(15);

4. COMPOSITE INDEX FOR WHERE + ORDER BY
   ❌ SEBELUM:
   SELECT * FROM service_orders
   WHERE status = 'completed'
   ORDER BY created_at DESC; // 2 separate indexes

   ✓ SESUDAH:
   -- Index: (status, created_at)
   SELECT * FROM service_orders
   WHERE status = 'completed'
   ORDER BY created_at DESC;

5. LIMIT FOR SUBQUERIES
   ❌ SEBELUM:
   SELECT * FROM customers
   WHERE id IN (SELECT customer_id FROM service_orders);

   ✓ SESUDAH:
   SELECT DISTINCT c.*
   FROM customers c
   INNER JOIN service_orders so ON c.id = so.customer_id;

6. USE INDEXES FOR JOINS
   Make sure all join columns are indexed
   - Foreign key columns must be indexed
   - Join on id columns (primary keys)

7. DENORMALIZATION FOR PERFORMANCE (Use sparingly)
   Example: Store customer_name in service_orders
   - Pros: Avoid join, faster queries
   - Cons: Data duplication, consistency issues
   - Use: Only when heavily queried and rarely updated

8. CACHING
   Cache slow queries:
   - Cache::remember('customers_top_10', 3600, function() {
       return Customer::orderByDesc('total_spent')->limit(10)->get();
     });
   - Invalidate on updates
```

### 8.2 Specific Query Optimizations

```php
// QUERY 1: Get service orders with details
// ❌ Problem: N+1 on vehicle, customer, user, details
$orders = ServiceOrder::all();
foreach ($orders as $order) {
    echo $order->vehicle->license_plate;
    echo $order->customer->name;
    echo $order->createdBy->name;
    foreach ($order->details as $detail) {
        echo $detail->product->name;
    }
}

// ✓ Solution: Eager loading
$orders = ServiceOrder::with([
    'vehicle:id,license_plate',
    'customer:id,name',
    'createdBy:id,name',
    'details.product:id,name'
])->get();

// QUERY 2: Get active customers with their service history
// ❌ Problem: Missing index on (status, created_at)
$customers = Customer::where('is_active', true)
    ->orderBy('created_at', 'desc')
    ->get();

// ✓ Solution: Use composite index
// Migration: $table->index(['is_active', 'created_at']);

// QUERY 3: Low stock alerts
// ❌ Problem: Calculation after query
$lowStock = [];
foreach (Product::all() as $product) {
    if ($product->inventory->quantity_on_hand < $product->inventory->reorder_level) {
        $lowStock[] = $product;
    }
}

// ✓ Solution: Use calculated column in database
// Migration: $table->virtualAs('quantity_on_hand - quantity_reserved', 'quantity_available');
// Query:
$lowStock = Inventory::whereRaw('quantity_available < reorder_level')
    ->with('product')
    ->get();

// QUERY 4: Customer interaction history
// ❌ Problem: Missing composite index
$interactions = CustomerInteraction::where('customer_id', $customerId)
    ->orderBy('created_at', 'desc')
    ->paginate();

// ✓ Solution: Index (customer_id, created_at)
// Migration: $table->index(['customer_id', 'created_at']);

// QUERY 5: Search customers
// ❌ Problem: LIKE without full text index
$customers = Customer::where('name', 'like', "%$search%")
    ->orWhere('phone', 'like', "%$search%")
    ->get();

// ✓ Solution: Full text index
// Migration: $table->fullText(['name', 'phone', 'email']);
// Query:
$customers = Customer::whereRaw("MATCH(name, phone, email) AGAINST(? IN BOOLEAN MODE)", [$search])
    ->get();

// QUERY 6: Get service orders by status and date
// ❌ Problem: Two separate indexes
SELECT * FROM service_orders
WHERE status = 'completed'
  AND created_at >= '2026-01-01'
ORDER BY created_at DESC;

// ✓ Solution: Composite index (status, created_at)
// Check: EXPLAIN SELECT ...
// Should show: type=range, rows should be small

// QUERY 7: Monthly revenue report
// ❌ Problem: Slow without aggregation optimization
SELECT
  YEAR(created_at) as year,
  MONTH(created_at) as month,
  SUM(total_cost) as revenue,
  COUNT(*) as order_count
FROM service_orders
WHERE status = 'completed'
GROUP BY YEAR(created_at), MONTH(created_at)
ORDER BY year DESC, month DESC;

// ✓ Solution: Add index on (status, created_at)
// Use materialized view for frequently accessed reports
CREATE VIEW monthly_revenue AS
SELECT ... (above query);

// QUERY 8: Pagination with filter
// ❌ Problem: Counts all rows
Paginator::total count: SELECT COUNT(*) FROM customers WHERE is_active = true

// ✓ Solution: Use estimated count for large tables
// Or add count cache that updates on data change
```

### 8.3 Query Execution Plan Analysis

```
EXPLAIN + QUERY:

✓ GOOD (Type = eq_ref or ref):
+----+-----------+-------+-------+---------------+--------+
| id | select_type | table | type | possible_keys | key    |
+----+-----------+-------+-------+---------------+--------+
|  1 | SIMPLE  | customers | ref | idx_created_by | idx_created_by |
+----+-----------+-------+-------+---------------+--------+

❌ BAD (Type = ALL):
+----+-----------+-------+------+---------------+------+
| id | select_type | table | type | possible_keys | key  |
+----+-----------+-------+------+---------------+------+
|  1 | SIMPLE  | customers | ALL | NULL          | NULL |
+----+-----------+-------+------+---------------+------+

Key Metrics:
- Type: eq_ref > ref > range > index > ALL
- Rows: Lower is better
- Extra: Use index = good; Using where = might be slow
```

### 8.4 Performance Benchmarks

```
Target Performance:
┌─────────────────────────┬──────────────┐
│ Query Type              │ Target Time  │
├─────────────────────────┼──────────────┤
│ Simple SELECT (indexed) │ < 10ms       │
│ JOIN (2 tables)         │ < 50ms       │
│ JOIN (3+ tables)        │ < 100ms      │
│ Aggregation (GROUP BY)  │ < 200ms      │
│ Full text search        │ < 500ms      │
│ Pagination (1000 rows)  │ < 100ms      │
│ Report generation       │ < 5s         │
└─────────────────────────┴──────────────┘

Monitor with:
mysql> SET SESSION slow_query_log = 'ON';
mysql> SET SESSION long_query_time = 1;
-- Check /var/log/mysql/slow-query.log
```

---

## 9. IMPLEMENTASI DATABASE MIGRATIONS

### 9.1 Migration Execution Order

```bash
# Jalankan dalam urutan ini:
php artisan migrate:fresh --step

# Step 1: Create roles
php artisan migrate --path=database/migrations/2026_01_01_create_roles_table.php

# Step 2: Create users (depends on roles)
php artisan migrate --path=database/migrations/2026_01_02_create_users_table.php

# Step 3: Create customers (depends on users)
php artisan migrate --path=database/migrations/2026_01_03_create_customers_table.php

# Step 4: Create vehicles (depends on customers)
php artisan migrate --path=database/migrations/2026_01_04_create_vehicles_table.php

# Step 5: Create products
php artisan migrate --path=database/migrations/2026_01_05_create_products_table.php

# Step 6: Create inventory (depends on products)
php artisan migrate --path=database/migrations/2026_01_06_create_inventory_table.php

# Step 7: Create service_orders (depends on vehicles, customers, users)
php artisan migrate --path=database/migrations/2026_01_07_create_service_orders_table.php

# Step 8: Create service_order_details (depends on service_orders, products)
php artisan migrate --path=database/migrations/2026_01_08_create_service_order_details_table.php

# Step 9: Create customer_interactions (depends on customers, users)
php artisan migrate --path=database/migrations/2026_01_09_create_customer_interactions_table.php

# Step 10: Create audit_logs (depends on users)
php artisan migrate --path=database/migrations/2026_01_10_create_audit_logs_table.php
```

### 9.2 Rollback Strategy

```bash
# Rollback semua
php artisan migrate:rollback

# Rollback dengan step
php artisan migrate:rollback --step=5

# Rollback dan remigrate
php artisan migrate:refresh

# Rollback, remigrate, dan seed
php artisan migrate:refresh --seed
```

---

## 10. DATABASE STATISTICS & MONITORING

### 10.1 Database Size Estimation

```
Assumptions:
- 10,000 customers
- 15,000 vehicles
- 50,000 service orders
- 150,000 service order details
- 100,000 products
- 200,000 interactions
- 1,000,000 audit logs

Estimated Database Size:
┌──────────────────────────┬──────────────┐
│ Table                    │ Approx. Size │
├──────────────────────────┼──────────────┤
│ customers                │ 5-10 MB      │
│ vehicles                 │ 10-15 MB     │
│ service_orders           │ 20-30 MB     │
│ service_order_details    │ 30-50 MB     │
│ products                 │ 10-15 MB     │
│ inventory                │ 5 MB         │
│ customer_interactions    │ 30-50 MB     │
│ audit_logs               │ 300-500 MB   │
│ indexes                  │ 200-400 MB   │
├──────────────────────────┼──────────────┤
│ TOTAL                    │ ~650-1200 MB │
└──────────────────────────┴──────────────┘

Scale up for 10x data: 6.5-12 GB
Scale up for 100x data: 65-120 GB
```

### 10.2 Maintenance Tasks

```
Daily:
- Monitor slow query log
- Check disk space
- Verify backups

Weekly:
- ANALYZE TABLE (update statistics)
- CHECK TABLE (verify integrity)
- Review audit logs

Monthly:
- OPTIMIZE TABLE (defragment)
- Prune old audit logs (> 1 year)
- Review index usage
- Capacity planning

Quarterly:
- Full backup verification
- Disaster recovery test
- Performance tuning
```

---

## 11. DATABASE BEST PRACTICES CHECKLIST

```
✓ Naming Conventions
  ├─ Tables: lowercase, plural (users, customers)
  ├─ Columns: lowercase, singular (user_id, customer_name)
  ├─ Keys: id, *_id for FK, uuid for distributed
  ├─ Indexes: idx_table_column
  └─ Constraints: Always be explicit

✓ Data Types
  ├─ Use BIGINT UNSIGNED for IDs
  ├─ Use Char(36) for UUIDs
  ├─ Use Decimal(12,2) for money
  ├─ Use ENUM for fixed values
  ├─ Use TEXT for > 255 chars
  ├─ Use JSON for flexible data
  └─ Use Timestamp for dates

✓ Constraints
  ├─ PRIMARY KEY on every table
  ├─ UNIQUE on natural keys
  ├─ FOREIGN KEY for referential integrity
  ├─ NOT NULL for required fields
  ├─ DEFAULT for logical defaults
  └─ CHECK constraints where needed

✓ Performance
  ├─ Index all foreign keys
  ├─ Index frequently searched columns
  ├─ Use composite indexes for WHERE + ORDER BY
  ├─ Avoid SELECT * (select specific columns)
  ├─ Use LIMIT for pagination
  ├─ Eager load relationships
  └─ Monitor slow queries

✓ Security
  ├─ Use parameterized queries
  ├─ Validate input on application level
  ├─ Hash sensitive data
  ├─ Audit all modifications
  ├─ Limit user database privileges
  └─ Encrypt backups

✓ Backup & Recovery
  ├─ Daily automated backups
  ├─ Test restore procedures
  ├─ Keep backup logs
  ├─ Archive old backups
  ├─ Monitor backup success
  └─ Have RTO/RPO defined

✓ Documentation
  ├─ Document schema changes
  ├─ Keep ERD updated
  ├─ Document business rules
  ├─ Document index strategy
  └─ Document migration dependencies
```

---

## KESIMPULAN

Database ini dirancang dengan:
✓ **Normalisasi 3NF** - Menghilangkan redundansi data
✓ **Referential Integrity** - Foreign keys menjaga konsistensi
✓ **Optimal Indexing** - Performa query yang cepat
✓ **Audit Logging** - Compliance dan accountability
✓ **Scalability** - Siap untuk pertumbuhan data
✓ **Best Practices** - Mengikuti industry standards

---

**END OF DATABASE DESIGN DOCUMENTATION**
