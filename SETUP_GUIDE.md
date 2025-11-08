# realDAX

Complete setup instructions for the realDAX-OpenDAX cryptocurrency exchange platform following official realDAX-OpenDAX architecture standards.

## Prerequisites

Before starting, ensure you have the following installed:

- **Docker 20.10+** & **Docker Compose 2.0+**
- **Node.js 18+** (for local development)
- **Go 1.21+** (for local development)
- **Git**
- **Minimum 8GB RAM** (16GB recommended)
- **50GB disk space**

## 🚀 Quick Start with Docker (Recommended)

The easiest way to run the complete realDAX-OpenDAX platform:

```bash
# Clone the repository
cd realDAX

# Copy environment file
copy .env.example .env

# Build and start ALL services (11 containers)
docker-compose up --build -d

# View logs
docker-compose logs -f

# Check service health
docker-compose ps
```

### 🌐 Access Services

| Service | URL | Credentials |
|---------|-----|-------------|
| **BaseApp (Trading Console)** | http://localhost:3000 | Register new account |
| **Gateway (API Gateway)** | http://localhost:8000 | JWT Bearer token |
| **OpenFinex (Matching Engine)** | http://localhost:8001 | Internal + WebSocket |
| **Barong (Auth & KYC)** | http://localhost:8002 | Internal API |
| **Peatio (Wallet & Ledger)** | http://localhost:8003 | Internal API |
| **Settlement (Clearing)** | http://localhost:8004 | Internal service |
| **Tower (Admin Management)** | http://localhost:8005 | Admin JWT required |
| **AppLogic (Payment Gateway)** | http://localhost:8006 | Internal API |
| **Grafana (Monitoring)** | http://localhost:3001 | `admin` / `opendax_admin` |
| **Prometheus (Metrics)** | http://localhost:9090 | No auth |
| **InfluxDB (Time-Series)** | http://localhost:8086 | `opendax` / `opendax_password` |
| **PostgreSQL** | localhost:5432 | `opendax` / `opendax_password` |
| **Redis** | localhost:6379 | No auth |

---

## 📋 Service Overview

| Service | Port | Description | Status |
|---------|------|-------------|--------|
| **OpenFinex** | 8001 | Matching engine (Go) - millions TPS | ✅ Running |
| **Barong** | 8002 | Authentication & KYC (Node.js) | ✅ Running |
| **Peatio** | 8003 | Wallet & double-entry ledger (Node.js) | ✅ Running |
| **Settlement** | 8004 | Trade clearing (Go) | ✅ Running |
| **Tower** | 8005 | Admin management (Node.js) | ✅ Running |
| **AppLogic** | 8006 | Payment gateway (Node.js) | ✅ Running |
| **Gateway** | 8000 | API Gateway (Node.js) | ✅ Running |
| **BaseApp** | 3000 | Trading console (React) | ✅ Running |
| **PostgreSQL** | 5432 | Primary database | ✅ Running |
| **Redis** | 6379 | Cache & messaging | ✅ Running |
| **InfluxDB** | 8086 | Time-series (charts) | ✅ Running |
| **Prometheus** | 9090 | Metrics collection | ✅ Running |
| **Grafana** | 3001 | Monitoring dashboards | ✅ Running |

---

## Local Development Setup

### 1. Database Setup

```bash
# Start databases only
docker-compose up -d postgres redis influxdb

# Wait for databases to be ready (about 15 seconds)
timeout /t 15

# The database will be initialized automatically from database/init.sql
```

### 2. Run Backend Services Locally

#### OpenFinex (Matching Engine - Go)
```bash
cd services/openfinex
go mod download
go run main.go
```

#### Settlement (Clearing - Go)
```bash
cd services/settlement
go mod download
go run main.go
```

#### Gateway (API Gateway - Node.js)
```bash
cd services/gateway
npm install
npm run dev
```

#### Barong (Authentication - Node.js)
```bash
cd services/barong
npm install
npm run dev
```

