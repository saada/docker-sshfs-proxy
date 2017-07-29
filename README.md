# Docker SSHFS Proxy

Experiment with streaming files using protocols SSH, SFTP, SSHFS simulated in Docker from macOS clients
To run the experiment, we simulate two ssh servers on the same network.

## Setup

Clone this repo, then run `docker-compose up`.

You will effectively run two containers as `ssh-proxy => ssh-server`.

Update `~/.ssh/config` to setup ssh proxy configuration.

```ssh
Host ssh-server
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
sshfs ssh-server:/tmp/mounted_fs /tmp/mounted_fs
# open finder on mounted drive
open /tmp/mounted_fs
# ssh into the machine
ssh ssh-server
# sftp
sftp ssh-server
# edit text file
ssh ssh-server cat /tmp/mounted_fs/testfile | open -f -a /Applications/Visual\ Studio\ Code.app
# open pdf file
ssh ssh-server cat /tmp/mounted_fs/file.pdf | open -f -a /Applications/Preview.app
```
