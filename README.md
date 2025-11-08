# realDAX - Complete Crypto Exchange Platform

A comprehensive, enterprise-grade cryptocurrency exchange system following **realDAX-OpenDAX architecture** with high-frequency trading, institutional-grade double-entry accounting, complete fiat integration, and professional monitoring.

## 🏗️ Architecture Overview (OpenDAX-Aligned)

```
┌─────────────────────────────────────────────────────────────────┐
│       BaseApp (Trading Console) + Tower Admin Dashboard         │
│   Trading | Wallet | KYC | Charts | Admin Panel                 │
└──────────┬──────────────────────────────────┬───────────────────┘
           │ REST API / WebSocket             │ Admin API
┌──────────┴────────────────────────┬─────────┴───────────────────┐
│     Gateway (API Gateway)         │   Tower (Admin Management)  │
│ Routing | Rate Limit | Auth       │   Config | Reports | Users  │
└──┬───┬────┬────┬─────┬──────┬─────┴─────────────────────────────┘
   │   │    │    │     │      │
┌──▼─┐ │ ┌──▼┐   │  ┌──▼──┐   │   ┌────▼─────┐    ┌─────────┐
│Open│ │ │Bar│   │  │Peat-│   │   │Settlement│    │AppLogic │
│Finex │ │ong│   │  │io   │   │   │(Go)      │    │(Fiat)   │
│(Go)│ │ │(JS)   │  │(Node)   │   └──────────┘    │(Node)   │
└────┘ │ └───┘   │  └─────┘   │                   └─────────┘
       │         │     │      │                        │
┌──────┴─────────┴─────┴──────┴────────────────────────┴────────┐
│        PostgreSQL (Primary) + Redis (Cache/PubSub)            │
└───────────────────────────────────────────────────────────────┘
┌─────────────────────┐  ┌──────────────────────────────────┐
│ InfluxDB (Charts &  │  │ Prometheus + Grafana (Monitoring)│
│ Historical Data)    │  │ Metrics | Alerts | Dashboards    │
└─────────────────────┘  └──────────────────────────────────┘
```

## 📦 Core Components (Full realDAX-OpenDAX Stack)

| Component      | Port | Description                      | Technology    | Status |
|----------------|------|----------------------------------|---------------|--------|
| **OpenFinex™** | 8001 | Matching engine (millions TPS)   | Go 1.21       | ✅ Complete |
| **Peatio**     | 8003 | Wallet + Double-entry accounting | Node.js/TS    | ✅ Enhanced |
| **Barong**     | 8002 | Auth, KYC/AML, identity          | Node.js/TS    | ✅ Complete |
| **Gateway**    | 8000 | API Gateway & routing            | Node.js       | ✅ Complete |
| **Tower**      | 8005 | Admin management dashboard       | Node.js/TS    | ✅ NEW |
| **AppLogic**   | 8006 | Fiat on/off ramps                | Node.js/TS    | ✅ NEW |
| **Settlement** | 8004 | Trade clearing engine            | Go 1.21       | ✅ Complete |
| **BaseApp**    | 3000 | React trading console            | React 18      | ✅ Complete |
| **PostgreSQL** | 5432 | Primary database                 | PostgreSQL 15 | ✅ Complete |
| **Redis**      | 6379 | Cache & messaging                | Redis 7       | ✅ Complete |
| **InfluxDB**   | 8086 | Time-series (charts)             | InfluxDB 2.7  | ✅ NEW |
| **Prometheus** | 9090 | Metrics collection               | Prometheus    | ✅ NEW |
| **Grafana**    | 3001 | Monitoring dashboards            | Grafana       | ✅ NEW |

