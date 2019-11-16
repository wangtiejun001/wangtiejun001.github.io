

## Prepare:
- Debian Stretch
- Docker
- GitlabCE


## Install docker
[Official Doc to install Docker](https://docs.docker.com/install/linux/docker-ce/debian/)

For Debian Stretch:
```bash
sudo apt-get update

sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg2 \
    software-properties-common
    
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -

## last 8 fringerprint: 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88
sudo apt-key fingerprint 0EBFCD88

# a may-be-master docker soucce for china main land:
# http://mirrors.ustc.edu.cn/docker-ce/linux/debian/
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/debian \
   $(lsb_release -cs) \
   stable"
      
sudo apt-get update

# do install
sudo apt-get install docker-ce docker-ce-cli containerd.io

# check installation
sudo docker run hello-world
```


## Install Gitlab CE
[Official GitLab Docker Install](https://docs.gitlab.com/omnibus/docker/)


#### Install with docker run
> --publish 8543:443 --publish 8780:80 --publish 8722:22 \
> use 8543 instead of 443: 443 is used by https on host machine \
> use 8780 instead of 80: 80 is used by nginx on host machine \
> use 8722 instead of 22: 22 is used by ssh on host machine
```bash
sudo docker run --detach \
  --hostname gitlab.dev.xjianke.com \
  --publish 8543:443 --publish 8780:80 --publish 8722:22 \
  --name gitlab_home \
  --restart always \
  --volume /srv/gitlab_home/config:/etc/gitlab \
  --volume /srv/gitlab_home/logs:/var/log/gitlab \
  --volume /srv/gitlab_home/data:/var/opt/gitlab \
  gitlab/gitlab-ce:latest
```

#### config ssh keys
- Add SSH key

    Add ssh key on webpage after logging in.

- Check SSH key on client machine terminal
```bash
# Success if receive:
# Welcome to GitLab, @username! message
ssh -T git@<your-git-domain.com>
```

- change port for ssh pushing
```bash
sudo docker exec -it gitlab /bin/bash

# edit the config file
vim /etc/gitlab/gitlab.rb

# change port 2289 to enable pushing under ssh
# Permission denied issue will occur on pushing by ssh 
# after adding ssh public by gitlab webpage
gitlab_rails['gitlab_shell_ssh_port'] = 2289

# restart container to take effect
sudo docker restart gitlab
```