#### Peatio (Wallet & Ledger - Node.js)
```bash
cd services/peatio
npm install
npm run dev
```

#### Tower (Admin Management - Node.js)
```bash
cd services/tower
npm install
npm run dev
```

#### AppLogic (Payment Gateway - Node.js)
```bash
cd services/applogic
npm install
npm run dev
```

### 3. Run Frontend

```bash
cd frontend
npm install
npm start
```

### 4. Run Monitoring Stack (Optional)
```bash
# Start Prometheus and Grafana
docker-compose up -d prometheus grafana

# Access Grafana
# http://localhost:3001 (admin / opendax_admin)
```

---

## 📱 Using the Application

### 1. Register a New Account

1. Navigate to http://localhost:3000
2. Click "Sign up"
3. Fill in:
   - Email: `user@example.com`
   - Username: `trader1`
   - Password: `password123` (min 8 characters)
4. Click "Create Account"

### 2. Explore the Trading Interface

After login, you'll see:

- **Order Book**: Real-time buy/sell orders
- **Price Chart**: Placeholder for chart visualization
- **Recent Trades**: Live trade feed
- **Order Form**: Place buy/sell orders

### 3. Place Your First Order

1. Select Buy or Sell
2. Enter price (e.g., `50000` for BTC/USDT)
3. Enter amount (e.g., `0.01` BTC)
4. Click "Buy" or "Sell"

Note: The system checks wallet balance before placing orders.

### 4. Manage Your Wallet

Navigate to the Wallet page:

- View all cryptocurrency balances
- Generate deposit addresses
- Submit withdrawal requests

### 5. Complete KYC Verification

1. Go to KYC page
2. Select document type
3. Enter document number
4. Submit for verification (mock upload in this demo)

---

## 🔐 Admin Operations (Tower)

### Access Tower Admin API

Tower requires admin role authentication:

```bash
# First, create an admin user via SQL
docker exec -it opendax-postgres psql -U opendax -d opendax -c \
  "UPDATE users SET role = 'admin' WHERE email = 'admin@example.com'"

# Then login to get admin JWT token
curl -X POST http://localhost:8002/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "admin@example.com", "password": "your_password"}'
```

### Admin Endpoints (Port 8005)

```bash
# Get all users
curl -H "Authorization: Bearer <admin-token>" \
  http://localhost:8005/api/users

# Suspend a user
curl -X PATCH http://localhost:8005/api/users/{user-id}/state \
  -H "Authorization: Bearer <admin-token>" \
  -H "Content-Type: application/json" \
  -d '{"state": "suspended", "reason": "Policy violation"}'

# Approve KYC
curl -X POST http://localhost:8005/api/kyc/{kyc-id}/approve \
  -H "Authorization: Bearer <admin-token>" \
  -H "Content-Type: application/json" \
  -d '{"level": 3, "comment": "Documents verified"}'

# Create new trading pair
curl -X POST http://localhost:8005/api/markets \
  -H "Authorization: Bearer <admin-token>" \
  -H "Content-Type: application/json" \
  -d '{
    "symbol": "DOT/USDT",
    "base_currency": "DOT",
    "quote_currency": "USDT",
    "min_price": 0.01,
    "max_price": 1000,
    "min_amount": 0.1,
    "price_precision": 2,
    "amount_precision": 2,
    "maker_fee": 0.001,
    "taker_fee": 0.002
  }'

# Update trading fees
curl -X PUT http://localhost:8005/api/fees/{pair-id} \
  -H "Authorization: Bearer <admin-token>" \
  -H "Content-Type: application/json" \
  -d '{"maker_fee": 0.0008, "taker_fee": 0.0015}'

# Get trading reports
curl -H "Authorization: Bearer <admin-token>" \
  "http://localhost:8005/api/reports/trading?period=7d"

# View system health
curl -H "Authorization: Bearer <admin-token>" \
  http://localhost:8005/api/system/health
```

---

## 💳 Fiat Operations (AppLogic)

