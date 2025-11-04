# Laravel Backend Architecture

**ENF Platform - Electronic Notarization Facility**  
**Version:** 1.0  
**Last Updated:** November 2025

---

## Technology Stack Overview

The ENF Platform backend is built on **Laravel 12** (PHP 8.2+) with a modular architecture leveraging existing, battle-tested packages from the **sss-acop** codebase.

### Core Framework & Packages

```yaml
Backend Framework:
  Framework: Laravel 12
  PHP Version: ^8.2
  Frontend: Inertia.js + Vue.js
  Authentication: Laravel Sanctum
  
Key Dependencies:
  - bavix/laravel-wallet: ^11.4      # Wallet system (Commerce package)
  - spatie/laravel-data: ^4.15       # DTOs and data transfer
  - spatie/laravel-medialibrary: ^11.12  # Document/media handling
  - lorisleiva/laravel-actions: ^2.9  # Action-based architecture
  - tightenco/parental: ^1.4         # Single-table inheritance
  
Testing:
  - pestphp/pest: ^3.7               # Modern PHP testing
  - laravel/dusk: ^8.3               # Browser testing
```

---

## Existing Packages Integration

### 1. Commerce Package (`app/Commerce`)

**Purpose:** Handles all payment, wallet, and transaction operations

**Key Components:**

```
app/Commerce/
├── Models/
│   ├── Product.php           # Notarization service products
│   ├── Order.php             # Notarization orders
│   ├── Vendor.php            # ENP as vendor
│   ├── Transfer.php          # Money transfers
│   └── System.php            # Platform wallet
├── Actions/
│   └── [Transaction actions]
├── Events/
│   └── [Wallet events]
├── Services/
│   └── [Payment services]
└── Traits/
    └── CanTransferUnconfirmed.php
```

**Integration Strategy:**

1. **ENP as Vendor**
   - Each ENP is a `Vendor` model
   - Has associated wallet (via `bavix/laravel-wallet`)
   - Can set custom pricing/tariff

2. **Notarization as Product**
   - Each notarization service type is a `Product`
   - Dynamic pricing based on ENP tariff
   - Platform fee calculated automatically

3. **Transaction Flow**
   ```
   Customer → Order (notarization request)
           → Payment (wallet deduction or charge)
           → Transfer (split: platform fee + ENP fee)
           → Settlement (ENP can withdraw)
   ```

**Database Tables (from Commerce):**
```sql
-- Already exists in Commerce package
products (id, name, price, vendor_id, metadata)
orders (id, customer_id, vendor_id, product_id, amount, status)
wallets (id, holder_id, holder_type, balance, currency)
transactions (id, wallet_id, type, amount, meta, confirmed_at)
transfers (id, from_id, to_id, amount, status)
```

---

### 2. KYC Package (`app/KYC`)

**Purpose:** Identity verification using HyperVerge API

**Key Components:**

```
app/KYC/
├── Models/
│   └── Identification.php    # KYC verification records
├── Actions/
│   ├── VerifyIdentity.php
│   └── [KYC actions]
├── Data/
│   └── [DTOs for HyperVerge]
├── Enums/
│   ├── KYCIdType.php         # ID types (passport, drivers_license, etc.)
│   ├── HypervergeCountry.php
│   └── HypervergeDocument.php
├── Pipelines/
│   ├── MapIdType.php         # Transform ID types
│   └── TransformName.php     # Normalize names
├── Services/
│   ├── LivelinessService.php  # Liveness detection
│   ├── MatchFaceService.php   # Face matching
│   └── FaceVerificationPipeline.php
└── Traits/
    ├── HasKYCUser.php
    └── HasBridgeIdentifiers.php
```

**Integration Strategy:**

1. **User KYC Verification**
   ```php
   // Customer and ENP both use KYC
   class User extends Authenticatable {
       use HasKYCUser;
       
       // Relationship
       public function identifications() {
           return $this->hasMany(Identification::class);
       }
   }
   ```

2. **Verification Flow**
   ```
   1. Customer uploads ID + selfie
   2. LivelinessService::check($selfie)
   3. MatchFaceService::compare($selfie, $idPhoto)
   4. Store Identification record
   5. Update user KYC status
   ```

3. **HyperVerge Integration**
   ```php
   // From existing KYC package
   $result = app(FaceVerificationPipeline::class)->verify(
       referenceCode: $user->id,
       base64img: $request->selfie,
       storedImagePath: $user->getFirstMediaPath('profile')
   );
   
   // Returns: ['match' => true, 'confidence' => 0.95, 'liveness_passed' => true]
   ```

