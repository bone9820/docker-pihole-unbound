# Pihole with Unbound DNS within Docker

# Contents
1. [ Git Install ](#git)
2. [ Docker Install ](#docker)
3. [ Pihole with Unbound DNS Install ](#pihole)

<a name="git"></a>
# Git

### Install Git
Run `sudo apt install -y git`, then check the installation by running `git --version`.

### Configure Git
Set up your username and email by running:
`git config --global user.name "Harry Potter"`, then `git config --global user.email "h.potter@hogwarts.prof"`.

Check the config by running `git config --list`.

The configuration is stored in the `~/.gitconfig` file. Edit this directly, or via `git config` to make further changes if required.

You can also tell git what text editor you'd like to use, for example this sets it to nano: `git config --global core.editor nano`.

<a name="docker"></a>
# Install Docker and Docker Compose

### Install Docker

Run `curl -sSL https://get.docker.com | sh`.

Then add your user to the docker usergroup by running `sudo gpasswd -a pi docker` - replacing pi with whatever account you use docker on your raspberry pi. Logout using `logout` command and log back in to make sure the group setting is applied. (You can check that your username is in the right groups using this command `grep '<username>' /etc/group`.)

Now test docker is installed with `docker version`.

You can also check that your user can run a docker container by running the hello-world container, `docker run hello-world`. Afterwards, clean up by removing the container and the hello-world image by firstly getting the container id by running `docker ps -a`. Then force remove the container by running `docker rm -f <container id>` which will stop and remove it. Finally, remove the downloaded image by running `docker image rm hello-world`.

### Install Docker Compose

Run `sudo apt install -y libffi-dev libssl-dev python3 python3-pip`, then `sudo apt remove python-configparser`, and finally run `sudo pip3 -v install docker-compose`.

Reboot the pi by running `sudo reboot`.

### Dump of Common Docker Commands

`docker ps -a`  
`docker stop <container-id>`  
`docker image ls`  
`docker image rm <image-id>`  
`docker rm -f <container-id>`  
`docker exec -it <container-id> bash`  
`docker build -t <docker-username>/<image-name>`  
`docker push <docker-username>/<image-name>`

`docker-compose pull`  
`docker-compose down`  
`docker-compose up -d`

<a name="pihole"></a>
# Install Pihole and Unbound DNS

### Setup
Create environment variables referenced in the `docker-compose.yml` file:

- `sudo nano .env` in same directory as `docker-compose.yml`
- add the referenced env variables to the file, e.g.:
  ```
  PIHOLE_PASSWORD=<password Pihole for web UI>
  PIHOLE_TIMEZONE=Europe/London
  PIHOLE_ServerIP=<local IP Address of host>
  ```

Build and start pihole-unbound docker container:

- `docker-compose up -d`
- `docker ps -a` to get container id
- `docker logs <container_id>` to check the startup logs

### Configure
Add whitelist of common false-positives (optional)

- `git clone https://github.com/anudeepND/whitelist.git ~/Documents/pihole-whitelist`
- `cd ~/Documents/pihole-whitelist/`
- `sudo python3 ./scripts/whitelist.py --dir ~/.pihole-unbound/etc-pihole/ --docker`

### Update
Stop pihole-unbound docker container

- `docker-compose down`

Start pihole-unbound docker container again, forcing it to build again by supplying flag.

- `docker-compose up --build -d`