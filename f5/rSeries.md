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
	- Since the **<ins>tenant OS</ins>** is deployed on rSeries appliances, select the option labeled **tenant f5os**.
- **Tenant Image Size:** When selecting the image, options like micro, small, medium, and large are available.
    - **Micro and Small** images include only the base system and limited BIG-IP modules (LTM and DNS), with remaining components downloaded during deployment.
    - **Medium and Large** images support all BIG-IP modules.
    - The **Large** image provides more disk space for tenants that require heavy logging, database storage, and feature scalability.

#### 2. Importing the Tenant OS

- **Upload:** To import the tenant image, navigate to **Tenant Management** and click **Tenant Images**. Click **Upload**, locate and select the downloaded tenant image.
- **Verification:** The system will upload the image and then verify it, which takes a couple of minutes. Once the image status shows **ready**, the tenant image is available for deployment on the rSeries platform.

> **Note on TMOS Support:** When migrating from iSeries to rSeries, it is important to know the minimum supported TMOS version. For lower-end rSeries 2000 and 4000 models, the minimum supported TMOS version is 15.1.6 and later. For higher-end rSeries 5000 and 10000 models, the minimum supported version is 15.1.5 and later. If replacing iSeries hardware, the iSeries must be running 15.1.6 or above to successfully migrate the configuration to rSeries.

## F5OS rSeries Networking Architecture

Chapter provides a detailed examination of the **F5OS rSeries networking architecture**, covering the separation of data and management planes, the concepts of port groups and pipeline groups, and a lab demonstration of configuring LAGs (Link Aggregation Groups) and VLANs.
### F5OS Networking Architecture (Out-of-Band vs. In-Band)

The F5OS platform layer supports two distinct types of networking:

1. **Out-of-Band Network (Management Plane):**
    
    - This network is used exclusively for the **management plane**.
    - The F5OS platform layer management plane is **fully isolated** from the in-band data plane,.
    - The F5OS out-of-band IP address is accessible _only_ through the out-of-band management network and cannot be accessed via the in-band network.
    - **Tenant Management:** R series tenants automatically inherit the management VLAN from the F5OS platform layer, and each tenant is assigned a unique management IP address within that inherited VLAN.
    
2. **In-Band Network (Data Plane):**
    
    - This network is used exclusively for the **data plane**.
    - All data plane networking is configured at the **F5OS host layer**.
    - F5OS utilizes **Link Aggregation Groups (LAGs)**, and VLANs are created and assigned to these LAGs.
    - When tenants are created, they only inherit the VLANs they are **explicitly permitted to access** based on their specific requirements. The F5OS layer does not have any in-band data plane IP address configured.

### Port Group and Pipeline Group Theory

These concepts define how the physical hardware resources and available bandwidth are organized and utilized.
#### Port Group

A **port group** organizes the physical ports on the rSeries platform.

- It sets up possible combinations that dictate **which ports to use**, **what speed each port should run at**, the type of cable/connector used, and which ports are active or disabled.
- It is crucial to ensure that the total configured bandwidth **does not exceed the device capacity** (e.g., 100 Gbps),.
- The valid combinations are analogous to "valid serving combinations" in the source's birthday cake analogy, where the cake represents the available bandwidth.

**F5 R 2800 Port Group Profiles Example:** The R 2800 features 4 copper ports (1/10 gig) and 4 fiber ports (1/10/25 gig). Three configuration profiles are supported:

1. **Profile 1 (8 x 10 gig mode):** Ports 1–4 (copper) and 5–8 (SFP) operate at **10 gig each**. The total bandwidth is 80 Gbps, and 25 gig ports cannot be used.
2. **Profile 2 (4 x 10 gig + 2 x 25 gig mode):** Ports 1–4 operate at 10 gig; Ports 5–6 operate at **25 gig each**. Ports 7 and 8 are disabled. Total bandwidth is 90 Gbps.
3. **Profile 3 (4 x 25 gig mode):** Ports 1–4 (copper) are disabled; Ports 5–8 operate at **25 Gbps each** (only four SFP ports are active). Total bandwidth remains within the limit.

#### Pipeline Group

A **pipeline group** manages the fundamental hardware processing layers.

