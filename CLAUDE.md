# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Architecture

This is a customized fork of the "Oh my tmux!" configuration that follows a specific dual-branch architecture:

### Branch Structure
- **`main` branch**: Mirror of upstream `gpakosz/.tmux` - DO NOT MODIFY directly
- **`config` branch**: Contains all custom configurations and modifications
- **`sync-upstream` branch**: Temporary branch used for syncing upstream changes with custom config

### Remote Configuration
- **`origin`**: `git@github.com:Alrefai/mitmux.git` (this fork)
- **`upstream`**: `git@github.com:gpakosz/.tmux.git` (original repo)

## Key Files

- **`.tmux.conf`**: Main tmux configuration script (DO NOT MODIFY - from upstream)
- **`.tmux.conf.local`**: Customization file containing all personal configurations

## Custom Configuration Features

The `.tmux.conf.local` file contains extensive customizations:

### Theme & Visual
- Catppuccin color scheme with custom color variables (`colour_black`, `colour_base`, `colour_mantle`, etc.)
- Powerline separators and enhanced status bar styling
- Custom window status formatting with powerline elements

### Enhanced Status Bar
- Network bandwidth monitoring via `tmux-network-bandwidth` plugin
- CPU and RAM percentage display with custom formatting
- Custom status line with monitoring: `#{network_bandwidth}`, `#{cpu_icon}#{cpu_percentage}`, `#{ram_icon}#{ram_percentage}`
- Custom shell functions: `#{online}` (connectivity status) and `#{isRemote}` (SSH detection)

### Custom Shell Functions

The configuration includes custom POSIX shell functions in `.tmux.conf.local`:

```bash
# These MUST remain commented (tmux parsing requirement)
# online() {
#   ping -c 1 1.1.1.1 >/dev/null 2>&1 && printf '󰲝 ' || printf '󰲜 '
# }
# 
# isRemote() {
#   if tmux show-env -s | grep -q '^SSH_CONNECTION'; then
#     printf ' '
#     hostname | sed -e 's/\.local$/ /' -e 's/\.lan$/ /'
#   fi
# }
```

**Critical**: Custom functions must stay commented with `# ` prefix for tmux's shell execution system to work.

### Key Bindings & Plugins
- Custom prefix: `C-s` instead of default `C-b` (upstream uses `C-a` as secondary)
- Session management via `tmux-sessionx` plugin bound to `s` key
- Custom split bindings: `_` (horizontal) and `-` (vertical)
- Last window: `l` key
- Enabled plugins: vim-navigator, yank, copycat, cpu, resurrect, continuum, nerd-font-window-name, network-bandwidth, sessionx

### Important Key Binding Differences from Upstream
This fork modifies the upstream "Oh my tmux!" default bindings:
- **Prefix**: `C-s` (this fork) vs `C-a`/`C-b` (upstream)
- **Split panes**: `_`/`-` (this fork) vs upstream defaults

### Environment Integration
- direnv integration for environment variable management
- Custom terminal settings and mouse mode enabled
- Enhanced clipboard support

## Sync Workflow

**IMPORTANT**: Never rebase directly on the `config` branch. Always use the `sync-upstream` branch for upstream integration.

```bash
# 1. Sync main branch with upstream
git checkout main
git pull upstream master
git push origin main

# 2. Review upstream changes first (recommended)
git checkout config
git log --oneline config..main
git diff config..main -- .tmux.conf.local

# 3. Create sync branch from config and rebase
git checkout -b sync-upstream
git rebase main

# 4. Resolve conflicts using simple strategy (see Conflict Resolution section)

# 5. Create PR to merge into config branch
gh pr create --base config --head sync-upstream

# 6. Merge PR and cleanup
gh pr merge --squash
git checkout config
git pull origin config
git branch -D sync-upstream
```

## Conflict Resolution Strategy

During upstream sync, conflicts typically occur in:
- **`.tmux.conf.local`**: Status line configuration conflicts (custom vs. upstream format)
- **`.tmux.conf`**: Main configuration conflicts from upstream updates

### Simple Resolution Approach

When `git rebase main` encounters conflicts:

```bash
# Review upstream changes before resolving (recommended)
git log --oneline config..main
git diff config..main -- .tmux.conf.local

# Resolve all conflicts by keeping custom versions
# Note: --theirs in rebase context means "the branch being rebased FROM" (config)
git checkout --theirs .tmux.conf .tmux.conf.local
git add .tmux.conf .tmux.conf.local
git rebase --continue

# Repeat if additional conflicts occur
```

### Pre-Rebase Review (Optional)

For important upstream changes that might need manual integration:

```bash
# Check for breaking changes or new features
git log config..main --grep="feat\|BREAKING\|fix.*important"

# Review changes to configuration variables
git diff config..main -- .tmux.conf.local | grep -E "^[+-].*tmux_conf_"
```

**Always preserve these custom configurations**:
- Catppuccin color theme with custom variables
- Enhanced status bar with `#{online}#{network_bandwidth}#{isRemote}`
- Plugin configurations and TPM settings
- Custom key bindings (`C-s` prefix, split bindings)
- direnv integration settings

## GitHub CLI Configuration

The repository is configured with GitHub CLI default: `gh repo set-default Alrefai/mitmux`

## Plugin Management

Uses TPM (Tmux Plugin Manager) with custom plugins:
- `christoomey/vim-tmux-navigator`
- `tmux-plugins/tmux-yank`
- `tmux-plugins/tmux-cpu` 
- `tmux-plugins/tmux-resurrect`
- `tmux-plugins/tmux-continuum`
- `joshmedeski/tmux-nerd-font-window-name`
- `Alrefai/tmux-network-bandwidth#separator-option`
- `omerxx/tmux-sessionx`

## Important Notes

- **GitHub Default Branch**: `config` branch is the GitHub default and contains production configuration
- Never modify the main `.tmux.conf` file directly (it's from upstream)
- All customizations go in `.tmux.conf.local`
- Use `sync-upstream` branch only for merging upstream changes
- Always test configuration changes before committing to config branch
- Custom shell functions must remain commented with `# ` prefix for tmux parsing

## Troubleshooting

### Configuration Override Issues
If tmux overwrites your preferences in `.tmux.conf.local`, append `#!important`:
```bash
bind c new-window -c '#{pane_current_path}' #!important
set -g default-terminal "screen-256color" #!important
```

### Status Line Problems
- **Broken/duplicated status line**: Usually caused by Unicode width mismatches between glib and glibc
- **Custom functions not working**: Ensure they remain commented with `# ` prefix

### Testing Configuration
```bash
# Launch clean tmux session for testing
tmux -f /dev/null -L test

# Check if issue persists - if yes, it's a tmux problem, not configuration
```