**Database Tables (from KYC):**
```sql
-- Already exists in KYC package
identifications (
    id, user_id, session_id,
    id_type, id_number, id_image_path,
    selfie_path, face_match_score,
    liveness_score, liveness_passed,
    verification_status, verified_at
)
```

---

## ENF-Specific Models & Architecture

### Core ENF Models

```php
namespace App\Models;

// ENP (Electronic Notary Public)
class ENP extends User {
    use HasWallet, HasKYCUser;
    
    protected $table = 'users';
    protected $type = 'enp';
    
    public function profile() {
        return $this->hasOne(ENPProfile::class);
    }
    
    public function notarizations() {
        return $this->hasMany(Notarization::class);
    }
}

// ENP Profile (additional ENP data)
class ENPProfile extends Model {
    protected $fillable = [
        'enp_id', 'license_number', 'commission_date',
        'commission_expiry', 'specializations', 'tariff',
        'is_available', 'rating', 'total_notarizations'
    ];
    
    protected $casts = [
        'specializations' => 'array',
        'tariff' => 'decimal:2',
        'is_available' => 'boolean'
    ];
}

// Customer
class Customer extends User {
    use HasWallet, HasKYCUser;
    
    protected $table = 'users';
    protected $type = 'customer';
    
    public function notarizations() {
        return $this->hasMany(Notarization::class);
    }
    
    public function favoriteENPs() {
        return $this->belongsToMany(ENP::class, 'customer_enp_favorites');
    }
}

// Notarization (core model)
class Notarization extends Model {
    protected $fillable = [
        'customer_id', 'enp_id', 'document_id',
        'status', 'amount', 'platform_fee', 'enp_fee',
        'video_room_id', 'video_recording_url',
        'video_duration_seconds',
        'comprehension_confirmed', 'duress_check_passed',
        'truth_envelope', 'blockchain_tx_hash',
        'notarized_at', 'completed_at'
    ];
    
    protected $casts = [
        'comprehension_confirmed' => 'boolean',
        'duress_check_passed' => 'boolean',
        'truth_envelope' => 'array',
        'notarized_at' => 'datetime',
        'completed_at' => 'datetime'
    ];
    
    public function customer() {
        return $this->belongsTo(Customer::class);
    }
    
    public function enp() {
        return $this->belongsTo(ENP::class);
    }
    
    public function document() {
        return $this->belongsTo(Document::class);
    }
}

// Document
class Document extends Model {
    use HasMedia; // Spatie MediaLibrary
    
    protected $fillable = [
        'user_id', 'original_filename', 'file_hash',
        'document_type', 'is_notarized', 'metadata'
    ];
    
    protected $casts = [
        'is_notarized' => 'boolean',
        'metadata' => 'array'
    ];
    
    public function notarization() {
        return $this->hasOne(Notarization::class);
    }
}
```

### Database Schema (New Tables)

