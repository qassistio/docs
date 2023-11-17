# Setting up a machine

In QAssist you can easily manage multiple machines all in one place, easily launch into them from a single click, without
exposing any credentials.

## Setup a machine

!!! note "Using a Cloud Provider"
    When using a provider the machine is not initially started, as they are on-demand, you will have full control of when
    the machines are running

1.  In [QAssist](https://app.qassist.io) go to `Machines` and click `Create Machine`
    1. `Provider` _(Optional)_ - The cloud provider you want to use, if using on-demand machines
    2. `Bridge` - The bridge that will be used to communicate with the machine
    3. `Protocol` - The protocol that will be used to communicate with the bridge
         1. Supported protocols are `RDP`, `VNC`, `SSH`, `Telnet` and `Kubernetes`
    4. `Name` - choose a friendly name
    5. `Address/Hostname` - The Address/Hostname of the machine, relative to the `Bridge` selected
         1. If using a `Provider` this will be populated for you when the machine starts
    6. `Port` _(Optional)_ - The port that will be used to communicate with the machine and the selected `Protocol`
         1. If left blank, it will use the default port for that protocol, for example `3389` for `RDP`
    7. `Additional Options` - If using a `Provider`, you may see additional options, these options are out-of-scope of this guide
2.  Click `Add`, QAssist will run some checks, which may take a few minutes
3.  You can now manage that machine within QAssist
