# git-pkgs Demo - FOSDEM 2025

15 minute live demo using octobox as the example repo.

Pdf of slides: https://github.com/git-pkgs/demo/blob/main/git-pkgs.pdf

## Pre-flight (do before the talk)

```bash
# Make sure octobox is cloned and up to date
cd ~/code/octobox
git pull

# Install latest git-pkgs
brew upgrade git-pkgs

# Build the database (indexes full git history)
git pkgs init

# Warm caches for offline commands (these hit the network)
git pkgs outdated
git pkgs licenses
git pkgs vulns scan

# Create a tag for time-travel demo if not present
git tag -f january-2025 $(git rev-list -1 --before="2025-01-01" HEAD)
```

## Demo

### What is git-pkgs?

```bash
git pkgs --version

# Show what ecosystems are supported
git pkgs ecosystems
```

### Init and info

```bash
cd ~/code/octobox
git pkgs info

# Show ecosystems with dependency counts
git pkgs info --ecosystems
```

### Listing dependencies

```bash
# Current dependencies
git pkgs list

# Filter by manifest file
git pkgs list --manifest=Gemfile

# Time travel: dependencies at a past point
git pkgs list january-2025
```

### Inspecting changes

```bash
# What changed in HEAD?
git pkgs show

# Diff between two points
git pkgs diff --from=january-2025

# Full changelog of a package
git pkgs history rails

# Changes by a specific author
git pkgs history --author=Andrew
```

### Understanding your dependencies

```bash
# Why is this dependency here? Who added it and when?
git pkgs why rails

# Where is a package declared?
git pkgs where dotenv-rails

# Browse installed package source
git pkgs browse rails --path
```

### Package health

```bash
# Find outdated packages (compares to registry)
git pkgs outdated

# Show licenses
git pkgs licenses
git pkgs licenses --permissive
```

### Vulnerability scanning

```bash
# Scan current dependencies against OSV
git pkgs vulns scan

# Show commits that introduced vulnerabilities
git pkgs vulns log

# Exposure metrics
git pkgs vulns exposure --all-time
```

### Bisect

```bash
# Binary search through dependency-changing commits
# Like git bisect but only stops at commits that changed dependencies
git pkgs bisect start HEAD january-2025
git pkgs bisect log
git pkgs bisect reset
```

### SBOM export

```bash
# CycloneDX
git pkgs sbom | head -30

# SPDX
git pkgs sbom --type=spdx | head -30
```

### Git integration

```bash
# Semantic lockfile diffs via git diff driver
git pkgs diff-driver --install
git diff HEAD~5 -- Gemfile.lock
```

### Database

```bash
# Everything is stored in .git/pkgs.sqlite3
git pkgs schema

# You can query it directly
sqlite3 .git/pkgs.sqlite3 "SELECT name, COUNT(*) FROM dependency_changes GROUP BY name ORDER BY 2 DESC LIMIT 15;"
sqlite3 .git/pkgs.sqlite3 "SELECT change_type, COUNT(*) FROM dependency_changes WHERE name='rails' GROUP BY change_type;"
```
