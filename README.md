vds
===

VDS setup guide

Some conventions
- `☺` means that command should be run on your local machine
- `#` means that command should be run as `root` user on remote machine
- `$` means that command should be run as `deployer` user on remote machine

## Initial setup

First of all, ssh to your freshly created remote server. I'll use Ubuntu 12.04 as a test machine
```
☺ ssh root@yourhost
```

```
# apt-get update
# apt-get -y updrade
# apt-get -y install vim
```

Let's add deployer user and grant him sudo access without password prompt:
```
# adduser deployer
# echo "deployer ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
```

Next, copy your public ssh key. If you're on OS X run:
```
☺ cat ~/.ssh/id_rsa.pub | pbcopy
```

Then paste it to `/home/deployer/.ssh/authorized_keys`:
```
# su deployer
$ mkdir -p ~/.ssh
$ vim ~/.ssh/authorized_keys # and paste your ssh key
```

Now, let's test our connection:
```
☺ ssh deployer@yourhost
```
If you are not prompted for password, ssh key works.

## Securing your server
```
☺ ssh deployer@yourhost
```
```
$ sudo su
# vim /etc/ssh/sshd_config
```

Next, change it so that you have the following options (replace `$SSH_PORT` with some random port)

```
# no passwords, because we use ssh keys
PasswordAuthentication no
# disallow root login
PermitRootLogin no
# change default port to something fancy
Port $SSH_PORT
```
`:wq` file and restart sshd:
```
/etc/init.d/ssh restart
```

Now, try connecting to your ssh

```
☺ ssh -p $SSH_PORT deployer@yourhost
```

Let's install firewall:
```
$ sudo apt-get install ufw
$ sudo ufw allow $SSH_PORT
$ sudo ufw allow 80
$ sudo ufw allow 443
$ sudo ufw --force enable
```

Now you have opened 3 ports for SSH, HTTP and HTTPS and disabled root login.

## Installing the packages

First of all let's install basic packages:

```
$ sudo apt-get -y update
$ sudo apt-get -y install python-software-properties
$ sudo apt-add-repository ppa:blueyed/ppa
$ sudo apt-get -y update
$ sudo apt-get -y install vim tree imagemagick curl git-core htop ufw build-essential openssl libreadline6 libreadline6-dev curl git-core zlib1g zlib1g-dev libssl-dev libyaml-dev \
  libsqlite3-dev sqlite3 libxml2-dev libxslt-dev autoconf libc6-dev ncurses-dev automake libtool bison subversion
```

### NGINX
```
$ sudo add-apt-repository ppa:nginx/stable
$ sudo apt-get -y update
$ sudo apt-get -y install nginx
```

### MySQL
```
$ sudo apt-get install mysql-server mysql-client libmysqlclient-dev
```

### rbenv

```
$ curl -L https://raw.github.com/fesplugas/rbenv-installer/master/bin/rbenv-installer | bash
```

and add the following lines to beginning of your .bashrc
```
export RBENV_ROOT="${HOME}/.rbenv"

if [ -d "${RBENV_ROOT}" ]; then
  export PATH="${RBENV_ROOT}/bin:${PATH}"
  eval "$(rbenv init -)"
fi
```

```
$ source ~/.bashrc
$ rbenv install 2.0.0-p247
$ rbenv gloval 2.0.0-p247
$ gem install bundler --no-ri --no-rdoc
$ rbenv rehash
```
