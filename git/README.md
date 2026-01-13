# Git

Set preferred editor:

```sh
$ git config --global core.editor "nvim"
```

Credential store:

```sh
$ git config --global --replace-all credential.helper /usr/lib/git-core/git-credential-libsecret
```

## Specify SSH key

Reference: <https://stackoverflow.com/a/11251797>

To use a specific SSH key for a certain repo you can use the SSH config file (`~./.ssh/config`).

Example:

```ssh_config
Host github-personal
  HostName github.com
  IdentityFile ~/.ssh/id_rsa.personal
  IdentitiesOnly yes
```

And then use `git@github-personal:[repo].git` as the remote. Which can also be aliased in git config.
With the following config you can use eq. `git clone weedz:dotfiles` instead of `git clone git@github-personal:weedz/dotfiles`.

```git_config
[url "git@github-personal:weedz/"]
  insteadOf = "weedz:"
```
