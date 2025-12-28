Google Cloud **Identity and Access Management (IAM)** is a crucial security feature that manages authorization by defining **who** (principals) has **what access** (roles/permissions) to **which resources**. It allows you to securely control access to resources and manage permissions for various entities within your Google Cloud account.

### Key Concepts of IAM

IAM relies on binding principals to roles using policies to grant permissions for resources.

- **Principals (Who):** These are the entities that request access to Google Cloud resources. Principals can be several different types of accounts or groups:
    
    - Individual users with a Google account (Google accounts).
    - Applications or virtual machines (VMs) interacting with services (Service accounts).
    - A collection of Google accounts (Google Groups).
    - Managed accounts belonging to an organization (Cloud Identity or G Suite domains).
    - Anonymous access (AllUsers) or anyone authenticated with a Google account (AllAuthenticatedUsers).
- **Resources (What):** These include VMs, APIs, Cloud Storage buckets, databases, and other services contained within a Google Cloud project. Services like Cloud Storage utilize IAM for fine-grained access control.
    
- **Roles (What Level of Access):** Roles group specific permissions together and are used to grant access, as principals do not receive permissions directly. There are three types of roles:
    
    - **Primitive Roles:** Broad, basic roles such as Owner, Editor, and Viewer.
    - **Predefined Roles:** More granular roles tailored to specific services (e.g., Compute Viewer or Storage Admin).
    - **Custom Roles:** User-defined roles created to fit specific access needs.

### Policy Structure and Hierarchy

IAM policies bind principals to roles, thereby granting them specific permissions on resources.

Permissions are applied hierarchically within the Google Cloud resource structure:

**Organization → Folder → Project → Resource**.

Permissions are inherited from the top down; policies set at higher levels, such as the organization or project level, are inherited by the resources located underneath them. Projects specifically use IAM policies to manage permissions independently and provide fine-grained control over their resources.

### IAM in Fortinet Context

Fortinet's centralized login portal, FortiCloud, utilizes the IAM portal to manage permissions. Using this capability, administrators can create users with distinct permissions to access various Fortinet portals (e.g., granting FortiCloud administrator A access only to FortiWeb Cloud, and administrator B access only to the FortiMail portal).