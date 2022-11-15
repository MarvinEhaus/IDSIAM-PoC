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

### Install Docker:

```shell
sudo apt-get install docker.io
sudo apt-get install docker-compose
```
Run this command to check if the installation was successfull. It should return the following output: "docker-compose version 1.29.2, build 5becea4c"

```shell
docker-compose --version
```


If there are permissioning issues with docker, try

```shell
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
```

Now log out and log in and the permission issues with docker should be fixed

### Download the gitlab Repo:
```shell
git clone --recurse-submodules https....
```

---

### Initial Setup:

Run the following commands to build the von-network initially:

```shell
cd von-network
./manage build
```
Wait until the build process is completed. Should you have any issues please see [here](https://github.com/bcgov/von-network).

To start the von-network run this command in the von-network directory:

```shell
./manage up
```

You can see the current network status at [localhost:9000](localhost:9000).
If you want to seee the pooled transactions in the current network visit [localhost:9000/browse](localhost:9000/browse).

To start the three Agennts representing a holder, issuer and verifier please use the following commands:

```shell
cd ..
docker-compose up -d
```

You can see the logs of a container with this command:
```shell
docker logs CONTAINER_NAME
```
You can add *-f* if you want a continous log output
