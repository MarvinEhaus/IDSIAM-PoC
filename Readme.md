# Readme - Prototyp SSI based IAM

---

## Setup:

Setup a Virtual Machine with the latest Ubuntu version.
If possible the IDE should have at least the following settings
- up to 120 GB Disk space
- Minimum of 8 GB RAM
- Minimum of 4 CPU Cores
- Enable Guest Addition

Install git, node, Docker and an IDE of your choice e.g. VSCode or Webstorm. To do this please follow these instructions:

### Install git:

```shell
sudo apt-get install git-all

git config --global user.name "FIRST_NAME LAST_NAME"
git config --global user.email "MY_EMAIL@example.com"
```

### Install node

It is highly recommended to use Node version manager to setup npm

```shell
sudo apt-get update
apt-get install build-essential libssl-dev -y
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
nvm --version
```

Close and reopen your terminal

```shell
nvm install node
```

### Install Docker

```
sudo apt-get install docker.io
sudo apt-get install docker-compose
docker-compose --version
```

If there are permissioning issues with docker, try

```shell
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
```

Now log out and log in and the permission issues with docker should be fixed

### Download the gitlab Repo.
```shell
git clone --recurse-submodules https....
```

---

