# Phase 4: Database Integration (2-3 weeks)

## 🎯 Learning Objectives
By the end of this phase, you will:
- Understand relational database concepts
- Work with SQL databases in Rust
- Use ORMs and query builders effectively
- Handle database migrations
- Implement connection pooling
- Design efficient database schemas

## 📚 Study Materials

### Week 1: SQL and Database Fundamentals

#### Day 1-2: SQL Basics
- **Resources:**
  - [SQL Tutorial (W3Schools)](https://www.w3schools.com/sql/)
  - [PostgreSQL Tutorial](https://www.postgresqltutorial.com/)
  - [Database Design Fundamentals](https://www.lucidchart.com/pages/database-diagram/database-design)
  
- **Topics to Study:**
  - Basic SQL operations (SELECT, INSERT, UPDATE, DELETE)
  - Joins and relationships
  - Indexes and performance
  - Transactions and ACID properties
  - Database normalization

#### Day 3-4: PostgreSQL Setup and Advanced SQL
- **Resources:**
  - [PostgreSQL Documentation](https://www.postgresql.org/docs/)
  - [Advanced SQL Patterns](https://www.postgresql.org/docs/current/sql.html)
  
- **Topics to Study:**
  - PostgreSQL installation and setup
  - Advanced queries (subqueries, CTEs, window functions)
  - Database constraints and triggers
  - JSON/JSONB in PostgreSQL
  - Full-text search

#### Day 5-7: Database Design Principles
- **Resources:**
  - [Database Design Best Practices](https://www.vertabelo.com/blog/database-design-best-practices/)
  - [Schema Design Patterns](https://docs.microsoft.com/en-us/azure/architecture/patterns/)
  
- **Topics to Study:**
  - Entity-Relationship (ER) modeling
  - Normalization vs denormalization
  - Indexing strategies
  - Schema versioning and migrations
  - Performance considerations

### Week 2: Rust Database Integration

#### Day 1-3: SQLx - Async SQL with Compile-time Checking
- **Resources:**
  - [SQLx Documentation](https://docs.rs/sqlx/)
  - [SQLx GitHub Examples](https://github.com/launchbadge/sqlx/tree/main/examples)
  
- **Topics to Study:**
  - Setting up SQLx with PostgreSQL
  - Raw SQL queries with type safety
  - Query macros and compile-time verification
  - Connection pools and async operations
  - Transactions and error handling

#### Day 4-5: Diesel ORM
- **Resources:**
  - [Diesel Documentation](https://diesel.rs/)
  - [Diesel Getting Started Guide](https://diesel.rs/guides/getting-started)
  
- **Topics to Study:**
  - Setting up Diesel
  - Schema definition and migrations
  - Query builder patterns
  - Associations and joins
  - Custom types and SQL functions

#### Day 6-7: Database Migrations and Schema Management
- **Resources:**
  - [Database Migration Best Practices](https://www.brunton-spall.co.uk/post/2014/05/06/database-migrations-done-right/)
  - [SQLx Migrations](https://docs.rs/sqlx/latest/sqlx/migrate/index.html)
  
- **Topics to Study:**
  - Migration strategies
  - Schema versioning
  - Rollback procedures
  - Data seeding and fixtures
  - Environment-specific configurations

## 💻 Practice Exercises

### Exercise 1: SQL Mastery
Practice complex SQL operations:
1. Design a multi-table schema for an e-commerce system
2. Write complex queries with multiple joins
3. Optimize queries with proper indexing
4. Practice transactions and error scenarios
5. Implement full-text search features

### Exercise 2: SQLx Integration
Build database operations with SQLx:
1. Connect to PostgreSQL with connection pooling
2. Implement CRUD operations with compile-time checking
3. Handle complex data types and JSON
4. Write transaction-based operations
5. Implement proper error handling

### Exercise 3: Migration Management
Practice database evolution:
1. Create initial schema migrations
2. Add new tables and columns
3. Modify existing structures safely
4. Handle data transformations
5. Test rollback procedures

## 🚀 Phase 4 Project: E-commerce Backend with Database

### Project Requirements

Extend your blog API into a full e-commerce backend with proper database integration:

#### Core Features:
1. **Product Catalog**: Complete product management with categories
2. **User Management**: User registration, profiles, and authentication
3. **Shopping Cart**: Session-based and persistent cart functionality
4. **Order System**: Complete order processing workflow
5. **Inventory Management**: Stock tracking and low-stock alerts
6. **Analytics**: Sales reports and product analytics

#### Database Schema:
```sql
-- Users table
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    first_name VARCHAR(100) NOT NULL,
    last_name VARCHAR(100) NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Categories table
CREATE TABLE categories (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) UNIQUE NOT NULL,
    description TEXT,
    parent_id INTEGER REFERENCES categories(id),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Products table
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    description TEXT,
    price DECIMAL(10,2) NOT NULL,
    category_id INTEGER REFERENCES categories(id),
    stock_quantity INTEGER DEFAULT 0,
    sku VARCHAR(100) UNIQUE NOT NULL,
    is_active BOOLEAN DEFAULT true,
    metadata JSONB,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Orders table
CREATE TABLE orders (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id),
    status VARCHAR(50) DEFAULT 'pending',
    total_amount DECIMAL(10,2) NOT NULL,
    shipping_address JSONB NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Order items table
CREATE TABLE order_items (
    id SERIAL PRIMARY KEY,
    order_id INTEGER REFERENCES orders(id),
    product_id INTEGER REFERENCES products(id),
    quantity INTEGER NOT NULL,
    unit_price DECIMAL(10,2) NOT NULL,
    total_price DECIMAL(10,2) NOT NULL
);

-- Cart items table (for persistent carts)
CREATE TABLE cart_items (
    id SERIAL PRIMARY KEY,
    user_id INTEGER REFERENCES users(id),
    product_id INTEGER REFERENCES products(id),
    quantity INTEGER NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
    UNIQUE(user_id, product_id)
);
```

#### API Endpoints:
```
# Products
GET    /api/products                    - List products (with filtering)
GET    /api/products/{id}               - Get product details
POST   /api/products                    - Create product (admin)
PUT    /api/products/{id}               - Update product (admin)
DELETE /api/products/{id}               - Delete product (admin)

# Categories
GET    /api/categories                  - List categories
POST   /api/categories                  - Create category (admin)

# Users
POST   /api/auth/register               - User registration
POST   /api/auth/login                  - User login
GET    /api/users/profile               - Get user profile
PUT    /api/users/profile               - Update profile

# Cart
GET    /api/cart                        - Get user's cart
POST   /api/cart/items                  - Add item to cart
PUT    /api/cart/items/{product_id}     - Update cart item quantity
DELETE /api/cart/items/{product_id}     - Remove item from cart

# Orders
GET    /api/orders                      - List user's orders
POST   /api/orders                      - Create order from cart
GET    /api/orders/{id}                 - Get order details
PUT    /api/orders/{id}/status          - Update order status (admin)
```

#### Technical Requirements:
- Use SQLx for database operations
- Implement proper database migrations
- Use connection pooling
- Handle transactions correctly
- Implement proper error handling
- Add database indexes for performance
- Use JSON fields for flexible data
- Implement soft deletes where appropriate

#### Project Structure:
```
phase-04-project/
├── Cargo.toml
├── .env
├── migrations/
│   ├── 001_initial_schema.sql
│   ├── 002_add_categories.sql
│   └── 003_add_indexes.sql
├── src/
│   ├── main.rs
│   ├── lib.rs
│   ├── config.rs
│   ├── database.rs
│   ├── models/
│   │   ├── mod.rs
│   │   ├── user.rs
│   │   ├── product.rs
│   │   ├── category.rs
│   │   ├── cart.rs
│   │   └── order.rs
│   ├── handlers/
│   │   ├── mod.rs
│   │   ├── auth.rs
│   │   ├── products.rs
│   │   ├── cart.rs
│   │   └── orders.rs
│   ├── services/
│   │   ├── mod.rs
│   │   ├── user_service.rs
│   │   ├── product_service.rs
│   │   ├── cart_service.rs
│   │   └── order_service.rs
│   ├── repositories/
│   │   ├── mod.rs
│   │   ├── user_repository.rs
│   │   ├── product_repository.rs
│   │   ├── cart_repository.rs
│   │   └── order_repository.rs
│   └── errors.rs
├── tests/
│   ├── integration_tests.rs
│   └── helpers/
│       └── test_db.rs
└── README.md
```

#### Example Implementation:
```rust
// Product repository with SQLx
#[derive(Debug)]
pub struct ProductRepository {
    pool: PgPool,
}

impl ProductRepository {
    pub async fn find_by_category(&self, category_id: i32) -> Result<Vec<Product>, sqlx::Error> {
        sqlx::query_as!(
            Product,
            r#"
            SELECT id, name, description, price, category_id, 
                   stock_quantity, sku, is_active, metadata,
                   created_at, updated_at
            FROM products 
            WHERE category_id = $1 AND is_active = true
            ORDER BY name
            "#,
            category_id
        )
        .fetch_all(&self.pool)
        .await
    }

    pub async fn update_stock(&self, product_id: i32, quantity_change: i32) -> Result<(), AppError> {
        let mut tx = self.pool.begin().await?;
        
        // Check current stock
        let current_stock: i32 = sqlx::query_scalar!(
            "SELECT stock_quantity FROM products WHERE id = $1",
            product_id
        )
        .fetch_one(&mut *tx)
        .await?;
        
        let new_stock = current_stock + quantity_change;
        if new_stock < 0 {
            return Err(AppError::InsufficientStock);
        }
        
        // Update stock
        sqlx::query!(
            "UPDATE products SET stock_quantity = $1, updated_at = NOW() WHERE id = $2",
            new_stock,
            product_id
        )
        .execute(&mut *tx)
        .await?;
        
        tx.commit().await?;
        Ok(())
    }
}
```

### Assessment Criteria:
- [ ] Proper database schema design
- [ ] All migrations work correctly
- [ ] Efficient queries with proper indexing
- [ ] Transaction handling for data consistency
- [ ] Comprehensive error handling
- [ ] Connection pooling implemented
- [ ] All API endpoints functional
- [ ] Thorough testing including database tests

### Bonus Challenges:
- Implement database sharding for scalability
- Add Redis caching layer
- Implement database replication (read/write splitting)
- Add full-text search with PostgreSQL
- Implement audit logging for all changes
- Add database performance monitoring
- Create database backup and restore procedures
- Implement soft deletes with proper querying

## 🔧 Dependencies

Your `Cargo.toml` should include:
```toml
[dependencies]
actix-web = "4.0"
sqlx = { version = "0.7", features = ["runtime-tokio-rustls", "postgres", "chrono", "uuid", "json"] }
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
chrono = { version = "0.4", features = ["serde"] }
uuid = { version = "1.0", features = ["v4", "serde"] }
thiserror = "1.0"
anyhow = "1.0"
tokio = { version = "1.0", features = ["full"] }
dotenv = "0.15"
bcrypt = "0.14"
jsonwebtoken = "8.0"

[dev-dependencies]
sqlx-test = "0.7"
testcontainers = "0.14"
```

## 📝 Database Development Tips
1. **Plan your schema**: Design before coding
2. **Use migrations**: Never modify database directly
3. **Index strategically**: Monitor query performance
4. **Handle transactions**: Ensure data consistency
5. **Test with real data**: Use realistic datasets
6. **Monitor performance**: Profile your queries
7. **Backup strategies**: Plan for data recovery

## 🔗 Additional Resources
- [PostgreSQL Performance Tips](https://wiki.postgresql.org/wiki/Performance_Optimization)
- [Database Design Patterns](https://www.martinfowler.com/eaaCatalog/)
- [SQLx Migration Guide](https://docs.rs/sqlx/latest/sqlx/migrate/index.html)
- [Database Testing Strategies](https://martinfowler.com/articles/nonDeterminism.html)
- [SQL Style Guide](https://www.sqlstyle.guide/)

## ✅ Phase Completion Checklist
- [ ] Comfortable with SQL and PostgreSQL
- [ ] Can design efficient database schemas
- [ ] Proficient with SQLx for database operations
- [ ] Understands migration strategies
- [ ] Implements proper transaction handling
- [ ] Built the e-commerce backend successfully
- [ ] All database tests pass

**Next Phase Preview**: In Phase 5, you'll build production-ready REST APIs with authentication, authorization, middleware, and advanced features!