```sql
-- ENP Profiles
CREATE TABLE enp_profiles (
    id BIGINT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
    enp_id BIGINT UNSIGNED NOT NULL,
    license_number VARCHAR(255) UNIQUE NOT NULL,
    commission_date DATE NOT NULL,
    commission_expiry DATE NOT NULL,
    specializations JSON,
    tariff DECIMAL(10,2) DEFAULT 300.00,
    is_available BOOLEAN DEFAULT true,
    rating DECIMAL(3,2) DEFAULT 0.00,
    total_notarizations INT DEFAULT 0,
    created_at TIMESTAMP,
    updated_at TIMESTAMP,
    FOREIGN KEY (enp_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_available (is_available),
    INDEX idx_rating (rating)
);

-- Notarizations
CREATE TABLE notarizations (
    id BIGINT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
    customer_id BIGINT UNSIGNED NOT NULL,
    enp_id BIGINT UNSIGNED,
    document_id BIGINT UNSIGNED NOT NULL,
    status ENUM('requested', 'assigned', 'in_progress', 'completed', 'cancelled', 'failed') DEFAULT 'requested',
    amount DECIMAL(10,2) NOT NULL,
    platform_fee DECIMAL(10,2) NOT NULL,
    enp_fee DECIMAL(10,2) NOT NULL,
    video_room_id VARCHAR(255),
    video_recording_url TEXT,
    video_duration_seconds INT,
    comprehension_confirmed BOOLEAN DEFAULT false,
    duress_check_passed BOOLEAN DEFAULT false,
    truth_envelope JSON,
    blockchain_tx_hash VARCHAR(255),
    created_at TIMESTAMP,
    assigned_at TIMESTAMP,
    video_started_at TIMESTAMP,
    video_ended_at TIMESTAMP,
    notarized_at TIMESTAMP,
    completed_at TIMESTAMP,
    FOREIGN KEY (customer_id) REFERENCES users(id),
    FOREIGN KEY (enp_id) REFERENCES users(id),
    FOREIGN KEY (document_id) REFERENCES documents(id),
    INDEX idx_status (status),
    INDEX idx_customer (customer_id),
    INDEX idx_enp (enp_id),
    INDEX idx_created (created_at DESC)
);

-- Documents
CREATE TABLE documents (
    id BIGINT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
    user_id BIGINT UNSIGNED NOT NULL,
    original_filename VARCHAR(255) NOT NULL,
    file_hash VARCHAR(64) NOT NULL,
    document_type VARCHAR(100),
    is_notarized BOOLEAN DEFAULT false,
    metadata JSON,
    created_at TIMESTAMP,
    updated_at TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id),
    INDEX idx_user (user_id),
    INDEX idx_hash (file_hash),
    INDEX idx_notarized (is_notarized)
);

-- Customer-ENP Favorites
CREATE TABLE customer_enp_favorites (
    id BIGINT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
    customer_id BIGINT UNSIGNED NOT NULL,
    enp_id BIGINT UNSIGNED NOT NULL,
    created_at TIMESTAMP,
    FOREIGN KEY (customer_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (enp_id) REFERENCES users(id) ON DELETE CASCADE,
    UNIQUE KEY unique_favorite (customer_id, enp_id)
);
```

---

## Action-Based Architecture

Using `lorisleiva/laravel-actions`, we structure business logic as reusable actions:

### Example Actions

```php
namespace App\Actions\Notarization;

use Lorisleiva\Actions\Concerns\AsAction;

class CreateNotarizationRequest
{
    use AsAction;
    
    public function handle(Customer $customer, Document $document, ?ENP $enp = null)
    {
        // Calculate fees
        $amount = $enp?->profile->tariff ?? 400;
        $platformFee = $amount * 0.25;
        $enpFee = $amount - $platformFee;
        
        // Create notarization
        $notarization = Notarization::create([
            'customer_id' => $customer->id,
            'enp_id' => $enp?->id,
            'document_id' => $document->id,
            'status' => 'requested',
            'amount' => $amount,
            'platform_fee' => $platformFee,
            'enp_fee' => $enpFee
        ]);
        
        // Hold funds in escrow (using Commerce package)
        $customer->wallet->withdraw($amount, [
            'description' => "Notarization #{$notarization->id}",
            'meta' => ['notarization_id' => $notarization->id]
        ]);
        
        // Notify ENP
        NotifyENPOfNewRequest::dispatch($notarization);
        
        return $notarization;
    }
    
    public function asController(Request $request)
    {
        $this->authorize('create', Notarization::class);
        
        $validated = $request->validate([
            'document_id' => 'required|exists:documents,id',
            'enp_id' => 'nullable|exists:users,id'
        ]);
        
        $notarization = $this->handle(
            $request->user(),
            Document::find($validated['document_id']),
            $validated['enp_id'] ? ENP::find($validated['enp_id']) : null
        );
        
        return response()->json($notarization);
    }
}
```

```php
namespace App\Actions\Notarization;

class CompleteNotarization
{
    use AsAction;
    
    public function handle(Notarization $notarization, array $truthEnvelope)
    {
        // Update notarization
        $notarization->update([
            'status' => 'completed',
            'truth_envelope' => $truthEnvelope,
            'notarized_at' => now(),
            'completed_at' => now()
        ]);
        
        // Release funds using Commerce package
        $platformWallet = System::platform()->wallet;
        $enpWallet = $notarization->enp->wallet;
        
        // Transfer platform fee
        Transfer::create([
            'from_id' => $notarization->customer->wallet->id,
            'to_id' => $platformWallet->id,
            'amount' => $notarization->platform_fee,
            'status' => 'completed'
        ]);
        
        // Transfer ENP fee
        Transfer::create([
            'from_id' => $notarization->customer->wallet->id,
            'to_id' => $enpWallet->id,
            'amount' => $notarization->enp_fee,
            'status' => 'completed'
        ]);
        
        // Update ENP stats
        $notarization->enp->profile->increment('total_notarizations');
        
        // Notify all parties
        NotifyNotarizationComplete::dispatch($notarization);
        
        return $notarization;
    }
}
```

