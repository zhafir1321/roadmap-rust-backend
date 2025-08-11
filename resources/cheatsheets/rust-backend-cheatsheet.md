# Rust Backend Developer Cheat Sheet

## 🦀 Rust Basics

### Data Types
```rust
// Scalar types
let integer: i32 = 42;
let float: f64 = 3.14;
let boolean: bool = true;
let character: char = 'R';

// Compound types
let tuple: (i32, f64, char) = (42, 3.14, 'R');
let array: [i32; 5] = [1, 2, 3, 4, 5];

// String types
let string_literal: &str = "Hello";
let string_object: String = String::from("Hello");
```

### Ownership Rules
```rust
// 1. Each value has a single owner
let s1 = String::from("hello");
let s2 = s1; // s1 is moved, no longer valid

// 2. Borrowing with references
let s1 = String::from("hello");
let s2 = &s1; // s1 is borrowed, still valid

// 3. Mutable references
let mut s = String::from("hello");
let r1 = &mut s; // Only one mutable reference allowed
```

### Error Handling
```rust
// Result type
fn divide(a: f64, b: f64) -> Result<f64, String> {
    if b == 0.0 {
        Err("Division by zero".to_string())
    } else {
        Ok(a / b)
    }
}

// Option type
fn find_user(id: u32) -> Option<User> {
    // Returns Some(user) or None
}

// Error propagation with ?
fn process_data() -> Result<String, Box<dyn std::error::Error>> {
    let data = read_file("data.txt")?;
    let processed = transform_data(data)?;
    Ok(processed)
}
```

## 🌐 Web Development with Actix-web

### Basic Server Setup
```rust
use actix_web::{web, App, HttpServer, Result, HttpResponse};

#[actix_web::main]
async fn main() -> std::io::Result<()> {
    HttpServer::new(|| {
        App::new()
            .route("/", web::get().to(hello))
            .route("/users", web::post().to(create_user))
            .route("/users/{id}", web::get().to(get_user))
    })
    .bind("127.0.0.1:8080")?
    .run()
    .await
}

async fn hello() -> Result<HttpResponse> {
    Ok(HttpResponse::Ok().json(serde_json::json!({
        "message": "Hello, World!"
    })))
}
```

### Request Handling
```rust
use actix_web::{web, HttpResponse, Result};
use serde::{Deserialize, Serialize};

#[derive(Deserialize)]
struct CreateUserRequest {
    name: String,
    email: String,
}

#[derive(Serialize)]
struct User {
    id: u32,
    name: String,
    email: String,
}

// JSON body extraction
async fn create_user(user_data: web::Json<CreateUserRequest>) -> Result<HttpResponse> {
    let user = User {
        id: 1,
        name: user_data.name.clone(),
        email: user_data.email.clone(),
    };
    Ok(HttpResponse::Created().json(user))
}

// Path parameter extraction
async fn get_user(path: web::Path<u32>) -> Result<HttpResponse> {
    let user_id = path.into_inner();
    // Fetch user logic here
    Ok(HttpResponse::Ok().json(user))
}

// Query parameter extraction
#[derive(Deserialize)]
struct QueryParams {
    page: Option<u32>,
    limit: Option<u32>,
}

async fn list_users(query: web::Query<QueryParams>) -> Result<HttpResponse> {
    let page = query.page.unwrap_or(1);
    let limit = query.limit.unwrap_or(10);
    // List users logic here
    Ok(HttpResponse::Ok().json("users"))
}
```

### Middleware
```rust
use actix_web::{dev::ServiceRequest, Error, HttpMessage};
use actix_web_httpauth::extractors::bearer::BearerAuth;

// Authentication middleware
async fn validator(req: ServiceRequest, credentials: BearerAuth) -> Result<ServiceRequest, Error> {
    // Validate JWT token
    let token = credentials.token();
    if validate_jwt(token) {
        Ok(req)
    } else {
        Err(actix_web::error::ErrorUnauthorized("Invalid token"))
    }
}

// CORS middleware
use actix_cors::Cors;

let cors = Cors::default()
    .allowed_origin("http://localhost:3000")
    .allowed_methods(vec!["GET", "POST", "PUT", "DELETE"])
    .allowed_headers(vec!["Authorization", "Accept", "Content-Type"])
    .max_age(3600);

App::new().wrap(cors)
```

## 🗄️ Database Operations with SQLx

