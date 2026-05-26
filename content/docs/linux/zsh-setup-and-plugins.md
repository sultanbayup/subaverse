---
title: "Zsh Setup and Plugins"
date: 2026-05-24
draft: false
weight: 2
type: docs
tags:
  - linux
  - zsh
  - terminal
  - shell
  - productivity
  - oh-my-zsh
summary: "Install and configure zsh with Oh My Zsh, Powerlevel10k, and essential plugins — autosuggestions, syntax highlighting, and fzf."
---

## Overview

Bash is fine. Zsh is better — not because it is fundamentally different, but because the ecosystem around it is mature enough to make your terminal noticeably faster to work in.

This doc covers installing zsh, setting up Oh My Zsh as the plugin framework, configuring Powerlevel10k as the prompt theme, and adding the plugins that actually make a difference day-to-day.

> [!NOTE]
> This guide targets Ubuntu/Debian. Package names and paths are the same on most Debian-based distros. macOS users can follow along — just replace `apt` with `brew`.

---

## Prerequisites

- Ubuntu 22.04+ or Debian 11+
- `sudo` access
- `curl` and `git` installed

```bash
sudo apt install -y curl git
```

---

## Install Zsh

```bash
sudo apt update && sudo apt install -y zsh
```

Verify the install:

```bash
zsh --version
# zsh 5.8.1 (or newer)
```

Set zsh as your default shell:

```bash
chsh -s $(which zsh)
```

Log out and back in. Your next terminal session will open in zsh. The first time it runs, zsh will ask you to configure it — skip that for now, Oh My Zsh will handle it.

---

## Install Oh My Zsh

Oh My Zsh is a framework that manages your zsh configuration, plugins, and themes. It gives you a clean `~/.zshrc` to work with and a straightforward way to add plugins without manually sourcing scripts.

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

This creates `~/.zshrc` and sets up the `~/.oh-my-zsh/` directory. Your old `.zshrc` (if any) gets backed up automatically.

---

## Install Powerlevel10k

Powerlevel10k is the most widely used zsh theme. It shows git status, Kubernetes context, exit codes, command execution time, and more — all in the prompt, without slowing down your terminal.

```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git \
  ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

Open `~/.zshrc` and set the theme:

```bash {filename="~/.zshrc"}
ZSH_THEME="powerlevel10k/powerlevel10k"
```

Reload your shell:

```bash
source ~/.zshrc
```

Powerlevel10k will launch its configuration wizard automatically. It walks you through choosing your prompt style — takes about two minutes. You can re-run it anytime with `p10k configure`.

> [!TIP]
> Install a [Nerd Font](https://www.nerdfonts.com/) in your terminal emulator before running the wizard. Powerlevel10k uses special glyphs for icons and separators. Without a Nerd Font, you will see broken characters. **MesloLGS NF** is the recommended font and is linked directly in the p10k wizard.

---

## Install Plugins

Plugins live in `~/.oh-my-zsh/custom/plugins/`. After installing each one, add its name to the `plugins=(...)` list in `~/.zshrc`.

### zsh-autosuggestions

Suggests commands from your history as you type. The suggestion appears as grey ghost text to the right of your cursor. Press `→` (right arrow) to accept it, or keep typing to ignore it.

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions \
  ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

### zsh-syntax-highlighting

Colors your command as you type it — green if the command is valid, red if it is not. Catches typos before you hit Enter.

```bash
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git \
  ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

> [!IMPORTANT]
> `zsh-syntax-highlighting` must be the **last plugin** in your `plugins=(...)` list. If it is not last, it will not highlight correctly.

### zsh-completions

Extends zsh's built-in tab completion with additional completions for tools like `docker`, `kubectl`, `terraform`, and many others.

```bash
git clone https://github.com/zsh-users/zsh-completions \
  ${ZSH_CUSTOM:-${ZSH:-~/.oh-my-zsh}/custom}/plugins/zsh-completions
```

### fzf

fzf is a command-line fuzzy finder. On its own it is a general-purpose filter, but the zsh integration is what makes it valuable — it replaces the default `Ctrl+R` history search with an interactive, searchable list.

```bash
sudo apt install -y fzf
```

> [!NOTE]
> The `fzf` Oh My Zsh plugin enables the key bindings automatically. No extra configuration needed beyond adding it to your plugins list.

---

## Configure plugins in .zshrc

Open `~/.zshrc` and update the plugins line:

```bash {filename="~/.zshrc"}
plugins=(
  git
  kubectl
  terraform
  docker
  fzf
  zsh-autosuggestions
  zsh-completions
  zsh-syntax-highlighting  # must be last
)
```

