## F5 iSeries to the F5 rSeries

The chapter details the architectural differences between the two hardware families, particularly concerning virtualization:
### F5 iSeries Architecture

The F5 iSeries architecture offers two primary deployment modes: 
+ without virtualization
+ with virtualization.

#### 1. iSeries Without Virtualization (Default)

In this scenario, virtualization is an optional licensed feature, not mandatory.

- **TMOS** (Traffic Management Operating System) **runs directly on the bare metal hardware**.
- TMOS boots as the **unique guest operating system** on the bare metal hardware,.
- On top of TMOS, you run modules such as LTM, ASM, APM, and GTM, depending on your needs.
- TMOS handles the core networking configurations, including creating VLANs and adding them to trunks.

#### 2. iSeries With Virtualization (vCMP)

In the iSeries, this is accomplished through the **vCMP** (Virtual Clustered Multiprocessing) **hypervisor**, which is a licensed feature built-in with TMOS.

- TMOS is not a hypervisor itself, but after the vCMP license is enabled, the system reboots,.
- Upon reboot, TMOS boots on the bare metal hardware as the **vCMP host OS**. This host OS plays the role of the hypervisor,.
- The vCMP hypervisor then creates **multiple isolated TMOS guest instances** on the same physical device,.
- This setup effectively turns a single F5 device into multiple independent logical devices.
- Networking components like interfaces, trunks, and VLANs are configured and monitored at the **vCMP host level** and then assigned to the guests as needed. Guests only inherit the VLANs they are allowed to access.
- You can run multiple BIG-IP guest instances, but you **cannot run BIG-IP Next** as a guest instance in this architecture.

### F5 rSeries Architecture

The rSeries is F5's next-generation hardware, and the architecture mandates virtualization.

- Unlike the iSeries, **TMOS cannot run directly on the F5 rSeries bare metal hardware without virtualization**.
- Virtualization is achieved using a **new Kubernetes-based platform layer called F5OS**.
- **F5OS** is the **host OS** that always runs on the rSeries bare metal hardware.
- TMOS runs as the **guest OS** in rSeries. In rSeries terminology, the guest OS is referred to as a **tenant**.
- F5OS is built with a **microservices-based architecture**.
	-  Microservices-based architecture (what F5OS uses)
		- The system is split into **small, focused services**
		- Each service:
		    - Does one specific job (e.g., networking, management, logging)
		    - Runs independently
		    - Communicates with other services through APIs
		- Services can be updated, restarted, or scaled **without affecting the entire system**
- F5OS configures and monitors the networking components (VLANs, interfaces, and LAGs). Tenants are only assigned the VLANs they are allowed to use.
- A main advantage of the rSeries is the ability to run **multiple mixed tenants**, such as **BIG-IP** and **BIG-IP Next**, on the same hardware—something that is not possible in the iSeries. BIG-IP Next is described as a next-generation, cloud-native, and modernized version of BIG-IP.

In summary, the transition from iSeries to rSeries involves a shift from optional vCMP virtualization (where TMOS can be the host or guest) to mandatory **F5OS virtualization** (where F5OS is always the host and TMOS is always a tenant/guest).

## F5OS basic setup

Using the **F5 R 2800** model.

The basic setup involves configuring the hardware, initial management access, system settings (Hostname, DNS, NTP), and license activation.

### 1. Hardware Overview and Power

The F5 R 2800 model features an out-of-band management port, a serial console port, four copper ports, four fiber ports, status LEDs, and an LCD panel.

- **Power Redundancy:** The R 2800 has two power supply slots, though only one Power Supply Unit (PSU) is included by default; purchasing an additional PSU is necessary to enable power supply redundancy.
- **Power Connectors:** F5 uses different power connector types based on the device model. The lower-end models (R 2000, R 4000, R 5000) use **C13 connectors**, which do not have a notch. Higher-end models (R 10000, R 12000) use C15 connectors, which have a notch. The R 2800 specifically requires two **C13 power cords**.

### 2. Initial Management Access

After powering on the device and confirming the system status via the LEDs and LCD panel, the next step is to access the F5OS platform layer.

- **Connecting:** The management port must be connected to the management network switch, and the link status must be confirmed as up.
- **Configuring the IP:** The management IP address, subnet mask, and gateway are configured. The LCD panel can be used to set these parameters (e.g., setting the management IP to 10.105.1.15, subnet mask to /24, and default gateway to 10.105.1.1).
- **Verification:** The reachability of the management IP address from the local area network is verified using ping.

### 3. Web UI Access and Security

The R series follows an **API-driven architecture**, allowing access to the platform layer via CLI, web UI, and REST API.

- **Access:** To access the web UI, open a browser and type `https://` followed by the management IP address.
- **Default Credentials:** The default credentials for login are `admin` for both the username and password.
- **Initial Password Change:** Upon the initial login, the system will immediately prompt the user to change the default password.

### 4. Basic System Configuration in F5OS

Once logged in, the basic setup involves configuring the hostname, DNS, and NTP.

#### A. Setting Up the Hostname

- Navigate to **System Settings > General**.
- Type the desired hostname and save the changes.
- **Important Note:** Updating the hostname causes the **management plane of the F5OS host to restart**, terminating the current session and requiring the user to log in again.

#### B. Setting Up DNS

- Navigate to **System Settings > DNS**.
- Add DNS server IP addresses under "DNS lookup servers".
- Add domain names under "DNS search domains".

#### C. Setting Up NTP

- Navigate to **System Settings > Time Settings**.
- **Enable the NTP service** and click save.
- Add NTP servers under "NTP servers".
- Set the correct time zone (e.g., Asia/Dubai).

### 5. Verifying DNS and NTP

To test if DNS and NTP are working, you log into the CLI (Command Line Interface).

