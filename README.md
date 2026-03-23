# FiveM Reverse Proxy (UDP + HTTP)

This project is a small utility script I wrote to make it easier to run
**multiple FiveM reverse proxies on a single VPS**.

Instead of manually configuring containers or proxy services every time,
this script builds everything automatically using **Docker, Nginx, and
Socat**.

The idea is simple: you run one command, and the script creates a
container that listens on a port and forwards the traffic to your real
FiveM server.

It supports both:

-   **UDP traffic** (game traffic)
-   **HTTP / WebSocket traffic** (info.json, txAdmin, etc.)

This can be useful if you want to:

-   hide your real server IP
-   run multiple entry points for the same server
-   separate infrastructure layers
-   build hosting or proxy systems for FiveM

------------------------------------------------------------------------

# How it works

When you run the script, it automatically:

1.  Generates a Docker image
2.  Creates a container with Nginx + Socat
3.  Opens the chosen port
4.  Forwards traffic to your real FiveM server

Traffic flow looks like this:

Player → Proxy VPS → Docker container → Real FiveM server

Nginx handles the HTTP side while **socat forwards the UDP packets**.

------------------------------------------------------------------------

# Requirements

You only need:

-   A Linux VPS
-   Root access
-   Debian or Ubuntu
-   Docker

------------------------------------------------------------------------

# Installing Docker

If Docker is not installed on your server, you can install it with the
following commands:

``` bash
sudo apt update
sudo apt upgrade -y

sudo apt install -y ca-certificates curl gnupg

sudo install -m 0755 -d /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian $(. /etc/os-release && echo $VERSION_CODENAME) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update

sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo systemctl enable docker
sudo systemctl start docker
```

You can verify that Docker works with:

``` bash
docker --version
```

------------------------------------------------------------------------

# Download the project

Clone the repository:

``` bash
git clone https://github.com/YOUR_GITHUB_USERNAME/fivem-reverse-proxy.git
cd fivem-reverse-proxy
```

Make the script executable:

``` bash
chmod 777 Proxy
```

------------------------------------------------------------------------

# Usage

The script syntax is very simple:

    ./Proxy <FiveM_Server_IP> <FiveM_Server_Port> <Proxy_Port>

Example:

``` bash
./Proxy 1.2.3.4 30120 30155
```

This will create a proxy like this:

    0.0.0.0:30155 → 1.2.3.4:30120

------------------------------------------------------------------------

# Running multiple proxies

You can create as many proxies as you want, as long as the ports are
different.

Example:

``` bash
./proxy 1.2.3.4 30120 30155
./proxy 1.2.3.4 30120 30156
./proxy 1.2.3.4 30120 30157
```

Each command creates a **separate Docker container**.

------------------------------------------------------------------------

# Managing containers

View running proxies:

``` bash
docker ps
```

View logs:

``` bash
docker logs -f fivem_proxy_PORT
```

Example:

``` bash
docker logs -f fivem_proxy_30155
```

Stop a proxy:

``` bash
docker stop fivem_proxy_30155
```

Remove a proxy:

``` bash
docker rm -f fivem_proxy_30155
```

------------------------------------------------------------------------

# Notes

Make sure:

-   your firewall allows the proxy port
-   your FiveM server accepts external connections

Example firewall rule:

``` bash
ufw allow 30155
```

------------------------------------------------------------------------

# License

MIT License

------------------------------------------------------------------------

# Author

GitHub: https://github.com/stdhexx

------------------------------------------------------------------------

If you find this project useful, feel free to star the repository or
improve it.
