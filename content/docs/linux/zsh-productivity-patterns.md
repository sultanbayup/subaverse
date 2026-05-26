---
title: "Zsh Productivity Patterns"
date: 2026-05-24
draft: false
weight: 3
type: docs
tags:
  - linux
  - zsh
  - terminal
  - shell
  - productivity
  - kubectl
  - aliases
summary: "Practical zsh patterns for engineers — aliases, shell functions, fzf workflows, and key bindings for cloud, Kubernetes, and Git work."
---

## Overview

This doc covers the patterns that actually speed up day-to-day terminal work — aliases, shell functions, fzf integrations, and key bindings. The focus is on cloud, Kubernetes, and Git workflows since that is where most of the repetition lives.

> [!NOTE]
> This doc assumes you have zsh set up with Oh My Zsh and the core plugins installed. If not, start with [Zsh Setup and Plugins](./zsh-setup-and-plugins).

---

## Aliases

Aliases are the lowest-effort productivity gain. A few well-chosen ones eliminate a surprising amount of typing over time.

Add these to `~/.zshrc` (or a separate `~/.zsh_aliases` file sourced from `.zshrc`).

### Git

```bash {filename="~/.zshrc"}
alias gs='git status'
alias ga='git add'
alias gc='git commit'
alias gp='git push'
alias gl='git log --oneline --graph --decorate --all'
alias gco='git checkout'
alias gcb='git checkout -b'
alias gd='git diff'
alias gds='git diff --staged'
```

> [!TIP]
> Oh My Zsh's built-in `git` plugin already defines many of these. Run `alias | grep git` to see what is already available before adding duplicates.

### Kubernetes

```bash {filename="~/.zshrc"}
alias k='kubectl'
alias kgp='kubectl get pods'
alias kgpa='kubectl get pods -A'
alias kgs='kubectl get svc'
alias kgn='kubectl get nodes'
alias kd='kubectl describe'
alias kdp='kubectl describe pod'
alias kl='kubectl logs'
alias klf='kubectl logs -f'
alias kex='kubectl exec -it'
alias kns='kubectl config set-context --current --namespace'
alias kctx='kubectl config use-context'
alias kgctx='kubectl config get-contexts'
```

### Terraform

```bash {filename="~/.zshrc"}
alias tf='terraform'
alias tfi='terraform init'
alias tfp='terraform plan'
alias tfa='terraform apply'
alias tfd='terraform destroy'
alias tfo='terraform output'
alias tfs='terraform state'
alias tfsl='terraform state list'
```

### Navigation

```bash {filename="~/.zshrc"}
alias ..='cd ..'
alias ...='cd ../..'
alias ....='cd ../../..'
alias ll='ls -lah'
alias la='ls -A'
```

---

## Shell Functions

Functions are for anything that needs logic, arguments, or multiple commands. Keep them in `~/.zshrc` or a dedicated `~/.zsh_functions` file.

### Switch kubectl namespace quickly

```bash {filename="~/.zshrc"}
kns() {
  kubectl config set-context --current --namespace="$1"
  echo "Switched to namespace: $1"
}
```

Usage: `kns production`

### Tail pod logs by partial name

Useful when you do not want to copy the full pod name with the random suffix.

```bash {filename="~/.zshrc"}
klp() {
  local pod
  pod=$(kubectl get pods --no-headers | grep "$1" | awk '{print $1}' | head -1)
  if [[ -z "$pod" ]]; then
    echo "No pod found matching: $1"
    return 1
  fi
  echo "Tailing logs for: $pod"
  kubectl logs -f "$pod"
}
```

Usage: `klp api` — finds the first pod with "api" in the name and tails its logs.

### Quick port-forward

```bash {filename="~/.zshrc"}
kpf() {
  # Usage: kpf <pod-partial-name> <local-port>:<remote-port>
  local pod
  pod=$(kubectl get pods --no-headers | grep "$1" | awk '{print $1}' | head -1)
  if [[ -z "$pod" ]]; then
    echo "No pod found matching: $1"
    return 1
  fi
  echo "Port-forwarding $pod → $2"
  kubectl port-forward "$pod" "$2"
}
```

Usage: `kpf grafana 3000:3000`

### Create and enter a directory

```bash {filename="~/.zshrc"}
mkcd() {
  mkdir -p "$1" && cd "$1"
}
```

Usage: `mkcd ~/projects/new-thing`

### Extract any archive

One command for all archive formats — no more remembering flags.

```bash {filename="~/.zshrc"}
extract() {
  case "$1" in
    *.tar.gz|*.tgz)   tar xzf "$1"   ;;
    *.tar.bz2|*.tbz2) tar xjf "$1"   ;;
    *.tar.xz)         tar xJf "$1"   ;;
    *.tar)            tar xf "$1"    ;;
    *.gz)             gunzip "$1"    ;;
    *.bz2)            bunzip2 "$1"   ;;
    *.zip)            unzip "$1"     ;;
    *.7z)             7z x "$1"      ;;
    *)                echo "Unknown archive format: $1" ;;
  esac
}
```

Usage: `extract archive.tar.gz`

### GCP project switcher

```bash {filename="~/.zshrc"}
gcpswitch() {
  gcloud config set project "$1"
  echo "Active project: $(gcloud config get-value project)"
}
```

Usage: `gcpswitch my-project-id`

---

## fzf Workflows

fzf becomes genuinely useful once you wire it into your daily commands. These patterns use fzf as an interactive picker rather than just a history search.

