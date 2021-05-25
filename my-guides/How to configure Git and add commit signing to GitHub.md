# How to configure Git and add commit signing to GitHub

## Installation:

If you don't have Git installed, go to it's [official website](https://git-scm.com/downloads) you can find how to install it for your system.

## User identity configuration:

After installing Git open your terminal or, in case you're using Windows, Git Bash.

Then, enter the following code replacing your actual name and email:

```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
```

Now, thanks to the `--config` option, those will be the default name and email for the commits.

## Saving remote user credentials

Also, if you are on a safe personal computer and want to save you remote credentials so you don't have to enter it every time you push your commits, you can do it so by changing the following configuration:

```bash
# Saves username and password for any remote (GitHub, GitLab, etc.)
git config --global credential.helper "store"

# You can also just make it stored temporarily, for 7200 seconds (2 hours) in this example
git config --global credential.helper "store --timeout 7200"
```

If you prefer to enter at least your password, you can use instead the following:

```bash
# Saves username for selected remote, GitHub in this example
git config --global credential.https://github.com.username "my_github_username"
```

## Creating a GPG key and adding it to GitHub:

Still on your terminal, now you need to generate a GPG key. The `gpg` command is available by default in Windows' Git Bash, but you may have to install it on Linux (`sudo apt install gpg` for Ubuntu) and Mac (`brew install gpg`). After assuring it is installed:

```bash
gpg --full-generate-key
```

You'll be prompted for some options and then for the key credentials you want to use. The only option you **must** change is the key size, that should be at least `4096` bits. After finishing it, use `gpg --list-secret-keys --keyid-format LONG` list your keys. It will return something like:

```bash
/Users/hubot/.gnupg/secring.gpg
------------------------------------
sec   4096R/3AA5C34371567BD2 2016-03-10 [expires: 2017-03-10]
uid                          Hubot 
ssb   4096R/42B317FD4BA89E7A 2016-03-10
```

Your GPG key ID is located at the `sec` line, after the `/`, in this case it is `3AA5C34371567BD2`. Now we are exporting it in ASCII armor format with:

```bash
gpg --armor --export 3AA5C34371567BD2
```

The return will be a very long text beginning with `-----BEGIN PGP PUBLIC KEY BLOCK-----` and ending with `-----END PGP PUBLIC KEY BLOCK-----`. Copy all of it and head to GitHub. You can follow [this simple guide from GitHub](https://docs.github.com/en/github/authenticating-to-github/adding-a-new-gpg-key-to-your-github-account) to finish adding it there.

## Configuring commit signing:

Now you need to tell Git you want to use this GPG key for commit signing. You can do this, replacing the key ID, with:

```bash
git config --global user.signingKey 3AA5C34371567BD2
```

Just commit with the `-S` flag for signing it!

## Fixing a common problem:

On Linux and Mac you might find an error stating that `gpg failed to sign the data`. A common fix is just adding `export GPG_TTY=$(tty)` to your `~/.bashrc`, `~/.zshrc` or equivalent. 

## Auto-signing your commits:

Instead of running `git commit -S` all the time, you can change this configuration:

```bash
git config --global commit.gpgsign true
```

With that, Git will sign your commits by default!