### Setup and Connection
```rust
use sqlx::{PgPool, postgres::PgPoolOptions};

// Create connection pool
let pool = PgPoolOptions::new()
    .max_connections(5)
    .connect("postgresql://user:password@localhost/database")
    .await?;
```

### Query Examples
```rust
use sqlx::{query, query_as, query_scalar};

// Raw query
let users = query!("SELECT id, name, email FROM users WHERE active = $1", true)
    .fetch_all(&pool)
    .await?;

// Query with struct mapping
#[derive(sqlx::FromRow)]
struct User {
    id: i32,
    name: String,
    email: String,
}

let users = query_as!(User, "SELECT id, name, email FROM users")
    .fetch_all(&pool)
    .await?;

// Insert with returning
let user_id = query_scalar!(
    "INSERT INTO users (name, email) VALUES ($1, $2) RETURNING id",
    "John Doe",
    "john@example.com"
)
.fetch_one(&pool)
.await?;

// Transaction
let mut tx = pool.begin().await?;

query!("UPDATE accounts SET balance = balance - $1 WHERE id = $2", amount, from_account)
    .execute(&mut *tx)
    .await?;

query!("UPDATE accounts SET balance = balance + $1 WHERE id = $2", amount, to_account)
    .execute(&mut *tx)
    .await?;

tx.commit().await?;
```

## 🔐 Authentication & Authorization

### JWT Implementation
```rust
use jsonwebtoken::{encode, decode, Header, Algorithm, Validation, EncodingKey, DecodingKey};
use serde::{Deserialize, Serialize};

#[derive(Debug, Serialize, Deserialize)]
struct Claims {
    sub: String,
    exp: usize,
    iat: usize,
}

fn create_jwt(user_id: &str, secret: &str) -> Result<String, jsonwebtoken::errors::Error> {
    let expiration = chrono::Utc::now()
        .checked_add_signed(chrono::Duration::hours(24))
        .unwrap()
        .timestamp() as usize;

    let claims = Claims {
        sub: user_id.to_string(),
        exp: expiration,
        iat: chrono::Utc::now().timestamp() as usize,
    };

    encode(&Header::default(), &claims, &EncodingKey::from_secret(secret.as_ref()))
}

fn validate_jwt(token: &str, secret: &str) -> Result<Claims, jsonwebtoken::errors::Error> {
    decode::<Claims>(
        token,
        &DecodingKey::from_secret(secret.as_ref()),
        &Validation::new(Algorithm::HS256),
    ).map(|data| data.claims)
}
```

### Password Hashing
```rust
use bcrypt::{hash, verify, DEFAULT_COST};

fn hash_password(password: &str) -> Result<String, bcrypt::BcryptError> {
    hash(password, DEFAULT_COST)
}

fn verify_password(password: &str, hash: &str) -> Result<bool, bcrypt::BcryptError> {
    verify(password, hash)
}
```

## 🔍 Testing

### Unit Tests
```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_user_creation() {
        let user = User::new("John", "john@example.com");
        assert_eq!(user.name, "John");
        assert_eq!(user.email, "john@example.com");
    }

    #[tokio::test]
    async fn test_async_function() {
        let result = async_function().await;
        assert!(result.is_ok());
    }
}
```

### Integration Tests
```rust
use actix_web::{test, App};

#[actix_rt::test]
async fn test_get_users() {
    let app = test::init_service(
        App::new().route("/users", web::get().to(get_users))
    ).await;

    let req = test::TestRequest::get()
        .uri("/users")
        .to_request();

    let resp = test::call_service(&app, req).await;
    assert!(resp.status().is_success());
}
```

## 📊 Logging and Monitoring

### Structured Logging with Tracing
```rust
use tracing::{info, warn, error, instrument};

#[instrument]
async fn process_user_request(user_id: u32) -> Result<User, AppError> {
    info!("Processing request for user {}", user_id);
    
    match get_user_from_db(user_id).await {
        Ok(user) => {
            info!("Successfully retrieved user");
            Ok(user)
        }
        Err(e) => {
            error!("Failed to retrieve user: {}", e);
            Err(AppError::UserNotFound)
        }
    }
}

// Initialize tracing
use tracing_subscriber;

fn init_logging() {
    tracing_subscriber::fmt()
        .with_env_filter("info")
        .init();
}
```

