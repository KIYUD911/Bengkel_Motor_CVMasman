# Sistem Manajemen Bengkel Motor - Enterprise Architecture
## Architecture Documentation

**Versi:** 1.0  
**Tanggal:** 21 Mei 2026  
**Framework:** Laravel 13.x  
**Database:** MySQL 8.0+  
**PHP:** 8.4+

---

## 1. OVERVIEW ARSITEKTUR

Aplikasi Bengkel Motor dibangun dengan **Enterprise Architecture Pattern** yang menggunakan:
- **MVC (Model-View-Controller)** untuk separation of concerns
- **Repository Pattern** untuk data access abstraction
- **Service Layer** untuk business logic
- **Middleware** untuk cross-cutting concerns
- **Event-Driven Architecture** untuk audit logging

```
┌──────────────────────────────────────────────────────────────────┐
│                         PRESENTATION LAYER                        │
│                    (Views, Controllers, API)                      │
└──────────────────┬───────────────────────────────────────────────┘
                   │
┌──────────────────▼───────────────────────────────────────────────┐
│                         SERVICE LAYER                             │
│              (Business Logic, Validation, Rules)                  │
└──────────────────┬───────────────────────────────────────────────┘
                   │
┌──────────────────▼───────────────────────────────────────────────┐
│                      REPOSITORY LAYER                             │
│                  (Data Access Abstraction)                        │
└──────────────────┬───────────────────────────────────────────────┘
                   │
┌──────────────────▼───────────────────────────────────────────────┐
│                        MODEL LAYER                                │
│                    (Database Models)                              │
└──────────────────┬───────────────────────────────────────────────┘
                   │
┌──────────────────▼───────────────────────────────────────────────┐
│                       DATABASE LAYER                              │
│                    (MySQL / Data Storage)                         │
└──────────────────────────────────────────────────────────────────┘
```

---

## 2. STRUKTUR FOLDER LARAVEL (ENTERPRISE EDITION)

