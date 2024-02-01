# Setting up a private bridge

If the QAssist public bridges are not accessible to you, or you want to set up your own,
follow this guide.

You can set up your own bridge using our open-source Docker container, at no additional
cost to your team.

## Communication

Bridges will need to be both accessible by your local web browser (typically exposed to the web) and able
to communicate with the remote machine directly (typically joined to a private subnet).

When you access the [QAssist](https://app.qassist.io) app, your local web browser will retrieve an encrypted token
that is then passed to the bridge, to then authenticate against a machine.

``` mermaid
graph LR
  A[Local Web Browser] <-->|Encrypted Token| B[QAssist];
  A -->|WebSocket| C[Bridge];
  C -->|RDP| D[Remote Machine];
  C -->|VNC| D;
  C -->|SSH| D;
  C -->|Etc...| D;
```

## Pre-requisites

-   A Linux machine capable of running a Docker container.
    * containerd and other runtimes are supported.

## Setup

1.  In [QAssist](https://app.qassist.io) go to `Bridges` and click `Create Bridge`
    1. `Name` - choose a friendly name
    2. `URL` - The domain name/IP address and port (Default `8080`) of the machine running the bridge container, prefixed with `wss://` or `ws://` if you don`t have an SSL certificate
2.  Click `Add`, after a few minutes you will receive an email with a **secret key**
3.  Continue the setup&hellip;
    1. [Using a Cloud Platform](#using-a-cloud-platform)
    2. [Using a container runtime (e.g. Docker)](#using-a-container-runtime-eg-docker)

!!! warning "Sharing a Bridge Secret"
    Do not share the bridge **secret key**, as it can potentially be used to steal credentials,
    it is unique to you and cannot be recovered by us. You can re-generate a token in [QAssist](https://app.qassist.io)
    if you accidentally share it.

### Using a Cloud Platform

If you are using a cloud platform the following instructions may not apply to you, 
please take a look at our [Leveraging your cloud](../cloud-providers/index.md) page on how to
set up the bridge on your cloud platform.

### Using a container runtime (e.g. Docker)
 
For a basic way to run the bridge, see the following steps,
there are [additional settings](#container-settings-advanced) to allow you to configure the bridge below.

The bridge also supports SSL certificates (in `pfx` format), that you can use to enable SSL, additional
information can be found in the [SSL Setup](#ssl-setup) section.

1. To start the bridge, on your machine that you want to run the bridge, run the following in a command line
    1. `docker run --name qassist-bridge --env Cipher__Key='SECRET KEY HERE' --restart always --detach glokon/guacws-next:latest`
    2. Replace `SECRET KEY HERE` with the **secret key** you received in your email
    3. The default port for the container is `8080`
    4. If using a different container runtime, the command may be different
2.  To stop the bridge, run the following commands
    1. `docker stop qassist-bridge`
    2. `docker rm --force qassist-bridge`

## SSL Setup

To configure SSL, you have two options, provide your own certificate or use LetsEncrypt, by default when SSL is configured,
the bridges default port is `8081`.

### Certificate

To use your own SSL certificate you will need to make sure its in `pfx` format, and you will need
to provide the certificate password (If needed).

You will need to mount the directory where your certificate is, mount your directory to the container `/certs/` directory
by default, the container will look for a file called `certificate.pfx`.

1.  If the bridge is running, stop it
2.  Set the following environment variables
    1. `Server__SSL__CertificatePath` - (By default `/certs/certificate.pfx'`) Path to look for the certificate
    2. `Server__SSL__CertificatePassword` - (If required) Password for the certificate
3.  The bridge will now be using the certificate for SSL

### LetsEncrypt

To use LetsEncrypt, you must change the default HTTP port to 80, or have some way for LetsEncrypt to communicate with
the bridge.

1.  If the bridge is running, stop it
2.  Set the following environment variables
    1. `Server__LetsEncrypt__EmailAddress` - your LetsEncrypt email
    2. `Server__LetsEncrypt__Domains_N` - (Where N is a zero-indexed incrementing number) each domain you want a LetsEncrypt certificate to be generated for
3.  Start the bridge again, the bridge will now request a certificate for each configured domain
4.  The bridge will now be using LetsEncrypt

## Container Settings (Advanced)

The following are some advanced settings, that may require some knowledge of container
runtimes, that aren't covered in this document, all the following settings are configured through environment
variables.

### Basic

*   `Cipher__Key` - The key provided by QAssist
    * Default: Empty

### Logging

*   `Logging__LogLevel__Default` - Log level
    * Default: `Information`
    * Options: `Error`, `Warning`, `Information`, `Debug`, `Trace`

### Server

*   `Server__UseHsts` - (When using SSL) Use HSTS
    * Default: `false`
    * Type: `boolean`
*   `Server__HttpPort` - HTTP Port
    * Default: `8080`
    * Type: `uint16`
*   `Server__HttpsPort` - HTTPS Port
    * Default: `8081`
    * Type: `uint16`
*   `Server__MaxUploadSize` - Max Upload size (Bytes) for file uploads to machines
    * Default: `100000000` (100 MB)
    * Type: `uint32`
*   `Server__WebSocket__UseCompression` - Whether to use compression for WebSockets
    * Default: `false`
    * Type: `boolean`

### SSL

*   `Server__LetsEncrypt__UseStagingServer` - Use staging LetsEncrypt server
    * Default: `false`
    * Type: `boolean`
*   `Server__LetsEncrypt__Domains__N` - (N being a zero-indexed number) A domain to request a LetsEncrypt certificate
    * Default: Empty
*   `Server__LetsEncrypt__EmailAddress` - Email Address to use for requesting LetsEncrypt certificates
    * Default: Empty
*   `Server__SSL__CertificatePath` - Certificate path to use for SSL
    * Default: `/etc/certificate.pfx`
*   `Server__SSL__CertificatePassword` - Certificate password
    * Default: Empty
