# Story 1.5: Monitoring & Observability

Status: ready-for-dev

## Story

As a **developer debugging production issues**,
I want **structured logging and health monitoring**,
so that **I can quickly identify and fix problems**.

## Acceptance Criteria

1. **Structured logging implemented in all services**

   - Given services are deployed
   - When monitoring is configured
   - Then all Go services:
     - Use zerolog for structured JSON logging
     - Log level: DEBUG (local), INFO (dev), INFO (staging), WARN (production)
     - Every log includes: service, timestamp, level, request_id, shul_id, user_id
     - HTTP requests logged with: method, path, status, duration

2. **Health check endpoints enhanced with dependency status**

   - `GET /health` returns 200 OK with:
     ```json
     {
       "status": "healthy",
       "service": "zmanim",
       "version": "0.1.0",
       "database": "connected",
       "redis": "connected"
     }
     ```
   - Returns 503 if database or Redis unavailable
   - Health check includes dependency checks (PostgreSQL ping, Redis PING)

3. **CloudWatch Logs integration configured**

   - All logs stream to CloudWatch Logs
   - Log groups:
     - `/aws/lambda/shtetl-dev-zmanim`
     - `/aws/lambda/shtetl-dev-shul`
     - `/aws/lambda/shtetl-dev-kehilla`
   - Retention: 30 days (all environments)
   - Logs searchable by request_id

4. **CloudWatch Alarms created for critical metrics**

   - Lambda errors > 10/minute → alert
   - API Gateway 5xx responses > 5% → alert
   - RDS CPU > 80% → alert
   - Lambda duration > 10s → alert
   - Alarms send notifications to SNS topic

5. **CloudWatch Dashboard provisioned via CDKTF**

   - Dashboard includes:
     - Request count per service (last 24h)
     - Error rate percentage (last 24h)
     - P50, P95, P99 latency (last 24h)
     - Database connection pool usage
     - Redis memory usage
   - Dashboard accessible in AWS Console

6. **SNS alerts configured**

   - SNS topic: `shtetl-dev-platform-alerts`
   - Subscription: `platform-alerts@shtetl.io` (Google Group)
   - Email notifications for alarm state changes
   - Alert includes: service name, alarm description, metric value

7. **Request ID middleware implemented**
   - Middleware extracts API Gateway trace ID (X-Amzn-Trace-Id)
   - Middleware generates UUID for request_id (fallback + future portability)
   - Both IDs logged with every request
   - Enables distributed tracing across services

## Tasks / Subtasks

- [ ] **Task 1: Install and configure zerolog** (AC: 1)

  - [ ] 1.1 Install `github.com/rs/zerolog` in all 3 services
  - [ ] 1.2 Create `internal/logging/logger.go` (shared logger setup)
  - [ ] 1.3 Configure log level by environment (DEBUG/INFO/WARN)
  - [ ] 1.4 Configure JSON output format
  - [ ] 1.5 Add global logger fields (service, version)

- [ ] **Task 2: Implement request ID middleware** (AC: 7)

  - [ ] 2.1 Create `internal/middleware/request_id.go`
  - [ ] 2.2 Extract API Gateway trace ID (X-Amzn-Trace-Id header)
  - [ ] 2.3 Generate UUID v4 for request_id
  - [ ] 2.4 Store both IDs in request context
  - [ ] 2.5 Add middleware to all HTTP handlers
  - [ ] 2.6 Document request_id usage

- [ ] **Task 3: Implement HTTP request logging middleware** (AC: 1)

  - [ ] 3.1 Create `internal/middleware/logging.go`
  - [ ] 3.2 Log request start (method, path, request_id)
  - [ ] 3.3 Log request completion (status, duration)
  - [ ] 3.4 Extract user_id and shul_id from context (if available)
  - [ ] 3.5 Add middleware to all HTTP handlers

- [ ] **Task 4: Enhance health check endpoints** (AC: 2)

  - [ ] 4.1 Update zmanim service `/health` endpoint
  - [ ] 4.2 Update shul service `/health` endpoint
  - [ ] 4.3 Update kehilla service `/health` endpoint
  - [ ] 4.4 Add PostgreSQL ping check
  - [ ] 4.5 Add Redis PING check
  - [ ] 4.6 Return 503 if dependencies unavailable
  - [ ] 4.7 Add version field from build metadata

- [ ] **Task 5: Configure CloudWatch Logs** (AC: 3)

  - [ ] 5.1 Create CloudWatch log groups in CDKTF
  - [ ] 5.2 Configure log group retention (30 days)
  - [ ] 5.3 Configure Lambda to stream logs to CloudWatch
  - [ ] 5.4 Add IAM permissions for Lambda → CloudWatch Logs
  - [ ] 5.5 Test log streaming from deployed Lambda

- [ ] **Task 6: Create CloudWatch Alarms** (AC: 4)

  - [ ] 6.1 Create alarm: Lambda errors > 10/minute
  - [ ] 6.2 Create alarm: API Gateway 5xx > 5%
  - [ ] 6.3 Create alarm: RDS CPU > 80%
  - [ ] 6.4 Create alarm: Lambda duration > 10s
  - [ ] 6.5 Configure alarm actions (SNS notification)
  - [ ] 6.6 Test alarm triggering and notifications

- [ ] **Task 7: Create CloudWatch Dashboard** (AC: 5)

  - [ ] 7.1 Create `infrastructure/dashboards/main.json`
  - [ ] 7.2 Add widget: Request count per service
  - [ ] 7.3 Add widget: Error rate percentage
  - [ ] 7.4 Add widget: Latency percentiles (P50, P95, P99)
  - [ ] 7.5 Add widget: Database connection pool
  - [ ] 7.6 Add widget: Redis memory usage
  - [ ] 7.7 Provision dashboard via CDKTF