```
bengkel-motor/
├── app/
│   ├── Http/
│   │   ├── Controllers/
│   │   │   ├── AuthController.php
│   │   │   ├── DashboardController.php
│   │   │   └── Modules/
│   │   │       ├── Customer/
│   │   │       │   ├── CustomerController.php
│   │   │       │   └── CustomerApiController.php
│   │   │       ├── Vehicle/
│   │   │       │   └── VehicleController.php
│   │   │       ├── Service/
│   │   │       │   └── ServiceController.php
│   │   │       └── Inventory/
│   │   │           └── InventoryController.php
│   │   ├── Middleware/
│   │   │   ├── Authenticate.php
│   │   │   ├── CheckRole.php
│   │   │   ├── LogUserActivity.php
│   │   │   └── AuditTrail.php
│   │   ├── Requests/
│   │   │   ├── StoreCustomerRequest.php
│   │   │   ├── UpdateCustomerRequest.php
│   │   │   └── Modules/
│   │   │       └── Customer/
│   │   │           ├── StoreCustomerRequest.php
│   │   │           └── UpdateCustomerRequest.php
│   │   └── Resources/
│   │       ├── CustomerResource.php
│   │       ├── VehicleResource.php
│   │       └── Modules/
│   │           └── Customer/
│   │               └── CustomerResource.php
│   ├── Models/
│   │   ├── User.php
│   │   ├── Customer.php
│   │   ├── Vehicle.php
│   │   ├── Service.php
│   │   ├── SparePart.php
│   │   ├── ServiceHistory.php
│   │   └── AuditLog.php
│   ├── Services/
│   │   ├── CustomerService.php
│   │   ├── VehicleService.php
│   │   ├── ServiceService.php
│   │   ├── InventoryService.php
│   │   └── Modules/
│   │       └── Customer/
│   │           ├── CustomerService.php
│   │           ├── CustomerExportService.php
│   │           └── CustomerReportService.php
│   ├── Repositories/
│   │   ├── Contracts/
│   │   │   ├── RepositoryInterface.php
│   │   │   ├── CustomerRepositoryInterface.php
│   │   │   ├── VehicleRepositoryInterface.php
│   │   │   └── ServiceRepositoryInterface.php
│   │   ├── BaseRepository.php
│   │   ├── CustomerRepository.php
│   │   ├── VehicleRepository.php
│   │   ├── ServiceRepository.php
│   │   └── Modules/
│   │       └── Customer/
│   │           └── CustomerRepository.php
│   ├── Events/
│   │   ├── CustomerCreated.php
│   │   ├── CustomerUpdated.php
│   │   ├── ServiceCompleted.php
│   │   └── InventoryAdjusted.php
│   ├── Listeners/
│   │   ├── LogAuditTrail.php
│   │   ├── SendCustomerNotification.php
│   │   ├── UpdateInventoryOnService.php
│   │   └── NotifyLowStock.php
│   ├── Exceptions/
│   │   ├── Handler.php
│   │   ├── CustomerNotFoundException.php
│   │   ├── InsufficientStockException.php
│   │   └── InvalidOperationException.php
│   ├── Traits/
│   │   ├── HasUuid.php
│   │   ├── LogsActivity.php
│   │   └── FilterQueryTrait.php
│   ├── Enums/
│   │   ├── ServiceStatus.php
│   │   ├── UserRole.php
│   │   ├── PaymentMethod.php
│   │   └── StockMovementType.php
│   ├── DTO/
│   │   ├── CustomerDTO.php
│   │   ├── VehicleDTO.php
│   │   ├── ServiceDTO.php
│   │   └── AuditLogDTO.php
│   ├── Queries/
│   │   ├── GetCustomerByCode.php
│   │   ├── GetServicesByCustomer.php
│   │   └── GetLowStockItems.php
│   ├── Rules/
│   │   ├── ValidCustomerCode.php
│   │   ├── UniqueEmail.php
│   │   └── ValidatePoliceNumber.php
│   ├── Actions/
│   │   ├── CreateCustomerAction.php
│   │   ├── UpdateServiceStatusAction.php
│   │   ├── ProcessPaymentAction.php
│   │   └── AdjustInventoryAction.php
│   └── Providers/
│       ├── AppServiceProvider.php
│       ├── AuthServiceProvider.php
│       ├── EventServiceProvider.php
│       └── RepositoryServiceProvider.php
├── database/
│   ├── migrations/
│   │   ├── 2026_05_20_000001_create_customers_table.php
│   │   ├── 2026_05_20_000002_create_vehicles_table.php
│   │   ├── 2026_05_20_000003_create_services_table.php
│   │   ├── 2026_05_20_000004_create_spare_parts_table.php
│   │   ├── 2026_05_20_000005_create_audit_logs_table.php
│   │   ├── 2026_05_20_000006_create_service_items_table.php
│   │   └── 2026_05_20_000007_create_inventory_movements_table.php
│   ├── factories/
│   │   ├── UserFactory.php
│   │   ├── CustomerFactory.php
│   │   ├── VehicleFactory.php
│   │   └── ServiceFactory.php
│   └── seeders/
│       ├── DatabaseSeeder.php
│       ├── UserSeeder.php
│       ├── CustomerSeeder.php
│       ├── VehicleSeeder.php
│       └── InventorySeeder.php
├── routes/
│   ├── web.php
│   ├── api.php
│   ├── auth.php
│   └── modules/
│       ├── customer.php
│       ├── vehicle.php
│       ├── service.php
│       └── inventory.php
├── resources/
│   ├── views/
│   │   ├── layouts/
│   │   │   ├── app.blade.php
│   │   │   ├── auth.blade.php
│   │   │   └── sidebar.blade.php
│   │   ├── auth/
│   │   │   ├── login.blade.php
│   │   │   ├── register.blade.php
│   │   │   └── reset-password.blade.php
│   │   ├── modules/
│   │   │   ├── customer/
│   │   │   │   ├── index.blade.php
│   │   │   │   ├── create.blade.php
│   │   │   │   ├── edit.blade.php
│   │   │   │   └── show.blade.php
│   │   │   ├── vehicle/
│   │   │   │   ├── index.blade.php
│   │   │   │   └── show.blade.php
│   │   │   ├── service/
│   │   │   │   ├── index.blade.php
│   │   │   │   ├── create.blade.php
│   │   │   │   └── show.blade.php
│   │   │   └── inventory/
│   │   │       └── index.blade.php
│   │   └── errors/
│   │       ├── 403.blade.php
│   │       ├── 404.blade.php
│   │       └── 500.blade.php
│   ├── js/
│   │   ├── app.js
│   │   ├── bootstrap.js
│   │   └── modules/
│   │       ├── customer.js
│   │       ├── service.js
│   │       └── validation.js
│   └── css/
│       ├── app.css
│       └── custom.css
├── config/
│   ├── app.php
│   ├── database.php
│   ├── auth.php
│   ├── cache.php
│   ├── logging.php
│   ├── queue.php
│   ├── audit.php
│   ├── pagination.php
│   └── services.php
├── storage/
│   ├── app/
│   │   ├── uploads/
│   │   │   ├── documents/
│   │   │   ├── invoices/
│   │   │   └── photos/
│   │   └── exports/
│   ├── logs/
│   └── framework/
├── tests/
│   ├── Feature/
│   │   ├── CustomerTest.php
│   │   ├── VehicleTest.php
│   │   ├── ServiceTest.php
│   │   └── AuthenticationTest.php
│   ├── Unit/
│   │   ├── CustomerServiceTest.php
│   │   ├── CustomerRepositoryTest.php
│   │   └── ValidationRulesTest.php
│   └── Pest.php
├── stubs/
│   ├── repository.stub
│   ├── service.stub
│   └── action.stub
├── public/
│   ├── css/
│   ├── js/
│   ├── images/
│   └── index.php
├── .env.example
├── .env
├── composer.json
├── phpunit.xml
├── vite.config.js
├── package.json
├── artisan
├── ARCHITECTURE.md
├── PRD_BENGKEL_MOTOR.md
└── README.md
```

---

## 3. ARCHITECTURE DIAGRAM

### 3.1 Request Lifecycle Flow

