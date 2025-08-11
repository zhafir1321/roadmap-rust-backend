# Git Fundamentals for Backend Developers

## 🎯 Learning Objectives
Master Git version control system to manage code effectively throughout your Rust backend development journey.

## 📚 Git Basics

### Essential Git Concepts
- **Repository**: A project's complete history and files
- **Commit**: A snapshot of your code at a specific point in time
- **Branch**: A parallel version of your code
- **Merge**: Combining changes from different branches
- **Remote**: A version of your repository hosted elsewhere (like GitHub)

### Git Configuration
```bash
# Set up your identity
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Set default branch name
git config --global init.defaultBranch main

# Set up useful aliases
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.cm commit
git config --global alias.lg "log --oneline --graph --all"
```

## 🛠️ Basic Git Workflow

### 1. Repository Initialization
```bash
# Create a new repository
mkdir my-rust-project
cd my-rust-project
git init

# Or clone an existing repository
git clone https://github.com/username/repository.git
```

### 2. Basic Commands
```bash
# Check repository status
git status

# Add files to staging area
git add filename.rs           # Add specific file
git add src/                  # Add directory
git add .                     # Add all changes

# Commit changes
git commit -m "Add user authentication module"

# View commit history
git log --oneline             # Concise format
git log --graph --all         # Visual representation
```

### 3. Working with Remotes
```bash
# Add remote repository
git remote add origin https://github.com/username/repo.git

# Push changes to remote
git push origin main

# Pull changes from remote
git pull origin main

# Check remote repositories
git remote -v
```

## 🌿 Branching Strategy

### Feature Branch Workflow
```bash
# Create and switch to new branch
git checkout -b feature/user-registration

# Or using newer syntax
git switch -c feature/user-registration

# Work on your feature, make commits
git add src/auth.rs
git commit -m "Implement user registration endpoint"

# Push feature branch
git push origin feature/user-registration

# Switch back to main branch
git checkout main
git switch main

# Merge feature branch
git merge feature/user-registration

# Delete feature branch after merging
git branch -d feature/user-registration
git push origin --delete feature/user-registration
```

### Common Branching Patterns
```
main                    ←── Production-ready code
├── develop            ←── Integration branch
├── feature/api-auth   ←── New feature development
├── hotfix/security-fix ←── Critical bug fixes
└── release/v1.2.0     ←── Release preparation
```

## 🔧 Advanced Git Operations

### Handling Merge Conflicts
```bash
# When merge conflicts occur
git merge feature-branch
# CONFLICT (content): Merge conflict in src/main.rs

# Edit conflicted files, then:
git add src/main.rs
git commit -m "Resolve merge conflict in main.rs"
```

### Useful Git Commands for Backend Development
```bash
# Stash changes temporarily
git stash                     # Save current changes
git stash pop                 # Restore stashed changes
git stash list                # List all stashes

# Revert changes
git checkout -- filename.rs  # Discard changes to file
git reset HEAD filename.rs    # Unstage file
git reset --hard HEAD~1       # Reset to previous commit (dangerous!)

# View differences
git diff                      # See unstaged changes
git diff --staged             # See staged changes
git diff branch1..branch2     # Compare branches

# Cherry-pick commits
git cherry-pick commit-hash   # Apply specific commit to current branch

# Interactive rebase (clean up history)
git rebase -i HEAD~3          # Interactively rebase last 3 commits
```

## 📋 Git Best Practices for Rust Projects

### 1. Commit Message Conventions
```bash
# Good commit messages
git commit -m "feat: add JWT authentication middleware"
git commit -m "fix: handle database connection timeout"
git commit -m "refactor: extract user service into separate module"
git commit -m "docs: update API documentation for auth endpoints"
git commit -m "test: add unit tests for password validation"

# Commit message format
<type>(<scope>): <subject>

<body>

<footer>
```

### 2. .gitignore for Rust Projects
```gitignore
# Rust
/target/
Cargo.lock

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Environment
.env
.env.local

# Logs
*.log

# Database
*.db
*.sqlite

# Temporary files
*.tmp
*.temp
```

