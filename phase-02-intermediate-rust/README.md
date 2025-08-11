# Phase 2: Intermediate Rust (2-3 weeks)

## 🎯 Learning Objectives
By the end of this phase, you will:
- Master traits, generics, and lifetimes
- Understand modules and package organization
- Write comprehensive tests
- Handle errors gracefully
- Use iterators and closures effectively

## 📚 Study Materials

### Week 1: Advanced Rust Concepts

#### Day 1-2: Traits and Generics
- **Resources:**
  - [Rust Book Chapter 10](https://doc.rust-lang.org/book/ch10-00-generics.html)
  - [Trait Objects vs Generics](https://doc.rust-lang.org/book/ch17-02-trait-objects.html)
  
- **Topics to Study:**
  - Defining and implementing traits
  - Generic data types and functions
  - Trait bounds and where clauses
  - Associated types
  - Default implementations

#### Day 3-4: Lifetimes and Advanced Ownership
- **Resources:**
  - [Rust Book Chapter 10.3](https://doc.rust-lang.org/book/ch10-03-lifetime-syntax.html)
  - [Lifetime Elision Rules](https://doc.rust-lang.org/reference/lifetime-elision.html)
  
- **Topics to Study:**
  - Lifetime annotations
  - Lifetime elision
  - Static lifetimes
  - Lifetime bounds on generics

#### Day 5-7: Iterators and Closures
- **Resources:**
  - [Rust Book Chapter 13](https://doc.rust-lang.org/book/ch13-00-functional-features.html)
  - [Iterator Trait Documentation](https://doc.rust-lang.org/std/iter/trait.Iterator.html)
  
- **Topics to Study:**
  - Closure syntax and capture modes
  - Iterator trait and lazy evaluation
  - Iterator adaptors vs consuming adaptors
  - Performance characteristics

### Week 2: Code Organization and Testing

#### Day 1-3: Modules and Packages
- **Resources:**
  - [Rust Book Chapter 7](https://doc.rust-lang.org/book/ch07-00-managing-growing-projects-with-packages-crates-and-modules.html)
  - [Cargo Book](https://doc.rust-lang.org/cargo/)
  
- **Topics to Study:**
  - Packages, crates, and modules
  - Controlling scope and privacy
  - Use keyword and paths
  - Separating modules into different files
  - Workspace management

#### Day 4-7: Testing and Documentation
- **Resources:**
  - [Rust Book Chapter 11](https://doc.rust-lang.org/book/ch11-00-testing.html)
  - [rustdoc Book](https://doc.rust-lang.org/rustdoc/)
  
- **Topics to Study:**
  - Unit tests, integration tests, and doc tests
  - Test organization and running tests
  - Test-driven development in Rust
  - Writing documentation
  - Benchmarking basics

## 💻 Practice Exercises

### Exercise 1: Trait System Practice
Create a shape calculation system:
1. Define traits for `Area`, `Perimeter`, and `Display`
2. Implement for different shapes (Circle, Rectangle, Triangle)
3. Use trait objects to store different shapes in a collection
4. Practice with generic functions that work with any shape

### Exercise 2: Iterator and Closure Practice
Build data processing utilities:
1. Filter and transform collections using iterators
2. Write custom iterator implementations
3. Use closures for flexible callback patterns
4. Chain multiple iterator operations

### Exercise 3: Module Organization
Restructure previous projects:
1. Split code into logical modules
2. Create a library crate and binary crate
3. Write proper documentation
4. Set up integration tests

## 🚀 Phase 2 Project: Library Management System

### Project Requirements

Build a comprehensive library management system with advanced Rust features:

#### Core Features:
1. **Book Management**: Add, remove, update, and search books
2. **User Management**: Register users, manage borrowing limits
3. **Borrowing System**: Check out and return books
4. **Inventory Tracking**: Track available copies and due dates
5. **Reporting**: Generate various reports (overdue books, popular books, etc.)

#### Technical Requirements:
- Use traits to define common behavior (e.g., `Borrowable`, `Searchable`)
- Implement generics for flexible data structures
- Use proper module organization
- Write comprehensive tests (unit, integration, doc tests)
- Handle all errors gracefully with custom error types
- Use iterators and closures for data processing

#### Advanced Features:
- Custom error types with proper error handling
- Serialization/deserialization (JSON/TOML)
- Configuration management
- Logging system
- Date/time handling for due dates

#### Project Structure:
```
phase-02-project/
├── Cargo.toml
├── src/
│   ├── lib.rs
│   ├── main.rs
│   ├── models/
│   │   ├── mod.rs
│   │   ├── book.rs
│   │   ├── user.rs
│   │   └── borrowing.rs
│   ├── services/
│   │   ├── mod.rs
│   │   ├── library.rs
│   │   └── reporting.rs
│   ├── storage/
│   │   ├── mod.rs
│   │   └── file_storage.rs
│   └── errors.rs
├── tests/
│   └── integration_tests.rs
├── examples/
│   └── basic_usage.rs
├── docs/
└── README.md
```

#### Example API Usage:
```rust
// Library trait for different storage backends
trait LibraryStorage {
    fn save_book(&mut self, book: Book) -> Result<(), LibraryError>;
    fn find_books_by_author(&self, author: &str) -> Vec<&Book>;
}

// Generic library manager
struct Library<T: LibraryStorage> {
    storage: T,
    // ... other fields
}

// Usage
let mut library = Library::new(FileStorage::new("data.json")?);
library.add_book(Book::new("Rust in Action", "Tim McNamara"))?;
```

#### Testing Requirements:
- Unit tests for all modules (min 80% coverage)
- Integration tests for complete workflows
- Property-based testing for edge cases
- Benchmark tests for performance-critical operations
- Doc tests for all public APIs

#### Error Handling Requirements:
```rust
#[derive(Debug, thiserror::Error)]
pub enum LibraryError {
    #[error("Book not found: {isbn}")]
    BookNotFound { isbn: String },
    
    #[error("User has reached borrowing limit")]
    BorrowingLimitExceeded,
    
    #[error("IO error: {0}")]
    Io(#[from] std::io::Error),
    
    #[error("Serialization error: {0}")]
    Serialization(#[from] serde_json::Error),
}
```

### Assessment Criteria:
- [ ] All features implemented correctly
- [ ] Proper use of traits and generics
- [ ] Well-organized module structure
- [ ] Comprehensive test coverage
- [ ] Proper error handling throughout
- [ ] Clear documentation and examples
- [ ] Performance considerations addressed

### Bonus Challenges:
- Implement multiple storage backends (JSON, CSV, Database)
- Add a reservation system for popular books
- Implement fine calculation for overdue books
- Add book recommendation system
- Create a simple CLI interface with colored output
- Add email notification system (mock implementation)

## 🔧 Tools and Dependencies

Add these to your `Cargo.toml`:
```toml
[dependencies]
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
chrono = { version = "0.4", features = ["serde"] }
thiserror = "1.0"
anyhow = "1.0"
clap = { version = "4.0", features = ["derive"] }

[dev-dependencies]
criterion = "0.5"
proptest = "1.0"
```

## 📝 Advanced Study Tips
1. **Read the standard library**: Study how std traits are implemented
2. **Benchmark your code**: Use `criterion` for performance testing
3. **Practice with real crates**: Read source code of popular libraries
4. **Error handling patterns**: Study `anyhow` and `thiserror` usage
5. **API design**: Think about how your APIs will be used

## 🔗 Additional Resources
- [Rust API Guidelines](https://rust-lang.github.io/api-guidelines/)
- [Effective Rust](https://www.lurklurk.org/effective-rust/)
- [Rust Performance Book](https://nnethercote.github.io/perf-book/)
- [thiserror Documentation](https://docs.rs/thiserror/)
- [criterion.rs Benchmarking](https://bheisler.github.io/criterion.rs/book/)

## ✅ Phase Completion Checklist
- [ ] Mastered traits, generics, and lifetimes
- [ ] Can organize code into proper modules
- [ ] Writes comprehensive tests
- [ ] Handles errors gracefully
- [ ] Uses iterators and closures effectively
- [ ] Built the library management system
- [ ] All tests pass and code is well-documented

**Next Phase Preview**: In Phase 3, you'll start building web applications, learning about HTTP, and creating your first web servers with Rust!