```
┌──────────────────────────────────────────────────────────────────┐
│ 1. HTTP REQUEST                                                  │
│    GET /api/customers/550e8400-e29b-41d4-a716-446655440000     │
└──────────────────┬───────────────────────────────────────────────┘
                   │
┌──────────────────▼───────────────────────────────────────────────┐
│ 2. LARAVEL KERNEL                                                │
│    - Bootstrap Application                                       │
│    - Initialize Service Providers                               │
└──────────────────┬───────────────────────────────────────────────┘
                   │
┌──────────────────▼───────────────────────────────────────────────┐
│ 3. MIDDLEWARE PIPELINE (Pre-Controller)                          │
│    ├─ CheckForMaintenanceMode                                   │
│    ├─ ValidatePostSize                                          │
│    ├─ TrustProxies                                              │
│    ├─ HandleCors                                                │
│    ├─ ConvertEmptyStringsToNull                                 │
│    ├─ TrimStrings                                               │
│    └─ Authenticate (Check if user is logged in)               │
└──────────────────┬───────────────────────────────────────────────┘
                   │
┌──────────────────▼───────────────────────────────────────────────┐
│ 4. CUSTOM MIDDLEWARE                                             │
│    ├─ CheckRole (Verify user has required role)                │
│    ├─ LogUserActivity (Log user action)                        │
│    └─ AuditTrail (Prepare audit logging)                       │
└──────────────────┬───────────────────────────────────────────────┘
                   │
┌──────────────────▼───────────────────────────────────────────────┐
│ 5. ROUTE MATCHING                                                │
│    Route::get('/api/customers/{customer}',                      │
│        [CustomerApiController::class, 'show'])                  │
└──────────────────┬───────────────────────────────────────────────┘
                   │
┌──────────────────▼───────────────────────────────────────────────┐
│ 6. CONTROLLER - DEPENDENCY INJECTION                             │
│    public function show(                                         │
│        Customer $customer,                                       │
│        CustomerService $service                                  │
│    )                                                             │
└──────────────────┬───────────────────────────────────────────────┘
                   │
┌──────────────────▼───────────────────────────────────────────────┐
│ 7. SERVICE LAYER - BUSINESS LOGIC                                │
│    CustomerService::getDetailedCustomerInfo($customer)          │
│    ├─ Validate business rules                                   │
│    ├─ Check user permissions                                    │
│    ├─ Call repository methods                                   │
│    ├─ Transform data (DTO)                                      │
│    └─ Handle exceptions                                         │
└──────────────────┬───────────────────────────────────────────────┘
                   │
┌──────────────────▼───────────────────────────────────────────────┐
│ 8. REPOSITORY LAYER - DATA ACCESS                                │
│    CustomerRepository::getWithRelations($id)                    │
│    ├─ Build query with eager loading                            │
│    ├─ Apply filters and pagination                              │
│    ├─ Execute database query                                    │
│    └─ Return collection/model                                   │
└──────────────────┬───────────────────────────────────────────────┘
                   │
┌──────────────────▼───────────────────────────────────────────────┐
│ 9. MODEL LAYER - DATA REPRESENTATION                             │
│    Customer Model with:                                          │
│    ├─ Relationships (HasMany vehicles)                          │
│    ├─ Accessors/Mutators                                        │
│    ├─ Scopes                                                    │
│    └─ Methods                                                   │
└──────────────────┬───────────────────────────────────────────────┘
                   │
┌──────────────────▼───────────────────────────────────────────────┐
│ 10. DATABASE - QUERY EXECUTION                                   │
│    SELECT * FROM customers WHERE id = ? WITH relationships     │
└──────────────────┬───────────────────────────────────────────────┘
                   │
┌──────────────────▼───────────────────────────────────────────────┐
│ 11. RESPONSE TRANSFORMATION                                      │
│    ├─ EventListener: Dispatch CustomerViewed event              │
│    ├─ Serialize to CustomerResource                             │
│    ├─ Apply visible/hidden fields                               │
│    └─ Format as JSON                                            │
└──────────────────┬───────────────────────────────────────────────┘
                   │
┌──────────────────▼───────────────────────────────────────────────┐
│ 12. RESPONSE - HTTP                                              │
│    {                                                             │
│      "id": "550e8400-e29b-41d4-a716-446655440000",             │
│      "customer_code": "CUST-001",                               │
│      "name": "John Doe",                                        │
│      "vehicles": [...],                                         │
│      "services": [...]                                          │
│    }                                                             │
└──────────────────────────────────────────────────────────────────┘
```

---

## 4. SERVICE LAYER DESIGN

### 4.1 CustomerService Implementation