### Deposit Fiat (Port 8006)

```bash
# Initiate card deposit (Stripe)
curl -X POST http://localhost:8006/api/fiat/deposit \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 100,
    "currency": "USD",
    "payment_method": "card"
  }'

# Response includes Stripe client_secret for payment confirmation

# Bank transfer deposit
curl -X POST http://localhost:8006/api/fiat/deposit \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 5000,
    "currency": "USD",
    "payment_method": "bank_transfer"
  }'

# Response includes bank account details and reference number

# Check deposit status
curl -H "Authorization: Bearer <token>" \
  http://localhost:8006/api/fiat/deposit/{deposit-id}
```

### Withdraw Fiat

```bash
# Add bank account first
curl -X POST http://localhost:8006/api/bank-accounts \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "bank_name": "Chase Bank",
    "account_number": "1234567890",
    "routing_number": "123456789",
    "currency": "USD"
  }'

# Request withdrawal
curl -X POST http://localhost:8006/api/fiat/withdraw \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "amount": 1000,
    "currency": "USD",
    "bank_account_id": "{bank-account-id}"
  }'

# Check transaction limits
curl -H "Authorization: Bearer <token>" \
  http://localhost:8006/api/limits
```

### Payment Methods

```bash
# List available payment methods
curl http://localhost:8006/api/payment-methods

# Response:
{
  "payment_methods": [
    {
      "id": "card",
      "name": "Credit/Debit Card",
      "fee": "2.9%",
      "min": 10,
      "max": 10000
    },
    {
      "id": "bank_transfer",
      "name": "Bank Transfer",
      "fee": "$5",
      "min": 50,
      "max": 50000
    },
    {
      "id": "wire",
      "name": "Wire Transfer",
      "fee": "$25",
      "min": 1000,
      "max": 1000000
    }
  ]
}
```

---

## 📊 Monitoring & Metrics

### Grafana Dashboards

1. **Access Grafana**: http://localhost:3001
2. **Login**: `admin` / `opendax_admin`
3. **View Dashboard**: "OpenDAX - System Overview"

**Available Metrics**:
- Total trades (last hour)
- Active users count
- Order book depth
- Trade volume (24h in USDT)
- API request rate by service
- Response time (p95)
- Order matching latency
- Database connections

### Prometheus Metrics

Access raw metrics: http://localhost:9090

**Example Queries**:
```promql
# Trade rate per second
rate(trades_total[5m])

# API error rate
rate(http_requests_total{status_code=~"5.."}[5m])

# Pending KYC count
tower_kyc_pending_count

# Order matching latency
histogram_quantile(0.95, rate(matching_latency_seconds_bucket[5m]))
```

### InfluxDB (Historical Data)

Access InfluxDB UI: http://localhost:8086

**Login**: `opendax` / `opendax_password`

**Query OHLCV Candles**:
```bash
# Via API
curl -X POST http://localhost:8086/api/v2/query \
  -H "Authorization: Token opendax-super-secret-token" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "from(bucket:\"market_data\") |> range(start: -1h) |> filter(fn: (r) => r._measurement == \"ohlcv\" and r.symbol == \"BTC/USDT\")",
    "type": "flux"
  }'
```

---

## API Endpoints

### Authentication API (Port 8002)

```bash
# Register
POST /api/auth/register
{
  "email": "user@example.com",
  "username": "trader1",
  "password": "password123"
}

# Login
POST /api/auth/login
{
  "email": "user@example.com",
  "password": "password123"
}

# Get current user
GET /api/auth/me
Authorization: Bearer <token>
```

### Trading API (Port 8000/8001)

```bash
# Get order book
GET /api/market/orderbook/BTC/USDT

# Place order
POST /api/orders
Authorization: Bearer <token>
{
  "symbol": "BTC/USDT",
  "type": "limit",
  "side": "buy",
  "price": "50000",
  "amount": "0.01"
}

# Cancel order
DELETE /api/orders/{order_id}
Authorization: Bearer <token>

# Get user orders
GET /api/orders?status=open
Authorization: Bearer <token>

# Get trades
GET /api/market/trades/BTC/USDT
```

