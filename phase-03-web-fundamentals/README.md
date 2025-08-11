# Phase 3: Web Fundamentals (2-3 weeks)

## 🎯 Learning Objectives
By the end of this phase, you will:
- Understand HTTP protocol and web server basics
- Build simple web servers using Rust
- Handle HTTP requests and responses
- Work with JSON data and APIs
- Understand async programming basics

## 📚 Study Materials

### Week 1: HTTP and Web Basics

#### Day 1-2: HTTP Protocol Fundamentals
- **Resources:**
  - [MDN HTTP Guide](https://developer.mozilla.org/en-US/docs/Web/HTTP)
  - [HTTP/1.1 Specification](https://tools.ietf.org/html/rfc2616)
  - [REST API Design Guide](https://restfulapi.net/)
  
- **Topics to Study:**
  - HTTP methods (GET, POST, PUT, DELETE, etc.)
  - Status codes and their meanings
  - Headers and their purposes
  - Request/response cycle
  - REST architectural principles

#### Day 3-4: Basic Web Server with std
- **Resources:**
  - [Rust Book Chapter 20](https://doc.rust-lang.org/book/ch20-00-final-project-building-a-multithreaded-web-server.html)
  - [std::net documentation](https://doc.rust-lang.org/std/net/)
  
- **Topics to Study:**
  - TCP listeners and streams
  - Parsing HTTP requests manually
  - Building HTTP responses
  - Basic routing
  - Serving static files

#### Day 5-7: JSON and Serialization
- **Resources:**
  - [serde documentation](https://serde.rs/)
  - [JSON RFC](https://tools.ietf.org/html/rfc7159)
  
- **Topics to Study:**
  - JSON format and structure
  - Serialization and deserialization with serde
  - Custom serialization logic
  - Working with nested data structures
  - Error handling in serialization

### Week 2: Web Frameworks and Async

#### Day 1-3: Introduction to Actix-web
- **Resources:**
  - [Actix-web Documentation](https://actix.rs/)
  - [Actix-web Examples](https://github.com/actix/examples)
  
- **Topics to Study:**
  - Setting up an Actix-web application
  - Handlers and routing
  - Extractors (Path, Query, Json)
  - Middleware basics
  - Application state

#### Day 4-5: Async Programming Basics
- **Resources:**
  - [Async Book](https://rust-lang.github.io/async-book/)
  - [Tokio Tutorial](https://tokio.rs/tokio/tutorial)
  
- **Topics to Study:**
  - What is async/await?
  - Futures and tasks
  - Async runtimes (Tokio)
  - Basic async patterns
  - Async error handling

#### Day 6-7: HTTP Client Programming
- **Resources:**
  - [reqwest documentation](https://docs.rs/reqwest/)
  - [HTTP Client Best Practices](https://docs.rs/reqwest/latest/reqwest/)
  
- **Topics to Study:**
  - Making HTTP requests
  - Handling responses
  - Authentication headers
  - Timeouts and retries
  - Connection pooling

## 💻 Practice Exercises

### Exercise 1: Manual HTTP Server
Build a basic HTTP server using only std library:
1. Listen on a port and accept connections
2. Parse HTTP requests manually
3. Route to different handlers based on path
4. Return proper HTTP responses
5. Serve static HTML files

### Exercise 2: JSON API with serde
Create JSON processing utilities:
1. Define structs for API data
2. Implement custom serialization
3. Handle nested JSON structures
4. Validate JSON data
5. Convert between different formats

### Exercise 3: Async Practice
Build async applications:
1. Fetch data from multiple APIs concurrently
2. Implement timeout handling
3. Use async channels for communication
4. Practice error propagation in async contexts

## 🚀 Phase 3 Project: Personal Blog API

### Project Requirements

Build a RESTful API for a personal blog with the following features:

#### Core Features:
1. **Post Management**: CRUD operations for blog posts
2. **Category System**: Organize posts by categories
3. **Comment System**: Allow comments on posts
4. **Search Functionality**: Search posts by title, content, or tags
5. **Content Formatting**: Support Markdown content

#### API Endpoints:
```
GET    /api/posts              - List all posts (with pagination)
GET    /api/posts/{id}         - Get a specific post
POST   /api/posts              - Create a new post
PUT    /api/posts/{id}         - Update a post
DELETE /api/posts/{id}         - Delete a post

GET    /api/posts/{id}/comments - Get comments for a post
POST   /api/posts/{id}/comments - Add a comment to a post

GET    /api/categories          - List all categories
POST   /api/categories          - Create a new category

GET    /api/search?q={query}    - Search posts
```

#### Data Models:
```rust
#[derive(Serialize, Deserialize, Debug, Clone)]
pub struct Post {
    pub id: u64,
    pub title: String,
    pub content: String,
    pub author: String,
    pub category_id: Option<u64>,
    pub tags: Vec<String>,
    pub created_at: DateTime<Utc>,
    pub updated_at: DateTime<Utc>,
    pub published: bool,
}

#[derive(Serialize, Deserialize, Debug, Clone)]
pub struct Comment {
    pub id: u64,
    pub post_id: u64,
    pub author: String,
    pub content: String,
    pub created_at: DateTime<Utc>,
}

#[derive(Serialize, Deserialize, Debug, Clone)]
pub struct Category {
    pub id: u64,
    pub name: String,
    pub description: Option<String>,
}
```

#### Technical Requirements:
- Use Actix-web framework
- Implement proper error handling and status codes
- Use JSON for all data exchange
- Implement request validation
- Add basic logging
- Support CORS for frontend integration
- Store data in memory (files) for now

#### Project Structure:
```
phase-03-project/
├── Cargo.toml
├── src/
│   ├── main.rs
│   ├── lib.rs
│   ├── models/
│   │   ├── mod.rs
│   │   ├── post.rs
│   │   ├── comment.rs
│   │   └── category.rs
│   ├── handlers/
│   │   ├── mod.rs
│   │   ├── posts.rs
│   │   ├── comments.rs
│   │   └── categories.rs
│   ├── services/
│   │   ├── mod.rs
│   │   └── blog_service.rs
│   ├── storage/
│   │   ├── mod.rs
│   │   └── memory_storage.rs
│   ├── errors.rs
│   └── utils.rs
├── static/
│   └── index.html
├── tests/
│   └── api_tests.rs
└── README.md
```

#### Example Implementation:
```rust
// Handler example
pub async fn create_post(
    post_data: web::Json<CreatePostRequest>,
    app_state: web::Data<AppState>,
) -> Result<impl Responder, BlogError> {
    let post = app_state.blog_service.create_post(post_data.into_inner()).await?;
    Ok(web::Json(post))
}

// Error handling
#[derive(Debug, thiserror::Error)]
pub enum BlogError {
    #[error("Post not found")]
    PostNotFound,
    
    #[error("Invalid input: {0}")]
    InvalidInput(String),
    
    #[error("Internal server error")]
    InternalError,
}

impl ResponseError for BlogError {
    fn status_code(&self) -> StatusCode {
        match self {
            BlogError::PostNotFound => StatusCode::NOT_FOUND,
            BlogError::InvalidInput(_) => StatusCode::BAD_REQUEST,
            BlogError::InternalError => StatusCode::INTERNAL_SERVER_ERROR,
        }
    }
}
```

#### Testing Requirements:
- Unit tests for all handlers
- Integration tests for API endpoints
- Test error conditions and edge cases
- Use test client for HTTP testing

### Assessment Criteria:
- [ ] All API endpoints work correctly
- [ ] Proper HTTP status codes and error handling
- [ ] Clean JSON API design
- [ ] Good code organization and structure
- [ ] Comprehensive tests
- [ ] Proper async/await usage
- [ ] Documentation and examples

### Bonus Challenges:
- Add pagination for post listings
- Implement post drafts vs published posts
- Add image upload functionality
- Implement basic authentication
- Add rate limiting middleware
- Create a simple web frontend
- Add RSS feed generation
- Implement content caching

## 🔧 Dependencies

Your `Cargo.toml` should include:
```toml
[dependencies]
actix-web = "4.0"
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
chrono = { version = "0.4", features = ["serde"] }
thiserror = "1.0"
anyhow = "1.0"
tokio = { version = "1.0", features = ["full"] }
uuid = { version = "1.0", features = ["v4", "serde"] }
env_logger = "0.10"
log = "0.4"

[dev-dependencies]
actix-rt = "2.0"
actix-test = "0.1"
```

## 📝 Web Development Tips
1. **Status codes matter**: Use appropriate HTTP status codes
2. **Error responses**: Always return meaningful error messages
3. **Validation**: Validate all incoming data
4. **Logging**: Add proper logging for debugging
5. **Testing**: Test all endpoints thoroughly
6. **Documentation**: Document your API clearly

## 🔗 Additional Resources
- [HTTP Status Codes Reference](https://httpstatuses.com/)
- [REST API Design Best Practices](https://blog.restcase.com/rest-api-design-best-practices/)
- [Actix-web User Guide](https://actix.rs/docs/)
- [Async Programming in Rust](https://rust-lang.github.io/async-book/)
- [JSON API Specification](https://jsonapi.org/)

## ✅ Phase Completion Checklist
- [ ] Understands HTTP protocol fundamentals
- [ ] Can build web servers with Actix-web
- [ ] Handles JSON data effectively
- [ ] Implements proper error handling
- [ ] Uses async/await correctly
- [ ] Built the blog API successfully
- [ ] All tests pass

**Next Phase Preview**: In Phase 4, you'll learn database integration, working with SQL databases, ORMs, and building persistent storage for your applications!