```php
<?php

namespace App\Services;

use App\DTO\CustomerDTO;
use App\Events\CustomerCreated;
use App\Events\CustomerUpdated;
use App\Exceptions\CustomerNotFoundException;
use App\Repositories\Contracts\CustomerRepositoryInterface;
use App\Repositories\Contracts\VehicleRepositoryInterface;
use Illuminate\Support\Collection;
use Illuminate\Pagination\Paginator;

class CustomerService
{
    public function __construct(
        private CustomerRepositoryInterface $customerRepository,
        private VehicleRepositoryInterface $vehicleRepository,
    ) {}

    /**
     * Create a new customer
     *
     * @param CustomerDTO $dto
     * @return CustomerDTO
     * @throws \Exception
     */
    public function createCustomer(CustomerDTO $dto): CustomerDTO
    {
        // Validate business rules
        $this->validateCustomerData($dto);
        
        // Check for duplicate email
        if ($this->customerRepository->findByEmail($dto->email)) {
            throw new \Exception('Email already exists');
        }

        // Create customer through repository
        $customer = $this->customerRepository->create($dto->toArray());

        // Dispatch event for audit trail
        event(new CustomerCreated($customer));

        return CustomerDTO::fromModel($customer);
    }

    /**
     * Update existing customer
     *
     * @param string $uuid
     * @param CustomerDTO $dto
     * @return CustomerDTO
     */
    public function updateCustomer(string $uuid, CustomerDTO $dto): CustomerDTO
    {
        $customer = $this->customerRepository->findByUuid($uuid);
        
        if (!$customer) {
            throw new CustomerNotFoundException("Customer not found");
        }

        // Validate business rules
        $this->validateCustomerData($dto);

        // Update customer
        $updated = $this->customerRepository->update($customer->id, $dto->toArray());

        // Dispatch event
        event(new CustomerUpdated($updated));

        return CustomerDTO::fromModel($updated);
    }

    /**
     * Get customer with all relationships
     *
     * @param string $uuid
     * @return CustomerDTO
     */
    public function getDetailedCustomerInfo(string $uuid): CustomerDTO
    {
        $customer = $this->customerRepository->findByUuidWithRelations(
            $uuid,
            ['vehicles', 'services', 'payments']
        );

        if (!$customer) {
            throw new CustomerNotFoundException("Customer not found");
        }

        // Enrich data
        $customerData = CustomerDTO::fromModel($customer);
        $customerData->vehicleCount = $customer->vehicles->count();
        $customerData->totalSpent = $customer->services->sum('total_cost');

        return $customerData;
    }

    /**
     * Get paginated list of customers
     *
     * @param array $filters
     * @param int $perPage
     * @return Paginator
     */
    public function getCustomers(array $filters = [], int $perPage = 15): Paginator
    {
        return $this->customerRepository->getFiltered($filters, $perPage);
    }

    /**
     * Delete customer (soft delete)
     *
     * @param string $uuid
     * @return bool
     */
    public function deleteCustomer(string $uuid): bool
    {
        $customer = $this->customerRepository->findByUuid($uuid);
        
        if (!$customer) {
            throw new CustomerNotFoundException("Customer not found");
        }

        return $this->customerRepository->delete($customer->id);
    }

    /**
     * Get customer service history
     *
     * @param string $uuid
     * @param int $limit
     * @return Collection
     */
    public function getServiceHistory(string $uuid, int $limit = 10): Collection
    {
        $customer = $this->customerRepository->findByUuid($uuid);
        
        if (!$customer) {
            throw new CustomerNotFoundException("Customer not found");
        }

        return $customer->services()
            ->latest()
            ->limit($limit)
            ->get();
    }

    /**
     * Validate customer data
     *
     * @param CustomerDTO $dto
     * @throws \InvalidArgumentException
     */
    private function validateCustomerData(CustomerDTO $dto): void
    {
        // Phone format validation
        if ($dto->phone && !preg_match('/^(\+62|0)[0-9]{9,12}$/', $dto->phone)) {
            throw new \InvalidArgumentException('Invalid phone number format');
        }

        // Email validation
        if ($dto->email && !filter_var($dto->email, FILTER_VALIDATE_EMAIL)) {
            throw new \InvalidArgumentException('Invalid email format');
        }

        // Name validation
        if (strlen($dto->name) < 3) {
            throw new \InvalidArgumentException('Name must be at least 3 characters');
        }
    }

    /**
     * Check if customer can be deleted
     * (business rule: can't delete if has ongoing services)
     *
     * @param string $uuid
     * @return bool
     */
    public function canDeleteCustomer(string $uuid): bool
    {
        $customer = $this->customerRepository->findByUuid($uuid);
        
        if (!$customer) {
            return false;
        }

        // Check for active services
        $activeServices = $customer->services()
            ->whereIn('status', ['intake', 'diagnosis', 'execution', 'qc'])
            ->count();

        return $activeServices === 0;
    }
}
```

### 4.2 Service Responsibilities

```
SERVICE LAYER RESPONSIBILITIES:

✓ Business Logic Execution
  ├─ Validate business rules
  ├─ Apply domain logic
  ├─ Enforce constraints
  └─ Handle workflows

✓ Data Orchestration
  ├─ Call multiple repositories
  ├─ Combine data from multiple sources
  ├─ Transform data (DTO)
  └─ Handle transactions

✓ Event Dispatching
  ├─ Fire domain events
  ├─ Trigger listeners
  ├─ Enable audit logging
  └─ Notify other systems

✓ Exception Handling
  ├─ Catch repository exceptions
  ├─ Convert to domain exceptions
  ├─ Log errors
  └─ Return meaningful messages

✓ Authorization Checks
  ├─ Verify user permissions
  ├─ Check resource access
  ├─ Enforce business rules
  └─ Audit access attempts
```

---

## 5. REPOSITORY PATTERN IMPLEMENTATION

### 5.1 Base Repository Contract

```php
<?php

namespace App\Repositories\Contracts;

use Illuminate\Database\Eloquent\Collection;
use Illuminate\Pagination\Paginator;

interface RepositoryInterface
{
    /**
     * Find resource by ID
     */
    public function find(int $id);

    /**
     * Find resource by UUID
     */
    public function findByUuid(string $uuid);

    /**
     * Get all resources
     */
    public function all(): Collection;

    /**
     * Create new resource
     */
    public function create(array $data);

    /**
     * Update resource
     */
    public function update(int $id, array $data);

    /**
     * Delete resource
     */
    public function delete(int $id): bool;

    /**
     * Get filtered and paginated resources
     */
    public function getFiltered(array $filters = [], int $perPage = 15): Paginator;

    /**
     * Get with eager loading relations
     */
    public function withRelations(array $relations);

    /**
     * Search resources
     */
    public function search(string $query): Collection;
}
```

### 5.2 Customer Repository Implementation

