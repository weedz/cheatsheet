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

To use a specific SSH key for a certain repo you can use the SSH config file. 

Example:

```ssh_config
Host github-personal
  HostName github.com
  IdentityFile ~/.ssh/id_rsa.personal
  IdentitiesOnly yes
```

And then use `git@github-personal:[repo].git` as the remote.
