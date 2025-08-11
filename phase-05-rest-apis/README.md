# Phase 5: REST APIs (3-4 weeks)

## 🎯 Learning Objectives
By the end of this phase, you will:
- Build production-ready REST APIs
- Implement authentication and authorization
- Create middleware for cross-cutting concerns
- Handle API versioning and documentation
- Implement rate limiting and security measures
- Design scalable API architectures

## 📚 Study Materials

### Week 1: Authentication and Security

#### Day 1-2: Authentication Fundamentals
- **Resources:**
  - [JWT.io Introduction](https://jwt.io/introduction)
  - [OAuth 2.0 Simplified](https://aaronparecki.com/oauth-2-simplified/)
  - [Authentication vs Authorization](https://auth0.com/docs/get-started/identity-fundamentals/authentication-and-authorization)
  
- **Topics to Study:**
  - Session-based vs token-based authentication
  - JSON Web Tokens (JWT) structure and usage
  - OAuth 2.0 and OpenID Connect
  - Password hashing and salting
  - Multi-factor authentication basics

#### Day 3-4: JWT Implementation in Rust
- **Resources:**
  - [jsonwebtoken crate documentation](https://docs.rs/jsonwebtoken/)
  - [JWT Best Practices](https://auth0.com/blog/a-look-at-the-latest-draft-for-jwt-bcp/)
  
- **Topics to Study:**
  - Creating and validating JWTs
  - Token refresh strategies
  - Secure token storage
  - Claims and custom data
  - Token expiration handling

#### Day 5-7: Authorization and RBAC
- **Resources:**
  - [RBAC Explained](https://en.wikipedia.org/wiki/Role-based_access_control)
  - [ABAC vs RBAC](https://www.osohq.com/post/rbac-vs-abac)
  
- **Topics to Study:**
  - Role-Based Access Control (RBAC)
  - Permission systems
  - Resource-based authorization
  - Middleware for authorization
  - Policy-based access control

### Week 2: API Design and Documentation

#### Day 1-2: RESTful API Design
- **Resources:**
  - [REST API Design Best Practices](https://blog.restcase.com/rest-api-design-best-practices/)
  - [Richardson Maturity Model](https://martinfowler.com/articles/richardsonMaturityModel.html)
  
- **Topics to Study:**
  - REST principles and constraints
  - Resource naming conventions
  - HTTP methods and status codes
  - HATEOAS (Hypermedia as the Engine of Application State)
  - API versioning strategies

#### Day 3-4: API Documentation with OpenAPI
- **Resources:**
  - [OpenAPI Specification](https://spec.openapis.org/oas/v3.0.3)
  - [Swagger/OpenAPI with Rust](https://docs.rs/utoipa/)
  
- **Topics to Study:**
  - OpenAPI 3.0 specification
  - Generating documentation from code
  - Interactive API documentation
  - Schema validation
  - API testing with documentation

#### Day 5-7: Input Validation and Error Handling
- **Resources:**
  - [Input Validation Guide](https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html)
  - [API Error Handling Best Practices](https://blog.restcase.com/rest-api-error-codes-101/)
  
- **Topics to Study:**
  - Request validation strategies
  - Custom validation rules
  - Error response formats
  - Problem Details for HTTP APIs (RFC 7807)
  - Graceful error handling

### Week 3: Middleware and Advanced Features

#### Day 1-3: Middleware Development
- **Resources:**
  - [Actix-web Middleware Guide](https://actix.rs/docs/middleware/)
  - [Middleware Patterns](https://en.wikipedia.org/wiki/Middleware)
  
- **Topics to Study:**
  - Custom middleware creation
  - Request/response transformation
  - Logging and monitoring middleware
  - CORS handling
  - Request ID tracking

#### Day 4-5: Rate Limiting and Throttling
- **Resources:**
  - [Rate Limiting Algorithms](https://konghq.com/blog/how-to-design-a-scalable-rate-limiting-algorithm)
  - [API Rate Limiting Best Practices](https://nordicapis.com/everything-you-need-to-know-about-api-rate-limiting/)
  
- **Topics to Study:**
  - Token bucket algorithm
  - Sliding window rate limiting
  - Distributed rate limiting
  - Rate limit headers
  - Graceful degradation

#### Day 6-7: Caching Strategies
- **Resources:**
  - [HTTP Caching Guide](https://web.dev/http-cache/)
  - [Redis Caching Patterns](https://redis.io/docs/manual/patterns/)
  
- **Topics to Study:**
  - HTTP caching headers
  - Application-level caching
  - Redis integration
  - Cache invalidation strategies
  - CDN integration concepts

## 💻 Practice Exercises

### Exercise 1: Authentication System
Build a complete authentication system:
1. User registration with email verification
2. JWT-based login/logout
3. Password reset functionality
4. Token refresh mechanism
5. Rate limiting for auth endpoints

### Exercise 2: Authorization Middleware
Create flexible authorization:
1. Role-based permissions
2. Resource-level access control
3. Dynamic permission checking
4. Admin vs user access patterns
5. API key authentication

### Exercise 3: API Documentation
Document your APIs properly:
1. Generate OpenAPI specs from code
2. Create interactive documentation
3. Add request/response examples
4. Document error responses
5. Version your API documentation

## 🚀 Phase 5 Project: Multi-Tenant SaaS API Platform

### Project Requirements

Build a multi-tenant SaaS platform API that can serve multiple organizations:

#### Core Features:
1. **Multi-Tenancy**: Organizations with isolated data
2. **User Management**: Users, roles, and permissions per organization
3. **API Key Management**: API keys for external integrations
4. **Resource Management**: CRUD operations with proper authorization
5. **Analytics**: Usage tracking and reporting
6. **Billing Integration**: Usage-based billing tracking

#### Multi-Tenant Architecture:
```rust
// Tenant isolation models
#[derive(Serialize, Deserialize, Debug)]
pub struct Organization {
    pub id: Uuid,
    pub name: String,
    pub slug: String,          // Unique identifier
    pub plan: SubscriptionPlan,
    pub created_at: DateTime<Utc>,
    pub is_active: bool,
}

#[derive(Serialize, Deserialize, Debug)]
pub struct User {
    pub id: Uuid,
    pub email: String,
    pub organization_id: Uuid,
    pub role: UserRole,
    pub permissions: Vec<Permission>,
    pub created_at: DateTime<Utc>,
}

#[derive(Serialize, Deserialize, Debug)]
pub struct ApiKey {
    pub id: Uuid,
    pub organization_id: Uuid,
    pub name: String,
    pub key_hash: String,
    pub permissions: Vec<String>,
    pub rate_limit: Option<u32>,
    pub expires_at: Option<DateTime<Utc>>,
}
```

#### API Endpoints:
```
# Authentication
POST   /api/v1/auth/register           - Register new organization
POST   /api/v1/auth/login              - User login
POST   /api/v1/auth/refresh            - Refresh access token
POST   /api/v1/auth/logout             - Logout

# Organization Management
GET    /api/v1/organizations           - List user's organizations
POST   /api/v1/organizations           - Create new organization
GET    /api/v1/organizations/{id}      - Get organization details
PUT    /api/v1/organizations/{id}      - Update organization
DELETE /api/v1/organizations/{id}      - Delete organization

# User Management (within organization)
GET    /api/v1/users                   - List organization users
POST   /api/v1/users                   - Invite new user
GET    /api/v1/users/{id}              - Get user details
PUT    /api/v1/users/{id}/role         - Update user role
DELETE /api/v1/users/{id}              - Remove user

# API Key Management
GET    /api/v1/api-keys                - List API keys
POST   /api/v1/api-keys                - Create API key
PUT    /api/v1/api-keys/{id}           - Update API key
DELETE /api/v1/api-keys/{id}           - Revoke API key

# Resources (example: projects)
GET    /api/v1/projects                - List projects
POST   /api/v1/projects                - Create project
GET    /api/v1/projects/{id}           - Get project
PUT    /api/v1/projects/{id}           - Update project
DELETE /api/v1/projects/{id}           - Delete project

# Analytics
GET    /api/v1/analytics/usage         - API usage statistics
GET    /api/v1/analytics/billing       - Billing information
```

#### Technical Requirements:
- **Authentication**: JWT-based with refresh tokens
- **Authorization**: RBAC with resource-level permissions
- **Multi-tenancy**: Data isolation by organization
- **Rate Limiting**: Per-organization and per-API-key limits
- **API Versioning**: Support multiple API versions
- **Documentation**: Auto-generated OpenAPI specs
- **Monitoring**: Request logging and metrics
- **Validation**: Comprehensive input validation
- **Caching**: Redis-based caching for frequently accessed data

#### Project Structure:
```
phase-05-project/
├── Cargo.toml
├── .env
├── docker-compose.yml
├── migrations/
├── src/
│   ├── main.rs
│   ├── lib.rs
│   ├── config.rs
│   ├── database.rs
│   ├── cache.rs
│   ├── auth/
│   │   ├── mod.rs
│   │   ├── jwt.rs
│   │   ├── middleware.rs
│   │   └── permissions.rs
│   ├── middleware/
│   │   ├── mod.rs
│   │   ├── tenant.rs
│   │   ├── rate_limit.rs
│   │   ├── logging.rs
│   │   └── cors.rs
│   ├── models/
│   │   ├── mod.rs
│   │   ├── organization.rs
│   │   ├── user.rs
│   │   ├── api_key.rs
│   │   └── project.rs
│   ├── handlers/
│   │   ├── mod.rs
│   │   ├── auth.rs
│   │   ├── organizations.rs
│   │   ├── users.rs
│   │   ├── api_keys.rs
│   │   └── projects.rs
│   ├── services/
│   │   ├── mod.rs
│   │   ├── auth_service.rs
│   │   ├── organization_service.rs
│   │   └── analytics_service.rs
│   ├── repositories/
│   │   ├── mod.rs
│   │   └── ...
│   ├── utils/
│   │   ├── mod.rs
│   │   ├── validation.rs
│   │   └── crypto.rs
│   └── errors.rs
├── docs/
│   └── api.yaml
├── tests/
└── README.md
```

#### Example Implementation:
```rust
// Tenant isolation middleware
pub struct TenantMiddleware;

impl<S, B> Transform<S, ServiceRequest> for TenantMiddleware
where
    S: Service<ServiceRequest, Response = ServiceResponse<B>, Error = Error>,
    S::Future: 'static,
    B: 'static,
{
    type Response = ServiceResponse<B>;
    type Error = Error;
    type Transform = TenantMiddlewareService<S>;
    type InitError = ();
    type Future = Ready<Result<Self::Transform, Self::InitError>>;

    fn new_transform(&self, service: S) -> Self::Future {
        ready(Ok(TenantMiddlewareService { service }))
    }
}

// Rate limiting implementation
pub struct RateLimiter {
    redis: redis::Client,
}

impl RateLimiter {
    pub async fn check_rate_limit(
        &self,
        key: &str,
        limit: u32,
        window: Duration,
    ) -> Result<bool, RateLimitError> {
        let mut conn = self.redis.get_async_connection().await?;
        
        let current_count: u32 = conn.incr(key, 1).await?;
        if current_count == 1 {
            conn.expire(key, window.as_secs() as usize).await?;
        }
        
        Ok(current_count <= limit)
    }
}

// Permission checking
#[derive(Debug, Clone)]
pub enum Permission {
    ReadProjects,
    WriteProjects,
    ManageUsers,
    ViewAnalytics,
    ManageApiKeys,
}

pub fn check_permission(
    user: &User,
    required_permission: Permission,
    resource_id: Option<Uuid>,
) -> Result<(), AuthError> {
    if user.permissions.contains(&required_permission) {
        Ok(())
    } else {
        Err(AuthError::InsufficientPermissions)
    }
}
```

### Assessment Criteria:
- [ ] Multi-tenant architecture properly implemented
- [ ] Comprehensive authentication and authorization
- [ ] Rate limiting works for different scenarios
- [ ] API documentation is complete and accurate
- [ ] All endpoints properly validated and secured
- [ ] Middleware functions correctly
- [ ] Performance considerations addressed
- [ ] Comprehensive test coverage

### Bonus Challenges:
- Implement OAuth 2.0 provider functionality
- Add webhook system for external integrations
- Implement API usage analytics dashboard
- Add support for custom domains per organization
- Implement data export/import functionality
- Add real-time notifications via WebSockets
- Implement audit logging for all operations
- Add API mocking/testing features

## 🔧 Dependencies

Your `Cargo.toml` should include:
```toml
[dependencies]
actix-web = "4.0"
sqlx = { version = "0.7", features = ["runtime-tokio-rustls", "postgres", "chrono", "uuid", "json"] }
redis = { version = "0.23", features = ["tokio-comp"] }
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
chrono = { version = "0.4", features = ["serde"] }
uuid = { version = "1.0", features = ["v4", "serde"] }
jsonwebtoken = "8.0"
bcrypt = "0.14"
validator = { version = "0.16", features = ["derive"] }
utoipa = { version = "4.0", features = ["actix_extras"] }
utoipa-swagger-ui = { version = "4.0", features = ["actix-web"] }
thiserror = "1.0"
anyhow = "1.0"
tokio = { version = "1.0", features = ["full"] }
tracing = "0.1"
tracing-subscriber = "0.3"
```

## 📝 REST API Development Tips
1. **Design first**: Plan your API before implementation
2. **Consistent naming**: Use clear, consistent resource names
3. **Proper status codes**: Return appropriate HTTP status codes
4. **Version your APIs**: Plan for API evolution
5. **Validate everything**: Never trust client input
6. **Document thoroughly**: Good documentation is crucial
7. **Monitor performance**: Track API metrics and usage

## 🔗 Additional Resources
- [REST API Design Principles](https://restfulapi.net/)
- [HTTP Status Codes Reference](https://httpstatuses.com/)
- [JWT Best Practices](https://auth0.com/blog/a-look-at-the-latest-draft-for-jwt-bcp/)
- [OpenAPI Specification](https://spec.openapis.org/oas/v3.0.3)
- [API Security Best Practices](https://owasp.org/www-project-api-security/)

## ✅ Phase Completion Checklist
- [ ] Built production-ready REST APIs
- [ ] Implemented secure authentication and authorization
- [ ] Created reusable middleware components
- [ ] Generated comprehensive API documentation
- [ ] Implemented rate limiting and security measures
- [ ] Built the multi-tenant SaaS platform successfully
- [ ] All security tests pass

**Next Phase Preview**: In Phase 6, you'll dive into advanced topics like async programming patterns, performance optimization, and monitoring!
