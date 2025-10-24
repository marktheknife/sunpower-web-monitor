# Dashboard Installation Instructions for *New* PVS Firmware

These instructions apply to **PVS firmware version 2025.09 Build 61845 and newer**.

> **IMPORTANT:**
> If your firmware version is **2025.06 Build 61839 or earlier**, return to the [main project page](../README.md#dashboard-installation) and follow the *Old Firmware* installation instructions.

---

## PVS Gateway

<img style="padding-right: 15px; padding-bottom: 5px;" align="right" src="../images/PVS5_1.png" width="150">
<img style="padding-right: 15px; padding-bottom: 5px;" align="right" src="../images/PVS6_1.png" width="175">

The PVS is a data logger and gateway device used for solar system monitoring, metering, and control.

The Dashboard has been tested with the **PVS6** gateway, which is referred to as **PVS** (*Power Visualization System*) throughout this document.

> ⚠️ **Attention PVS5 Users:**
> As of Oct-2025, SunPower has not yet released updated firmware for the older PVS5 gateway.
> They have announced plans to release new firmware that includes the same authentication feature found in the *New* PVS6 firmware.
> Use the instructions below **only after** your PVS5 has been updated.

To retrieve data, a direct Ethernet connection to the gateway unit is **NOT** required.
The latest PVS firmware allows access through your local network's WiFi using the LAN IP address assigned by your router.

---

## Local LAN Test

Before beginning installation, perform a quick LAN connectivity test:

1. Determine the IP address of your PVS gateway.
   - You can find it in your router’s **DHCP Client List**.

2. Open a web browser and visit:
   ```
   http://pvs_port_ip/vars?name=/sys/info/sw_rev
   ```
   *Replace `pvs_port_ip` with your gateway’s IP address. Only HTTP is supported. HTTPS will not work.*

3. Confirm that the response is a valid JSON output beginning with `"values"`.
   - If an HTTP error occurs, power cycle the gateway, wait ten minutes, then try again.
   - If the issue persists, review the [Checking PVS Firmware Version](../README.md#checking-pvs-firmware-version) section on the main project page.

---

## Installation Summary (Overview)

The Dashboard installation requires copying **three files** from the project’s `html` folder.
You can install them on an existing Linux system or on a low-cost **Raspberry Pi** (any model, including the Pi Zero).

A clean OS installation is recommended, otherwise ensure it has the latest updates. The installation may fail on older OS distributions.

### Installation workflow

1. Flash the Raspberry Pi OS onto an micro SD card (if needed).
2. Update system packages and install Python 3.
3. Create a project folder on the host system or Pi.
4. Create a Python virtual environment using `venv`.
5. Install Flask (used for the authentication proxy and static web server).
6. Copy the three project files.
7. Configure the OS to auto-start the proxy via a **systemd** service.
8. Enable and start the proxy service.

---

## Dashboard Installation

> 📝 **Note:** These instructions assume your host's username is **pi**. File edits will be required if another username is used, per the instructions below.

If you are installing on an existing host system, skip to the [Update system packages](#update-system-packages) section.

If using a new Raspberry Pi (RPi):
- Flash the RPi OS to a high-endurance 16 GB (or larger) micro SD card.
- **RPi OS Lite (32-bit) Debian Trixie** is recommended for *Pi Zero* installations.

> 💡 **Tip:**
> [The Raspberry Pi Imager](https://www.raspberrypi.com/news/raspberry-pi-imager-imaging-utility/) is highly recommended for creating the SD card.
> It allows you to set the username, Wi-Fi SSID, and enable SSH during setup.
> There are many online tutorials that explain how to use the Imager.

Insert the micro SD card and boot the RPi. Log in from the RPi desktop or by remote SSH. User **pi** is typical, but your system may have a different username.

The sections below provide all the steps for setting up the Dashboard software. Please execute the commands and actions in the order listed.

---

### Update system packages

Enter these commands:

```bash
sudo apt update
sudo apt upgrade -y
```

---

### Install Python3, venv, and pip

```bash
sudo apt install -y python3 python3-venv python3-dev build-essential
```

---

### Create the project folder

```bash
mkdir -p ~/solar_dashboard
cd ~/solar_dashboard
```

---

### Create and activate the virtual environment

```bash
python3 -m venv venv
source venv/bin/activate
```

---

### Upgrade pip and install dependencies (Flask & Requests)

```bash
pip install --upgrade pip
pip install flask requests
```
When finished, the venv Python binary will be located at:
`~/solar_dashboard/venv/bin/python`

---

### Move to the `/home/solar_dashboard` folder

```bash
cd ~/solar_dashboard
```

---

### Copy `proxy.py` to `~/solar_dashboard/`

```bash
wget -O proxy.py https://raw.githubusercontent.com/thomastech/SunPower-Web-Monitor/refs/heads/main/html/proxy.py
```

---

### Make `proxy.py` executable

```bash
chmod +x proxy.py
```

---

### Copy `solar_dashboard.html` to `~/solar_dashboard/`

```bash
wget -O solar_dashboard.html https://raw.githubusercontent.com/thomastech/SunPower-Web-Monitor/refs/heads/main/html/solar_dashboard.html
```

---

### Move to the `/etc/systemd/system` folder

```bash
cd /etc/systemd/system
```
---

### Copy `solar-proxy.service` to `/etc/systemd/system/` as superuser (root)

```bash
sudo wget -O solar-proxy.service https://raw.githubusercontent.com/thomastech/SunPower-Web-Monitor/refs/heads/main/html/solar-proxy.service
```
> ⚠️ **Attention:** If your username is not **pi**, edit the solar-proxy.service file and update the `User=pi` line and ALL related paths (five places total) with the correct name.

---

### Enable and start the proxy

```bash
sudo systemctl daemon-reload
sudo systemctl enable solar-proxy
sudo systemctl start solar-proxy
```

---

### Check service status for errors

```bash
sudo systemctl status solar-proxy
```

If there are no errors, the installation is complete.
Continue with the following steps to view the Dashboard.

---

## Dashboard: URL Parameters

The Dashboard’s URL requires **four parameters**:

| Parameter | Description |
|------------|-------------|
| `<HOST_IP>` | LAN IP address of the host (Raspberry Pi). The port is always 5000. |
| `<PVS_IP>` | LAN IP address of the PVS gateway |
| `<USER>` | Always use ```ssm_owner``` |
| `<PASSWORD>` | Last five characters of the PVS serial number |

---

## Dashboard: Basic Operation

> ⚠️ **Important:** Do **not** include the angle brackets `<` and `>` in the URL parameters!

From a browser (Firefox, Chrome, Edge) on the local network, enter the following URL with your parameters:

```
http://<HOST_IP>:5000/solar_dashboard.html?ip=<PVS_IP>&user=<USER>&pass=<PASSWORD>
```

**Example:**
```
http://192.168.1.197:5000/solar_dashboard.html?ip=192.168.1.199&user=ssm_owner&pass=W0123
```

Here’s a screenshot of the Dashboard:

<a href="../images/dashboard1b.png" target="_blank" style="text-align: center; display: block;">
  <img src="../images/dashboard1b.png" width="475" style="padding: 5px 15px 0 15px; display: block; margin: 0 auto;">
  <div style="font-size: 14px; color: #fff; text-align: center;"><strong>Click for Larger View</strong></div>
</a>

---

## Dashboard: Check Auto-Start

Finally, safely reboot the host system using this guide:
[How to safely reboot your Raspberry Pi](https://raspi.tv/2012/how-to-safely-shutdown-or-reboot-your-raspberry-pi).

After rebooting, wait five minutes and relaunch the Dashboard in your browser.
This confirms that the proxy was automatically started by the **systemd** service.

---

## Dashboard: Data

For details on the Dashboard’s data reporting, visit the [main page](../README.md#dashboard-data-reporting).

---

## Frequently Asked Questions (FAQ)

> ⚠️ **Note:** These FAQs apply only to *New Firmware* installations.

---

### FAQ 1

**Q.** I cannot connect to the Dashboard. I’ve tried everything. Help!

**A.** Do **not** use HTTPS in your URL, It must be **HTTP**. Also confirm the host computer’s IP address.

---

### FAQ 2

**Q.** The Dashboard was working before, but after a power failure it no longer works. What happened?

**A.** The Raspberry Pi and/or the PVS gateway likely received new IP addresses upon reboot.
Avoid this by assigning **static IPs** to both devices.

    In rare cases, a power failure can corrupt the Raspberry Pi’s SD card. A backup is recommended.

---

### FAQ 3

**Q.** The Dashboard was working fine, but now I get a [HTTP 500 error](../images/http_500_error1.jpg). What should I do?

**A.** Power cycle (cold reboot) the PVS gateway. Wait about ten minutes and try the Dashboard again.

---

© 2025 — SunPower Web Monitor Project
