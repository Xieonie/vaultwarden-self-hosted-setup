# Vaultwarden Password Manager: Self-Hosted Setup 🔑

This repository describes the installation, configuration, and maintenance of a self-hosted password manager using [Vaultwarden](https://github.com/dani-garcia/vaultwarden), an unofficial, lightweight Bitwarden server API written in Rust. The focus is on security, automated backups, and a user-friendly setup.

**Important Note:** The configurations and instructions provided here are for guidance. Always adapt them to your specific environment and security requirements. Ensure you understand the implications of each configuration step.

## 🎯 Goals

* Provide a secure, self-controlled password manager.
* Implement end-to-end encryption for passwords.
* Ensure data integrity and availability through automatic backups.
* Enhance security with SSL encryption and Two-Factor Authentication (2FA).
* Define and implement clear security policies for usage.

## 🛠️ Technologies Used

* [Docker](https://www.docker.com/) & [Docker Compose](https://docs.docker.com/compose/)
* [Vaultwarden (Bitwarden Server RS)](https://github.com/dani-garcia/vaultwarden)
* Reverse Proxy (e.g., [Nginx Proxy Manager](https://nginxproxymanager.com/), Traefik, Caddy) for SSL termination.
* [Let's Encrypt](https://letsencrypt.org/) (for SSL certificates).
* Cronjobs or a backup tool (e.g., Borg, Restic, or simple `tar` scripts) for automated backups.
* TOTP-based Two-Factor Authentication (2FA).

## ✨ Key Features/Highlights

* **Secure Password Management:** Self-hosted solution for full data control.
* **Docker Deployment:** Easy and isolated installation using Docker and Docker Compose.
* **SSL Encryption:** Secure connection to the Vaultwarden server via HTTPS (e.g., through Nginx Proxy Manager and Let's Encrypt).
* **Automatic Backups:** Regular backup of the Vaultwarden database and configuration files.
* **Two-Factor Authentication (2FA):** Implementation of 2FA (e.g., TOTP) for enhanced account security.
* **Admin Interface:** Access to the Vaultwarden admin panel for managing users and server settings.
* **Security Policies:** Defined policies for using the password manager (e.g., master password complexity, mandatory 2FA).

## 🚀 Getting Started / Configuration

The setup primarily uses Docker Compose.

1.  **Prerequisites:**
    * Docker and Docker Compose installed.
    * A domain name pointed to your server's IP address.
    * A reverse proxy (e.g., Nginx Proxy Manager, Traefik) is highly recommended to handle SSL.
2.  **Configuration:**
    * Clone this repository or use the provided files.
    * Copy `.env.example` to `.env` and customize the variables (e.g., `DATA_FOLDER`, `ADMIN_TOKEN`, `DOMAIN`).
    * Review and adjust the `docker-compose.yml` file if needed. The `DATA_FOLDER` environment variable will be used to create a subfolder for Vaultwarden data.
    * Ensure the data folder path specified by `DATA_FOLDER` (e.g., `./vw-data`) will be created with appropriate permissions for the Docker user.
3.  **Reverse Proxy Setup:**
    * Configure your reverse proxy to forward traffic to the Vaultwarden container (port 80 inside the Docker network by default, or `ROCKET_PORT` if changed) and manage SSL certificates (e.g., from Let's Encrypt). See [`config-examples/nginx-proxy-config.md`](./config-examples/nginx-proxy-config.md) for guidance.
    * Ensure your domain is correctly set in the `.env` file (`DOMAIN=https://your.domain.com`).
4.  **Start Vaultwarden:**
    ```bash
    docker-compose up -d
    ```
5.  **Initial Account & Admin Access:**
    * Navigate to your Vaultwarden URL (e.g., `https://your.domain.com`) in a browser and create an account.
    * To access the admin panel, go to `https://your.domain.com/admin` and use the `ADMIN_TOKEN` you set in the `.env` file.
6.  **Backup Setup:**
    * The `docker-compose.yml` includes a volume for data (`vw-data:/data/`). You need to back up this volume.
    * A simple backup approach is to regularly archive the contents of the `./vw-data` directory (or the path you configured for `DATA_FOLDER`).
    * See `docs/SECURITY_POLICIES.md` for more on backup strategies.
7.  **Enable 2FA:**
    * Encourage all users to enable 2FA for their accounts. See [`docs/2fa-setup-guide.md`](./docs/2fa-setup-guide.md).

## 📄 Important Files and Folders

* `docker-compose.yml`: Configuration file for the Docker container.
* `.env.example` / `.env`: Environment variables for Docker Compose.
* `vw-data/` (or your `DATA_FOLDER`): This directory will be created on the host to store Vaultwarden's persistent data (SQLite database, attachments, etc.). **This folder is critical and must be backed up.**
* `config-examples/`: Contains example configurations for reverse proxies.
* `docs/`: Contains additional documentation like security policies and 2FA setup.

## 🔮 Potential Improvements/Future Plans

* Integrate with Fail2Ban to protect against brute-force attacks on the admin panel or login attempts.
* Implement notifications for failed backups.
* Regularly check and update the Vaultwarden version and other components.
* Explore more advanced backup solutions like BorgBackup with off-site storage.

## ⚠️ Security Considerations

* **Admin Token:** Keep your `ADMIN_TOKEN` secure.
* **HTTPS:** Always use HTTPS.
* **Backups:** Regularly back up your data and test restore procedures. Store backups securely, preferably off-site and encrypted.
* **Updates:** Keep Vaultwarden, your Docker environment, and the host system updated.
* **Firewall:** Use a firewall to restrict access to necessary ports.
