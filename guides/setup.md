# Coder Installation Guide

This guide outlines the steps to install Coder on your machine, including the setup of Docker Engine, Docker Compose, domain configuration on CloudFlare, and securing your installation using the certificates and certbot setup provided in the Coder GitHub repository.

## Prerequisites

Before starting, ensure you have:

- A machine with at least 4GB of RAM and 2 CPUs.
- Administrative or root access on the machine.
- An account on [CloudFlare](https://cloudflare.com/) for domain management.

## 1. Install Docker Engine

Coder runs inside a Docker container, so the first step is to install Docker Engine on your machine.

- For Linux users, follow the [official Docker installation guide for Linux](https://docs.docker.com/engine/install/).
- Windows and Mac users can install Docker Desktop, which includes Docker Engine and Docker Compose. [Download Docker Desktop](https://docs.docker.com/desktop/).

## 2. Install Coder
## Standalone Installation of Coder on Linux

The easiest way to install Coder on Linux is to use our install script.

```bash
curl -fsSL https://coder.com/install.sh | sh
```

You can preview what occurs during the install process:

```bash
curl -fsSL https://coder.com/install.sh | sh -s -- --dry-run
```

You can modify the installation process by including flags. Run the help command for reference:

```bash
curl -fsSL https://coder.com/install.sh | sh -s -- --help
```

Run Coder as a system service.

(Optional) Set up an access URL:

```bash
sudo vim /etc/coder.d/coder.env
```

To systemd to start Coder now and on reboot:

```bash
sudo systemctl enable --now coder
```

View the logs to see Coder URL and ensure a successful start:

```bash
journalctl -u coder.service -b
```

## Install Coder using Docker Compose

After installing Docker and Docker Compose, the next step is to deploy Coder.

1. Create a directory for Coder and navigate into it:

   ```bash
   mkdir coder
   cd coder
   ```

2. Download the `docker-compose.yaml` file from the official Coder GitHub repository:

   ```bash
   wget https://raw.githubusercontent.com/coder/coder/main/docker-compose.yaml
   ```

3. Update the `group_add:` value in `docker-compose.yaml` with the gid of the docker group. You can get the docker group gid by running:

   ```bash
   getent group docker | cut -d: -f3
   ```

4. Start Coder with Docker Compose:

   ```bash
   docker-compose up -d
   ```

   This command will pull the necessary Docker images and start Coder in a detached mode.

5. Visit the web UI via the configured URL.

6. Follow the on-screen instructions to log in and create your first template and workspace.

Refer to the [official Coder Docker installation documentation](https://coder.com/docs/v2/latest/install/docker) for more details and customization options.

## 3. Domain Setup on CloudFlare

To access Coder through a domain and enable HTTPS, configure your domain on CloudFlare.

1. Log in to your CloudFlare account and select your domain.
2. Go to the DNS section and add an A record pointing to your server's IP address.
3. Create an [API key](https://dash.cloudflare.com/profile/api-tokens) for DNS Zone Managing by navigating to `My Profile > API Tokens` and create a token with the necessary permissions.

## 4. Setup Certificates and Certbot

For securing your Coder installation with HTTPS, follow the guide provided in the Coder GitHub repository:

- Follow the instructions in the README.md file located on [coder repo](https://github.com/coder/coder/tree/main/examples/web-server/nginx) to set up Nginx with SSL certificates for your Coder installation.

This step involves configuring Nginx as a reverse proxy for Coder and securing it with SSL certificates obtained via Certbot or your preferred method.