# Preparing Azure

You can leverage the Microsoft Azure ecosystem to run machines on-demand all in one place.

Follow the guide below to link [QAssist](https://app.qassist.io) and your Microsoft Azure instance.

## Pre-requisites

!!! note "Azure Regions"
    The following resources should be in the same region

-   A subscription with the following resource providers attached[^1]
    * `Microsoft.Compute`
    * `Microsoft.Network`
    * `Microsoft.Quota`
    * `Microsoft.Web`
-   A resource group, within that subscription[^2]
-   An Azure Virtual Network[^3]
    * With a NAT Gateway[^4]
    * With 2 subnets with a NAT Gateway (It can be the same NAT Gateway)
        * `Subnet A` - Will be used for the bridge
        * `Subnet B` - Will be used for the on-demand machines
-   A way to run the bridge, we recommend `Web App for Containers`[^5]
    * If you wish to host it differently, follow the guide [here](../bridges/setup-bridge.md)

??? note "Recommended 'Web App for Containers' setup"
    The following is a basic recommendation and may not apply to all use cases.

    -   **Basics**
        - Publish: `Docker Container`
        - Operating System: `Linux`
    -   **Docker**
        - Options: `Single Container`
        - Image Source: `Docker Hub`
        - Access Type: `Public`
        - Image and tag: `glokon/guacws:latest`
    -   **Networking**
        - Enable public access: `On`
        - Enable network injection: `On`
        - Virtual Network: `The virtual network created earlier`
        - Enable VNet integration: `On`
        - Subnet: `Subnet A`
    
    After creating the web app, change the following Web App settings
    
    -   **Application settings**
        - Create an `Application Setting`
            - `Name` - `WEBSITES_PORT`
            - `Value` - `8080`
    -   **General settings**
        - Always on: `On`

## Setup App Registration

For QAssist to communicate with Microsoft Azure, you will need to create a `Microsoft Entra App Registration`

Follow the guide [here](https://learn.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal), make a note of the following:

-   `Directory (tenant) ID`
-   `Application (client) ID`
-   `Client Secret`

Once you have created the app and made a note of those, you will need to give the `App Registration` you have just created the following permissions ([How to assign roles](https://learn.microsoft.com/en-us/entra/identity-platform/howto-create-service-principal-portal#assign-a-role-to-the-application)):

-   `Contributor` role to the `resource group` you want to use
-   `Quota Request Operator` role to the `subscription` you want to use

## Setup Bridge

This section only applies if you are using `Web App for Containers` using the recommended settings.
Any other services or ways to run the bridge are out of scope of this document.

1.  Create your `Web App for Containers` app, using the recommended settings
2.  Follow [this guide](../bridges/setup-bridge.md) to create a bridge on QAssist, with the following settings
    1. `URL` - This will be the `Domain` from the `Web App for Containers`
    2. Make a note of the **secret key** you receive in your email
3.  Create a new `Application Setting` in the `Web App`
    1. `Name` - `CRYPT_SECRET`
    2. `Value` - _The **secret key** you received when creating the bridge_
4.  Restart the `Web App`, after that the bridge setup is complete

## Setup Provider

1.  In [QAssist](https://app.qassist.io) go to `Providers` and click `Create Provider`
    1. `Type` - `Azure`
    2. `Name` - choose a friendly name
    3. `Directory (tenant) ID` - From `App Registration`
    4. `Subscription ID` - The subscription ID you want QAssist to use
    5. `Application (client) ID` - From `App Registration`
    6. `Client Secret` - From `App Registration`
    7. `Resource Group` - The resource group you want QAssist to use (It must belong to the subscription)
2.  Click `Add`, QAssist will run some checks, which may take a few minutes
3.  QAssist should now be able to communicate with your Microsoft Azure instance


[^1]: QAssist uses those resource providers to check and provision machines, [Learn more](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/resource-providers-and-types#register-resource-provider).
[^2]: QAssist will only create machines in the resource group configured, [Learn more](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups).
[^3]: The virtual network allows communication between the bridge and the machines, [Learn more](https://learn.microsoft.com/en-us/azure/virtual-network/quick-create-portal).
[^4]: A NAT gateway is required for machines to access the internet
[^5]: `Web App For Containers` is a fast and cost-effective way to get the bridge up and running