```

## Services

### 1. Trading Engine (Port 8001)
- Order matching engine
- Real-time order book management
- WebSocket market data streaming
- High-frequency trade execution

### 2. Backend API (Port 8000)
- RESTful API
- User management
- Order management
- Market data aggregation

### 3. Authentication Service (Port 8002)
- JWT authentication
- User registration/login
- KYC verification workflow
- Session management

### 4. Wallet Service (Port 8003)
- Multi-currency wallet management
- Deposits and withdrawals
- Balance tracking
- Transaction history

### 5. Settlement Tower (Port 8004)
- Trade settlement and clearing
- Balance reconciliation
- Transaction finalization
- Audit trail

### 6. Frontend (Port 3000)
- Trading console UI
- Real-time order book visualization
- Chart integration
- User dashboard

## Technology Stack

- **Frontend**: React, Redux, WebSocket, TradingView Charts
- **Backend API**: Node.js, Express, TypeScript
- **Trading Engine**: Go (high performance)
- **Settlement**: Go (concurrent processing)
- **Databases**: PostgreSQL, Redis
- **Message Queue**: Redis Pub/Sub
- **Containerization**: Docker, Docker Compose

---

## 🚀 Quick Start

### Prerequisites
```bash
- Docker 20.10+ and Docker Compose 2.0+
- 8GB RAM minimum (16GB recommended)
- 50GB disk space
```

### Launch Platform

```bash
# Start all services
docker-compose up -d

# Check service status
docker-compose ps

# View logs
docker-compose logs -f openfinex
docker-compose logs -f peatio
docker-compose logs -f tower

# Stop all services
docker-compose down
```

---

## 🌐 Access URLs

| Service             | URL                   | Credentials |
|---------------------|-----------------------|-------------|
| **Trading Console** | http://localhost:3000 | Register new account |
| **Admin Dashboard** | http://localhost:3002 | *Coming soon* |
| **API Gateway**     | http://localhost:8000 | JWT Bearer token |
| **Grafana**         | http://localhost:3001 | `admin` / `opendax_admin` |
| **Prometheus**      | http://localhost:9090 | No auth |
| **InfluxDB**        | http://localhost:8086 | `opendax` / `opendax_password` |

---

## 📡 Key Features by Component

### 1. OpenFinex (Matching Engine - Go)
- Sub-millisecond order matching
- Price-time priority algorithm
- WebSocket real-time streaming
- InfluxDB integration for charts
- Supports millions of TPS

### 2. Peatio (Wallet & Accounting - Node.js) **ENHANCED**
- Multi-currency wallet management
- **Double-entry ledger system** ✨ NEW
- Ledger operations table (audit trail)
- Hot/cold wallet separation
- Deposit/withdrawal processing

### 3. Barong (Authentication - Node.js)
- JWT with refresh tokens
- 2FA (TOTP) with QR codes
- KYC document verification
- Role-based access control (RBAC)
- User levels: 0 (unverified) → 3 (full KYC)

### 4. Gateway (API Gateway - Node.js)
- Centralized routing
- Rate limiting (100 req/min)
- Request validation
- Service health checks

### 5. Tower (Admin Management - Node.js) ✨ NEW
- User management (suspend/ban/activate)
- Market pair configuration
- Fee management (maker/taker)
- KYC review & approval
- System reports & analytics
- Audit log viewer

### 6. AppLogic (Payment Gateway - Node.js) ✨ NEW
- Fiat deposit (Stripe card payments)
- Bank transfer instructions
- Wire transfer support
- Withdrawal processing with fees
- KYC-based transaction limits
- Bank account management

### 7. Settlement (Clearing - Go)
- Automatic trade settlement
- Balance reconciliation
- Retry mechanism
- Atomic transactions

### 8. InfluxDB (Time-Series DB) ✨ NEW
- OHLCV candlesticks (1m, 5m, 1h, 1d)
- Trade history storage
- Order book snapshots
- Query API for TradingView

### 9. Prometheus + Grafana ✨ NEW
- HTTP request metrics
- Order matching performance
- Database connection pools
- Active user sessions
- Trade volumes (24h/7d/30d)
- Pre-configured dashboards

---

## 🗂️ Database Enhancements

### New Tables Added
- `ledger_accounts` - Double-entry accounts (asset/liability/revenue/expense)
- `ledger_operations` - Transaction journal (debits & credits)
- `bank_accounts` - User bank info for fiat withdrawals
- `wallet_addresses` - Hot/cold wallet management

### Enhanced Tables
- `users` - Added `level` (KYC tiers 0-3), `state` (active/suspended/banned)
- `kyc_documents` - Added admin review fields (`reviewed_by`, `admin_comment`)
- `trading_pairs` - Added `is_active`, `min_price`, `max_price`

---

## 📊 Monitoring & Metrics

### Grafana Dashboard
**URL**: http://localhost:3001 (`admin` / `opendax_admin`)

**Panels**:
- Total trades (last hour)
- Active users
- Order book depth
- Trade volume (24h USDT)
- API request rate
- Response time (p95)
- Order matching latency
- DB connection pools

### Prometheus Queries
```promql
# Trade rate
rate(trades_total[5m])

