# Local Network Setup

You can set up the PVS on your local network. While it takes a bit of extra effort, it’s worth it.

> **IMPORTANT:**
> These instructions are for **PVS firmware versions 2025.06 Build 61839 and earlier** (pre-authentication firmware). If your firmware version is **2025.09 Build 61845 or newer**, return to the [main project page](../README.md#dashboard-installation) and follow the *New Firmware* installation instructions.

Connecting the PVS to your network isn’t a basic plug-and-play setup. That’s because the PVS's LAN1 port includes a built-in DHCP service, which conflicts with the DHCP provided by your home router. To make local access work properly, you’ll need a special router to "bridge" the PVS and your home network.

---

### Recommended Solution

<img style="padding-right: 15px; padding-bottom: 5px;" align="right" src="../images/mango1.jpg" width="150">

Some solutions involve using a Raspberry Pi flashed with OpenWrt (a popular router OS). But a simpler option is to purchase a [GL.iNet GL-MT300N-V2 Mango](https://www.amazon.com/GL-iNET-GL-MT300N-V2-Repeater-300Mbps-Performance/dp/B073TSK26W) router. It’s affordable, extremely compact, and pre-loaded with OpenWrt.

So that’s the router solution we’ll cover here.

Configuration instructions for the Mango router are available in this guide (ignore references to Home Assistant):

- **SunPower PVS Local Monitoring Guide**: [Click to view](../resources/SunPower_PVS_Local_Monitoring.pdf)

> 📝 Attribution: The guide is based on information from https://github.com/thedmpd/Sunpower_PVS_Guides

---

### Connecting the Dashboard

Once you've completed the router setup, your PVS will be assigned an IP address within your home network and will respond on port *8080.*

Suppose the PVS is assigned the IP address *192.168.1.194.* To connect the Dashboard to this address, you'll add a query string to the Dashboard’s URL.

#### Example:

1. Save the HTML dashboard file somewhere on your PC or NAS. In this example, we’ll use:
   *D:\SunPower\dashboard.html*

2. Select the file (one click) to highlight it. Right-click and choose **Open with**, then select your preferred browser. The browser will open and display an empty Dashboard. After a few seconds, you'll see “Error loading data” at the bottom of the page.

   This is expected—the Dashboard defaults to the PVS’s LAN1 IP address, but it needs the Mango-assigned IP using port 8080.

   <img style="padding-right: 10px; padding-bottom: 25px; padding-top: 10px;" align="center" src="../images/dashboard_error1.png" width="450">

3. Edit the browser’s address bar and append the following query string:
   *?ip=192.168.1.194:8080*

Your full URL will look like this:
*file:///D:/SunPower/dashboard.html?ip=192.168.1.194:8080*


After the Dashboard is confirmed working, save the URL as a bookmark in your browser. Now you don’t have to manually edit it each time. That's it, you're all set!

---

> 📝 Tip: Hosting *dashboard.html* on your network storage (NAS) means you won’t need to copy it to each device. Just create a bookmark pointing to the network location instead of your local hard drive. If you’ve made it this far, you definitely have the skills to make that happen!