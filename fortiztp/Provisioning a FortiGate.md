# Provisioning a FortiGate

The following describes provisioning a FortiGate. After FortiZTP provisions devices, they appear as _Provisioned_.

###### To provision a FortiGate to FortiGate Cloud:

1. Go to _Settings > FortiGate_.
2. Ensure that _FortiGate Cloud_ is enabled.
3. Click _Update_.
    
    ![](https://fortinetweb.s3.amazonaws.com/docs.fortinet.com/v2/resources/e09ecc0a-88e6-11f0-9bfd-6af4c3636dc7/images/8ee8ec0f5045e73b5ecaf57b5e9a71d5_Provision.png)
    
4. In _Assets_, select the checkboxes for the desired FortiGates, then click _Provision_.
5. Under _Target Location_ in the _Provision_ dialog, select the desired target location for the FortiGate(s). Only options that you have configured in _Settings_ appear in this dialog.
6. From the _Please select a firmware profile_ dropdown list, select the desired firmware profile. See [Firmware Profile](https://docs.fortinet.com/document/fortigate-cloud/25.3.a/administration-guide/460884).
7. Click _Provision_.

###### To provision a FortiGate to FortiManager:

1. Go to _Settings > FortiGate_.
2. Optionally, configure a pre-run CLI template. Pre-run CLI scripts will be pushed to FortiGate before it connects with FortiManager.
    1. Select the _Pre-run CLI Scripts_ tab.
    2. Click _Add_ to create a new script.
    3. Enter a _Name_ for the script, and provide the _CLI Content_.  
        ![](https://fortinetweb.s3.amazonaws.com/docs.fortinet.com/v2/resources/e09ecc0a-88e6-11f0-9bfd-6af4c3636dc7/images/1ed5ad99afbd57e4bbef7c66aebb70cc_Pre-Run%20CLI%20-%20Enter%20Details.png)  
        
    4. Click _Update_ to save the script.  
        This script can now be specified as the default pre-run CLI script for a target FortiManager, or selected during the provisioning process. You can edit or delete an existing script by clicking the _Edit_ or _Delete_ options.
3. Select the _Provision Target_ tab.
4. Enable _FortiManager_.
5. Click _Add_.
6. In the _Serial Number_ and _IP Address/FQDN_ fields, enter the FortiManager details. If you are provisioning FortiGate to FortiManagers in a high availability (HA) pair, enter both nodes' serial numbers separated by a comma in the _Serial Number_ field and one IP address (the virtual IP address or hostname) in the _IP Address/FQDN_ field. FortiZTP supports provisioning for FortiManager 7.2 HA.
7. Optionally, enable _Pre-run CLI Script_ to choose a previously configured pre-run CLI script as the default for this FortiManager.  
    ![](https://fortinetweb.s3.amazonaws.com/docs.fortinet.com/v2/resources/e09ecc0a-88e6-11f0-9bfd-6af4c3636dc7/images/42fd32165eb7e1886ee0b1a1afbe6030_FMG%20HA.png)
8. By default, FortiGate Cloud is enabled. If desired, you can disable FortiGate Cloud.
9. Click _Update_.
10. In _Assets_, select the checkboxes for the desired FortiGates, then click the _Provision_ button.
11. Under _Target Location_ in the _Provision_ dialog, select the desired target location for the FortiGate(s). Only options that you have configured in _Settings_ appear in this dialog.
12. Optionally, enable _Pre-Run CLI_ to provision the FortiGate with a pre-run CLI script. You can choose the _FortiManager Default_ script or select _Specify_ to choose another script configured under the _Pre-run CLI Scripts_ tab.  
    ![](https://fortinetweb.s3.amazonaws.com/docs.fortinet.com/v2/resources/e09ecc0a-88e6-11f0-9bfd-6af4c3636dc7/images/2db7569e209ae46a428edf9b21c0d21b_Select%20Pre-Run%20CLI.png)  
    
13. Click _Provision_.

###### To provision a FortiGate to FortiManager Cloud:

1. Go to _Settings > FortiGate_.
2. Enable _FortiManager Cloud_.
3. In _Assets_, select the checkboxes for the desired FortiGates, then click the _Provision_ button.
4. Under _Target Location_ in the _Provision_ dialog, select the desired target location for the FortiGate(s). Only options that you have configured in _Settings_ appear in this dialog.
5. Click _Provision_.

See [Using FortiZTP with FortiManager Cloud](https://docs.fortinet.com/document/fortimanager-cloud/latest/cloud-deployment/552626) for FortiManager Cloud-side instructions.

In previous versions, provisioning a device to FortiManager Cloud using FortiZTP automatically created a model device in FortiManager Cloud. This no longer occurs. You can manually create a model device or perform the provisioning without a model device, then authorize it in the unregistered list in FortiManager Cloud. Performing preconfiguration requires creating a model device manually in FortiManager Cloud before provisioning.