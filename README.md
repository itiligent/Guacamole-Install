<div align="center">

![GitHub release version](https://img.shields.io/github/v/release/itiligent/Easy-Guacamole-Installer?style=flat-square&color=orange&labelColor=black)
![GitHub stars](https://img.shields.io/github/stars/itiligent/Easy-Guacamole-Installer?style=flat-square&color=yellow&labelColor=black)
![GitHub forks](https://img.shields.io/github/forks/itiligent/Easy-Guacamole-Installer?style=flat-square&color=blue&labelColor=black)

# 🥑 Easy Guacamole Installer



</div>

<p align="center">
<a href="https://www.paypal.com/donate/?business=PSZ878JBJDMB8&amount=10&no_recurring=0&item_name=Thankyou+for+your+support+in+maintaining+this+project&currency_code=AUD">
  <img src="https://github.com/itiligent/Guacamole-Install/raw/main/.github/ISSUE_TEMPLATE/paypal-donate-button.png" width="125" />
</a>
</p>

## Introduction

This install script automatically sets up a Guacamole jump-host with optional for TLS reverse proxy (self-signed or Let's Encrypt), Active Directory integration, multi-factor authentication, Quick Connect & History Recording Storage UI enhancements. Other options also include a custom UI dark themed template, auto database backups, email alerts and internal hardening options including fail2ban for defence against brute force attacks. There is also facility for tiered enterprise deployment similar to [Amazon's Guacamole Bastion Cluster](http://netcubed-ami.s3-website-us-east-1.amazonaws.com/guaws/v2.3.1/cluster/).

## Automatic Installation

🚀 Move to you your home directory, paste the below link, then follow the prompts (**do NOT run as root, the script will prompt for sudo**): 

```shell
wget https://raw.githubusercontent.com/itiligent/Guacamole-Install/main/1-setup.sh && chmod +x 1-setup.sh && ./1-setup.sh
```
---

> [!TIP]
> Some distros from time to time may encounter FreeRDP version conflicts.  
> See [Issue #92 for the workaround](https://github.com/itiligent/Easy-Guacamole-Installer/issues/92)


## Prerequisites

📋 **You will need:**
  - **Supported OS: Debian 12 or 13** | **Ubuntu LTS 22.x or 24.x** | **Raspbian**
  - **1 CPU core + 2GB RAM for every 25 users (plus minimum RAM & disk space for your selected OS).**
- **Open TCP ports: 22, 80, and 443 (no other services using 80, 8080 & 443)**
- **For both TLS reverse proxy options you will need a PRIVATE DNS record for the internal proxy site, and an additional PUBLIC DNS record for the Let's Encrypt option.**
- **Sudo & wget packages installed**
- **The user running `1-setup.sh` must have sudo permissions.**

---

## Setup Script Menu

🔧 **The main `1-setup.sh` script guides the installation with the following steps:**

1. Setup the system hostname & local DNS name (Local DNS must be consistent for TLS proxy).
2. Select either a local MySQL install or use a pre-existing local or remote MySQL instance.
3. Pick an authentication extension: DUO, TOTP, LDAP/Active Directory, or none.
4. Select optional console features: Quick Connect & History Recorded Storage UI integrations.
5. Select the Guacamole front end: Nginx reverse proxy (HTTP or HTTPS) or use the native Guacamole interface on port 8080.
   - If you opt to install Nginx with self-signed TLS:
     - New server & client browser certificates are saved to `$HOME/guac-setup/tls-certs/[date-time]/`.
     - Optionally follow on-screen instructions for client certificate import to avoid https browser warnings.

---

## Customising The Build

⚙️ **To customise the many available script options:**

- Exit `1-setup.sh` at the first prompt.
- All configurable script options are shown under **Silent setup options** at the start of `1-setup.sh`. 
- Certain combinations of the **Silent setup options** will allow for a fully unattended install supporting mass deployment or highly customised docker builds.
- Re-run your edited script locally after making changes (do not re-run the automatic install web link - see below). 

**Other custom install notes:**
- **Caution:** Re-running the auto-installer link re-downloads the suite of scripts which will overwrite any custom script edits. You must run 1-setup.sh LOCALLY after editing. If any child scripts are edited, their corresponding download links in 1-setup.sh script must also be commented out.
- Upgrade scripts are **automatically customised with your specifc installation settings** for consistent future updates.
- Nginx reverse proxy is configured to default to at least TLS 1.2. For ancient systems, see commented sections of the `/etc/nginx/nginx.conf` file after install.
- A daily MySQL backup job is automatically configured under the script owner's crontab.
- The Quick Connect option brings some extra security implications, be aware of potential risks in your environment.

**Post-install manual hardening options:**

- `add-fail2ban.sh`: Adds a lockdown policy for Guacamole to guard against brute force password attacks.
- `add-tls-guac-daemon.sh`: Wraps internal traffic between the guac server & guac application in TLS.
- `add-auth-ldap.sh`: Template script for simplified Active Directory integration.
- `add-smtp-relay-o365.sh`: Template script for email alert integration with MSO65 (BYO app password).

---

## Branding The Guacamole UI Theme

🎨 **Follow the theme and branding instructions** [here](https://github.com/itiligent/Guacamole-Install/tree/main/guac-custom-theme-builder). To revert to the default theme, simply delete the branding.jar file from `/etc/guacamole/extensions`, clear your browser cache and restart.

---

## Managing Self-Signed TLS Certs With Nginx

**To renew self-signed certificates or change the reverse proxy local DNS name/IP address:** 
- Re-run `4a-install-tls-self-signed-nginx.sh` to create a new Nginx certificate (new browser client certificates will also be created for re-import). Always clear your browser cache after changing certificates.

---

## Active Directory Integration

🔑 See [here](https://github.com/itiligent/Guacamole-Install/blob/main/ACTIVE-DIRECTORY-HOW-TO.md).

---

## SS0 Extensions (Radius, Base, CAS, OpenID, SAML, Dist)
🔑 See [here](https://github.com/itiligent/Guacamole-Installer/blob/main/SSO-EXTENSIONS-HOW-TO.md)

---

## Upgrading Guacamole

🌐 To upgrade Guacamole, edit `upgrade-guacamole.sh` to reflect the latest versions of Guacamole & MySQL connector/J before running. This script will automatically update TOTP, DUO, LDAP, Quick Connect, and History Recorded Storage extensions if present.

---

## High Availability Deployment

- 👔 **For a separate DATABASE layer:** Use the `install-mysql-backend-only.sh` [here](https://github.com/itiligent/Guacamole-Install/tree/main/guac-enterprise-build) to install a standalone instance of the Guacamole MySQL database.
- 👔 **For a separate APPLICATION layer:** Run `1-setup.sh` and point new installations to your separate database instance. Just say **no** to the "Install MySQL locally" option and any other local reverse proxy install options.
- 👔 **For a separate FRONT END layer:** Use the included Nginx installer scripts to build out a separate Nginx front end layer, and then apply your preferred TLS load balancing technique. Alternatively, AWS/Azure/GCP load balancers or [HA Proxy](https://www.haproxy.org/) may provide superior session persistence & affinity compared to [Open Source Nginx](https://www.nginx.com/products/nginx/compare-models/).

---

### Script Download Manifest

📦 **The autorun link downloads these files into `$HOME/guac-setup`:**

- `1-setup.sh`: The parent setup script.
- `2-install-guacamole.sh`: Guacamole source build & installer script.
- `3-install-nginx.sh`: Nginx installation script.
- `4a-install-tls-self-signed-nginx.sh`: Install/refresh self-signed TLS certificates script.
- `4b-install-tls-letsencrypt-nginx.sh`: Let's Encrypt for Nginx installer script.
- `add-auth-duo.sh`: Duo MFA extension install script.
- `add-auth-ldap.sh`: Active Directory extension installer template script.
- `add-auth-totp.sh`: TOTP MFA extension installer script.
- `add-xtra-quickconnect.sh`: Quick Connect console extension installer script.
- `add-xtra-histrecstore.sh`: History Recorded Storage extension installer script.
- `add-smtp-relay-o365.sh`: Script for O365 SMTP auth relay setup (BYO app password).
- `add-tls-guac-daemon.sh`: Wrap internal traffic between guacd server & Guacamole web app in TLS.
- `add-fail2ban.sh`: Fail2ban (& Guacamole protection policy) installer script.
- `backup-guacamole.sh`: MySQL backup setup script.
- `upgrade-guacamole.sh`: Guacamole application, extension, and MySQL connector upgrade script.
- `branding.jar`: Base template for customizing Guacamole's UI theme.

😄🥑












