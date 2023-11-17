# Setting up a private bridge

If the QAssist public bridges are not accessible to you, or you want to set up your own,
follow this guide.

You can set up your own bridge using our open-source Docker container, at no additional
cost to your team.

## Communication

Bridges will need to be both accessible by your local machine (typically exposd to the web) and able
to communicate with the remote machine directly.

``` mermaid
graph LR
  A[Local Machine] -->|WebSocket| B[Bridge];
  B -->|RDP| C[Remote Machine];
  B -->|VNC| C;
  B -->|SSH| C;
  B -->|Etc...| C;
```

## Pre-requisites

-   A Linux machine capable of running a Docker container.
    * containerd and other runtimes are supported.

## Setup

1.  In [QAssist](https://app.qassist.io) go to `Bridges` and click `Create Bridge`
    1. `Name` - choose a friendly name
    2. `URL` - The domain name/IP address of the machine running the bridge container, prefixed with `wss://` or `ws://` if you don`t have an SSL certificate
2.  Click `Add`, after a few minutes you will receive an email with a **secret key**
3.  Continue the setup&hellip;
    1. [Using a Cloud Platform](#using-a-cloud-platform)
    2. [Manually starting/stopping the bridge](#manually-startingstopping-the-bridge)

!!! warning "Sharing a Bridge Secret"
    Do not share the bridge **secret key**, as it can potentially be used to steal credentials,
    it is unique to you and cannot be recovered by us. You can re-generate a token in [QAssist](https://app.qassist.io)
    if you accidentally share it.

### Using a Cloud Platform

If you are using a cloud platform the following instructions may not apply to you, 
please take a look at our [Leveraging your cloud](../cloud-providers/index.md) page on how to
set up the bridge on your cloud platform.

### Manually starting/stopping the bridge
 
1. To start the bridge, on your machine that you want to run the bridge, run the following in a command line
    1. `docker run --name qassist-bridge --env CRYPT_SECRET='SECRET KEY HERE' --restart always --detach glokon/guacws:latest`
    2. Replace `SECRET KEY HERE` with the **secret key** you received in your email
    3. The default port for the container is `8080`
    4. If using `containerd`, the command may be different
2.  To stop the bridge, run the following commands
    1. `docker stop qassist-bridge`
    2. `docker rm --force qassist-bridge`