---

## API Routes Structure

```php
// routes/api.php

use App\Actions\Notarization;
use App\Actions\Payment;
use App\Actions\KYC;

Route::middleware('auth:sanctum')->group(function () {
    
    // KYC Routes (using existing KYC package)
    Route::prefix('kyc')->group(function () {
        Route::post('/upload-id', KYC\UploadIDAction::class);
        Route::post('/upload-selfie', KYC\UploadSelfieAction::class);
        Route::post('/verify', KYC\VerifyIdentityAction::class);
        Route::get('/status', KYC\GetKYCStatusAction::class);
    });
    
    // Notarization Routes
    Route::prefix('notarizations')->group(function () {
        Route::get('/', Notarization\ListNotarizationsAction::class);
        Route::post('/', Notarization\CreateNotarizationRequest::class);
        Route::get('/{notarization}', Notarization\GetNotarizationAction::class);
        Route::post('/{notarization}/assign-enp', Notarization\AssignENPAction::class);
        Route::post('/{notarization}/start-video', Notarization\StartVideoCallAction::class);
        Route::post('/{notarization}/complete', Notarization\CompleteNotarization::class);
        Route::post('/{notarization}/cancel', Notarization\CancelNotarizationAction::class);
    });
    
    // Payment & Wallet Routes (using Commerce package)
    Route::prefix('wallet')->group(function () {
        Route::get('/', Payment\GetWalletBalanceAction::class);
        Route::post('/topup', Payment\TopUpWalletAction::class);
        Route::get('/transactions', Payment\GetTransactionsAction::class);
        Route::post('/payout', Payment\RequestPayoutAction::class);
    });
    
    // ENP Routes
    Route::prefix('enps')->group(function () {
        Route::get('/available', Notarization\GetAvailableENPsAction::class);
        Route::get('/{enp}', Notarization\GetENPProfileAction::class);
        Route::post('/favorites/{enp}', Notarization\AddFavoriteENPAction::class);
    });
    
    // Document Routes
    Route::prefix('documents')->group(function () {
        Route::post('/upload', Notarization\UploadDocumentAction::class);
        Route::get('/{document}', Notarization\GetDocumentAction::class);
        Route::get('/{document}/download', Notarization\DownloadDocumentAction::class);
    });
});
```

---

## Service Providers

### ENF Service Provider

```php
namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use App\Services\TRUTHEngine;
use App\Services\VideoService;
use App\Services\NotarizationService;

class ENFServiceProvider extends ServiceProvider
{
    public function register()
    {
        // Register TRUTH Engine
        $this->app->singleton(TRUTHEngine::class, function ($app) {
            return new TRUTHEngine(
                config('services.truth.api_key'),
                config('services.truth.endpoint')
            );
        });
        
        // Register Video Service (Twilio)
        $this->app->singleton(VideoService::class, function ($app) {
            return new VideoService(
                config('services.twilio.account_sid'),
                config('services.twilio.auth_token')
            );
        });
        
        // Register Notarization Service
        $this->app->singleton(NotarizationService::class);
    }
    
    public function boot()
    {
        // Load migrations
        $this->loadMigrationsFrom(__DIR__ . '/../database/migrations');
        
        // Load routes
        $this->loadRoutesFrom(__DIR__ . '/../routes/enf.php');
    }
}
```

---

## Queue Jobs

```php
namespace App\Jobs;

use App\Models\Notarization;
use App\Services\TRUTHEngine;
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;

class GenerateTRUTHEnvelope implements ShouldQueue
{
    use Queueable;
    
    public function __construct(
        public Notarization $notarization
    ) {}
    
    public function handle(TRUTHEngine $truth)
    {
        $envelope = $truth->canonicalize([
            'uid' => "ENF-{$notarization->id}",
            'timestamp' => $notarization->notarized_at,
            'document_hash' => $notarization->document->file_hash,
            'customer' => [
                'kyc_session_id' => $notarization->customer->latestKYC->session_id,
                'identity_verified' => true,
                'face_match_score' => $notarization->customer->latestKYC->face_match_score
            ],
            'enp' => [
                'license_number' => $notarization->enp->profile->license_number,
                'name' => $notarization->enp->name,
                'digital_signature' => $notarization->enp_signature
            ],
            'session' => [
                'video_recording_url' => $notarization->video_recording_url,
                'duration_seconds' => $notarization->video_duration_seconds,
                'comprehension_confirmed' => $notarization->comprehension_confirmed,
                'duress_check_passed' => $notarization->duress_check_passed
            ]
        ]);
        
        $notarization->update(['truth_envelope' => $envelope]);
        
        // Optional: Anchor to blockchain
        if (config('enf.blockchain_enabled')) {
            AnchorToBlockchain::dispatch($notarization);
        }
    }
}
```

