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

### Key Bindings & Plugins
- Custom prefix: `C-s` instead of default `C-b`
- Session management via `tmux-sessionx` plugin bound to `s` key
- Custom split bindings: `_` (horizontal) and `-` (vertical)
- Enabled plugins: vim-navigator, yank, copycat, cpu, resurrect, continuum, nerd-font-window-name, network-bandwidth, sessionx

### Environment Integration
- direnv integration for environment variable management
- Custom terminal settings and mouse mode enabled
- Enhanced clipboard support

## Sync Workflow

To sync upstream changes while preserving custom configuration:

```bash
# 1. Sync main branch with upstream
git checkout main
git pull upstream master
git push origin main

# 2. Update config branch
git checkout config
git pull origin config

# 3. Create sync branch and merge
git checkout -b sync-upstream
git rebase main

# 4. Resolve conflicts preserving custom config
# When conflicts occur, keep custom configurations from config branch

# 5. Create PR to merge into config branch
gh pr create --base config --head sync-upstream

# 6. Merge PR into config branch
```

## Conflict Resolution Strategy

During upstream sync, conflicts will occur between:
- Upstream basic configuration (main branch)  
- Custom enhanced configuration (config branch)

**Always preserve custom configurations** including:
- Catppuccin color theme
- Enhanced status bar with monitoring
- All enabled plugins and their configurations
- Custom key bindings and settings
- direnv integration

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

- Never modify the main `.tmux.conf` file directly
- All customizations go in `.tmux.conf.local`
- The config branch contains the production configuration
- Use sync-upstream branch only for merging upstream changes
- Always test configuration changes before committing to config branch