### Metrics with Prometheus
```rust
use prometheus::{Counter, Histogram, Gauge, register_counter, register_histogram};

lazy_static! {
    static ref HTTP_REQUESTS: Counter = register_counter!(
        "http_requests_total", 
        "Total number of HTTP requests"
    ).unwrap();
    
    static ref REQUEST_DURATION: Histogram = register_histogram!(
        "http_request_duration_seconds", 
        "HTTP request duration"
    ).unwrap();
}

async fn metrics_middleware(req: ServiceRequest, srv: &mut S) -> Result<ServiceResponse, Error> {
    let start = std::time::Instant::now();
    
    let res = srv.call(req).await?;
    
    HTTP_REQUESTS.inc();
    REQUEST_DURATION.observe(start.elapsed().as_secs_f64());
    
    Ok(res)
}
```

## 🐳 Docker Configuration

### Multi-stage Dockerfile
```dockerfile
FROM rust:1.75 as builder
WORKDIR /app
COPY Cargo.toml Cargo.lock ./
RUN mkdir src && echo "fn main() {}" > src/main.rs
RUN cargo build --release
COPY src ./src
RUN touch src/main.rs && cargo build --release

FROM debian:bookworm-slim
RUN apt-get update && apt-get install -y ca-certificates && rm -rf /var/lib/apt/lists/*
WORKDIR /app
COPY --from=builder /app/target/release/app .
EXPOSE 8080
CMD ["./app"]
```

## 🚀 Performance Tips

### Memory Management
```rust
// Use Box for heap allocation
let large_data = Box::new(very_large_struct);

// Use Rc for shared ownership
use std::rc::Rc;
let shared_data = Rc::new(data);

// Use Arc for thread-safe shared ownership
use std::sync::Arc;
let shared_data = Arc::new(data);
```

### Async Best Practices
```rust
// Use spawn for concurrent tasks
use tokio::task;

let handle1 = task::spawn(async { process_data_1().await });
let handle2 = task::spawn(async { process_data_2().await });

let (result1, result2) = tokio::try_join!(handle1, handle2)?;

// Use channels for communication
use tokio::sync::mpsc;

let (tx, mut rx) = mpsc::channel(100);

// Send data
tx.send(data).await?;

// Receive data
while let Some(data) = rx.recv().await {
    process(data).await;
}
```

## 🔧 Common Patterns

### Repository Pattern
```rust
#[async_trait]
pub trait UserRepository {
    async fn find_by_id(&self, id: u32) -> Result<Option<User>, Error>;
    async fn create(&self, user: CreateUserRequest) -> Result<User, Error>;
    async fn update(&self, id: u32, user: UpdateUserRequest) -> Result<User, Error>;
    async fn delete(&self, id: u32) -> Result<(), Error>;
}

pub struct PostgresUserRepository {
    pool: PgPool,
}

#[async_trait]
impl UserRepository for PostgresUserRepository {
    async fn find_by_id(&self, id: u32) -> Result<Option<User>, Error> {
        // Implementation
    }
}
```

### Builder Pattern
```rust
#[derive(Default)]
pub struct ApiClientBuilder {
    base_url: Option<String>,
    timeout: Option<Duration>,
    headers: HashMap<String, String>,
}

impl ApiClientBuilder {
    pub fn new() -> Self {
        Self::default()
    }

    pub fn base_url(mut self, url: impl Into<String>) -> Self {
        self.base_url = Some(url.into());
        self
    }

    pub fn timeout(mut self, timeout: Duration) -> Self {
        self.timeout = Some(timeout);
        self
    }

    pub fn build(self) -> Result<ApiClient, BuildError> {
        Ok(ApiClient {
            base_url: self.base_url.ok_or(BuildError::MissingBaseUrl)?,
            timeout: self.timeout.unwrap_or(Duration::from_secs(30)),
            headers: self.headers,
        })
    }
}
```

## 📚 Essential Crates for Backend Development

```toml
[dependencies]
# Web framework
actix-web = "4.0"

# Async runtime
tokio = { version = "1.0", features = ["full"] }

# Database
sqlx = { version = "0.7", features = ["postgres", "runtime-tokio-rustls", "chrono", "uuid"] }

# Serialization
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"

# Date/Time
chrono = { version = "0.4", features = ["serde"] }

# UUID
uuid = { version = "1.0", features = ["v4", "serde"] }

# Error handling
thiserror = "1.0"
anyhow = "1.0"

# Authentication
jsonwebtoken = "8.0"
bcrypt = "0.14"

# Logging
tracing = "0.1"
tracing-subscriber = "0.3"

# Environment variables
dotenv = "0.15"

# Testing
tokio-test = "0.4"
```

This cheat sheet covers the most common patterns and operations you'll use in Rust backend development. Keep it handy for quick reference!