- [ ] **Task 8: Configure SNS notifications** (AC: 6)

  - [ ] 8.1 Create SNS topic: `shtetl-dev-platform-alerts`
  - [ ] 8.2 Subscribe `platform-alerts@shtetl.io` to topic
  - [ ] 8.3 Configure email subscription confirmation
  - [ ] 8.4 Link alarms to SNS topic
  - [ ] 8.5 Test email notification delivery
  - [ ] 8.6 Document alert response procedures

- [ ] **Task 9: Test observability end-to-end** (AC: 1-7)
  - [ ] 9.1 Deploy services to dev environment
  - [ ] 9.2 Generate test traffic (health checks, API calls)
  - [ ] 9.3 Verify logs appear in CloudWatch
  - [ ] 9.4 Verify request_id searchable in logs
  - [ ] 9.5 Trigger test alarm (e.g., high error rate)
  - [ ] 9.6 Verify SNS email received
  - [ ] 9.7 Verify dashboard shows metrics

## Dev Notes

### Structured Logging with zerolog

**Installation:**

```bash
go get github.com/rs/zerolog
```

**Logger Setup:**

```go
package logging

import (
    "github.com/rs/zerolog"
    "github.com/rs/zerolog/log"
    "os"
)

func Init(serviceName, version, env string) {
    // Set log level by environment
    level := zerolog.InfoLevel
    if env == "local" || env == "dev" {
        level = zerolog.DebugLevel
    } else if env == "production" {
        level = zerolog.WarnLevel
    }

    zerolog.SetGlobalLevel(level)
    log.Logger = log.Output(zerolog.ConsoleWriter{Out: os.Stdout})

    // Add global fields
    log.Logger = log.With().
        Str("service", serviceName).
        Str("version", version).
        Logger()
}
```

**Request Logging Example:**

```go
log.Info().
    Str("request_id", requestID).
    Str("method", r.Method).
    Str("path", r.URL.Path).
    Int("status", status).
    Dur("duration", duration).
    Msg("Request completed")
```

### Request ID Middleware

**Implementation:**

```go
func RequestIDMiddleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        // Extract AWS trace ID
        awsTraceID := r.Header.Get("X-Amzn-Trace-Id")

        // Generate our own request ID (UUID v4)
        requestID := uuid.New().String()

        // Store in context
        ctx := context.WithValue(r.Context(), "request_id", requestID)
        ctx = context.WithValue(ctx, "aws_trace_id", awsTraceID)

        // Log both IDs
        log.Info().
            Str("request_id", requestID).
            Str("aws_trace_id", awsTraceID).
            Msg("Request started")

        next.ServeHTTP(w, r.WithContext(ctx))
    })
}
```

### Enhanced Health Check

**Implementation:**

```go
func healthHandler(db *sql.DB, redisClient *redis.Client) http.HandlerFunc {
    return func(w http.ResponseWriter, r *http.Request) {
        status := "healthy"
        httpStatus := http.StatusOK

        // Check database
        dbStatus := "connected"
        if err := db.Ping(); err != nil {
            dbStatus = "disconnected"
            status = "unhealthy"
            httpStatus = http.StatusServiceUnavailable
        }

        // Check Redis
        redisStatus := "connected"
        if _, err := redisClient.Ping(r.Context()).Result(); err != nil {
            redisStatus = "disconnected"
            status = "unhealthy"
            httpStatus = http.StatusServiceUnavailable
        }

        response := map[string]string{
            "status":   status,
            "service":  "zmanim",
            "version":  "0.1.0",
            "database": dbStatus,
            "redis":    redisStatus,
        }

        w.Header().Set("Content-Type", "application/json")
        w.WriteHeader(httpStatus)
        json.NewEncoder(w).Encode(response)
    }
}
```

### CloudWatch Cost Estimate

**Free Tier (Always Free):**

- 10 metrics, 10 alarms
- 5GB log ingestion per month
- 3 custom dashboards

**Dev Environment (Low Traffic):**

- 3 Lambda functions
- 4 alarms
- ~100MB logs/month
- 1 dashboard

**Estimated Cost:** $0-$5/month (stays within free tier)

### CDKTF Dashboard Provisioning

**Example:**

```typescript
import { cloudwatch } from "@cdktf/provider-aws";

new cloudwatch.CloudwatchDashboard(this, "main-dashboard", {
  dashboardName: "shtetl-dev-main",
  dashboardBody: JSON.stringify({
    widgets: [
      {
        type: "metric",
        properties: {
          metrics: [
            ["AWS/Lambda", "Invocations", { stat: "Sum" }],
          ],
          period: 300,
          region: "us-east-1",
          title: "Lambda Invocations",
        },
      },
    ],
  }),
});
```

### Prerequisites

- Story 1.3 (CI/CD Pipeline - services deployed to AWS)

### Deliverables

- `internal/logging/logger.go` - Structured logging setup
- `internal/middleware/request_id.go` - Request ID middleware
- `internal/middleware/logging.go` - HTTP request logging
- Enhanced `/health` endpoints in all 3 services
- CDKTF CloudWatch configuration:
  - Log groups with 30-day retention
  - 4 CloudWatch alarms
  - CloudWatch dashboard
  - SNS topic + email subscription
- Documentation: `docs/observability.md`
  - Log structure reference
  - Dashboard guide
  - Alert response procedures

### Future Work

- Story 1.16: Distributed tracing with OpenTelemetry
- Story 1.17: Custom metrics (business KPIs)
- Story 1.18: Log aggregation with Loki/Mimir/Tempo
- Story 1.19: On-call rotation and PagerDuty integration