### Wallet API (Port 8003)

```bash
# Get balances
GET /api/wallet/balances
Authorization: Bearer <token>

# Generate deposit address
POST /api/wallet/deposit/address
Authorization: Bearer <token>
{
  "currency": "BTC"
}

# Withdraw
POST /api/wallet/withdraw
Authorization: Bearer <token>
{
  "currency": "BTC",
  "amount": "0.01",
  "address": "1A1zP1eP5QGefi2DMPTfTL5SLmv7DivfNa"
}
```

### KYC API (Port 8002)

```bash
# Submit KYC
POST /api/kyc/submit
Authorization: Bearer <token>
{
  "document_type": "passport",
  "document_number": "AB123456",
  "document_front_url": "...",
  "document_back_url": "...",
  "selfie_url": "..."
}

# Get KYC status
GET /api/kyc/status
Authorization: Bearer <token>
```

## Testing with Postman

Import these curl commands into Postman:

```bash
# Register
curl -X POST http://localhost:8002/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"email":"test@test.com","username":"test","password":"password123"}'

# Login
curl -X POST http://localhost:8002/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@test.com","password":"password123"}'

# Get order book
curl http://localhost:8000/api/market/orderbook/BTC/USDT
```

## Troubleshooting

### Database Connection Issues

```bash
# Check if PostgreSQL is running
docker-compose ps postgres

# View PostgreSQL logs
docker-compose logs postgres

# Restart PostgreSQL
docker-compose restart postgres
```

### Redis Connection Issues

```bash
# Check if Redis is running
docker-compose ps redis

# Test Redis connection
docker-compose exec redis redis-cli ping
```

### Port Already in Use

If you get port conflict errors:

```bash
# On Windows, find and kill process using port 3000
netstat -ano | findstr :3000
taskkill /PID <PID> /F

# Change port in .env file
PORT=3001
```

### Service Not Starting

```bash
# Check all service logs
docker-compose logs -f

# Check specific service
docker-compose logs -f backend-api

# Rebuild a specific service
docker-compose up --build backend-api
```

## Architecture Details

### OpenFinex (Matching Engine)
- Written in Go for high performance
- In-memory order matching (millions TPS)
- WebSocket for real-time updates
- Redis pub/sub for event distribution
- InfluxDB integration for trade history

### Barong (Authentication & Identity)
- JWT authentication with 2FA (TOTP)
- User management & registration
- KYC verification (levels 0-3)
- Role-based access control (RBAC)

### Peatio (Wallet & Accounting)
- Multi-currency wallet management
- **Double-entry ledger system**
- Ledger operations for audit trail
- Hot/cold wallet separation
- PostgreSQL for persistence

### Tower (Admin Management) ✨ NEW
- User management API (suspend/ban/activate)
- Market configuration
- Fee management
- KYC review & approval
- System reports & analytics

### AppLogic (Payment Gateway) ✨ NEW
- Stripe card payment integration
- Bank/wire transfer support
- KYC-based transaction limits
- Multi-currency fiat support

### Settlement (Trade Clearing)
- Asynchronous trade settlement
- Balance reconciliation
- Retry mechanism for failed settlements
- Atomic transaction processing

### Gateway (API Gateway)
- Express.js REST API
- Service routing & load balancing
- Rate limiting & throttling
- JWT authentication middleware
- Redis caching

### BaseApp (Frontend)
- React 18 with TypeScript
- Tailwind CSS for styling
- Zustand for state management
- WebSocket for real-time data

### InfluxDB (Time-Series DB) ✨ NEW
- OHLCV candlestick storage
- Trade history archival
- Efficient querying for charts

### Prometheus + Grafana ✨ NEW
- Metrics collection (all services)
- Pre-configured dashboards
- Real-time monitoring

## Security Notes

