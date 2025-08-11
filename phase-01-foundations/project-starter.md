# Phase 1 Project Starter

Welcome to your first Rust project! This template will help you get started with the Personal Task Manager CLI.

## 🎯 Project Overview

You'll build a command-line task management application with these features:
- Add new tasks
- List all tasks
- Mark tasks as completed
- Delete tasks
- Save/load tasks from a file

## 🚀 Getting Started

### 1. Create Your Project
```bash
cargo new task_manager
cd task_manager
```

### 2. Update Cargo.toml
```toml
[package]
name = "task_manager"
version = "0.1.0"
edition = "2021"

[dependencies]
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
clap = { version = "4.0", features = ["derive"] }
chrono = { version = "0.4", features = ["serde"] }
```

### 3. Project Structure
```
task_manager/
├── Cargo.toml
├── src/
│   ├── main.rs
│   ├── task.rs
│   └── storage.rs
├── README.md
└── .gitignore
```

## 📋 Implementation Guide

### Step 1: Define Your Task Structure (src/task.rs)
```rust
use serde::{Deserialize, Serialize};
use chrono::{DateTime, Utc};

#[derive(Serialize, Deserialize, Debug, Clone)]
pub struct Task {
    pub id: u32,
    pub description: String,
    pub completed: bool,
    pub created_at: DateTime<Utc>,
    pub completed_at: Option<DateTime<Utc>>,
}

impl Task {
    pub fn new(id: u32, description: String) -> Self {
        Task {
            id,
            description,
            completed: false,
            created_at: Utc::now(),
            completed_at: None,
        }
    }

    pub fn complete(&mut self) {
        self.completed = true;
        self.completed_at = Some(Utc::now());
    }

    pub fn status(&self) -> &str {
        if self.completed {
            "✓"
        } else {
            "○"
        }
    }
}
```

### Step 2: Implement Storage (src/storage.rs)
```rust
use crate::task::Task;
use serde_json;
use std::fs;
use std::io;

pub struct TaskStorage {
    file_path: String,
}

impl TaskStorage {
    pub fn new(file_path: String) -> Self {
        TaskStorage { file_path }
    }

    pub fn load_tasks(&self) -> Result<Vec<Task>, Box<dyn std::error::Error>> {
        if !std::path::Path::new(&self.file_path).exists() {
            return Ok(Vec::new());
        }

        let contents = fs::read_to_string(&self.file_path)?;
        let tasks: Vec<Task> = serde_json::from_str(&contents)?;
        Ok(tasks)
    }

    pub fn save_tasks(&self, tasks: &[Task]) -> Result<(), Box<dyn std::error::Error>> {
        let json = serde_json::to_string_pretty(tasks)?;
        fs::write(&self.file_path, json)?;
        Ok(())
    }
}
```

### Step 3: Command Line Interface (src/main.rs)
```rust
mod task;
mod storage;

use clap::{Parser, Subcommand};
use task::Task;
use storage::TaskStorage;

#[derive(Parser)]
#[command(name = "task_manager")]
#[command(about = "A simple task management CLI")]
struct Cli {
    #[command(subcommand)]
    command: Commands,
}

#[derive(Subcommand)]
enum Commands {
    /// Add a new task
    Add {
        /// Task description
        description: String,
    },
    /// List all tasks
    List,
    /// Complete a task
    Complete {
        /// Task ID to complete
        id: u32,
    },
    /// Delete a task
    Delete {
        /// Task ID to delete
        id: u32,
    },
}

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let cli = Cli::parse();
    let storage = TaskStorage::new("tasks.json".to_string());
    let mut tasks = storage.load_tasks()?;

    match cli.command {
        Commands::Add { description } => {
            let id = tasks.len() as u32 + 1;
            let task = Task::new(id, description);
            tasks.push(task);
            storage.save_tasks(&tasks)?;
            println!("Task added successfully!");
        }
        Commands::List => {
            if tasks.is_empty() {
                println!("No tasks found.");
            } else {
                println!("Tasks:");
                for task in &tasks {
                    println!("{} [{}] {}", task.status(), task.id, task.description);
                }
            }
        }
        Commands::Complete { id } => {
            if let Some(task) = tasks.iter_mut().find(|t| t.id == id) {
                task.complete();
                storage.save_tasks(&tasks)?;
                println!("Task {} completed!", id);
            } else {
                println!("Task with ID {} not found.", id);
            }
        }
        Commands::Delete { id } => {
            let initial_len = tasks.len();
            tasks.retain(|t| t.id != id);
            if tasks.len() < initial_len {
                storage.save_tasks(&tasks)?;
                println!("Task {} deleted!", id);
            } else {
                println!("Task with ID {} not found.", id);
            }
        }
    }

    Ok(())
}
```

## 🧪 Testing Your Application

### Build and Run
```bash
# Build the project
cargo build

# Run with different commands
cargo run -- add "Learn Rust ownership"
cargo run -- add "Build a web server"
cargo run -- list
cargo run -- complete 1
cargo run -- delete 2
```

### Expected Output
```bash
$ cargo run -- add "Learn Rust ownership"
Task added successfully!

$ cargo run -- list
Tasks:
○ [1] Learn Rust ownership
○ [2] Build a web server

$ cargo run -- complete 1
Task 1 completed!

$ cargo run -- list
Tasks:
✓ [1] Learn Rust ownership
○ [2] Build a web server
```

## 🔧 Git Setup

### Initialize Repository
```bash
git init
git add .
git commit -m "Initial commit: basic task manager CLI"
```

### Create .gitignore
```gitignore
/target/
tasks.json
Cargo.lock
.DS_Store
```

## 📝 Enhancement Ideas

Once you have the basic version working, try adding:

1. **Task Priorities**: Add high/medium/low priority levels
2. **Due Dates**: Set and display due dates for tasks
3. **Categories**: Organize tasks by categories
4. **Search**: Find tasks by keyword
5. **Statistics**: Show completion rates and statistics

### Example Enhancement: Task Priorities
```rust
#[derive(Serialize, Deserialize, Debug, Clone)]
pub enum Priority {
    High,
    Medium,
    Low,
}

// Add to Task struct
pub priority: Priority,

// Update display in main.rs
println!("{} [{}] {:?} {}", task.status(), task.id, task.priority, task.description);
```

## ✅ Completion Checklist

- [ ] Project compiles without errors
- [ ] Can add tasks successfully
- [ ] Can list all tasks
- [ ] Can complete tasks
- [ ] Can delete tasks
- [ ] Tasks persist between runs
- [ ] Error handling works properly
- [ ] Git repository is properly initialized
- [ ] README.md explains how to use the application

## 🚀 Next Steps

Once you complete this project:
1. Share your code for review
2. Write unit tests for your functions
3. Add more features from the enhancement ideas
4. Start preparing for Phase 2!

Good luck with your first Rust project! Remember, the key is to start simple and gradually add complexity. 🦀