### Interactive kubectl pod selector

Pick a pod from a fuzzy list instead of typing the full name:

```bash {filename="~/.zshrc"}
kfzf() {
  local pod
  pod=$(kubectl get pods --no-headers | fzf --height=40% --reverse | awk '{print $1}')
  [[ -n "$pod" ]] && echo "$pod"
}

# Tail logs with fzf pod picker
klf-pick() {
  local pod
  pod=$(kfzf)
  [[ -n "$pod" ]] && kubectl logs -f "$pod"
}

# Exec into a pod with fzf picker
kex-pick() {
  local pod
  pod=$(kfzf)
  [[ -n "$pod" ]] && kubectl exec -it "$pod" -- /bin/sh
}
```

Usage: `klf-pick` — opens a fuzzy list of pods, select one, logs start tailing.

### Interactive git branch switcher

```bash {filename="~/.zshrc"}
gbr() {
  local branch
  branch=$(git branch --all | grep -v HEAD | fzf --height=40% --reverse | sed 's/remotes\/origin\///' | xargs)
  [[ -n "$branch" ]] && git checkout "$branch"
}
```

Usage: `gbr` — fuzzy list of all branches, select to checkout.

### Interactive process killer

```bash {filename="~/.zshrc"}
fkill() {
  local pid
  pid=$(ps aux | fzf --height=40% --reverse --header-lines=1 | awk '{print $2}')
  [[ -n "$pid" ]] && kill -9 "$pid" && echo "Killed PID $pid"
}
```

Usage: `fkill` — pick a process from a fuzzy list and kill it.

---

## Key Bindings

A few key bindings worth knowing in zsh. Most of these work out of the box.

| Key | Action |
|-----|--------|
| `Ctrl+R` | Fuzzy history search (fzf plugin) |
| `Ctrl+T` | Fuzzy file search in current directory (fzf) |
| `Alt+C` | Fuzzy cd into a subdirectory (fzf) |
| `Ctrl+A` | Move cursor to beginning of line |
| `Ctrl+E` | Move cursor to end of line |
| `Ctrl+W` | Delete word before cursor |
| `Ctrl+U` | Clear entire line |
| `Ctrl+L` | Clear screen (same as `clear`) |
| `Alt+.` | Insert last argument from previous command |
| `!!` | Repeat last command (useful with `sudo !!`) |
| `!$` | Last argument of previous command |

> [!TIP]
> `sudo !!` is one of the most useful combos — run the previous command with sudo when you forgot to prefix it.

---

## .zshrc Organization

Once your `.zshrc` grows, it helps to keep it structured. A clean layout makes it easier to find and update things.

```bash {filename="~/.zshrc"}
# ── Oh My Zsh ────────────────────────────────────────────────
export ZSH="$HOME/.oh-my-zsh"
ZSH_THEME="powerlevel10k/powerlevel10k"

fpath+=${ZSH_CUSTOM:-${ZSH:-~/.oh-my-zsh}/custom}/plugins/zsh-completions/src

plugins=(
  git
  kubectl
  terraform
  docker
  fzf
  zsh-autosuggestions
  zsh-completions
  zsh-syntax-highlighting
)

source $ZSH/oh-my-zsh.sh

# ── Environment ──────────────────────────────────────────────
export EDITOR="vim"
export PATH="$HOME/.local/bin:$PATH"

# ── Aliases ──────────────────────────────────────────────────
[[ -f ~/.zsh_aliases ]] && source ~/.zsh_aliases

# ── Functions ────────────────────────────────────────────────
[[ -f ~/.zsh_functions ]] && source ~/.zsh_functions

# ── Tools ────────────────────────────────────────────────────
eval "$(zoxide init zsh)"

# ── p10k ─────────────────────────────────────────────────────
[[ -f ~/.p10k.zsh ]] && source ~/.p10k.zsh
```

Splitting aliases and functions into separate files keeps `.zshrc` readable and makes it easier to sync across machines.

---

## Syncing Across Machines

If you work across multiple servers or machines, keeping your zsh config in a dotfiles repo is worth the setup time.

The basic approach:

```bash
# On your main machine
mkdir ~/dotfiles
cp ~/.zshrc ~/dotfiles/
cp ~/.zsh_aliases ~/dotfiles/
cp ~/.zsh_functions ~/dotfiles/
cp ~/.p10k.zsh ~/dotfiles/

cd ~/dotfiles
git init && git add . && git commit -m "init dotfiles"
git remote add origin git@github.com:yourname/dotfiles.git
git push -u origin main
```

On a new machine:

```bash
git clone git@github.com:yourname/dotfiles.git ~/dotfiles
ln -sf ~/dotfiles/.zshrc ~/.zshrc
ln -sf ~/dotfiles/.zsh_aliases ~/.zsh_aliases
ln -sf ~/dotfiles/.zsh_functions ~/.zsh_functions
ln -sf ~/dotfiles/.p10k.zsh ~/.p10k.zsh
```

Symlinks mean changes on any machine sync back to the repo with a `git push`.

---

## References

- [Zsh Setup and Plugins](./zsh-setup-and-plugins) — prerequisite for this doc
- [fzf key bindings and fuzzy completion](https://github.com/junegunn/fzf#key-bindings-for-command-line)
- [zoxide](https://github.com/ajeetdsouza/zoxide)
- [Oh My Zsh plugins](https://github.com/ohmyzsh/ohmyzsh/wiki/Plugins)