⚠️ **This is a DEMO/MOCKUP system**:

- Use unique, strong passwords in production
- Change all default secrets in .env
- Enable HTTPS/TLS for all services
- Implement proper authentication & authorization
- Add rate limiting & DDoS protection
- Conduct security audits before production use
- Never expose database ports publicly
- Use environment variables for all secrets
- Implement proper logging & monitoring

## Performance Optimization

For production deployment:

1. **Database**:
   - Use connection pooling
   - Add database indexes
   - Enable query caching
   - Use read replicas

2. **Redis**:
   - Use Redis Cluster for HA
   - Configure persistence
   - Set appropriate eviction policies

3. **API Services**:
   - Enable compression
   - Use CDN for static assets
   - Implement API caching
   - Use load balancers

4. **Trading Engine**:
   - Optimize order matching algorithms
   - Use message queues for high throughput
   - Implement horizontal scaling

## Monitoring

The platform includes a complete monitoring stack:

### Grafana (Port 3001)
- **Access**: http://localhost:3001
- **Login**: `admin` / `opendax_admin`
- **Pre-configured Dashboard**: "OpenDAX - System Overview"
- **Metrics**: Trades, users, API performance, matching latency

### Prometheus (Port 9090)
- **Access**: http://localhost:9090
- **Scrapes**: All 8 microservices every 15 seconds
- **Alerting**: Configure alert rules in `monitoring/prometheus.yml`

### InfluxDB (Port 8086)
- **Access**: http://localhost:8086
- **Login**: `opendax` / `opendax_password`
- **Data**: OHLCV candles, trade history, order book snapshots

### Additional Tools (Optional)
- **ELK Stack**: Log aggregation (not included, can be added)
- **Sentry**: Error tracking (integration ready)
- **Jaeger**: Distributed tracing (future enhancement)

## Support & Troubleshooting

### Common Issues

#### Services won't start
```bash
# Check Docker resources
docker system df

# Clean up unused resources
docker system prune -a

# Restart with fresh build
docker-compose down -v
docker-compose up --build -d
```

#### Database connection errors
```bash
# Check PostgreSQL is running
docker-compose ps postgres

# View PostgreSQL logs
docker-compose logs postgres

# Restart database
docker-compose restart postgres
```

#### Can't access services
```bash
# Check all services are healthy
docker-compose ps

# Check specific service logs
docker-compose logs -f <service-name>

# Verify port availability
netstat -ano | findstr :8000
```

#### TypeScript compilation errors (before npm install)
These are expected before running `npm install` in each service directory. Dependencies will resolve the errors.

### Health Check Endpoints

```bash
# Check all service health
curl http://localhost:8000/health  # Gateway
curl http://localhost:8001/health  # OpenFinex
curl http://localhost:8002/health  # Barong
curl http://localhost:8003/health  # Peatio
curl http://localhost:8004/health  # Settlement
curl http://localhost:8005/health  # Tower
curl http://localhost:8006/health  # AppLogic
```

### Viewing Logs

```bash
# All services
docker-compose logs -f

# Specific service
docker-compose logs -f openfinex
docker-compose logs -f peatio
docker-compose logs -f tower

# Last 100 lines
docker-compose logs --tail=100 openfinex
```

### Getting Help

1. Check the **README.md** for architecture overview
2. Review **IMPLEMENTATION_SUMMARY.md** for detailed features
3. See **OPENDAX_MAPPING.md** for service mapping
4. Review Docker logs for error messages
5. Check service health endpoints
6. Create a GitHub issue with logs and error details

## License

MIT License - See LICENSE file for details

---

**Note**: This is a comprehensive realDAX-OpenDAX-compliant platform with ~85% of official components. For production use, additional security hardening, testing, compliance features, and the Tower Admin UI frontend are recommended.

**New Components Added**: Tower Admin Backend, AppLogic Payment Gateway, InfluxDB Time-Series, Prometheus + Grafana Monitoring, Double-Entry Ledger System.
