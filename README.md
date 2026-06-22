# 💾 Media Server

This is project where I set up an Ubuntu media server to learn **Docker** and **Kubernetes**.
As such, all services are deployed using Docker.
All services are also deployed based on the official documentation, unless specified.
THe services were also all stored under the `/opt/` folder, where optional software are stored traditionally.
I expect the server to grow as I spend more time setting it up.

## 🏗️ Specs
- Ubuntu 24.04 LTS
- Dell Precision 3490 laptop
- Intel® Core™ Ultra 5 135H × 18
- 32GB RAM
- 1TB storage

## 🗃️ Containers & services
1. [Navidrome](#navidrome)
2. Jellyfin (coming soon...)

## 🎸 Navidrome
Navidrome is a service that serves music files of many formats, including FLAC, which makes up the bulk of my digital music library.
I created `docker-compose.yml` based on the official documentation, only changing the path to my volumes and uncommenting `ND_LOGLEVEL` as such:
```
services:
  navidrome:
    image: deluan/navidrome:latest
    user: 1000:1000 # should be owner of volumes
    ports:
      - "4533:4533"
    restart: unless-stopped
    environment:
      # Optional: put your config options customization here. Examples:
      ND_LOGLEVEL: debug
    volumes:
      - "/path/to/data:/data"
      - "/path/to/your/music/folder:/music:ro"
```

Since I had the error `No configuration file found. Loaded configuration only from environment variables` using the default settings, I changed the user from `1000` (me) to `0` (root), which solved the error.
This means that only root has permissions to modify the folder.
To fix this issue, I ran the following to give myself (user `1000`) ownership of the folder, in order to make recursive changes, i.e. to all files within the directory:
```
sudo chown -R 1000:1000 /opt/navidrome
```
where `1000:1000` stands for `<user-id>:<group-id>`.

To confirm that it is running, navigate to `localhost:4533` (or you custom port) on the local machine:
[image]

To stream from another device on the same network, you can find the server's IP address by running `hostname -I`, and then navigating to `<ip-address>:4533`:
[image]

You can also run the following to check the status of your containers:
```
$ docker ps
CONTAINER ID   IMAGE                     COMMAND            CREATED         STATUS         PORTS                                         NAMES
b0a5926ca179   deluan/navidrome:latest   "/app/navidrome"   2 minutes ago   Up 2 minutes   0.0.0.0:4533->4533/tcp, [::]:4533->4533/tcp   navidrome-navidrome-1
```