```php
<?php

namespace App\Repositories;

use App\Models\Customer;
use App\Repositories\Contracts\CustomerRepositoryInterface;
use Illuminate\Database\Eloquent\Collection;
use Illuminate\Pagination\Paginator;

class CustomerRepository extends BaseRepository implements CustomerRepositoryInterface
{
    protected $model = Customer::class;

    /**
     * Find customer by code
     */
    public function findByCode(string $code): ?Customer
    {
        return $this->model::where('customer_code', $code)->first();
    }

    /**
     * Find customer by email
     */
    public function findByEmail(string $email): ?Customer
    {
        return $this->model::where('email', $email)->first();
    }

    /**
     * Find customer with relations
     */
    public function findByUuidWithRelations(string $uuid, array $relations = []): ?Customer
    {
        return $this->model::with($relations)
            ->where('uuid', $uuid)
            ->first();
    }

    /**
     * Get filtered customers
     */
    public function getFiltered(array $filters = [], int $perPage = 15): Paginator
    {
        $query = $this->model::query();

        // Search by name
        if (!empty($filters['search'])) {
            $query->where('name', 'like', '%' . $filters['search'] . '%')
                ->orWhere('customer_code', 'like', '%' . $filters['search'] . '%')
                ->orWhere('phone', 'like', '%' . $filters['search'] . '%');
        }

        // Filter by status
        if (!empty($filters['status'])) {
            // Implement status filter (active/inactive)
        }

        // Filter by date range
        if (!empty($filters['from_date']) && !empty($filters['to_date'])) {
            $query->whereBetween('created_at', [
                $filters['from_date'],
                $filters['to_date']
            ]);
        }

        // Sort
        $sortBy = $filters['sort_by'] ?? 'created_at';
        $sortOrder = $filters['sort_order'] ?? 'desc';
        $query->orderBy($sortBy, $sortOrder);

        // Exclude soft deleted
        $query->whereNull('deleted_at');

        return $query->paginate($perPage);
    }

    /**
     * Search customers
     */
    public function search(string $query): Collection
    {
        return $this->model::where('name', 'like', "%{$query}%")
            ->orWhere('customer_code', 'like', "%{$query}%")
            ->orWhere('email', 'like', "%{$query}%")
            ->orWhere('phone', 'like', "%{$query}%")
            ->limit(20)
            ->get();
    }

    /**
     * Get customers with low activity
     * (No service in last 1 year)
     */
    public function getInactiveCustomers(): Collection
    {
        return $this->model::whereDoesntHave('services', function ($query) {
            $query->where('created_at', '>', now()->subYear());
        })->get();
    }

    /**
     * Get top customers by spending
     */
    public function getTopCustomers(int $limit = 10): Collection
    {
        return $this->model::withSum('services', 'total_cost')
            ->orderByDesc('services_sum_total_cost')
            ->limit($limit)
            ->get();
    }
}
```

### 5.3 Base Repository Class

```php
<?php

namespace App\Repositories;

use App\Repositories\Contracts\RepositoryInterface;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Collection;
use Illuminate\Pagination\Paginator;

abstract class BaseRepository implements RepositoryInterface
{
    protected $model;

    public function __construct()
    {
        $this->model = app($this->model);
    }

    public function find(int $id): ?Model
    {
        return $this->model::find($id);
    }

    public function findByUuid(string $uuid): ?Model
    {
        return $this->model::where('uuid', $uuid)->first();
    }

    public function all(): Collection
    {
        return $this->model::all();
    }

    public function create(array $data): Model
    {
        return $this->model::create($data);
    }

    public function update(int $id, array $data): Model
    {
        $model = $this->find($id);
        $model->update($data);
        return $model->fresh();
    }

    public function delete(int $id): bool
    {
        return $this->model::find($id)?->delete() ?? false;
    }

    public function getFiltered(array $filters = [], int $perPage = 15): Paginator
    {
        return $this->model::paginate($perPage);
    }

    public function withRelations(array $relations)
    {
        return $this->model::with($relations);
    }

    public function search(string $query): Collection
    {
        return $this->model::where('name', 'like', "%{$query}%")->get();
    }
}
```

---

## 6. MIDDLEWARE RECOMMENDATION

### 6.1 Middleware Stack Architecture

```
REQUEST
   │
   ├─► HTTP Kernel Middleware (Laravel Built-in)
   │   ├─ EncryptCookies
   │   ├─ AddQueuedCookiesToResponse
   │   ├─ StartSession
   │   ├─ AuthenticateSession
   │   ├─ ShareErrorsFromSession
   │   ├─ VerifyCsrfToken
   │   └─ SubstituteBindings
   │
   ├─► Custom Global Middleware
   │   ├─ Authenticate (Check if user is logged in)
   │   ├─ SetupLocalization (Set app locale)
   │   └─ CorsMiddleware (Handle CORS for API)
   │
   ├─► Route-Specific Middleware
   │   ├─ CheckRole (Verify user role)
   │   ├─ CheckPermission (Verify user permission)
   │   ├─ LogUserActivity (Log user actions)
   │   └─ AuditTrail (Audit logging for sensitive operations)
   │
   └─► CONTROLLER
        └─► SERVICE
            └─► REPOSITORY
                └─► DATABASE
```

### 6.2 Middleware Implementations

#### CheckRole Middleware
```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class CheckRole
{
    public function handle(Request $request, Closure $next, ...$roles): mixed
    {
        if (!auth()->check()) {
            return response()->json(['error' => 'Unauthorized'], 401);
        }

        if (!in_array(auth()->user()->role, $roles)) {
            return response()->json(['error' => 'Forbidden'], 403);
        }

        return $next($request);
    }
}
```

