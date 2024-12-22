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
