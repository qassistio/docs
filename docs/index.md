# Welcome

This documentation site will assist you in setting up and using QAssist, below you can find
some terminology that QAssist uses in the app and in the documentation.

## Terminology

### Machine

A machine is exactly what it is, it covers either a physical, virtual or an on-demand machine,
that are either managed by you or by QAssist. [Learn more](machines/setup-machine.md).

### Bridge

A bridge is a piece of software that allows the QAssist web app to communicate with your configured machines.
We provide [public bridges](bridges/public-bridges.md) that you are free to use, or you can [setup your own](bridges/setup-bridge.md).

The bridge is provided as a docker container.

### Provider

A provider is another name for a cloud provider, such as AWS, Azure and Google Cloud, that QAssist can use to
provision and manage machines on-demand, on your behalf. [Learn more](cloud-providers/index.md).

### Credential

A credential is a way to login to a machine on QAssist. This can be through either Username/Password,
Private Key or Password Only authentication, depending on the protocol.
