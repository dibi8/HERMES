---
name: git-embedded-repository-management
description: "Manage issues related to embedded repositories in Git."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [Git, repository management, troubleshooting]
---

# Git Embedded Repository Management

This skill addresses common issues that arise from embedded Git repositories during push operations.

## Pitfalls
- **Embedded Repository Issue**: When attempting to push, embedded repositories may cause errors. To resolve this:
  1. **Identify** embedded repositories in your project (e.g., `.nvm`, `DIBI8coin`).
  2. **Remove from Index**: Use `git rm --cached <repository>` to remove the embedded repository references.
  3. **Push** your changes.

## Usage
- Before pushing changes to a new or existing repository, ensure unwanted embedded repositories are managed properly.