**Usage in routes:**
```php
Route::middleware('auth:sanctum')
    ->middleware('role:manager,admin')
    ->group(function () {
        Route::post('/customers', [CustomerController::class, 'store']);
        Route::put('/customers/{customer}', [CustomerController::class, 'update']);
        Route::delete('/customers/{customer}', [CustomerController::class, 'destroy']);
    });
```

#### LogUserActivity Middleware
```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Log;

class LogUserActivity
{
    public function handle(Request $request, Closure $next)
    {
        $response = $next($request);

        if (auth()->check()) {
            Log::info('User Activity', [
                'user_id' => auth()->id(),
                'action' => $request->method(),
                'path' => $request->path(),
                'ip' => $request->ip(),
                'timestamp' => now(),
            ]);
        }

        return $response;
    }
}
```

#### AuditTrail Middleware
```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class AuditTrail
{
    public function handle(Request $request, Closure $next)
    {
        // Store request data for audit logging
        $request->attributes->set('audit_data', [
            'method' => $request->method(),
            'path' => $request->path(),
            'ip' => $request->ip(),
            'user_id' => auth()->id(),
            'timestamp' => now(),
        ]);

        return $next($request);
    }
}
```

### 6.3 Middleware Configuration

**Register in `app/Http/Kernel.php`:**
```php
protected $routeMiddleware = [
    'auth' => \App\Http\Middleware\Authenticate::class,
    'role' => \App\Http\Middleware\CheckRole::class,
    'permission' => \App\Http\Middleware\CheckPermission::class,
    'log.activity' => \App\Http\Middleware\LogUserActivity::class,
    'audit' => \App\Http\Middleware\AuditTrail::class,
];
```

---

## 7. AUTHENTICATION FLOW

### 7.1 Authentication Architecture

```
┌────────────────────────────────────────────────────────────────┐
│ LOGIN REQUEST                                                  │
│ POST /login                                                    │
│ {username, password}                                           │
└─────────────────────────┬──────────────────────────────────────┘
                          │
┌─────────────────────────▼──────────────────────────────────────┐
│ AuthController@login                                           │
│ - Validate request                                             │
│ - Check credentials                                            │
└─────────────────────────┬──────────────────────────────────────┘
                          │
┌─────────────────────────▼──────────────────────────────────────┐
│ User Model                                                     │
│ - Find user by email/username                                 │
│ - Verify password hash                                        │
└─────────────────────────┬──────────────────────────────────────┘
                          │
                    ┌─────┴─────┐
                    │           │
            ✓ Valid     Invalid
                    │           │
     ┌──────────────▼──┐    ┌──▼─────────────────┐
     │ Create Token    │    │ Return Error       │
     │ (Sanctum/JWT)   │    │ 401 Unauthorized   │
     └────────┬────────┘    └────────────────────┘
              │
     ┌────────▼──────────────┐
     │ Return Token + User   │
     │ Store in Client       │
     │ (LocalStorage/Cookie) │
     └────────┬──────────────┘
              │
     ┌────────▼──────────────────────────────────────┐
     │ AUTHENTICATED REQUEST                         │
     │ Authorization: Bearer <token>                 │
     │ GET /api/customers                            │
     └────────┬──────────────────────────────────────┘
              │
     ┌────────▼──────────────────────────────────────┐
     │ Middleware: Authenticate                      │
     │ - Extract token from header                   │
     │ - Verify token signature                      │
     │ - Check token expiration                      │
     │ - Load user from token                        │
     └────────┬──────────────────────────────────────┘
              │
         ┌────┴────┐
         │          │
      Valid    Invalid/Expired
         │          │
    ┌────▼─┐   ┌───▼──────────┐
    │ 200  │   │ 401          │
    │ Next │   │ Unauthorized │
    └──────┘   └──────────────┘
```

### 7.2 Authentication Implementation

#### AuthController
```php
<?php

namespace App\Http\Controllers;

use App\Models\User;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;

class AuthController extends Controller
{
    /**
     * Login user
     */
    public function login(Request $request)
    {
        $credentials = $request->validate([
            'email' => ['required', 'email'],
            'password' => ['required'],
        ]);

        $user = User::where('email', $credentials['email'])->first();

        if (!$user || !Hash::check($credentials['password'], $user->password)) {
            return response()->json([
                'error' => 'Invalid credentials'
            ], 401);
        }

        // Create API token (Sanctum)
        $token = $user->createToken('auth_token')->plainTextToken;

        return response()->json([
            'token' => $token,
            'user' => [
                'id' => $user->id,
                'name' => $user->name,
                'email' => $user->email,
                'role' => $user->role,
            ]
        ]);
    }

    /**
     * Logout user
     */
    public function logout(Request $request)
    {
        $request->user()->currentAccessToken()->delete();

        return response()->json(['message' => 'Logged out']);
    }

    /**
     * Get authenticated user
     */
    public function me(Request $request)
    {
        return response()->json($request->user());
    }
}
```

#### User Model (Authentication)
```php
<?php

namespace App\Models;

use Laravel\Sanctum\HasApiTokens;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable
{
    use HasApiTokens;

    protected $fillable = [
        'name',
        'email',
        'password',
        'role',
    ];

    protected $hidden = [
        'password',
        'remember_token',
    ];

    protected $casts = [
        'email_verified_at' => 'datetime',
        'password' => 'hashed',
    ];

    /**
     * Check if user has role
     */
    public function hasRole(string $role): bool
    {
        return $this->role === $role;
    }

    /**
     * Check if user is admin
     */
    public function isAdmin(): bool
    {
        return $this->role === 'admin';
    }

    /**
     * Check if user is manager
     */
    public function isManager(): bool
    {
        return $this->role === 'manager';
    }
}
```

