# Docker SSHFS Proxy

Experiment with streaming files using SSH, SFTP, SSHFS protocols over two ssh hops via proxy simulated in Docker from macOS clients.
To run the experiment, we simulate two ssh servers on the same network.

## Setup

Clone this repo, then run `docker-compose up`.

You will effectively run two containers as `ssh-proxy => ssh-server`.

Update `~/.ssh/config` to setup ssh proxy configuration to use the `ssh-proxy` running on `localhost:2222` as a proxy.

```ssh
Host proxied-ssh-server
  HostName ssh-server
  User root
  IdentityFile ~/HackHackHack/Experiments/sshfs-proxy/sshserverkey
  ProxyCommand ssh -i ~/HackHackHack/Experiments/sshfs-proxy/sshproxykey root@localhost -p 2222 nc %h %p
```

Now we want to connect `macos => ssh-proxy => ssh-server`.

```sh
# install sshfs
brew cask install osxfuse
brew install sshfs
# mount remote directory
mkdir /tmp/mounted_fs
sshfs proxied-ssh-server:/tmp/mounted_fs /tmp/mounted_fs
# open finder on mounted drive
open /tmp/mounted_fs
# ssh into the machine
ssh proxied-ssh-server
# sftp
sftp proxied-ssh-server
# edit text file
ssh proxied-ssh-server cat /tmp/mounted_fs/testfile | open -f -a /Applications/Visual\ Studio\ Code.app
# open pdf file
ssh proxied-ssh-server cat /tmp/mounted_fs/test.pdf | open -f -a /Applications/Preview.app
```