### 3. Branch Naming Conventions
```bash
# Feature branches
git checkout -b feature/user-authentication
git checkout -b feature/payment-integration

# Bug fixes
git checkout -b fix/database-connection-leak
git checkout -b fix/security-vulnerability

# Hotfixes
git checkout -b hotfix/critical-api-bug

# Releases
git checkout -b release/v1.2.0
```

## 🔄 Git Workflow for Team Development

### 1. Pull Request Workflow
```bash
# 1. Create feature branch
git checkout -b feature/new-api-endpoint

# 2. Make changes and commit
git add .
git commit -m "feat: add new user profile endpoint"

# 3. Push to remote
git push origin feature/new-api-endpoint

# 4. Create Pull Request on GitHub/GitLab
# 5. After review and approval, merge PR
# 6. Clean up local branch
git checkout main
git pull origin main
git branch -d feature/new-api-endpoint
```

### 2. Code Review Guidelines
- Keep PRs small and focused
- Write clear PR descriptions
- Review code thoroughly
- Test changes locally
- Use meaningful commit messages

### 3. Release Management
```bash
# Create release branch
git checkout -b release/v1.2.0

# Update version numbers
# Run tests
# Update changelog

# Merge to main and tag
git checkout main
git merge release/v1.2.0
git tag v1.2.0
git push origin main --tags
```

## 🚨 Git Troubleshooting

### Common Issues and Solutions
```bash
# Undo last commit (keep changes)
git reset --soft HEAD~1

# Undo last commit (discard changes)
git reset --hard HEAD~1

# Fix commit message
git commit --amend -m "New commit message"

# Add files to last commit
git add forgotten-file.rs
git commit --amend --no-edit

# Recover deleted branch
git reflog                    # Find commit hash
git checkout -b recovered-branch commit-hash

# Remove file from Git but keep locally
git rm --cached filename.rs

# Clean untracked files
git clean -fd                 # Remove untracked files and directories
```

### Working with Large Files
```bash
# Use Git LFS for large files
git lfs track "*.pdf"
git lfs track "*.zip"
git add .gitattributes
git commit -m "Add Git LFS tracking"
```

## 📊 Git Aliases for Productivity
```bash
# Add these to your ~/.gitconfig
[alias]
    # Status and info
    st = status -sb
    br = branch
    co = checkout
    
    # Logging
    lg = log --oneline --graph --all
    ll = log --pretty=format:'%C(yellow)%h%Creset %C(blue)%an%Creset %C(green)%cr%Creset %s'
    
    # Diffing
    d = diff
    ds = diff --staged
    
    # Committing
    cm = commit -m
    ca = commit -a -m
    amend = commit --amend --no-edit
    
    # Pushing/Pulling
    pom = push origin main
    poh = push origin HEAD
    pl = pull
    
    # Shortcuts
    unstage = reset HEAD --
    discard = checkout --
    
    # Show contributors
    who = shortlog -sn
```

## 📖 Git Learning Resources

### Essential Reading
- [Pro Git Book](https://git-scm.com/book) (Free online)
- [Git Documentation](https://git-scm.com/docs)
- [GitHub Flow Guide](https://guides.github.com/introduction/flow/)

### Interactive Learning
- [Learn Git Branching](https://learngitbranching.js.org/)
- [Git Immersion](http://gitimmersion.com/)
- [GitHub Learning Lab](https://lab.github.com/)

### Cheat Sheets
- [Git Cheat Sheet](https://education.github.com/git-cheat-sheet-education.pdf)
- [Atlassian Git Tutorials](https://www.atlassian.com/git/tutorials)

## ✅ Git Mastery Checklist
- [ ] Can initialize and configure Git repositories
- [ ] Understands staging, committing, and pushing workflow
- [ ] Can create and manage branches effectively
- [ ] Knows how to resolve merge conflicts
- [ ] Familiar with collaboration workflows (PRs, code review)
- [ ] Uses proper commit message conventions
- [ ] Can troubleshoot common Git issues
- [ ] Understands advanced features (rebase, cherry-pick, etc.)

Remember: Git is a powerful tool that becomes more intuitive with practice. Start with the basics and gradually incorporate more advanced features as you become comfortable!