- Each pipeline supports a maximum bandwidth of **100 Gbps**.
- A pipeline group can contain a maximum of **two pipelines**, and every F5 rSeries device must have at least one pipeline.
- **Hardware Isolation:** Pipelines provide physical **hardware level isolation** by dividing the internal architecture into separate data processing pipelines, each with dedicated resources (like CPU cores and network interfaces). This allows for strict separation of traffic, such as dividing the hardware between Data Center and DMZ networks,.
- **Model Constraint:** Lower-end models (R 2000 and R 4000) only support a **single pipeline**, meaning hardware level separation is not possible on these models.
- **LAG Constraint:** A critical constraint is that when creating a LAG, all member ports must belong to the **same pipeline**.

### Lab Walkthrough: R 2800 Configuration

The lab walkthrough detailed the step-by-step configuration of the in-band data and HA networks on two F5 R 2800 devices (Host One and Host Two), using the F5OS platform layer.

The objective was to complete three tasks: set up the Data VLAN LAG, set up the HA VLAN LAG, and create/assign the necessary VLANs.

### Initial Lab Setup and Prerequisites

1. **Hardware:** The lab utilized two F5 R 2800 devices, named F5 Host One lab.AE and F5 Host Two lab.AE.
2. **Management Configuration:** The devices were previously configured with management IPs 10.105.15 (Host One) and 10.105.16 (Host Two).
3. **Port Group Profile:** The **8 x 10 gig port group profile** was applied, allowing all ports (1 through 8) to function at 10 Gbps speed,.
4. **Physical Connections:**
    - **Data LAG:** Ports 1 and 2 were used for the in-band data LAG uplink, connected to a network switch. Dynamic LACP was configured on the network switch side.
    - **HA LAG:** Ports 3 and 4 were used for the in-band HA LAG, physically interconnected between F5 Host One and F5 Host Two,.

---

### Step-by-Step Configuration

#### Task 1: Setting up the Data VLAN LAG

This configuration was performed on both Host One and Host Two:

1. **Log in to F5OS** on Host One (and repeat for Host Two).
2. Navigate to the **LAG configuration** section and click "add lag".
3. Enter the LAG name as **`data_lag`**.
4. Set the LAG type to **LACP** (for dynamic LAG).
5. Add **ports 1 and 2** as configured port members.
6. Click save and close.
7. **Verification:** After waiting a few seconds for LACP negotiation, the `data_lag` came up on both hosts with an aggregated bandwidth of **20 Gbps**.

#### Task 2: Setting up the HA VLAN LAG

This configuration was also performed on both Host One and Host Two:

1. Navigate to the **LAG configuration** section and click "add lag".
2. Enter the LAG name as **`HA_LAG`**.
3. Set the LAG type to **LACP** (for dynamic LAG).
4. Add **ports 3 and 4** as configured members.
5. Click save and close.
6. **Verification:** The `HA_LAG` did not come up until the peer configuration was completed on Host Two. Once configured on both sides, the `HA_LAG` came up on both hosts with an aggregated bandwidth of **20 Gbps**.

#### Task 3: Creating VLANs and Assigning them to LAGs

This configuration was performed on both Host One and Host Two:

1. **Create VLANs:** Navigate to the VLAN configuration section and click "add vlan",.
    - Create **VLAN ID 230**, named **`PUB`** (for client-side/publishing),,.
    - Create **VLAN ID 231**, named **`RP`** (for server-side/reverse proxy),,.
    - Create **VLAN ID 4001**, named **`HA`** (for High Availability),,.
2. **Assign Data VLANs:** Edit the **`data_lag`**,. Select VLAN 230 and VLAN 231, and then save and close,.
3. **Assign HA VLAN:** Edit the **`HA_LAG`**,. Select VLAN 4001, and then save and close,.

This final step assigned the necessary data and HA VLANs to their respective Link Aggregation Groups on both F5 rSeries hosts.

---
## Tenant OS Deployment Preparation in F5 rSeries

The preparation steps ensure that resources, networking, and deployment modes are correctly defined for the tenants (which are the guest operating systems running on the F5OS host).
 
Here is a comprehensive explanation of the prerequisites discussed:
### 1. Tenant Names and Management IP Addresses

Defining the tenant identity and management access is the first step. The lab design for this learning series planned to deploy two tenants: one on Host One and one on Host Two.

- **Tenant on Host One:** Named **`prod_LB1`**, with the management IP address **10.105.2.21**.
- **Tenant on Host Two:** Named **`prod_LB2`**, with the management IP address **10.105.2.31**.

### 2. Tenant OS Availability

To deploy the BIG-IP tenant, the required tenant image must be downloaded from the F5 website and imported to the rSeries host. This preparation was completed in Chapter 03 of the learning series, ensuring the tenant OS is available on both rSeries hosts.