- **CLI Access Types:**
    - **Root:** Logs into the privileged Linux shell, where Linux commands are used to verify settings (e.g., `dig google.com` for DNS and `timedatectl status` for NTP). F5 recommends against using the root login.
    - **Admin:** Logs into the restricted F5OS shell, which is role-based.
- **F5OS Shell Commands:** In the restricted F5OS cell, you use F5OS commands to check configuration:
    - To check NTP: `show system NTP`.
    - To check DNS: `show system DNS`.
- **F5OS Name Resolution Test:** To test name resolution within the F5OS cell, use the diagnostic command: `system diagnostic net-utils shell dig [hostname]`.
- **Access Limitation:** For security, access is restricted; the root cell cannot access the F5OS CLI, and the F5OS CLI cannot access the root Linux cell.

### 6. Activating the License

License activation is performed after DNS is correctly configured.

- Navigate to **System Settings > Licensing**.
- Go to **Base Registration Key** and click activate.
- Enter the base registration key and add-on keys.
- **Prerequisite:** Successful activation requires the management IP to connect to the F5 license activation server (activate.f5.com) over **TCP port 443**.
- Once activated, the license information can be viewed. Similar to the iSeries, **rSeries tenants inherit their licenses from the F5OS host**.

### 7. Changing the User Password (After Initial Setup)

To change an existing user password:

- Go to **Authentication and Access > Users and Roles**.
- Click the user account you wish to edit (e.g., the `admin` account).
- Locate and click "change," then enter the current password, and the new password twice.
- Saving the changes will log the user out and redirect them to the login page.

## F5OS Upgrade & Import the TMOS tenant OS

### F5OS Architecture Recap

The rSeries hardware operates with two layers of operating systems: the Platform Layer and the Tenant Layer.

1. **Platform Layer OS (F5OS):** F5OS is the platform layer operating system built on a **Kubernetes-based microservices architecture** that runs directly on the rSeries bare metal hardware. It virtualizes hardware resources and provides the interface for system operation and management, accessible via CLI, web UI, and REST API.
2. **Tenant Layer OS (TMOS/BIG-IP Next):** The tenant layer OS can be either BIG-IP or BIG-IP Next, running in an isolated virtual environment, similar to a virtual machine. Tenants are deployed, managed, and monitored through the F5OS platform layer.

### I. F5OS Upgrade Procedure

The F5OS upgrade requires identifying the correct software version, downloading the image, uploading it to the device, and initiating the upgrade.

#### 1. Identifying and Downloading the F5OS Image

Before upgrading, you must identify your hardware model (e.g., R series appliance 2800) and the current F5OS version running (found by navigating to Dashboard > System Summary).

- **F5OS Versions:** F5OS is available in two versions, 
	- **F5OS-A** (for appliances like the R series) and 
	- **F5OS-C** (for chassis systems). You must choose the right one based on your hardware platform.
- **Downloading:** The appropriate F5OS software version is downloaded from the F5 support portal (my.f5.com). You navigate to Resources > Downloads, select the **F5OS** product group, choose **F5OS appliance software** (for rSeries appliances), select the desired product version (e.g., upgrading from 1.7 to 1.8), and select the stable image corresponding to your hardware model (e.g., F5 rSeries 2800).

#### 2. Uploading and Installing the F5OS Image

- **Upload:** Log into the F5 rSeries hardware, navigate to **System Settings > Software Management**, and click **Upload** under Base OS images. Locate and select the downloaded F5OS image. The system will show upload progress and then verify the image, which may take a few minutes. The image status must show **ready** before proceeding.
- **Upgrade:** In the same **Base OS software** section, keep the "software version bundled" option selected, choose the new F5OS image under "upload software," and click **Save**.
- **System Restart:** The upgrade process begins immediately; the web service will go down temporarily as the system upgrades and reboots, and the user will be logged out. The upgrade typically takes about 10 to 15 minutes, and the system can be monitored by pinging the F5 host.
- **Verification:** After logging back in, navigate to **System Settings > Software Management** to confirm the new version is listed as "in use".

### II. Importing the TMOS Tenant OS Image

After the F5OS platform is upgraded, the next step is to prepare for tenant deployment by importing the necessary tenant OS image (BIG-IP virtual edition).

#### 1. Downloading the Tenant OS Image

- **Location:** The tenant OS image is downloaded from the F5 support portal (Downloads).
- **Selection:** Select the product group as **BIG-IP** and the product line as **BIG-IP virtual edition**. 
	- Since the **<ins>tenant OS</ins>** is deployed on rSeries appliances, select the option labeled **tenant fios**.
- **Tenant Image Size:** When selecting the image, options like micro, small, medium, and large are available.
    - **Micro and Small** images include only the base system and limited BIG-IP modules (LTM and DNS), with remaining components downloaded during deployment.
    - **Medium and Large** images support all BIG-IP modules.
    - The **Large** image provides more disk space for tenants that require heavy logging, database storage, and feature scalability.

#### 2. Importing the Tenant OS

- **Upload:** To import the tenant image, navigate to **Tenant Management** and click **Tenant Images**. Click **Upload**, locate and select the downloaded tenant image.
- **Verification:** The system will upload the image and then verify it, which takes a couple of minutes. Once the image status shows **ready**, the tenant image is available for deployment on the rSeries platform.

---

**Note on TMOS Support:** When migrating from iSeries to rSeries, it is important to know the minimum supported TMOS version. For lower-end rSeries 2000 and 4000 models, the minimum supported TMOS version is 15.1.6 and later. For higher-end rSeries 5000 and 10000 models, the minimum supported version is 15.1.5 and later. If replacing iSeries hardware, the iSeries must be running 15.1.6 or above to successfully migrate the configuration to rSeries.