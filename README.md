# gh-smoketest

## summary

A gh repo to test GITHUB_TOKENs with github's [`gh`](https://github.com/cli/cli) cli command.

## setup

First, create a [personal access token](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token) and [test it](https://docs.github.com/en/rest/overview/other-authentication-methods#basic-authentication):

```bash
github_username=mcarifio  # your name here
github_username=$USER  # alternatively...
xdg-open https://github.com/settings/tokens # might have to login as ${github_username}
token=${alphanums} # token you created
curl -u ${github_username}:${token} https://api.github.com/user | jq .
```

If you get a json response, then the personal access token `${token}` worked.



Next, install [`gh`](https://github.com/cli/cli/blob/trunk/docs/install_linux.md) using these commands:

```bash
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-key C99B11DEB97541F0
eval $(apt-config shell arch APT::Architecture) # amd64
sc=$(lsb_release -sc) # shortcode eoan
echo "deb [arch=${arch}] https://cli.github.com/packages ${sc} main" | sudo tee /etc/apt/sources.list.d/gh.list
sudo apt update
sudo apt upgrade
sudo apt install gh
```
This variant doesn't pollute `/etc/apt/sources.list`.

Optionally, you can configure `gh` locally:

```bash
gh config set git_protocol ssh
```

The `ssh` protocol assumes you have cut an ssh keypair and uploaded the public half to github. You've also crafted an ssh config stanza:

```bash
Host github.com:
  RequestTTY=no
  User=git
  IdentitiesOnly=yes
  IdentityFile=~/.ssh/keys.d/git@github.com_rsa
```
and tested _that_ with:

```bash
ssh -T git@github.com 2>&1 | grep $USER
Hi mcarifio! You've successfully authenticated, but GitHub does not provide shell access.
```

Almost there! Finally let's see if gh respects your personal token and it's privileges:

```bash
GITHUB_TOKEN=${token} gh api https://api.github.com/user # redo curl command above
GITHUB_TOKEN=${token} gh repo view gh-smoketest # gets this README.md
```
## other tests

I'm new to `gh`. You tell me.


## usage

All the work is in the setup above. Add `export GITHUB_TOKEN=${token}` in "the right place" consistent with your shell, e.g. `~/.bash_aliases` or `~/.bashrc`.




## more

If, like me, you access github as one of several users, you may need to change `GITHUB_TOKEN` "as needed". 
[Direnv](https://www.tecmint.com/direnv-manage-environment-variables-in-linux/) can be useful here. For example, 
given this `.envrc`:


```bash
export GITHUB_TOKEN=0000f0f7259fd3348efe030e6bbbbacdc58e3fff
export github_user=$(gh api https://api.github.com/user | jq .login)
```

and `direnv allow .`, then cd'ing into this directory establishes the right `GITHUB_TOKEN` and it's associated `github_username`.

## also

There's also a `gh login` command, but I haven't looked at it yet.



