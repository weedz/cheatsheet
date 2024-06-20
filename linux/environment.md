# Environment variables

## How to determine where an environment variable came from?

Source: <https://unix.stackexchange.com/a/249922>

### System wide

- `/etc/environment`: specifically meant for environment variables
- `/etc/env.d/*`: environment variables, split in multiple files
- `/etc/profile`: all types of initialization scripts
- `/etc/profile.d/*`: initialization scripts
- `/etc/bashrc`, `/etc/bash.bashrc`: meant for functions and aliases

### User specific

- `~/.profile`: used for all shells
- `~/.pam_environment`: part of Pluggable Authentication Modules for Linux
- `~/.bash_profile`: initialization for login (bash-)shells
- `~/.bashrc`: initialization for all interactive (bash-)shells
- `~/.cshrc`, `~/.zshrc`, `~/.tcshrc`: similar for non-bash shells
