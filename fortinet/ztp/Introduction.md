FortiZTP automatically loads devices that are registered to Asset Management under the same FortiCloud account and Cloud or FortiDeploy key verification.

You must perform the Cloud or FortiDeploy key verification during Asset Management registration. If Asset Management does not prompt for the verification step, the Cloud key or FortiDeploy key is invalid in FortiCare. Contact [Fortinet Support](https://support.fortinet.com) to inquire on the key status.

You must register or import devices to the [Asset Management portal](https://support.fortinet.com/) in the same FortiCloud account.

## Requirements
| FortiCloud account    | Create a FortiCloud account if you do not have one. Using FortiZTP requires a FortiCloud account.<br><br>You must register or import devices to the [Asset Management portal](https://support.fortinet.com/) in the same FortiCloud account for them to be available for provisioning in FortiZTP. |
| --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| FortiGate certificate | FortiGate devices must possess a valid certificate with serial number as CN signed by Fortinet to ensure proper functionality of FortiZTP.                                                                                                                                                         |