### 3. Tenant Networking Layer

The tenant must be connected to the existing in-band (data) and HA networks, which were configured at the F5OS host layer in Chapter 04 of the learning series.

- **Data LAG and VLANs:** The data Link Aggregation Group (LAG) must be configured. Based on the lab design, two data VLANs were required: **VLAN 230** (for the client side) and **VLAN 231** (for the server side), both assigned to the data LAG.
- **HA LAG and VLAN:** The High Availability (HA) LAG must also be configured, along with the corresponding HA VLAN. The lab used **VLAN 4001** for HA, which was assigned to the HA LAG.

### 4. Tenant MAC Block Size

The MAC block size determines the number of unique MAC addresses available within a tenant. These addresses are primarily assigned to virtual servers within the tenant.

**MAC Block Size Options:**

|Option|Allocated MAC Addresses|
|:--|:--|
|Option 1|1 MAC address|
|Small|8 MAC addresses|
|Medium|16 MAC addresses|
|Large|32 MAC addresses|

**Choosing a MAC Block Size:** The number of virtual servers configured is **not directly limited** by the MAC block size, as in standard routed mode, multiple virtual servers can share the same MAC address assigned to the tenant interfaces.

However, more MAC addresses are required for **special deployment cases**, such as deploying the F5 tenant in **transparent mode** or **Layer 2 inline mode**, where the F5 device is not the gateway. In these cases, each virtual server needs a unique MAC address for proper traffic handling and return path consistency.

For production deployment, it is always recommended to choose **medium or large** MAC block sizes. Allocating a larger MAC block size provides more flexibility for scaling virtual servers or handling special deployment scenarios without any negative impact or resource loss, even if not all addresses are used.

### 5. Resource Provisioning

Resource provisioning defines how vCPUs and DRAM (memory) are allocated to the tenant. Two options are available:

- **Recommended Option:** The system only allows you to select the number of **virtual CPUs** (vCPUs) for the tenant, and the **DRAM is automatically allocated** by the system based on the vCPU count. This option does not provide flexibility to manually adjust DRAM allocation.
- **Advanced Option:** This option allows for the manual configuration of **both vCPUs and DRAM**. This provides flexibility for customizing CPU and DRAM allocation beyond the default recommendations, which may be preferred if DRAM needs adjustment based on the specific modules installed on the tenant.

### 6. Understanding and Changing the Tenant Deployment State

Changing the tenant deployment state must be done with extra care as it can impact the tenant's availability. There are three available states:

|Deployment State|Description|Caution|
|:--|:--|:--|
|**Configured**|This state is only for **initial deployment**. Tenant settings are configured, but **no system resources** (CPU, memory, storage) are yet allocated.|Do not change a provisioned or deployed tenant back to the configured state, as this will stop the running tenant, **delete the tenant’s virtual disk** (removing all configuration and runtime data), and release all allocated resources. This action effectively resets the tenant's state.|
|**Provisioned**|**System resources** (CPU, memory, disk space) are allocated, and the **virtual disk is created and retained**. However, the tenant is **not actively running**.|Changing from deployed to provisioned gracefully shuts down the tenant while **preserving the virtual disk and all configuration data**. This state is useful for pausing the tenant (e.g., during host upgrades) without losing its data.|
|**Deployed**|This is the **fully operational state** where the tenant is active and running. The tenant utilizes the allocated resources to process traffic.|A tenant can move to the deployed state from either the configured state (which allocates resources and starts the tenant) or the provisioned state (which starts the tenant using already allocated resources).|

### 7. Enabling Crypto Acceleration

Enabling crypto acceleration is necessary to ensure optimal performance. F5 rSeries uses **built-in hardware acceleration** to offload intensive cryptographic tasks, such as SSL/TLS encryption and decryption. This process improves cryptographic speed and efficiency, reduces CPU usage, and improves overall traffic processing performance. It is recommended to always ensure this feature is enabled.

### 8. Appliance Mode

Understanding the difference between normal mode and appliance mode helps determine the resource allocation strategy.

- **Normal Mode:** A single tenant performs multiple tasks simultaneously, such as running LTM (Load Balancing) and ASM (Application Security) together.
- **Appliance Mode:** The tenant is designed to perform a **specific, single task** (e.g., only application security). This tenant uses dedicated resources for that specific job, which simplifies management and helps keep the performance steady.