---

## 8. AUDIT LOGGING STRATEGY

### 8.1 Audit Trail Architecture

```
EVENT SYSTEM
    │
    ├─► Model Events (Eloquent)
    │   ├─ created
    │   ├─ updated
    │   ├─ deleted
    │   └─ restored
    │
    └─► Domain Events (Custom)
        ├─ CustomerCreated
        ├─ CustomerUpdated
        ├─ ServiceCompleted
        ├─ PaymentProcessed
        └─ InventoryAdjusted
             │
             └─► Event Listeners
                 │
                 ├─ LogAuditTrail
                 ├─ SendNotification
                 ├─ UpdateInventory
                 └─ GenerateReport
                      │
                      └─► Audit Log Table
                          ├─ id
                          ├─ user_id
                          ├─ action (created/updated/deleted)
                          ├─ model (Customer/Service/Payment)
                          ├─ model_id
                          ├─ changes (JSON: before/after)
                          ├─ ip_address
                          ├─ user_agent
                          └─ created_at
```

### 8.2 Audit Log Model & Migration

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class AuditLog extends Model
{
    protected $table = 'audit_logs';
    public $timestamps = false;

    protected $fillable = [
        'user_id',
        'action',
        'model_type',
        'model_id',
        'changes',
        'ip_address',
        'user_agent',
        'created_at',
    ];

    protected $casts = [
        'changes' => 'array',
        'created_at' => 'datetime',
    ];

    /**
     * Relationship: User who made the change
     */
    public function user()
    {
        return $this->belongsTo(User::class);
    }

    /**
     * Scope: Filter by action
     */
    public function scopeByAction($query, string $action)
    {
        return $query->where('action', $action);
    }

    /**
     * Scope: Filter by model
     */
    public function scopeByModel($query, string $modelType)
    {
        return $query->where('model_type', $modelType);
    }

    /**
     * Scope: Filter by date range
     */
    public function scopeByDateRange($query, $from, $to)
    {
        return $query->whereBetween('created_at', [$from, $to]);
    }
}
```

**Migration:**
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
            $table->enum('action', ['created', 'updated', 'deleted', 'viewed'])->default('created');
            $table->string('model_type'); // Customer, Service, etc.
            $table->string('model_id');   // UUID or ID
            $table->json('changes')->nullable(); // {before: {}, after: {}}
            $table->string('ip_address')->nullable();
            $table->string('user_agent')->nullable();
            $table->timestamp('created_at')->useCurrent();

            $table->index(['user_id', 'created_at']);
            $table->index(['model_type', 'model_id']);
            $table->index(['action', 'created_at']);
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('audit_logs');
    }
};
```

### 8.3 Audit Listener Implementation

```php
<?php

namespace App\Listeners;

use App\Models\AuditLog;
use Illuminate\Support\Facades\Request;

class LogAuditTrail
{
    /**
     * Handle the event
     */
    public function handle($event): void
    {
        if (!auth()->check()) {
            return;
        }

        // Determine action type
        $action = $this->getAction($event);

        // Get changes
        $changes = $this->getChanges($event);

        // Log to audit trail
        AuditLog::create([
            'user_id' => auth()->id(),
            'action' => $action,
            'model_type' => class_basename($event->model),
            'model_id' => $event->model->uuid ?? $event->model->id,
            'changes' => $changes,
            'ip_address' => Request::ip(),
            'user_agent' => Request::userAgent(),
            'created_at' => now(),
        ]);
    }

    /**
     * Determine action from event
     */
    private function getAction($event): string
    {
        return match(class_basename($event)) {
            'CustomerCreated' => 'created',
            'CustomerUpdated' => 'updated',
            'CustomerDeleted' => 'deleted',
            default => 'viewed',
        };
    }

    /**
     * Get changes made
     */
    private function getChanges($event): array
    {
        if (!isset($event->changes)) {
            return [];
        }

        return [
            'before' => $event->changes['before'] ?? [],
            'after' => $event->changes['after'] ?? [],
        ];
    }
}
```

### 8.4 Domain Event Example

```php
<?php

namespace App\Events;

use App\Models\Customer;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

class CustomerUpdated
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    public $model;
    public $changes;

    public function __construct(Customer $model, array $changes = [])
    {
        $this->model = $model;
        $this->changes = $changes;
    }
}
```

### 8.5 Event Listener Registration

**In `app/Providers/EventServiceProvider.php`:**
```php
protected $listen = [
    \App\Events\CustomerCreated::class => [
        \App\Listeners\LogAuditTrail::class,
        \App\Listeners\SendCustomerNotification::class,
    ],
    \App\Events\CustomerUpdated::class => [
        \App\Listeners\LogAuditTrail::class,
    ],
    \App\Events\ServiceCompleted::class => [
        \App\Listeners\LogAuditTrail::class,
        \App\Listeners\GenerateInvoice::class,
    ],
];
```

### 8.6 Audit Log Query Examples

```php
// View all customer-related changes
$logs = AuditLog::byModel('Customer')
    ->byDateRange(now()->subMonth(), now())
    ->orderByDesc('created_at')
    ->get();

// View changes by specific user
$logs = AuditLog::where('user_id', $userId)
    ->orderByDesc('created_at')
    ->paginate();

// View deletion history
$logs = AuditLog::byAction('deleted')
    ->byDateRange(now()->subDays(30), now())
    ->get();

// View changes to specific resource
$logs = AuditLog::where('model_type', 'Customer')
    ->where('model_id', $customerId)
    ->orderByDesc('created_at')
    ->get();
```