# API errors
rate(http_requests_total{status_code=~"5.."}[5m])

# Pending KYC
tower_kyc_pending_count
```

---

## 📡 API Endpoints

### Authentication (Barong)
```bash
POST /api/auth/register          # Register
POST /api/auth/login             # Login (JWT)
POST /api/auth/2fa/enable        # Enable 2FA
POST /api/kyc/submit             # Submit KYC
```

### Trading (OpenFinex)
```bash
GET  /api/orderbook/:pair        # Order book
WS   ws://localhost:8001/ws      # Real-time data
```

### Wallet (Peatio)
```bash
GET  /api/wallet/balance         # Balances
POST /api/wallet/withdraw        # Withdraw
GET  /api/ledger/operations      # Ledger (double-entry)
```

### Fiat (AppLogic)
```bash
POST /api/fiat/deposit           # Deposit (card/bank/wire)
POST /api/fiat/withdraw          # Withdraw
POST /api/bank-accounts          # Add bank account
```

### Admin (Tower)
```bash
GET  /api/users                  # List users
PATCH /api/users/:id/state       # Suspend/ban
POST /api/kyc/:id/approve        # Approve KYC
POST /api/markets                # Create trading pair
PUT  /api/fees/:id               # Update fees
```

---

## 🔐 Security

- ✅ JWT authentication
- ✅ Bcrypt password hashing
- ✅ 2FA (TOTP) support
- ✅ Rate limiting
- ✅ SQL injection prevention
- ✅ CORS configuration
- ✅ Audit logging
- ✅ RBAC (user/trader/admin/superadmin)

---

## 🎯 realDAX-OpenDAX Compliance

| Feature                   | Status |
|---------------------------|--------|
| ✅ OpenFinex Matching    | Complete |
| ✅ Peatio Accounting     | Enhanced with double-entry |
| ✅ Barong Authentication | Complete with KYC |
| ✅ Tower Admin           | Backend complete |
| ✅ AppLogic Payments     | Complete with Stripe |
| ✅ BaseApp Frontend      | Complete |
| ✅ InfluxDB Charts       | Integrated |
| ✅ Prometheus/Grafana    | Complete |
| ⏳ Yellow Network        | Planned |

---

## 📝 License

MIT License

---

**Built with ❤️ following realDAX-OpenDAX architecture**
docker-compose ps

# View logs
docker-compose logs -f
```

### Access the System

- Frontend: http://localhost:3000
- API Gateway: http://localhost:8000
- Trading Engine: http://localhost:8001
- Auth Service: http://localhost:8002
- Wallet Service: http://localhost:8003
- Settlement Tower: http://localhost:8004

## API Documentation

### Authentication
```
POST /api/auth/register - Register new user
POST /api/auth/login - User login
POST /api/auth/verify-kyc - Submit KYC documents
GET /api/auth/me - Get current user
```

### Trading
```
POST /api/orders - Place new order
GET /api/orders - Get user orders
DELETE /api/orders/:id - Cancel order
GET /api/orderbook/:pair - Get order book
GET /api/trades/:pair - Get trade history
```

### Wallet
```
GET /api/wallet/balance - Get balances
POST /api/wallet/deposit - Create deposit address
POST /api/wallet/withdraw - Request withdrawal
GET /api/wallet/transactions - Get transaction history
```

## Development

### Local Development Setup

```bash
# Install dependencies for all services
npm run install-all

# Run in development mode
npm run dev
```

### Database Setup

```bash
# Run migrations
npm run migrate

# Seed test data
npm run seed
```

## Testing

```bash
# Run all tests
npm test

# Run specific service tests
npm test --workspace=backend
npm test --workspace=trading-engine
```

## Configuration

Environment variables are managed through `.env` files:
- `.env` - Main configuration
- `services/*/.env` - Service-specific configs

## Security Features

- JWT-based authentication
- Rate limiting
- SQL injection prevention
- CORS configuration
- API key management
- 2FA support (optional)

## Performance Optimizations

- Redis caching layer
- Database connection pooling
- WebSocket for real-time updates
- Order book optimization
- Trade matching algorithms

## License

MIT License

## Support

For issues and questions, please open a GitHub issue.
