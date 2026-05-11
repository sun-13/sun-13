# Terminal Setup

My terminal stack on macOS.

## Stack

- **Terminal app**: [Ghostty](https://ghostty.org/)
- **Prompt**: [Starship](https://starship.rs/)
- **Font**: AdwaitaMono Nerd Font Mono — required so Starship icons (branch, folder, etc.) render correctly

## Install

Prerequisite: [Homebrew](https://brew.sh/).

```bash
brew install --cask ghostty
brew install starship
brew install --cask font-adwaita-mono-nerd-font
```

## Configure

### Starship

Add to `~/.zshrc` (or `~/.bashrc` if using bash):

```sh
eval "$(starship init zsh)"
```

Apply the `gruvbox-rainbow` preset as the theme:

```sh
starship preset gruvbox-rainbow > ~/.config/starship.toml
```

### Ghostty

Config file: `~/.config/ghostty/config`

```
font-family = AdwaitaMono Nerd Font Mono
```

Reload Ghostty (`Cmd+Shift+,` opens the config; `Cmd+Shift+R` reloads).

### VSCode

In `settings.json`, set the integrated terminal font:

```json
"terminal.integrated.fontFamily": "AdwaitaMono Nerd Font Mono"
```

## Verify

Open Ghostty. The Starship prompt should show icons for the current directory and git branch. If you see boxes (☐) or question marks, the font isn't loaded — double-check the `font-family` value matches the installed font name exactly.
