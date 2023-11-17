# Preparing AWS

You can leverage the Amazon Web Services ecosystem to run machines on-demand all in one place.

Follow the guide below to link [QAssist](https://app.qassist.io) and your Microsoft Azure instance.

## Pre-requisites

!!! note "AWS Regions"
    The following resources should be in the same region and VPC

-   A VPC[^1]
    * With a NAT Gateway[^2]
    * With 2 subnets with a NAT Gateway (It can be the same NAT Gateway)
        * `Subnet A` - Will be used for the bridge
        * `Subnet B` - Will be used for the on-demand machines
-   A way to run the bridge, we recommend `ECS` service[^3]
    * If you wish to host it differently, follow the guide [here](../bridges/setup-bridge.md)

## Setup IAM Access

For QAssist to communicate with Amazon Web Services, you will need to create an `IAM Account` with an `Access Key`

Follow the guide [here](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html), make a note of the following:

-   `Access Key`
-   `Access Key Secret`

You will also need to apply the permissions below to the created user.

### IAM Permissions

!!! note "IAM Permissions"
    The following permissions are required and need to be applied to the IAM user
    
        ec2:CreateTags
        ec2:RunInstances
        ec2:DescribeRegions
        ec2:DescribeVpcs
        ec2:DescribeSubnets
        ec2:DescribeSecurityGroups
        ec2:DescribeInstanceTypeOfferings
        ec2:DescribeInstanceTypes
        ec2:GetPasswordData
        ec2:DescribeImages
        ec2:DescribeInstances
        ec2:StartInstances
        ec2:RebootInstances
        ec2:StopInstances
        ec2:TerminateInstances


## Setup Bridge

This section only applies if you are using `ECS` using the recommended settings.
Any other services or ways to run the bridge are out of scope of this document.

1.  Create your `ECS` service, using the recommended settings
2.  Follow [this guide](../bridges/setup-bridge.md) to create a bridge on QAssist, with the following settings
    1. `URL` - This will be the `Domain` from the `Web App for Containers`
    2. Make a note of the **secret key** you receive in your email
3.  Set the following environment variable in the `ECS` container
    1. `Name` - `CRYPT_SECRET`
    2. `Value` - _The **secret key** you received when creating the bridge_
4.  The bridge setup is complete

## Setup Provider

1.  In [QAssist](https://app.qassist.io) go to `Providers` and click `Create Provider`
    1. `Type` - `AWS`
    2. `Name` - choose a friendly name
    3. `Access Key` - From `IAM User`
    4. `Access Key Secret` - From `IAM User`
    5. `Region` - The region where the pre-requisite resources where created
    6. `Endpoint` _(Optional)_ - The API endpoint to communicate with
2.  Click `Add`, QAssist will run some checks, which may take a few minutes
3.  QAssist should now be able to communicate with your Amazon Web Services instance


[^1]: QAssist will only create machines in the VPC configured, [Learn more](https://docs.aws.amazon.com/vpc/latest/userguide/create-vpc.html).
[^2]: A NAT gateway is required for machines to access the internet
[^3]: `ECS` is a fast and cost-effective way to get the bridge up and running

