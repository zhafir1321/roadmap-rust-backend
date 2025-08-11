# Phase 1: Foundations (2-3 weeks)

## 🎯 Learning Objectives
By the end of this phase, you will:
- Understand Rust ownership, borrowing, and lifetimes
- Be comfortable with Git version control
- Build your first CLI applications
- Understand basic programming concepts in Rust

## 📚 Study Materials

### Week 1: Rust Basics
#### Day 1-2: Environment Setup & Hello World
- **Resources:**
  - [Official Rust Installation Guide](https://www.rust-lang.org/tools/install)
  - [Rust Book Chapter 1](https://doc.rust-lang.org/book/ch01-00-getting-started.html)
  
- **Topics to Study:**
  - Installing Rust and Cargo
  - Understanding `rustc` and `cargo`
  - Your first "Hello, World!" program
  - Cargo.toml basics

#### Day 3-4: Variables and Data Types
- **Resources:**
  - [Rust Book Chapter 3](https://doc.rust-lang.org/book/ch03-00-common-programming-concepts.html)
  
- **Topics to Study:**
  - Variables and mutability
  - Data types (integers, floats, booleans, characters)
  - Functions
  - Control flow (if, loop, while, for)

#### Day 5-7: Ownership System
- **Resources:**
  - [Rust Book Chapter 4](https://doc.rust-lang.org/book/ch04-00-understanding-ownership.html)
  - [Ownership Visualizer](https://github.com/rustviz/rustviz)
  
- **Topics to Study:**
  - What is ownership?
  - References and borrowing
  - The slice type
  - Memory safety without garbage collection

### Week 2: Git Fundamentals & More Rust

#### Day 1-2: Git Basics
- **Resources:**
  - [Git Handbook](https://guides.github.com/introduction/git-handbook/)
  - [Interactive Git Tutorial](https://learngitbranching.js.org/)
  
- **Topics to Study:**
  - What is version control?
  - Repository initialization
  - Basic commands: add, commit, push, pull
  - Working with remote repositories

#### Day 3-4: Structs and Enums
- **Resources:**
  - [Rust Book Chapter 5](https://doc.rust-lang.org/book/ch05-00-structs.html)
  - [Rust Book Chapter 6](https://doc.rust-lang.org/book/ch06-00-enums.html)
  
- **Topics to Study:**
  - Defining and instantiating structs
  - Method syntax
  - Defining enums
  - Pattern matching with `match`
  - `if let` control flow

#### Day 5-7: Collections and Error Handling
- **Resources:**
  - [Rust Book Chapter 8](https://doc.rust-lang.org/book/ch08-00-common-collections.html)
  - [Rust Book Chapter 9](https://doc.rust-lang.org/book/ch09-00-error-handling.html)
  
- **Topics to Study:**
  - Vectors, strings, and hash maps
  - `Result<T, E>` and `Option<T>`
  - Recoverable vs unrecoverable errors
  - When to panic and when to return Result

## 🛠️ Tools to Install
- Rust (rustup, rustc, cargo)
- Git
- A good text editor/IDE (VS Code with rust-analyzer recommended)
- Terminal/Command Line Interface

## 💻 Practice Exercises

### Exercise 1: Basic Syntax (Day 1-4)
Create small programs to practice:
1. Variable declarations and modifications
2. Function definitions with parameters and return values
3. Control flow with different conditions
4. Basic calculations and string manipulation

### Exercise 2: Ownership Practice (Day 5-7)
Write programs that demonstrate:
1. Moving ownership between variables
2. Borrowing with references
3. Mutable vs immutable references
4. String slices and array slices

### Exercise 3: Git Workflow (Day 8-10)
Practice Git commands:
1. Initialize a repository
2. Make commits with meaningful messages
3. Create and merge branches
4. Handle merge conflicts

## 🚀 Phase 1 Project: Personal Task Manager CLI

### Project Requirements

Build a command-line task management application with the following features:

#### Core Features:
1. **Add Task**: Users can add new tasks with a description
2. **List Tasks**: Display all tasks with their status (pending/completed)
3. **Complete Task**: Mark a task as completed
4. **Delete Task**: Remove a task from the list
5. **Save/Load**: Persist tasks to a file between program runs

#### Technical Requirements:
- Use Rust structs to represent tasks
- Implement proper error handling
- Use collections (Vec, HashMap) appropriately
- Save data to a JSON or simple text file
- Use command-line arguments for different operations

#### Example Usage:
```bash
# Add a new task
./task_manager add "Learn Rust ownership"

# List all tasks
./task_manager list

# Complete a task
./task_manager complete 1

# Delete a task
./task_manager delete 2
```

#### Git Requirements:
- Initialize a Git repository for your project
- Make frequent commits with descriptive messages
- Create a README.md explaining how to use your application
- Use branches for different features
- Tag your final version as "v1.0"

### Project Structure:
```
phase-01-project/
├── Cargo.toml
├── src/
│   ├── main.rs
│   ├── task.rs
│   └── storage.rs
├── README.md
└── .gitignore
```

### Assessment Criteria:
- [ ] All core features work correctly
- [ ] Proper error handling (no panics on user input)
- [ ] Code is well-organized and readable
- [ ] Git history shows good practices
- [ ] README explains the project clearly

### Bonus Challenges:
- Add task priorities (high, medium, low)
- Implement task due dates
- Add task categories/tags
- Create a search functionality
- Add colored output for better UX

## 📝 Study Tips
1. **Practice daily**: Even 30 minutes a day is better than cramming
2. **Read the compiler**: Rust's compiler messages are very helpful
3. **Experiment**: Try breaking things to understand how they work
4. **Ask questions**: Use the Rust community forums when stuck
5. **Code review**: Share your code and get feedback

## 🔗 Additional Resources
- [Rust By Example](https://doc.rust-lang.org/rust-by-example/)
- [Rustlings Exercises](https://github.com/rust-lang/rustlings)
- [Rust Discord/Forum](https://users.rust-lang.org/)
- [Git Cheat Sheet](https://education.github.com/git-cheat-sheet-education.pdf)

## ✅ Phase Completion Checklist
- [ ] Completed all reading materials
- [ ] Finished practice exercises
- [ ] Built and tested the task manager CLI
- [ ] Demonstrated proper Git usage
- [ ] Understood ownership, borrowing, and lifetimes
- [ ] Ready to move to Phase 2

**Next Phase Preview**: In Phase 2, you'll dive deeper into Rust's advanced features like traits, generics, and modules while building more complex applications!
