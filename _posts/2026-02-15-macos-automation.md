---
title: "Automating macOS Setup: From Hours to Minutes"
date: 2025-12-01
category: "DevOps"
tags: ["macOS", "Homebrew", "Automation", "DevOps", "Apple"]
---

We've all been there. New Mac in hand, 3 hours ahead of us, clicking "Next" through installer after installer. After doing this for the nth time, I decided to automate it. The result? [mac-catalyst](https://github.com/irfancode/mac-catalyst) — one command, fully configured development environment.

## The Problem

Every time I got a new Mac or had to rebuild:
- Install Homebrew packages (40+ apps)
- Configure VS Code with 80+ extensions
- Set up shell preferences
- Install GUI applications
- Configure everything by hand

**Time:** 3-4 hours of repetitive work

## The Solution

What if one command did all of it?

```bash
./mac-catalyst.sh
```

That's it. Go grab a coffee. Come back to a fully configured system.

## What's Inside mac-catalyst

### CLI Tools (38 packages)
- **Productivity:** eza, lsd, bat, fd, ripgrep, fzf
- **DevOps:** kubectl, helm, terraform, docker
- **Monitoring:** htop, k9s, tmux, starship
- **Languages:** node, python, ruby, go, rust

### GUI Applications (30 apps)
- **Browsers:** Arc, Firefox, Chrome
- **Development:** VS Code, Zed, Warp, TablePlus
- **Communication:** Slack, Discord, Zoom
- **Productivity:** Notion, Raycast, Rectangle

### VS Code Extensions (84 extensions)
- Themes, language servers, formatters
- Git tools, Docker/Kubernetes integration
- AI assistants, productivity boosters

## How It Works

### The Brewfile Approach

```ruby
# Core packages
brew "git"
brew "neovim"
brew "tmux"
brew "starship"

# Development tools
brew "node"
brew "python@3.13"
brew "go"

# CLI utilities
brew "eza"
brew "bat"
brew "fd"

# Applications
cask "visual-studio-code"
cask "arc"
cask "tableplus"
```

### Smart Features

1. **Dry-Run Mode**
   ```bash
   ./mac-catalyst.sh --dry-run
   ```
   See what would be installed without executing

2. **Selective Installation**
   ```bash
   ./mac-catalyst.sh --cli-only    # CLI tools only
   ./mac-catalyst.sh --cask-only   # GUI apps only
   ```

3. **Architecture Detection**
   - Automatically detects Apple Silicon vs Intel
   - Installs correct package versions

4. **Idempotent**
   - Run multiple times safely
   - Skips already-installed packages

## Key Learnings

### Homebrew is Underrated

For macOS, Homebrew is the ultimate package manager:
- Binary bottles (fast installs)
- Automatic updates
- Easy cleanup

### Configuration as Code

Your dotfiles and configs should be in version control. Every setting, every preference — tracked and reproducible.

### Documentation Matters

The best automation is well-documented. Include:
- What each component does
- How to update
- Troubleshooting steps

## Results

| Metric | Before | After |
|--------|--------|-------|
| Setup Time | 3-4 hours | 15-20 minutes |
| Manual Clicks | 200+ | 0 |
| Consistency | Varies | 100% |

## Try It Yourself

```bash
git clone https://github.com/irfancode/mac-catalyst
cd mac-catalyst
./mac-catalyst.sh
```

## Conclusion

Automation isn't about being lazy — it's about being efficient. With [mac-catalyst](https://github.com/irfancode/mac-catalyst), I've turned a 4-hour chore into a 15-minute task. That's time I can spend actually building things.

What's your automation story? Let's connect and share.

---

**Connect**: [LinkedIn](https://linkedin.com/in/sirfan98cs) | [GitHub](https://github.com/irfancode)