---

## Testing Strategy

### Feature Tests (Pest)

```php
// tests/Feature/NotarizationTest.php

use App\Models\Customer;
use App\Models\ENP;
use App\Models\Document;

test('customer can create notarization request', function () {
    $customer = Customer::factory()->verified()->create();
    $customer->wallet->deposit(1000);
    
    $document = Document::factory()->for($customer)->create();
    $enp = ENP::factory()->available()->create();
    
    $response = $this->actingAs($customer)
        ->postJson('/api/notarizations', [
            'document_id' => $document->id,
            'enp_id' => $enp->id
        ]);
    
    $response->assertCreated();
    expect($customer->wallet->balance)->toBeLessThan(1000);
});

test('enp can complete notarization', function () {
    $notarization = Notarization::factory()->inProgress()->create();
    $enp = $notarization->enp;
    
    $response = $this->actingAs($enp)
        ->postJson("/api/notarizations/{$notarization->id}/complete", [
            'comprehension_confirmed' => true,
            'duress_check_passed' => true,
            'video_duration' => 180
        ]);
    
    $response->assertOk();
    expect($notarization->fresh()->status)->toBe('completed');
    expect($enp->wallet->balance)->toBeGreaterThan(0);
});
```

---

## Configuration

### `config/enf.php`

```php
return [
    'platform_fee_percentage' => env('ENF_PLATFORM_FEE', 25),
    'default_notarization_price' => env('ENF_DEFAULT_PRICE', 400),
    'blockchain_enabled' => env('ENF_BLOCKCHAIN_ENABLED', false),
    'blockchain_network' => env('ENF_BLOCKCHAIN_NETWORK', 'ethereum-mainnet'),
    'min_enp_payout' => env('ENF_MIN_PAYOUT', 1000),
    'video_session_timeout' => env('ENF_VIDEO_TIMEOUT', 3600),
    'document_retention_years' => 10,
];
```

### `config/services.php`

```php
'truth' => [
    'api_key' => env('TRUTH_API_KEY'),
    'endpoint' => env('TRUTH_ENDPOINT', 'https://truth.engine.api'),
],

'twilio' => [
    'account_sid' => env('TWILIO_ACCOUNT_SID'),
    'auth_token' => env('TWILIO_AUTH_TOKEN'),
    'api_key' => env('TWILIO_API_KEY'),
    'api_secret' => env('TWILIO_API_SECRET'),
],

'hyperverge' => [
    'app_id' => env('HYPERVERGE_APP_ID'),
    'app_key' => env('HYPERVERGE_APP_KEY'),
    'endpoint' => env('HYPERVERGE_ENDPOINT', 'https://ind.docs.hyperverge.co'),
],

'paymongo' => [
    'public_key' => env('PAYMONGO_PUBLIC_KEY'),
    'secret_key' => env('PAYMONGO_SECRET_KEY'),
],
```

---

## Development Workflow

### Setup

```bash
# Clone repository
git clone <repo-url>
cd enf-platform

# Install dependencies
composer install
npm install

# Environment setup
cp .env.example .env
php artisan key:generate

# Database setup
php artisan migrate
php artisan db:seed

# Run development servers
php artisan serve
npm run dev
php artisan queue:work
```

### Key Commands

```bash
# Run tests
php artisan test
# or
./vendor/bin/pest

# Code style
./vendor/bin/pint

# Create action
php artisan make:action Notarization/CreateNotarizationRequest

# Create model with migration
php artisan make:model Notarization -m

# Queue worker
php artisan queue:work --tries=3

# Horizon (queue dashboard)
php artisan horizon
```

---

## Next Steps

1. **Refactor existing sss-acop packages** for ENF-specific needs
2. **Implement TRUTH Engine service** integration
3. **Set up Twilio Video** for REN sessions
4. **Configure HyperVerge** for KYC in production
5. **Implement blockchain anchoring** (optional)
6. **Build admin dashboard** with Inertia.js
7. **Set up Supreme Court reporting** pipeline

---

**This architecture leverages proven Laravel patterns and existing battle-tested packages to build a robust, compliant ENF platform.**
