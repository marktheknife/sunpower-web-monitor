# SunPower Web Monitor

<img style="padding-left: 15px; padding-bottom: 5px;" align="right" src="images/dashboard2.png" width="165">

## PVS Solar Energy Dashboard

The **PVS Solar Energy Dashboard** is a web-based viewer for monitoring a SunPower solar system that uses a **PVS5** or **PVS6** gateway. It displays the system's power production, consumption, and mains voltages. It shows each solar panel's DC voltage, DC current, and microinverter AC voltages. In addition, the Dashboard reports the model and serial numbers of all provisioned devices (gateways, panels, power meters).

> ⚠️ **Note:** Battery storage data is **NOT** supported. Unfortunately the Dashboard's creator does not have a *SunVault* battery to assist with feature development.

> 🔗 From this point forward, the PVS Solar Energy Dashboard will be referred to as the **Dashboard**.

---

## Gateway Firmware Compatibility

Beginning in **September 2025**, SunPower launched firmware version **2025.9 build 61845** for the PVS6 gateway. It added authentication (username and password) to the `dl_cgi` API used by this project. This release impacted the Dashboard.

### The good news
PVS gateways using SunPower's latest firmware no longer require a direct Ethernet connection to the LAN1 installer port.
The Dashboard can now use the LAN IP address assigned by your router, including via **Wi-Fi**.

### The bad news
The new firmware requires a **proxy** to handle session authentication. A custom **Python-based proxy** is used by this project.
It can be installed on an existing Linux system or on a low-cost Raspberry Pi (RPi). The proxy uses minimal host resources.

> 💡 **Tip:** Throughout this documentation the terms *PVS gateway*, *gateway*, and *PVS* are used interchangeably.

---

## SunPower Bankruptcy

The **2024 SunPower bankruptcy** created significant uncertainty for system owners. Warranty support for purchased systems was terminated, leaving repair costs in the hands of system owners. This situation prompted the creation of the Dashboard project, which provides system information not available in the standard SunPower (SunStrong) app.

One of the side affects of the Bankruptcy is the discontinuation of SunPower manufactured devices. One such victim is the PVS Gateway product line. This situation provides another reason to self-monitor; The Dashboard is an attractive alternative in case the SunPower App's *free* PVS monitoring becomes subscription based in the future.

---

## Dashboard Security

The Dashboard does **not** use the cloud to access your SunPower system. It does not require access to the internet. All communication is performed locally on your network.

---

## Dashboard Limitations

The Dashboard displays useful real-time data and helps with troubleshooting. It can assist detecting failing components without climbing on the roof. And in other ways too; Such as getting the serial number of a faulty microinverter, an important step to obtaining a warranty replacement directly from Enphase.

However, the Dashboard **cannot** be used to commission a SunPower system or to provision new devices. As of 2024, those tasks require the proprietary PVS Management App and SunPower authorization.

> 📝 **Note:** If you plan to integrate your SunPower system with a home automation platform such as *Home Assistant* (HA), this project is **not required** for the integration. The Dashboard can still be useful while preparing your HA setup.

---

## PVS Gateway

<img style="padding-right: 15px; padding-bottom: 5px;" align="right" src="images/PVS5_1.png" width="150">
<img style="padding-right: 15px; padding-bottom: 5px;" align="right" src="images/PVS6_1.png" width="175">

SunPower's PVS (*Photovoltaic Supervisor*) is a data logger and gateway device used for solar system monitoring, metering, and control.

The Dashboard has been tested with the **PVS6** gateway and is expected to be compatible with the older **PVS5** model as well. For simplicity, both models are referred to as **PVS** in this document.

---

## PVS Version Information

For clarity in these docs:

- Firmware **2025.9 build 61845 and newer** → referred to as the **NEW** PVS firmware
- Firmware **2025.06 build 61839 and older** → referred to as the **OLD** PVS firmware

It is important to determine your firmware version. Because Dashboard installation methods for them are different.

### Checking your PVS firmware version

If your gateway is a **PVS6** that has remained connected to the official SunPower (SunStrong) app, it is likely running the **NEW** firmware.
Beginning late **October 2025**, Sunpower began updating **PVS5** gateways to the **New** firmware. This rollout should have hit most gateways by the time your read this.