---

## 9. DESIGN PATTERNS SUMMARY

### 9.1 Applied Design Patterns

| Pattern | Usage | Benefit |
|---------|-------|---------|
| **Repository** | Data access abstraction | Loose coupling, testability |
| **Service** | Business logic | Reusability, maintainability |
| **Dependency Injection** | Constructor injection | Flexibility, testability |
| **Factory** | Object creation | Encapsulation, flexibility |
| **Observer/Event** | Audit logging | Decoupling, scalability |
| **DTO (Data Transfer Object)** | Data transformation | Type safety, validation |
| **Action** | Domain actions | Reusability, testability |
| **Strategy** | Multiple implementations | Flexibility, maintainability |
| **Decorator** | Add functionality | Single responsibility |
| **Facade** | Simplified interface | Easy usage |

### 9.2 SOLID Principles Applied

```
S - Single Responsibility
    ✓ Each class has one reason to change
    ✓ Service handles business logic only
    ✓ Repository handles data access only
    ✓ Controller handles HTTP only

O - Open/Closed
    ✓ Open for extension (traits, interfaces)
    ✓ Closed for modification (base classes)
    ✓ New features via listeners, not code changes

L - Liskov Substitution
    ✓ Repository implementations are interchangeable
    ✓ Service implementations follow contracts
    ✓ All repositories implement same interface

I - Interface Segregation
    ✓ Small, focused interfaces
    ✓ RepositoryInterface only has needed methods
    ✓ Services depend on specific interfaces

D - Dependency Inversion
    ✓ Depend on abstractions, not concretions
    ✓ Services depend on RepositoryInterface
    ✓ Controllers depend on Services
```

---

## 10. SCALABILITY CONSIDERATIONS

### 10.1 Database Optimization

```php
// Eager loading to prevent N+1 queries
$customers = Customer::with(['vehicles', 'services'])->get();

// Query optimization
$customers = Customer::select(['id', 'uuid', 'name', 'email'])
    ->with('vehicles:id,customer_id,vehicle_code')
    ->paginate();

// Indexing strategy
- Primary key: id
- Unique indexes: uuid, customer_code, email
- Search indexes: name, phone, customer_code
- Foreign key indexes: created_by
- Composite indexes: (deleted_at, created_at) for soft deletes

// Query caching
$customers = Cache::remember('customers_top_10', 3600, function() {
    return Customer::orderByDesc('total_spent')->limit(10)->get();
});
```

### 10.2 API Versioning

```php
// routes/api.php
Route::prefix('api/v1')->group(function () {
    Route::apiResource('customers', \App\Http\Controllers\Modules\Customer\CustomerApiController::class);
});

Route::prefix('api/v2')->group(function () {
    Route::apiResource('customers', \App\Http\Controllers\Api\V2\CustomerController::class);
});
```

### 10.3 Caching Strategy

```php
// Cache-aside pattern
$customer = Cache::remember("customer.{$uuid}", 3600, function() use ($uuid) {
    return $this->customerRepository->findByUuidWithRelations($uuid, ['vehicles']);
});

// Cache invalidation on update
event(new CustomerUpdated($customer));
// Listener invalidates cache:
Cache::forget("customer.{$customer->uuid}");
```

---

## 11. SECURITY BEST PRACTICES

### 11.1 Security Layers

```
Application Security
├─ Input Validation (Form Requests)
├─ Authorization (Policies/Gates)
├─ Authentication (Sanctum/JWT)
├─ CSRF Protection (Tokens)
├─ XSS Prevention (Blade escaping)
├─ SQL Injection Prevention (ORM/Queries)
├─ Rate Limiting (Middleware)
└─ Audit Logging (Events)

Infrastructure Security
├─ HTTPS/TLS (SSL certificates)
├─ Firewall (Network level)
├─ Environment Variables (.env)
├─ Secrets Management (Encryption)
├─ Database Encryption (Optional)
└─ Backup Encryption
```

### 11.2 Security Implementation

```php
// Password validation
protected function passwordRules(): array
{
    return [
        'required',
        'string',
        'min:8',
        'confirmed',
        'regex:/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&]/',
    ];
}

// Rate limiting
Route::middleware('throttle:60,1')->group(function () {
    Route::post('/login', [AuthController::class, 'login']);
});

// Permission check
if (!auth()->user()->can('update', $customer)) {
    abort(403, 'Unauthorized');
}
```

---

## 12. TESTING STRATEGY

```
Unit Tests
├─ Service layer logic
├─ Repository queries
├─ Validation rules
└─ Helpers/Traits

Feature Tests
├─ API endpoints
├─ Authentication flow
├─ Authorization checks
└─ Database transactions

Integration Tests
├─ Service to Repository
├─ Event dispatching
├─ Listener execution
└─ Database operations

Test Coverage Target: 70%+
```

---

## KESIMPULAN

Arsitektur ini dirancang untuk:
✓ **Scalability** - Handle growing business requirements
✓ **Maintainability** - Easy to understand and modify
✓ **Testability** - Each layer easily testable
✓ **Security** - Multiple protection layers
✓ **Performance** - Optimized queries and caching
✓ **Flexibility** - Add new features without breaking existing code

---

**END OF ARCHITECTURE DOCUMENTATION**
