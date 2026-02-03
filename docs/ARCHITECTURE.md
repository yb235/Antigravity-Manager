# Antigravity Tools - Architecture & Technical Overview 🏗️

This document provides a comprehensive technical deep-dive into the Antigravity Tools architecture, codebase structure, and implementation details.

## Table of Contents
- [System Architecture](#system-architecture)
- [Technology Stack](#technology-stack)
- [Core Components](#core-components)
- [Data Flow](#data-flow)
- [Backend Architecture](#backend-architecture)
- [Frontend Architecture](#frontend-architecture)
- [Protocol Conversion](#protocol-conversion)
- [Account Management](#account-management)
- [Security Model](#security-model)
- [Performance Optimizations](#performance-optimizations)
- [Deployment Architecture](#deployment-architecture)

## System Architecture

### High-Level Overview

Antigravity Tools follows a **hybrid desktop-server architecture**:

```
┌─────────────────────────────────────────────────────────────────┐
│                        User Interface Layer                      │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐       │
│  │Dashboard │  │Accounts  │  │API Proxy │  │Settings  │  ...  │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘       │
│         React 19 + TypeScript + TailwindCSS + Zustand          │
└────────────────────────┬────────────────────────────────────────┘
                         │ Tauri IPC (JSON-RPC)
┌────────────────────────┴────────────────────────────────────────┐
│                    Tauri Backend (Rust)                          │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │              Command Handlers (50+ Commands)              │  │
│  │  • Account CRUD    • OAuth Flow    • Proxy Control       │  │
│  │  • Token Stats     • Security      • Configuration       │  │
│  └──────────────────────────────────────────────────────────┘  │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                  Business Logic Layer                     │  │
│  │  • Account Manager  • Token Manager  • Quota Monitor      │  │
│  │  • OAuth Server     • Config Store   • Stats Collector    │  │
│  └──────────────────────────────────────────────────────────┘  │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                  Data Persistence Layer                   │  │
│  │  • SQLite (Stats)  • JSON Files (Accounts & Config)       │  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────┬────────────────────────────────────────┘
                         │ Axum Server (Embedded)
┌────────────────────────┴────────────────────────────────────────┐
│              API Gateway/Proxy Server (Axum + Tokio)            │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                  HTTP Endpoint Layer                      │  │
│  │  /v1/chat/completions    /v1/messages    /v1/models      │  │
│  │  /v1/images/generations  /health         /accounts/...   │  │
│  └────────────┬─────────────────────────┬───────────────────┘  │
│  ┌────────────┴─────────────────────────┴───────────────────┐  │
│  │                  Middleware Stack                         │  │
│  │  CORS → Auth → Logging → Monitor → Rate Limit → IP Check │  │
│  └────────────┬─────────────────────────┬───────────────────┘  │
│  ┌────────────┴─────────────────────────┴───────────────────┐  │
│  │              Protocol Handlers & Mappers                  │  │
│  │  • OpenAI Handler    • Anthropic Handler                  │  │
│  │  • Gemini Handler    • Request Normalization             │  │
│  │  • Response Mapping  • Streaming Adapters                │  │
│  └────────────┬─────────────────────────┬───────────────────┘  │
│  ┌────────────┴─────────────────────────┴───────────────────┐  │
│  │                  Token Pool Manager                       │  │
│  │  • Account Selection  • Rotation Logic  • Health Check    │  │
│  │  • Circuit Breaker    • Quota Protection                 │  │
│  └──────────────────────────────────────────────────────────┘  │
└────────────────────────┬────────────────────────────────────────┘
                         │ HTTPS/HTTP
┌────────────────────────┴────────────────────────────────────────┐
│                     Upstream AI Services                         │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐         │
│  │   Google     │  │  Anthropic   │  │    OpenAI    │         │
│  │   Gemini     │  │   Claude     │  │   (Optional) │         │
│  └──────────────┘  └──────────────┘  └──────────────┘         │
└─────────────────────────────────────────────────────────────────┘
```

### Key Architectural Principles

1. **Separation of Concerns**: Clear boundaries between UI, business logic, and data
2. **Async by Default**: Tokio-based async runtime for high concurrency
3. **Type Safety**: Rust's type system prevents common bugs at compile-time
4. **Resilience**: Circuit breakers, automatic retry, and graceful degradation
5. **Privacy First**: All data stored locally, no cloud dependencies

## Technology Stack

### Backend (Rust)

| Technology | Version | Purpose |
|------------|---------|---------|
| **Tauri** | 2.x | Desktop app framework, IPC bridge |
| **Axum** | 0.7.x | HTTP server framework |
| **Tokio** | 1.x | Async runtime |
| **Reqwest** | 0.11.x | HTTP client |
| **SQLite** | via Rusqlite | Stats and logs database |
| **Serde** | 1.x | Serialization/deserialization |
| **Tower** | 0.4.x | Middleware framework |
| **Anyhow** | 1.x | Error handling |
| **Tracing** | 0.1.x | Structured logging |

### Frontend (TypeScript/React)

| Technology | Version | Purpose |
|------------|---------|---------|
| **React** | 19.x | UI framework |
| **TypeScript** | 5.8.x | Type-safe JavaScript |
| **Vite** | 7.x | Build tool and dev server |
| **React Router** | 7.x | Client-side routing |
| **Zustand** | 5.x | State management |
| **TailwindCSS** | 3.4.x | Utility-first CSS |
| **DaisyUI** | 5.x | UI components |
| **i18next** | 25.x | Internationalization |
| **Recharts** | 3.x | Data visualization |
| **Framer Motion** | 11.x | Animations |

## Core Components

### 1. Tauri Application Layer

**File**: `src-tauri/src/lib.rs`, `src-tauri/src/main.rs`

The Tauri layer provides:
- Native window management
- System tray integration
- Auto-updater
- IPC command registration
- Plugin management (autostart, dialog, fs, etc.)

**Key Commands** (exposed to frontend):
```rust
// Account management
#[tauri::command]
async fn add_account(name: String, token: TokenData) -> Result<Account>;
#[tauri::command]
async fn list_accounts() -> Result<Vec<Account>>;
#[tauri::command]
async fn delete_account(id: String) -> Result<()>;
#[tauri::command]
async fn switch_account(id: String) -> Result<()>;

// OAuth flow
#[tauri::command]
async fn start_oauth_login() -> Result<OAuthSession>;
#[tauri::command]
async fn complete_oauth_login(session_id: String) -> Result<Account>;

// Proxy control
#[tauri::command]
async fn start_proxy_service(config: ProxyConfig) -> Result<()>;
#[tauri::command]
async fn stop_proxy_service() -> Result<()>;
#[tauri::command]
async fn get_proxy_status() -> Result<ProxyStatus>;

// Statistics
#[tauri::command]
async fn get_token_stats_hourly() -> Result<Vec<HourlyStats>>;
#[tauri::command]
async fn get_token_stats_by_account() -> Result<HashMap<String, AccountStats>>;
```

### 2. Account Manager

**File**: `src-tauri/src/modules/account.rs`, `src-tauri/src/modules/account_service.rs`

Manages the complete lifecycle of AI service accounts:

**Data Structure**:
```rust
pub struct Account {
    pub id: String,              // UUID v4
    pub name: String,            // Display name
    pub email: String,           // Associated email
    pub account_type: AccountType, // Google, Anthropic, OpenAI
    pub token_data: TokenData,   // Encrypted refresh token
    pub quota: Quota,            // Current quota state
    pub tags: Vec<String>,       // User-defined tags
    pub created_at: i64,         // Unix timestamp
    pub last_used: Option<i64>,  // Last API call timestamp
    pub disabled: bool,          // Manual disable flag
    pub disabled_models: Vec<String>, // Per-model disable
    pub device_fingerprint: Option<DeviceFingerprint>,
    pub priority: i32,           // Selection priority
}

pub struct Quota {
    pub gemini_pro: QuotaDetail,
    pub gemini_flash: QuotaDetail,
    pub claude: QuotaDetail,
    pub imagen: QuotaDetail,
    pub last_synced: i64,
}

pub struct QuotaDetail {
    pub used: u64,
    pub limit: u64,
    pub percentage: f64,
    pub reset_at: Option<i64>,
}
```

**Key Operations**:
- **CRUD**: Create, read, update, delete accounts
- **Persistence**: JSON files per account in `~/.antigravity_tools/accounts/`
- **Quota Sync**: Periodic refresh from upstream APIs
- **Health Check**: 403 detection, auto-disable on `invalid_grant`
- **Migration**: V1 → V4 data migration support

### 3. Token Manager

**File**: `src-tauri/src/proxy/token_manager.rs`

Implements intelligent account selection and rotation:

**Selection Algorithm**:
```rust
pub struct TokenManager {
    accounts: Arc<RwLock<Vec<Account>>>,
    circuit_breakers: Arc<RwLock<HashMap<String, CircuitBreaker>>>,
    session_affinity: Arc<RwLock<HashMap<String, String>>>, // session_id -> account_id
}

impl TokenManager {
    // Primary selection logic
    pub async fn select_account(
        &self, 
        model: &str,
        session_id: Option<&str>
    ) -> Result<Account> {
        // 1. Check session affinity (conversation continuity)
        if let Some(account_id) = self.get_session_account(session_id) {
            if self.is_account_healthy(account_id).await {
                return self.get_account(account_id).await;
            }
        }
        
        // 2. Filter eligible accounts
        let candidates = self.accounts.read().await
            .iter()
            .filter(|a| !a.disabled)
            .filter(|a| !a.disabled_models.contains(model))
            .filter(|a| self.get_quota_percentage(a, model) > 5.0)
            .filter(|a| !self.is_circuit_open(a.id))
            .collect::<Vec<_>>();
        
        // 3. Tiered routing (prioritize high-reset-rate accounts)
        let sorted = self.sort_by_tier_and_quota(candidates, model);
        
        // 4. Return best candidate
        sorted.first().ok_or(Error::NoAvailableAccount)
    }
    
    // Rotation on failure
    pub async fn on_request_failed(&self, account_id: &str, error: &Error) {
        match error {
            Error::RateLimit | Error::QuotaExhausted => {
                self.open_circuit(account_id, Duration::from_secs(300));
            },
            Error::Unauthorized | Error::InvalidGrant => {
                self.disable_account(account_id).await;
            },
            _ => {}
        }
    }
}
```

**Features**:
- **Tiered Routing**: Ultra > Pro > Free accounts
- **Quota Protection**: Auto-skip accounts below threshold
- **Session Affinity**: Same conversation → same account
- **Circuit Breaker**: Temporary disable on repeated failures
- **Smart Retry**: Millisecond-level failover on 429/401

### 4. OAuth Server

**File**: `src-tauri/src/modules/oauth_server.rs`

Implements OAuth 2.0 authorization code flow:

**Flow Diagram**:
```
User → Antigravity → Browser → Google OAuth → Callback → Antigravity → Save Token

1. User clicks "Add Account"
2. App generates OAuth URL with state token
3. App starts ephemeral HTTP server on random port (e.g., 8765)
4. Browser redirects to Google OAuth consent screen
5. User grants permissions
6. Google redirects to http://localhost:8765/callback?code=...&state=...
7. App validates state, exchanges code for token
8. App saves token, shuts down ephemeral server
```

**Implementation**:
```rust
pub struct OAuthServer {
    port: u16,
    state: String,
    result_tx: Sender<OAuthResult>,
}

impl OAuthServer {
    pub async fn start(&self) -> Result<()> {
        let app = Router::new()
            .route("/callback", get(Self::handle_callback))
            .layer(Extension(self.result_tx.clone()))
            .layer(Extension(self.state.clone()));
        
        let addr = SocketAddr::from(([127, 0, 0, 1], self.port));
        axum::Server::bind(&addr)
            .serve(app.into_make_service())
            .await?;
        
        Ok(())
    }
    
    async fn handle_callback(
        Query(params): Query<OAuthParams>,
        Extension(expected_state): Extension<String>,
        Extension(tx): Extension<Sender<OAuthResult>>,
    ) -> impl IntoResponse {
        // Validate state (CSRF protection)
        if params.state != expected_state {
            return Html("<h1>Error: Invalid state</h1>");
        }
        
        // Exchange code for token
        let token = exchange_code_for_token(&params.code).await?;
        
        // Send result
        tx.send(OAuthResult::Success(token)).await?;
        
        Html("<h1>Success! You can close this window.</h1>")
    }
}
```

### 5. Proxy Server

**File**: `src-tauri/src/proxy/server.rs`

HTTP gateway implementing multiple AI protocols:

**Server Structure**:
```rust
pub struct ProxyServer {
    config: Arc<RwLock<ProxyConfig>>,
    token_manager: Arc<TokenManager>,
    stats_collector: Arc<StatsCollector>,
    ip_monitor: Arc<IpMonitor>,
}

impl ProxyServer {
    pub async fn start(&self) -> Result<()> {
        let app = Router::new()
            // Health checks
            .route("/health", get(health_check))
            .route("/healthz", get(health_check))
            
            // OpenAI protocol
            .route("/v1/models", get(list_models))
            .route("/v1/chat/completions", post(handle_chat_completions))
            .route("/v1/completions", post(handle_completions))
            .route("/v1/images/generations", post(handle_image_generation))
            
            // Anthropic protocol
            .route("/v1/messages", post(handle_anthropic_messages))
            
            // Gemini protocol
            .route("/v1beta/models/:model_id/generateContent", post(handle_gemini_generate))
            .route("/v1beta/models/:model_id/streamGenerateContent", post(handle_gemini_stream))
            
            // Admin endpoints
            .route("/accounts/current", get(get_current_account))
            .route("/accounts/switch", post(switch_account))
            .route("/internal/warmup", post(warmup_accounts))
            
            // Middleware stack
            .layer(ServiceBuilder::new()
                .layer(middleware::from_fn(cors_middleware))
                .layer(middleware::from_fn(auth_middleware))
                .layer(middleware::from_fn(logging_middleware))
                .layer(middleware::from_fn(monitor_middleware))
                .layer(middleware::from_fn(ip_filter_middleware))
            )
            
            // Shared state
            .layer(Extension(self.config.clone()))
            .layer(Extension(self.token_manager.clone()))
            .layer(Extension(self.stats_collector.clone()))
            .layer(Extension(self.ip_monitor.clone()));
        
        let addr = SocketAddr::from((
            self.config.read().await.host.parse::<IpAddr>()?,
            self.config.read().await.port
        ));
        
        axum::Server::bind(&addr)
            .serve(app.into_make_service_with_connect_info::<SocketAddr>())
            .await?;
        
        Ok(())
    }
}
```

### 6. Protocol Handlers

**Files**: `src-tauri/src/proxy/handlers/`

Each protocol has a dedicated handler:

#### OpenAI Handler

```rust
pub async fn handle_chat_completions(
    Extension(token_mgr): Extension<Arc<TokenManager>>,
    Extension(config): Extension<Arc<RwLock<ProxyConfig>>>,
    Json(request): Json<OpenAIRequest>,
) -> Result<Response> {
    // 1. Extract session ID for affinity
    let session_id = extract_session_id(&request);
    
    // 2. Normalize model name
    let model = config.read().await
        .resolve_model_mapping(&request.model);
    
    // 3. Select account
    let account = token_mgr.select_account(&model, session_id.as_deref()).await?;
    
    // 4. Map OpenAI → Gemini request
    let gemini_request = openai_to_gemini(&request, &model)?;
    
    // 5. Make upstream request
    let response = make_gemini_request(&account, &gemini_request).await?;
    
    // 6. Map Gemini → OpenAI response
    let openai_response = gemini_to_openai(&response, &request)?;
    
    // 7. Record stats
    token_mgr.record_usage(&account.id, &model, request.tokens()).await;
    
    Ok(Json(openai_response).into_response())
}
```

#### Streaming Support

```rust
pub async fn handle_streaming(
    request: OpenAIRequest,
    account: Account,
) -> Result<Response> {
    // 1. Establish SSE connection
    let stream = make_gemini_stream(&account, &request).await?;
    
    // 2. Transform chunks on-the-fly
    let sse_stream = stream
        .map(|chunk| gemini_chunk_to_openai_sse(chunk))
        .map(|sse| format!("data: {}\n\n", serde_json::to_string(&sse)?));
    
    // 3. Return SSE response
    Ok(Response::builder()
        .header("Content-Type", "text/event-stream")
        .header("Cache-Control", "no-cache")
        .header("Connection", "keep-alive")
        .body(Body::from_stream(sse_stream))?)
}
```

### 7. Middleware Stack

**File**: `src-tauri/src/proxy/middleware/`

Layered middleware for cross-cutting concerns:

```rust
// 1. CORS
pub async fn cors_middleware(req: Request, next: Next) -> Response {
    let mut response = next.run(req).await;
    response.headers_mut().insert("Access-Control-Allow-Origin", "*".parse()?);
    response
}

// 2. Authentication
pub async fn auth_middleware(
    Extension(config): Extension<Arc<RwLock<ProxyConfig>>>,
    req: Request,
    next: Next,
) -> Result<Response> {
    let auth_mode = config.read().await.auth_mode;
    
    match auth_mode {
        ProxyAuthMode::Off => next.run(req).await,
        ProxyAuthMode::Strict => {
            validate_api_key(&req, &config.read().await.api_key)?;
            next.run(req).await
        },
        ProxyAuthMode::AllExceptHealth => {
            if req.uri().path().starts_with("/health") {
                next.run(req).await
            } else {
                validate_api_key(&req, &config.read().await.api_key)?;
                next.run(req).await
            }
        },
    }
}

// 3. Logging
pub async fn logging_middleware(req: Request, next: Next) -> Response {
    let method = req.method().clone();
    let path = req.uri().path().to_string();
    let start = Instant::now();
    
    let response = next.run(req).await;
    
    let duration = start.elapsed();
    tracing::info!(
        method = %method,
        path = %path,
        status = response.status().as_u16(),
        duration_ms = duration.as_millis(),
    );
    
    response
}

// 4. Monitoring (token counting)
pub async fn monitor_middleware(
    Extension(stats): Extension<Arc<StatsCollector>>,
    req: Request,
    next: Next,
) -> Response {
    let body = extract_body(&req).await;
    let tokens_in = estimate_tokens(&body);
    
    let response = next.run(req).await;
    
    let response_body = extract_body(&response).await;
    let tokens_out = estimate_tokens(&response_body);
    
    stats.record(TokenUsage {
        input: tokens_in,
        output: tokens_out,
        timestamp: Utc::now(),
    }).await;
    
    response
}

// 5. IP Filtering
pub async fn ip_filter_middleware(
    ConnectInfo(addr): ConnectInfo<SocketAddr>,
    Extension(monitor): Extension<Arc<IpMonitor>>,
    req: Request,
    next: Next,
) -> Result<Response> {
    let ip = addr.ip();
    
    if monitor.is_blacklisted(ip).await {
        return Err(Error::IpBlocked);
    }
    
    if !monitor.is_whitelisted(ip).await {
        return Err(Error::IpNotWhitelisted);
    }
    
    monitor.record_request(ip).await;
    
    Ok(next.run(req).await)
}
```

## Data Flow

### Request Flow (Chat Completion)

```
1. Client Request
   ↓
   curl -X POST http://localhost:8000/v1/chat/completions \
     -H "Authorization: Bearer sk-antigravity" \
     -d '{"model": "gpt-4", "messages": [...]}'

2. Middleware Stack
   ↓
   CORS → Auth (validate sk-antigravity) → Logging → Monitor → IP Check

3. Handler Selection
   ↓
   Route: POST /v1/chat/completions → handle_chat_completions()

4. Account Selection
   ↓
   TokenManager::select_account("gpt-4", session_id)
   → Filter: !disabled, quota>5%, !circuit_open
   → Sort: by tier (Ultra>Pro>Free) and quota percentage
   → Return: best_account@gmail.com

5. Model Mapping
   ↓
   Custom Mapping: "gpt-4" → "gemini-2.5-pro-exp"

6. Request Transformation
   ↓
   OpenAI format → Gemini format
   {
     "contents": [{"role": "user", "parts": [{"text": "..."}]}],
     "generationConfig": {...}
   }

7. Upstream Request
   ↓
   POST https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-pro-exp:generateContent
   Headers:
     Authorization: Bearer <access_token from account>
     X-Goog-Device-Fingerprint: <device_fp>

8. Response Handling
   ↓
   Success → Transform Gemini → OpenAI format
   Failure (429) → Circuit breaker → Retry with next account
   Failure (401) → Disable account → Retry with next account

9. Stats Recording
   ↓
   StatsCollector::record({
     account_id: "...",
     model: "gemini-2.5-pro-exp",
     tokens_in: 150,
     tokens_out: 800,
     duration_ms: 1200,
   })

10. Response
    ↓
    HTTP 200 OK
    {
      "id": "chatcmpl-...",
      "choices": [{
        "message": {"role": "assistant", "content": "..."},
        "finish_reason": "stop"
      }],
      "usage": {"prompt_tokens": 150, "completion_tokens": 800}
    }
```

## Backend Architecture

### Module Organization

```
src-tauri/src/
├── lib.rs                 # Tauri app entry, command registration
├── main.rs                # Main executable
├── error.rs               # Unified error types
├── commands/              # Tauri command handlers
│   ├── mod.rs
│   ├── proxy.rs           # Proxy control commands
│   ├── security.rs        # Security/IP commands
│   ├── autostart.rs       # System autostart
│   └── cloudflared.rs     # Cloudflare tunnel integration
├── models/                # Data models
│   ├── mod.rs
│   ├── account.rs         # Account struct
│   ├── config.rs          # Configuration structs
│   ├── quota.rs           # Quota tracking
│   └── token.rs           # Token/auth data
├── modules/               # Business logic
│   ├── account.rs         # Account CRUD
│   ├── account_service.rs # High-level operations
│   ├── oauth_server.rs    # OAuth flow
│   ├── config.rs          # Config management
│   └── stats.rs           # Statistics
├── proxy/                 # Proxy server
│   ├── server.rs          # Axum server setup
│   ├── token_manager.rs   # Account pool
│   ├── session_manager.rs # Session tracking
│   ├── monitor.rs         # Request monitoring
│   ├── handlers/          # Protocol handlers
│   │   ├── openai.rs      # OpenAI API
│   │   ├── anthropic.rs   # Anthropic API
│   │   ├── gemini.rs      # Gemini API
│   │   ├── image.rs       # Image generation
│   │   └── admin.rs       # Admin endpoints
│   ├── mappers/           # Request/response mapping
│   │   ├── openai/        # OpenAI transformations
│   │   ├── anthropic/     # Anthropic transformations
│   │   └── gemini/        # Gemini transformations
│   ├── middleware/        # HTTP middleware
│   │   ├── auth.rs        # Authentication
│   │   ├── cors.rs        # CORS headers
│   │   ├── logging.rs     # Request logging
│   │   ├── monitor.rs     # Token counting
│   │   └── ip_filter.rs   # IP whitelist/blacklist
│   └── zai_vision_mcp.rs  # z.ai integration
└── utils/                 # Utilities
    ├── http.rs            # HTTP helpers
    ├── protobuf.rs        # Protobuf parsing
    └── mod.rs
```

### Database Schema

**SQLite Database** (`~/.antigravity_tools/proxy.db`):

```sql
-- Token usage statistics
CREATE TABLE token_stats (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    timestamp INTEGER NOT NULL,
    account_id TEXT NOT NULL,
    model TEXT NOT NULL,
    input_tokens INTEGER NOT NULL,
    output_tokens INTEGER NOT NULL,
    duration_ms INTEGER NOT NULL,
    status_code INTEGER,
    error TEXT,
    session_id TEXT,
    client_ip TEXT
);

-- IP monitoring
CREATE TABLE ip_requests (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    ip TEXT NOT NULL,
    timestamp INTEGER NOT NULL,
    endpoint TEXT NOT NULL,
    status_code INTEGER NOT NULL,
    response_time_ms INTEGER
);

-- IP whitelist
CREATE TABLE ip_whitelist (
    ip TEXT PRIMARY KEY,
    description TEXT,
    added_at INTEGER NOT NULL
);

-- IP blacklist
CREATE TABLE ip_blacklist (
    ip TEXT PRIMARY KEY,
    reason TEXT,
    added_at INTEGER NOT NULL
);

-- Indexes for performance
CREATE INDEX idx_token_stats_timestamp ON token_stats(timestamp);
CREATE INDEX idx_token_stats_account ON token_stats(account_id);
CREATE INDEX idx_ip_requests_ip ON ip_requests(ip);
CREATE INDEX idx_ip_requests_timestamp ON ip_requests(timestamp);
```

## Frontend Architecture

### Component Hierarchy

```
src/
├── App.tsx                # Root component, router setup
├── main.tsx               # Entry point
├── pages/                 # Route pages
│   ├── Dashboard.tsx      # Home page with stats
│   ├── Accounts.tsx       # Account management
│   ├── ApiProxy.tsx       # Proxy configuration
│   ├── Security.tsx       # IP monitoring
│   ├── TokenStats.tsx     # Usage analytics
│   ├── Settings.tsx       # App settings
│   └── Monitor.tsx        # Real-time monitoring
├── components/            # Reusable UI components
│   ├── accounts/
│   │   ├── AccountTable.tsx      # List view
│   │   ├── AccountGrid.tsx       # Grid view
│   │   ├── AccountCard.tsx       # Card component
│   │   ├── AccountRow.tsx        # Table row
│   │   ├── QuotaItem.tsx         # Quota bar
│   │   ├── AddAccountDialog.tsx  # Add account modal
│   │   └── AccountDetailsDialog.tsx
│   ├── dashboard/
│   │   ├── StatsCard.tsx         # Metric card
│   │   ├── BestAccounts.tsx      # Recommended accounts
│   │   └── QuotaChart.tsx        # Quota visualization
│   └── common/
│       ├── Button.tsx
│       ├── Input.tsx
│       ├── Modal.tsx
│       └── Spinner.tsx
├── stores/                # State management (Zustand)
│   ├── useAccountStore.ts # Account state
│   ├── useConfigStore.ts  # Config state
│   ├── networkMonitorStore.ts # Monitoring state
│   └── useDebugConsole.ts # Debug logs
├── services/              # API communication
│   ├── accountService.ts  # Account operations
│   └── configService.ts   # Config operations
├── hooks/                 # Custom React hooks
│   ├── useAccounts.ts
│   ├── useConfig.ts
│   └── useStats.ts
├── types/                 # TypeScript types
│   ├── account.ts
│   ├── config.ts
│   └── stats.ts
├── utils/                 # Utility functions
│   ├── format.ts          # Formatting helpers
│   ├── request.ts         # HTTP wrappers
│   └── clipboard.ts       # Clipboard operations
├── locales/               # i18n translations
│   ├── en/
│   │   └── translation.json
│   └── zh/
│       └── translation.json
└── i18n.ts                # i18next config
```

### State Management

Using **Zustand** for lightweight, type-safe state:

```typescript
// stores/useAccountStore.ts
import create from 'zustand';

interface AccountStore {
  accounts: Account[];
  activeAccount: Account | null;
  loading: boolean;
  error: string | null;
  
  // Actions
  fetchAccounts: () => Promise<void>;
  addAccount: (account: Account) => Promise<void>;
  deleteAccount: (id: string) => Promise<void>;
  switchAccount: (id: string) => Promise<void>;
  refreshQuotas: () => Promise<void>;
}

export const useAccountStore = create<AccountStore>((set, get) => ({
  accounts: [],
  activeAccount: null,
  loading: false,
  error: null,
  
  fetchAccounts: async () => {
    set({ loading: true, error: null });
    try {
      const accounts = await invoke<Account[]>('list_accounts');
      set({ accounts, loading: false });
    } catch (error) {
      set({ error: error.message, loading: false });
    }
  },
  
  addAccount: async (account) => {
    await invoke('add_account', { account });
    await get().fetchAccounts();
  },
  
  // ... other actions
}));
```

### IPC Communication

**Frontend → Backend**:
```typescript
import { invoke } from '@tauri-apps/api/tauri';

// Type-safe invocation
const result = await invoke<Account[]>('list_accounts');

// With parameters
const account = await invoke<Account>('add_account', {
  name: 'My Account',
  tokenData: { /* ... */ }
});
```

**Backend → Frontend** (Events):
```typescript
// Frontend: Listen to events
import { listen } from '@tauri-apps/api/event';

listen<QuotaUpdate>('quota-updated', (event) => {
  console.log('Quota updated:', event.payload);
  // Update UI
});

// Backend: Emit events
app_handle.emit_all("quota-updated", QuotaUpdate { /* ... */ })?;
```

## Protocol Conversion

### OpenAI → Gemini

**Request Mapping**:
```rust
pub fn openai_to_gemini(request: &OpenAIRequest) -> GeminiRequest {
    GeminiRequest {
        contents: request.messages.iter().map(|msg| {
            GeminiContent {
                role: match msg.role.as_str() {
                    "system" => "user",  // Gemini doesn't have system role
                    "user" => "user",
                    "assistant" => "model",
                    _ => "user",
                },
                parts: vec![GeminiPart {
                    text: Some(msg.content.clone()),
                    inline_data: None,
                }],
            }
        }).collect(),
        
        generation_config: Some(GeminiGenerationConfig {
            temperature: request.temperature,
            top_p: request.top_p,
            top_k: request.top_k,
            max_output_tokens: request.max_tokens,
            stop_sequences: request.stop.clone(),
        }),
        
        safety_settings: vec![
            GeminiSafetySetting {
                category: "HARM_CATEGORY_HARASSMENT",
                threshold: "BLOCK_NONE",
            },
            // ... other categories
        ],
        
        tools: request.tools.as_ref().map(|tools| {
            tools.iter().map(|tool| {
                GeminiTool {
                    function_declarations: vec![GeminiFunctionDeclaration {
                        name: tool.function.name.clone(),
                        description: tool.function.description.clone(),
                        parameters: tool.function.parameters.clone(),
                    }],
                }
            }).collect()
        }),
    }
}
```

**Response Mapping**:
```rust
pub fn gemini_to_openai(response: &GeminiResponse, request: &OpenAIRequest) -> OpenAIResponse {
    let candidate = &response.candidates[0];
    let content = &candidate.content;
    
    OpenAIResponse {
        id: format!("chatcmpl-{}", uuid::Uuid::new_v4()),
        object: "chat.completion".to_string(),
        created: chrono::Utc::now().timestamp(),
        model: request.model.clone(),
        
        choices: vec![OpenAIChoice {
            index: 0,
            message: OpenAIMessage {
                role: "assistant".to_string(),
                content: content.parts[0].text.clone(),
                tool_calls: content.parts.iter()
                    .filter_map(|part| part.function_call.as_ref())
                    .map(|fc| OpenAIToolCall {
                        id: format!("call_{}", uuid::Uuid::new_v4()),
                        type_: "function".to_string(),
                        function: OpenAIFunction {
                            name: fc.name.clone(),
                            arguments: serde_json::to_string(&fc.args).unwrap(),
                        },
                    })
                    .collect(),
            },
            finish_reason: match candidate.finish_reason.as_str() {
                "STOP" => "stop",
                "MAX_TOKENS" => "length",
                "SAFETY" => "content_filter",
                _ => "stop",
            }.to_string(),
        }],
        
        usage: Some(OpenAIUsage {
            prompt_tokens: response.usage_metadata.prompt_token_count,
            completion_tokens: response.usage_metadata.candidates_token_count,
            total_tokens: response.usage_metadata.total_token_count,
        }),
    }
}
```

### Anthropic → Gemini

**Request Mapping**:
```rust
pub fn anthropic_to_gemini(request: &AnthropicRequest) -> GeminiRequest {
    let mut contents = vec![];
    
    // Handle system message separately
    if let Some(system) = &request.system {
        // Prepend as user message (Gemini limitation)
        contents.push(GeminiContent {
            role: "user".to_string(),
            parts: vec![GeminiPart {
                text: Some(format!("[System]: {}", system)),
                inline_data: None,
            }],
        });
    }
    
    // Convert messages
    for msg in &request.messages {
        contents.push(GeminiContent {
            role: match msg.role.as_str() {
                "user" => "user",
                "assistant" => "model",
                _ => "user",
            }.to_string(),
            parts: msg.content.iter().map(|content| {
                match content {
                    AnthropicContent::Text { text } => GeminiPart {
                        text: Some(text.clone()),
                        inline_data: None,
                    },
                    AnthropicContent::Image { source } => GeminiPart {
                        text: None,
                        inline_data: Some(GeminiBlob {
                            mime_type: source.media_type.clone(),
                            data: source.data.clone(),
                        }),
                    },
                }
            }).collect(),
        });
    }
    
    GeminiRequest {
        contents,
        generation_config: Some(GeminiGenerationConfig {
            temperature: request.temperature,
            max_output_tokens: request.max_tokens,
            top_p: request.top_p,
            top_k: request.top_k,
            ..Default::default()
        }),
        ..Default::default()
    }
}
```

## Security Model

### Authentication

**API Key Validation**:
```rust
pub fn validate_api_key(req: &Request, expected_key: &str) -> Result<()> {
    // Check Authorization header
    if let Some(auth) = req.headers().get("Authorization") {
        let auth_str = auth.to_str()?;
        if auth_str.starts_with("Bearer ") {
            let key = &auth_str[7..];
            if key == expected_key {
                return Ok(());
            }
        }
    }
    
    // Check x-api-key header
    if let Some(key) = req.headers().get("x-api-key") {
        if key.to_str()? == expected_key {
            return Ok(());
        }
    }
    
    // Check x-goog-api-key header (Gemini SDK compatibility)
    if let Some(key) = req.headers().get("x-goog-api-key") {
        if key.to_str()? == expected_key {
            return Ok(());
        }
    }
    
    Err(Error::Unauthorized)
}
```

### IP Filtering

```rust
pub struct IpMonitor {
    whitelist: Arc<RwLock<HashSet<IpAddr>>>,
    blacklist: Arc<RwLock<HashSet<IpAddr>>>,
    rate_limiter: Arc<RwLock<HashMap<IpAddr, RateLimitState>>>,
}

impl IpMonitor {
    pub async fn is_allowed(&self, ip: IpAddr) -> bool {
        // Check blacklist first
        if self.blacklist.read().await.contains(&ip) {
            return false;
        }
        
        // If whitelist is empty, allow all (except blacklisted)
        let whitelist = self.whitelist.read().await;
        if whitelist.is_empty() {
            return true;
        }
        
        // Check whitelist
        whitelist.contains(&ip)
    }
    
    pub async fn rate_limit(&self, ip: IpAddr) -> Result<()> {
        let mut limiter = self.rate_limiter.write().await;
        let state = limiter.entry(ip).or_insert(RateLimitState::new());
        
        if state.requests_in_window() > 100 {
            return Err(Error::RateLimitExceeded);
        }
        
        state.record_request();
        Ok(())
    }
}
```

### Token Encryption

```rust
use aes_gcm::{Aes256Gcm, Key, Nonce};
use aes_gcm::aead::{Aead, NewAead};

pub fn encrypt_token(token: &str, key: &[u8]) -> Result<Vec<u8>> {
    let cipher = Aes256Gcm::new(Key::from_slice(key));
    let nonce = Nonce::from_slice(b"unique nonce");
    
    cipher.encrypt(nonce, token.as_bytes())
        .map_err(|_| Error::EncryptionFailed)
}

pub fn decrypt_token(encrypted: &[u8], key: &[u8]) -> Result<String> {
    let cipher = Aes256Gcm::new(Key::from_slice(key));
    let nonce = Nonce::from_slice(b"unique nonce");
    
    let decrypted = cipher.decrypt(nonce, encrypted)
        .map_err(|_| Error::DecryptionFailed)?;
    
    String::from_utf8(decrypted)
        .map_err(|_| Error::InvalidToken)
}
```

## Performance Optimizations

### 1. Connection Pooling

```rust
use reqwest::Client;

lazy_static! {
    static ref HTTP_CLIENT: Client = Client::builder()
        .pool_max_idle_per_host(10)
        .pool_idle_timeout(Duration::from_secs(90))
        .timeout(Duration::from_secs(300))
        .build()
        .unwrap();
}
```

### 2. Async Everywhere

All I/O operations use Tokio's async runtime:
```rust
// Parallel quota fetching
async fn refresh_all_quotas(accounts: &[Account]) -> Result<Vec<Quota>> {
    let futures = accounts.iter()
        .map(|account| fetch_quota(account));
    
    let results = futures::future::join_all(futures).await;
    
    results.into_iter().collect()
}
```

### 3. Caching

```rust
use moka::future::Cache;

lazy_static! {
    static ref MODEL_CACHE: Cache<String, Vec<Model>> = Cache::builder()
        .max_capacity(100)
        .time_to_live(Duration::from_secs(300))
        .build();
}

pub async fn list_models() -> Result<Vec<Model>> {
    if let Some(cached) = MODEL_CACHE.get(&"models".to_string()).await {
        return Ok(cached);
    }
    
    let models = fetch_models_from_upstream().await?;
    MODEL_CACHE.insert("models".to_string(), models.clone()).await;
    
    Ok(models)
}
```

### 4. Streaming

All long-running requests use streaming to reduce latency:
```rust
pub async fn stream_response(request: Request) -> Result<Response> {
    let stream = make_upstream_request_stream(request).await?;
    
    let body = Body::from_stream(stream);
    
    Ok(Response::builder()
        .header("Content-Type", "text/event-stream")
        .body(body)?)
}
```

## Deployment Architecture

### Desktop Application

```
┌─────────────────────────────────────┐
│     User's Local Machine            │
│  ┌───────────────────────────────┐  │
│  │  Antigravity Tools Desktop    │  │
│  │  (Tauri App)                  │  │
│  │                               │  │
│  │  ┌─────────────────────────┐  │  │
│  │  │   React Frontend        │  │  │
│  │  │   (localhost:1420)      │  │  │
│  │  └───────────┬─────────────┘  │  │
│  │              │ IPC             │  │
│  │  ┌───────────┴─────────────┐  │  │
│  │  │   Rust Backend          │  │  │
│  │  │   - Account Manager     │  │  │
│  │  │   - Config Store        │  │  │
│  │  │   - OAuth Server        │  │  │
│  │  └───────────┬─────────────┘  │  │
│  │              │                 │  │
│  │  ┌───────────┴─────────────┐  │  │
│  │  │   Proxy Server          │  │  │
│  │  │   (localhost:8000)      │  │  │
│  │  └───────────┬─────────────┘  │  │
│  └──────────────┼─────────────────┘  │
│                 │                     │
│  ┌──────────────┴─────────────────┐  │
│  │  Local File System             │  │
│  │  ~/.antigravity_tools/         │  │
│  │  ├── accounts/                 │  │
│  │  ├── logs/                     │  │
│  │  ├── gui_config.json           │  │
│  │  └── proxy.db                  │  │
│  └────────────────────────────────┘  │
└───────────────────┬───────────────────┘
                    │ HTTPS
┌───────────────────┴───────────────────┐
│         Internet / Cloud              │
│  ┌──────────────┐  ┌──────────────┐  │
│  │   Google     │  │  Anthropic   │  │
│  │   Gemini API │  │  Claude API  │  │
│  └──────────────┘  └──────────────┘  │
└───────────────────────────────────────┘
```

### Docker Deployment

```
┌─────────────────────────────────────┐
│     Docker Host                     │
│  ┌───────────────────────────────┐  │
│  │  Antigravity Container        │  │
│  │  (headless mode)              │  │
│  │                               │  │
│  │  ┌─────────────────────────┐  │  │
│  │  │   Web UI                │  │  │
│  │  │   (http://localhost:8000)│ │  │
│  │  └───────────┬─────────────┘  │  │
│  │              │                 │  │
│  │  ┌───────────┴─────────────┐  │  │
│  │  │   Proxy Server          │  │  │
│  │  │   - OpenAI Endpoint     │  │  │
│  │  │   - Anthropic Endpoint  │  │  │
│  │  │   - Gemini Endpoint     │  │  │
│  │  │   - Admin API           │  │  │
│  │  └─────────────────────────┘  │  │
│  │                               │  │
│  │  Volume Mount:                │  │
│  │  /root/.antigravity_tools     │  │
│  └───────────────────────────────┘  │
│                                     │
│  ┌───────────────────────────────┐  │
│  │  Host File System             │  │
│  │  ~/.antigravity_tools/        │  │
│  │  (persisted)                  │  │
│  └───────────────────────────────┘  │
└─────────────────────────────────────┘
```

## Summary

**Key Takeaways**:

1. **Dual Architecture**: Desktop app (Tauri) + HTTP proxy server (Axum)
2. **Multi-Protocol**: OpenAI, Anthropic, Gemini - all in one
3. **Smart Routing**: Tier-based account selection with quota protection
4. **Resilient**: Circuit breakers, auto-retry, graceful degradation
5. **Type-Safe**: Rust backend, TypeScript frontend
6. **Privacy-First**: All data local, no cloud dependencies
7. **High-Performance**: Async runtime, connection pooling, streaming

**For More Information**:
- [Getting Started Guide](./GETTING_STARTED.md)
- [API Reference](./API_REFERENCE.md)
- [Workflow Guide](./WORKFLOW_GUIDE.md)
- [Source Code](https://github.com/lbjlaq/Antigravity-Manager)

---

**Version**: 4.0.15 | **Last Updated**: 2024-02-03
