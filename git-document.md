# Git Overview

**Git** is a distributed version control system used to track changes in source code during software development. It allows multiple developers to collaborate on a project by managing the history of code changes, enabling efficient branching, merging, and collaboration.

## Key Features of Git

1. **Distributed**: Every developer has a complete copy of the entire repository, including its history, on their local machine.
2. **Version Control**: Tracks changes to files, allowing you to view the history of modifications and revert to previous versions.
3. **Branching and Merging**: Git allows for easy branching (creating separate lines of development) and merging changes back into the main project.
4. **Collaboration**: Multiple developers can work on the same project without interfering with each other’s work, thanks to features like branching and pull requests.

# Common Git Terminologies

This document covers some of the most commonly used Git terminologies.

## 1. **Repository**
A **repository** (or **repo**) is a collection of files and their entire revision history. It contains all the project files, including the `.git` directory, which stores metadata and version history.

- **Local Repository**: The version of the repository on your local machine.
- **Remote Repository**: A version of the repository hosted on a server, such as GitHub or GitLab.

### Example:
```bash
git init
```

## 2. Commit
A commit is a snapshot of changes in your project. Each commit has a unique identifier (hash) and records who made the changes, when they were made, and a message describing the changes.
```bash
git commit -m "Add new feature"

```
## 3. Branch
A branch is a pointer to one of the commits in the repository. It allows you to work on different parts of the project independently without affecting the main codebase. The default branch is typically called main or master.
```bash
git branch feature-branch
git checkout feature-branch
```

## 4. Checkout
The checkout command allows you to switch between branches or restore files in the working directory to a specific state.
```bash
git checkout main  # Switch to the 'main' branch
```

## 5. Merge
Merge combines the changes from different branches into one. It is usually performed when a feature branch is ready to be incorporated into the main branch.
```bash
git merge feature-branch
```

## 6. Pull
The pull command is used to fetch changes from a remote repository and merge them into your local branch. This is a combination of git fetch and git merge.

```bash
git pull origin main
```

## 7. Push
The push command is used to upload your local changes (commits) to a remote repository.
```bash
git push origin main
```

## 8. Clone
The clone command is used to create a copy of an existing remote repository onto your local machine.
```bash
git clone https://github.com/user/repository.git
```

## 9. Staging Area (Index)
The staging area (or index) is where you place changes before committing them. You add files to the staging area using the git add command.
```bash
git add <file_name>
```

## 10. Tag
A tag is a reference to a specific point in Git history, usually used to mark releases or important milestones in the project.
```bash
git tag v1.0.0
```

## 11. Fork
A fork is a copy of someone else's repository. It allows you to freely make changes without affecting the original project. Forks are commonly used in open-source projects.

## 12. Remote
A remote is a version of the repository that is hosted on the internet or a network. It typically refers to repositories hosted on services like GitHub, GitLab, etc.
```bash
git remote add origin https://github.com/user/repository.git
```

## 13. Fetch
The fetch command is used to download changes from a remote repository to your local machine, but it does not automatically merge the changes. You can then inspect the changes and decide when and how to merge them.
```bash
git fetch origin
```

## 14. Diff
The diff command shows the differences between files or commits, helping you see what has changed.
```bash
git diff
```

## 15. Rebase
Rebase is an alternative to merging, which involves moving or combining a sequence of commits to a new base commit. This helps to keep the project history linear.
```bash
git rebase main
```

## 16. HEAD
HEAD refers to the current commit your working directory is based on. It is usually the latest commit on the current branch.
```bash
git log --oneline
```