Add this line just above the `plugins=(...)` block to load zsh-completions properly:

```bash {filename="~/.zshrc"}
fpath+=${ZSH_CUSTOM:-${ZSH:-~/.oh-my-zsh}/custom}/plugins/zsh-completions/src
```

Reload:

```bash
source ~/.zshrc
```

---

## Optional: zoxide (smarter cd)

zoxide is a replacement for `cd` that learns which directories you visit most. Instead of typing the full path, you type `z` followed by a partial name and it jumps to the right place.

```bash
curl -sSfL https://raw.githubusercontent.com/ajeetdsouza/zoxide/main/install.sh | sh
```

Add to the end of `~/.zshrc`:

```bash {filename="~/.zshrc"}
eval "$(zoxide init zsh)"
```

Usage:

```bash
z subaverse     # jumps to ~/repo/subaverse
z kube          # jumps to wherever your kubeconfig lives
```

---

## Verification

{{< tabs >}}
{{< tab name="Plugins" >}}
Check that all plugins loaded without errors:

```bash
echo $ZSH_CUSTOM
ls ~/.oh-my-zsh/custom/plugins/
```

You should see `zsh-autosuggestions`, `zsh-syntax-highlighting`, and `zsh-completions` listed.
{{< /tab >}}
{{< tab name="Theme" >}}
Confirm Powerlevel10k is active:

```bash
echo $ZSH_THEME
# powerlevel10k/powerlevel10k
```

If the prompt looks broken, run `p10k configure` to redo the wizard.
{{< /tab >}}
{{< tab name="fzf" >}}
Test the fzf history search:

Press `Ctrl+R` in your terminal. You should see an interactive fuzzy search panel instead of the default reverse history search.
{{< /tab >}}
{{< tab name="zoxide" >}}
```bash
which z
# should return a path, not "not found"

# Navigate to a few directories, then test
z <partial-name>
```
{{< /tab >}}
{{< /tabs >}}

---

## Troubleshooting

{{< details title="Prompt shows broken characters or boxes" closed="true" >}}
Your terminal font does not support Nerd Font glyphs. Install **MesloLGS NF** from [nerdfonts.com](https://www.nerdfonts.com/) and set it as your terminal's font. Then run `p10k configure` again.
{{< /details >}}

{{< details title="Powerlevel10k instant prompt warning on startup" closed="true" >}}
You will see this if the p10k instant prompt block is missing from the top of `~/.zshrc`, or if something produces console output before p10k initializes (common culprits: `zoxide init`, `nvm`, `pyenv`).

Make sure this block is the **very first thing** in `~/.zshrc`, before any other content:

```bash
if [[ -r "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh" ]]; then
  source "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh"
fi
```

If the block is already there and you still see the warning, a tool initialized after it is printing output. Move that tool's init line to after `source $ZSH/oh-my-zsh.sh`.
{{< /details >}}

{{< details title="command not found: ^M errors in .zsh_aliases or .zsh_functions" closed="true" >}}
The files have Windows-style line endings (`\r\n`) instead of Unix (`\n`). This happens when files are created or edited on Windows and transferred to Linux.

Fix it with `sed`:

```bash
sed -i 's/\r//' ~/.zsh_aliases
sed -i 's/\r//' ~/.zsh_functions
sed -i 's/\r//' ~/.zshrc
```

Then reload:

```bash
source ~/.zshrc
```
{{< /details >}}

{{< details title="Autosuggestions not appearing" closed="true" >}}
Check that `zsh-autosuggestions` is in your `plugins=(...)` list and that you ran `source ~/.zshrc` after editing. Also confirm the plugin directory exists:

```bash
ls ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions
```
{{< /details >}}

{{< details title="Syntax highlighting not working" closed="true" >}}
`zsh-syntax-highlighting` must be the last entry in `plugins=(...)`. Move it to the end and reload.
{{< /details >}}

{{< details title="zsh-completions not completing kubectl/terraform" closed="true" >}}
Make sure the `fpath` line appears **before** the `source $ZSH/oh-my-zsh.sh` line in your `.zshrc`. The completion path must be registered before Oh My Zsh initializes.
{{< /details >}}

---

## References

- [Oh My Zsh](https://ohmyz.sh/)
- [Powerlevel10k](https://github.com/romkatv/powerlevel10k)
- [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)
- [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)
- [fzf](https://github.com/junegunn/fzf)
- [zoxide](https://github.com/ajeetdsouza/zoxide)
