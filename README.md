# Dotfiles

Personal configuration files managed with Git and GNU Stow.

This repository contains selected configuration files for macOS and CachyOS/Linux. It does not contain the entire `~/.config` directory.

## Repository structure

Each top-level directory is a GNU Stow package:

```text
dotfiles/
├── nvim/
│   └── .config/
│       └── nvim/
├── git/
│   └── .config/
│       └── git/
├── tmux/
│   └── .tmux.conf
├── alacritty/
│   └── .config/
│       └── alacritty/
└── README.md
```

The path inside each package represents the path relative to the home directory.

For example:

```text
nvim/.config/nvim/init.lua
```

is linked to:

```text
~/.config/nvim/init.lua
```

## Requirements

- Git
- GNU Stow

Install Stow on CachyOS:

```bash
sudo pacman -S stow
```

Install Stow on macOS with Homebrew:

```bash
brew install stow
```

## Initial setup on a new machine

Clone the repository:

HTTPS:

```bash
git clone https://github.com/DorianDasic/dotfiles.git ~/dotfiles
```

Enter the repository:

```bash
cd ~/dotfiles
```

See all available Stow packages:

```bash
find . -mindepth 1 -maxdepth 1 -type d -not -path './.git'
```

Apply a package:

```bash
stow --target="$HOME" nvim
```

Apply multiple packages:

```bash
stow --target="$HOME" nvim git tmux
```

Apply all packages:

```bash
stow --target="$HOME" */
```

The last command may also try to process directories that are not configuration packages. Prefer explicitly listing packages if the repository contains directories such as `scripts`, `docs`, or `macos`.

## Preview changes before applying

Use simulation mode to see what Stow would do without changing files:

```bash
stow --simulate --verbose --target="$HOME" nvim
```

Apply the package after confirming that the output is correct:

```bash
stow --target="$HOME" nvim
```

## Check the created links

Check a configuration directory:

```bash
ls -la ~/.config/nvim
```

Check where a specific file points:

```bash
readlink ~/.config/nvim/init.lua
```

The result should point to a file inside:

```text
~/dotfiles/
```

## Adding a new configuration

### 1. Create a package directory

For an application whose configuration is stored in `~/.config/example-app`:

```bash
cd ~/dotfiles
mkdir -p example-app/.config
```

### 2. Copy the configuration

```bash
cp -a ~/.config/example-app example-app/.config/
```

The resulting structure should be:

```text
dotfiles/
└── example-app/
    └── .config/
        └── example-app/
```

### 3. Preview the Stow operation

```bash
stow --simulate --verbose --target="$HOME" example-app
```

If the existing configuration is still in `~/.config/example-app`, Stow may report a conflict because the target already exists.

Back up the original configuration:

```bash
mv ~/.config/example-app ~/.config/example-app.backup
```

Then apply the package:

```bash
stow --target="$HOME" example-app
```

Test the application. If everything works, remove the backup when you are certain it is no longer needed:

```bash
rm -rf ~/.config/example-app.backup
```

### Adding a configuration file directly in the home directory

For a file such as `~/.tmux.conf`:

```bash
cd ~/dotfiles
mkdir -p tmux
cp ~/.tmux.conf tmux/
```

Preview and apply it:

```bash
stow --simulate --verbose --target="$HOME" tmux
stow --target="$HOME" tmux
```

## Updating configurations

Because Stow creates symbolic links, edit the configuration normally.

For example:

```bash
nano ~/.config/nvim/init.lua
```

The change is made directly to the file inside the Git repository.

Review the changes:

```bash
cd ~/dotfiles
git status
git diff
```

Commit and push them:

```bash
git add .
git commit -m "Update Neovim configuration"
git push
```

## Getting updates on another machine

Run:

```bash
cd ~/dotfiles
git pull
```

If the package has already been applied with Stow, the symlinked configuration will use the updated files automatically.

If the package has not yet been applied:

```bash
stow --target="$HOME" nvim
```

## Removing a package

To remove the symlinks created by a package:

```bash
cd ~/dotfiles
stow --delete --target="$HOME" nvim
```

This removes the symlinks but does not delete the files from the Git repository.

To apply the package again:

```bash
stow --target="$HOME" nvim
```

## Moving an existing configuration back

If you want to stop managing a configuration with Stow:

```bash
cd ~/dotfiles
stow --delete --target="$HOME" nvim
```

Then copy the files back from the repository:

```bash
cp -a nvim/.config/nvim ~/.config/
```

Only do this if you want regular files instead of symlinks.

## Daily Git workflow

After making configuration changes:

```bash
cd ~/dotfiles
git status
git diff
git add .
git commit -m "Describe the configuration change"
git push
```

Before pulling changes made on another machine:

```bash
cd ~/dotfiles
git status
git pull
```

If you have uncommitted changes, commit them or temporarily stash them first:

```bash
git stash
git pull
git stash pop
```

## Safety checks

Before committing files, check for secrets:

```bash
grep -RniE 'password|token|secret|api.?key|private.?key' . --exclude-dir=.git
```

Do not commit:

- Passwords.
- API keys.
- Access tokens.
- Private SSH keys.
- Cookies or browser sessions.
- OAuth credentials.
- Machine-specific secrets.
- Large caches or generated files.

Check what Git is going to commit:

```bash
git status
git diff --cached
```

## Useful commands

List all packages:

```bash
find . -mindepth 1 -maxdepth 1 -type d -not -path './.git'
```

Preview a package:

```bash
stow --simulate --verbose --target="$HOME" PACKAGE_NAME
```

Apply a package:

```bash
stow --target="$HOME" PACKAGE_NAME
```

Remove a package:

```bash
stow --delete --target="$HOME" PACKAGE_NAME
```

Show the repository status:

```bash
git status
```

Show configuration changes:

```bash
git diff
```

Update the repository:

```bash
git pull
```

Upload changes:

```bash
git add .
git commit -m "Update dotfiles"
git push
```
