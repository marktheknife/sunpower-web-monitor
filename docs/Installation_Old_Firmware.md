# Dashboard Installation Instructions for *Old* PVS Firmware

These instructions are for **PVS firmware versions 2025.06 Build 61839 and earlier** (pre-authentication firmware).

> **IMPORTANT:**
> If your firmware version is **2025.09 Build 61845 or newer**, return to the [main project page](../README.md#dashboard-installation) and follow the *New Firmware* installation instructions.

---

## PVS Gateway

<img style="padding-right: 15px; padding-bottom: 5px;" align="right" src="../images/PVS5_1.png" width="150">
<img style="padding-right: 15px; padding-bottom: 5px;" align="right" src="../images/PVS6_1.png" width="175">

The PVS is a data logger and gateway device used for solar system monitoring, metering, and control.

The Dashboard has been tested with the **PVS6** gateway and is expected to be compatible with the older **PVS5** model as well.
For simplicity, both models are referred to as **PVS** (*Power Visualization System*) in this document.

To retrieve data, a **direct Ethernet connection** is required.
It is possible to use the gateway’s Wi-Fi access point, but a wired connection is simpler and more reliable.

Inside the PVS are two Ethernet ports (**LAN1** and **LAN2**) and other RJ45 jacks that are **not Ethernet**.
Be cautious and ensure you connect to the correct port!

---

## LAN Port Descriptions

The SunPower gateway’s two Ethernet (LAN) ports serve different purposes, as follows:

### LAN1: Dashboard Data

- **LAN1** is the Ethernet port that enables Dashboard functionality.
- It was originally intended for installer use. SunPower has since disabled the built-in *PVS Management App*, a web-based commissioning interface that allowed installers to provision systems.
  Today, those tasks require the *SunPower Pro Connect* smartphone app for authorized installers.
- Thankfully, the **LAN1 API** remains functional.
- The Dashboard webpage queries this API using IP address `172.27.153.1` on LAN1. This is a private network address, isolated from the customer’s home network. Hence the need for a direct Ethernet connection.

> 🔗 **Notes:** <br>
> The URL `www.sunpowerconsole.com` has been disabled by SunPower. It was an alias for the PVS’s internal nameserver at `172.27.153.1`.
> Your browser must now use the IP address directly: ```http://172.27.153.1```
> Only HTTP is supported; HTTPS will not work.

### LAN2: Customer Cloud Data

- **LAN2** is the Ethernet port that sends data to the SunPower cloud via the customer’s router. Most systems use Wi-Fi for this, but Ethernet is also supported.
- This connection enables access to production and consumption data in the official SunPower app.
- The Dashboard uses **LAN1**, so **LAN2** is not needed for Dashboard operation.

---

## Ethernet Cable Connection

Refer to the official **PVS Residential Installation Quick Start Guide (QSG)** for connection details:

- **PVS6 QSG:** [View PDF](../resources/PVS6_Installation1.pdf)
- **PVS5 QSG:** [View PDF](../resources/PVS5_Installation1.pdf)

> 🔗 **Note:** The latest PVS6 hardware version requires a USB-to-Ethernet adapter (dongle). Details are in the [PVS6 QSG](../resources/PVS6_Installation1.pdf).

---

## Ethernet Test

Now that the PVS is directly connected to your PC, perform an Ethernet connectivity test:

1. Turn off the PC’s Wi-Fi and confirm it is fully disabled. Otherwise the LAN1 test and the Dashboard will fail.
2. Open your browser and go to: [http://172.27.153.1](http://172.27.153.1)
3. Confirm that a **403 Forbidden** error appears.
   This means the IP is valid, but access is restricted. Which is a *good sign*!
   > ⚠️ **If a Connection Timeout occurs**, check your Ethernet configuration and confirm Wi-Fi is off.
4. Next, visit the following URL and confirm it returns a *devices* report in JSON format:
   [http://172.27.153.1:8080/cgi-bin/dl_cgi?Command=DeviceList](http://172.27.153.1:8080/cgi-bin/dl_cgi?Command=DeviceList)
5. If the report appears, the connection is working. You’re ready to launch the Dashboard!
   > 🔗 To use the Dashboard from your Wi-Fi network, see the [FAQ](#frequently-asked-questions-faq) section below.

---

## Dashboard File Installation

The Dashboard is a single HTML file.
Download [solar_dashboard.html](https://github.com/thomastech/SunPower-Web-Monitor/releases/download/html/solar_dashboard.html) and save it to your desktop or any preferred folder (NAS drives also work).

---

## Dashboard: Basic Operation

> 🔗 **Note:** These instructions are written for **Windows users**. Other operating systems may differ slightly.

Ensure Wi-Fi is **turned off** on your Windows PC.
Launch the Dashboard by double-clicking the HTML file.
For future convenience, add it to your browser’s favorites or bookmarks.

Here’s a screenshot:

<a href="../images/dashboard1b.png" target="_blank" style="text-align: center; display: block;">
  <img src="../images/dashboard1b.png" width="600" style="padding: 5px 15px 0 15px; display: block; margin: 0 auto;">
  <div style="font-size: 14px; color: #fff; text-align: center;"><strong>Click for Larger View</strong></div>
</a>

---

## Dashboard: Data

For a detailed explanation of the Dashboard’s data reporting, see the [main page](../README.md#dashboard-data-reporting).

---

## Frequently Asked Questions (FAQ)

> ⚠️ **Note:** This FAQ applies only to *Old Firmware* installations.

---

### FAQ 1

<img style="padding-right: 15px; padding-bottom: 5px;" align="right" src="../images/dongle1.png" width="250">

**Q.** I cannot connect to the PVS. I’ve tried everything. Help!

**A.** 1. Double-check that your PC’s Wi-Fi is turned **off**.
2. Do **not** use HTTPS in your URL. It must be **HTTP**.
3. If using a USB-to-Ethernet dongle on your PVS6, confirm it is a supported model. The [PVS6 Residential Installation Quick Start Guide](../resources/PVS6_Installation1.pdf) lists approved adapters (see *Technical Notification*).
4. In rare cases, the PVS may issue an IP address other than `172.27.153.1`. For example, `172.27.152.1`. Check your PC’s network settings to confirm. If it differs, see **FAQ 2** below.

<br clear="all">

---

### FAQ 2

**Q.** My PVS gateway didn’t assign IP address `172.27.153.1`. It’s using `172.27.152.1`. What should I do?

**A.** You can override the Dashboard’s default gateway address by adding the IP as a parameter, like this:
```
solar_dashboard.html?ip=172.27.152.1
```

---

### FAQ 3

**Q.** Can I connect the PVS to my local network so I can use the Dashboard from multiple devices (like my smartphone)?

**A.** Yes! This is a great way to use the Dashboard.
For details, review this document: [Local Network Setup](./local_network.md).

---

### FAQ 4

**Q.** The Dashboard was working fine, but now I get an HTTP 500 error. What should I check?

**A.** The typical solution is to **power cycle** (cold reboot) the PVS gateway, wait about ten minutes, then try the Dashboard again.

---

© 2025 — SunPower Web Monitor Project