From your local network open the following URL in a browser (replace `pvs_port_ip` with your gateway's local IP address):

```http://pvs_port_ip/vars?name=/sys/info/sw_rev```


- A valid JSON reply beginning with `"values"` indicates the **NEW** firmware.
- An **HTTP 403** error commonly indicates the **OLD** firmware.

If you cannot determine the firmware version with certainty, try installing the Dashboard using the **OLD** firmware method first. No extra hardware is required to try it. If it doesn't work then try the **NEW** firmware method (which requires additional hardware).

---

## Dashboard Installation

The Dashboard is managed by an HTML file with embedded CSS and JavaScript. Installations for the **NEW** firmware require two additional files that handle the authentication proxy.

The **OLD** PVS firmware does not require any additional hardware. However, the **NEW** PVS firmware requires a host computer running Linux or Raspberry Pi OS.

Before installing, confirm your PVS firmware version using the section above.

- **OLD firmware users:** [Installation Instructions](./docs/Installation_Old_Firmware.md)
- **NEW firmware users:** [Installation Instructions](./docs/Installation_New_Firmware.md)

---

## Dashboard Data Reporting

**Basic behavior**

- Data automatically refreshes every **20 seconds**.
- You can manually refresh by clicking the **yellow sun icon** at the top or the **[Refresh Now]** button at the bottom.
- A timestamp above the refresh button shows the local time of the last update.
- The page uses a **responsive** layout and resizes for desktop and mobile devices.

Here's a screenshot:

<a href="images/dashboard1d.png" target="_blank" style="text-align: center; display: block;" alt="Click for larger image" align="center">
  <img src="images/dashboard1d.png" width="500" style="padding: 5px 15px 0 15px; display: block; margin: 0 auto;">
  <div style="font-size: 14px; color: #fff; text-align: center;"><strong>Click for Larger View</strong></div>
</a>

---

### Power Performance

The *Power Performance* section provides:

- **Lifetime Solar:** Total accumulated production (kWh) since system installation.
- **Solar Output:** Instantaneous production (kW). The circle icon is **yellow** when panels are producing and **blue** when sunlight is insufficient.
- **Consumption:** Instantaneous consumption (kW). Requires SunPower's optional consumption *current transformers* (CT).
- **Net Power:** Production minus consumption (kW).
  - A **red** circle indicates consumption exceeds production (drawing from the grid or SunVault).
  - A **green** circle indicates surplus power available for export or storage.

---

### System Voltage

The *System Voltage* section provides:

- **System Voltage (L1 + L2):** Line-to-line mains voltage (nominal 240 VAC).
- **L1 Voltage:** Line 1 mains voltage (nominal 120 VAC).
- **L2 Voltage:** Line 2 mains voltage (nominal 120 VAC).

---

### Power Factor

The *Power Factor* section reports the ratio of **real power** to **apparent power**. Ideal values approach **100%**.

A low percentage indicates current and voltage are out of phase (often caused by highly reactive loads). The PVS cannot reliably measure extremely low power factors, so reported usage may be inaccurate in those cases.

- **Production:** Power factor measured on the production (inverter) side.
- **Consumption:** Power factor measured on the consumption (load) side.

---

### Inverter Panel Grid

The *Inverter Panel Grid* shows details for each solar panel in the array(s). Panels normally appear **blue** and will turn **yellow** if their production is significantly lower (≈ <85%) than other panels.

| Metric | Description |
|---|---|
| **Watts** | Instantaneous panel output (W) |
| **S/N** | Inverter serial number |
| **AC Volt** | Inverter AC output voltage (nominal 240 VAC) |
| **DC Volt** | Panel DC voltage |
| **DC Amp** | Panel DC current |
| **🌡️ Temperature** | Panel temperature |
| **✔ Status** | *Working* or *Error* (an Error usually means insufficient sunlight) |

---

### System Information

The *System Information* section provides:

| Parameter | Description |
|---|---|
| **Gateway** | Model description |
| **Gateway IP** | IP address used to access the PVS |
| **SN** | PVS serial number |
| **HW Vers** | Hardware version |
| **Firmware** | Firmware version |
| **State** | Runtime state |
| **Mem Used** | Memory usage (KiB) |
| **Panel ID** | SunPower customer identification number |
| **Consumption CT** | Subtype assigned to the consumption current transformer (CT)

---

## Frequently Asked Questions (FAQ)

### FAQ 1. I am having problems getting the Dashboard to work. What do I do?

See the FAQ sections in the [Dashboard Installation](#dashboard-installation) instructions. Note that **OLD** and **NEW** PVS firmware require different installation methods and each provides additional FAQ guidance.

---

### FAQ 2. Is the Dashboard secure?

The Dashboard runs locally and does **not** connect to external cloud services. Any security risks relate to your SunPower gateway or your local network configuration.

---

### FAQ 3. Why doesn't the Dashboard show daily kWh or past history?

If you want daily kWh or historical tracking, consider the SunPower integration for Home Assistant.
👉 [Home Assistant SunPower Add-On](https://github.com/krbaker/hass-sunpower)

---

### FAQ 4. How can I use the Dashboard remotely when I’m traveling?

You can use your router's **port forwarding**, but most IT professionals discourage this due to security risks. Protections exist, but they are beyond this project's scope. SunPower's cloud-based monitoring (if available for your system) is a typical alternative for remote access. All remote access has security trade-offs. Choose wisely.

---

© 2025 — SunPower Web Monitor Project