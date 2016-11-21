![](http://static.creatordev.io/logo.png)
# Ci40 Onboarding scripts

Visit us at [forum.creatordev.io](http://forum.creatordev.io) for support and discussion


This projects provides a bunch of scripts that allows you to do onboarding of
Your Ci40 either through WEB interface or using [mobile app](https://github.com/CreatorDev/android-provisioning-onboard-app).
Onboarding is a process of connecting your Ci40 to the Device Server.

There are three main components:

* **Luci onboarding module** that integrates with LuCI interface.
* **JsonRPC API** that exposes couple of methods that can be used to perfrom onboarding.
* **Ubus API** that exposes an **"Creator"** ubus object on which some methods can be performed.

## Prerequisites

Onboarding scripts depends on following packages:
* curl
* luci
* luci-mod-rpc
* libubox-lua
* luci-ssl-openssl
* uhttpd-mod-tls
* avahi-dbus-daemon
* avahi-dnsconfd

If You're using Creator Image You should already have that installed, otherwise You can install them
using following command:

	opkg install curl luci-mod-rpc libubox-lua luci-ssl uhttpd-mod-tls avahi-dbus-daemon avahi-dnsconfd

## How to install

Easiest way of installing **Onboarding Scripts** is using an opkg package manager.

    opkg install onboarding-scripts

Or You can compile it by yourself.

## LuCI onboarding module
It's a module to LuCI that allow to do onboarding of your Ci40 board through a web page.
Onboarding module is available on your Luci page under tab "Creator". Once you've
connected you're Ci40 to the internet You can access it through `https://<CI40_IP>/cgi-bin/luci` where
**CI40_IP** is ip of your Ci40 board in a local network.

## Json RPC API
This component exposes couple of Json RPC methods through https. Api is accessible through
`https://<CI40_IP>/cgi-bin/luci/rpc/creator_onboarding?auth=AUTH_TOKEN` endpoint.
Uhttpd deamon running on Ci40 uses self signed certificate. You may need to tell your client to
accept such certificates. in curl this will be `-k` parameter.

### Authentication

To call any method You need to be authenticated first. To authenticate yourself using JsonRPC call

    http://<CI40_IP>/cgi-bin/luci/rpc/auth

with following request body

```json
{
    "id" : 1,
    "jsonrpc" : 2.0,
    "method" : "login",
    "params" : [
        "USERNAME", "PASSWORD"
    ]
}
```

where USERNAME and PASSWORD are credentials to your board. When successfull this call should return
an authentication token which You have to pass when calling other methods.

On successfull login you should get response similar to following one

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": "13f749ec6789ae6032eda560139ccded"
}
```


### doOnboarding
Calling this method will connect Your Ci40 board to Device Server. You need to know
Your api key, api secret and device server url in advance. These can be obtained from device server console.

ENDPOINT_NAME define how Your device will be named on device server.

Return true on success, or error on failure

#### Example request

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method" : "doOnboarding",
  "params" : [
    "http://deviceserver.flowcloud.systems",
    "YOUR_API_KEY",
    "YOUR_API_SECRET",
    "ENDPOINT_NAME"
  ]
}
```

#### Example response

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "result" : true,
  "error" : null
}
```


### isProvisioned
Return true if this board is Provisioned, false otherwise

#### Example request

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method" : "isProvisioned",
  "params" : []
}
```

#### Example response

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "result" : true,
  "error" : null
}
```

### unProvision
Disconnects Ci40 from device server and removes configurations set by onboarding method

#### Example request

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method" : "unProvision",
  "params" : []
}
```

#### Example response

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "result" : true,
  "error" : null
}
```

### boardName
Return board name set during onboarding process or null if board is not yet provisioned to device server.

#### Example request

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method" : "boardName",
  "params" : []
}
```

#### Example response

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "result" : true,
  "error" : null
}
```

### boardInfo
Return set of key : value pairs with some board details

#### Example request

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method" : "boardInfo",
  "params" : []
}
```

#### Example response

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "result" : {
    "hostname" : "OpenWrt"
  },
  "error" : null
}
```


## Ubus API

Last component of onboarding-scripts is a ubus object "creator" which for the time being exposes only
one method.

### generatePsk

Once onboarding process is completed this method can be used to generate new PSK on device server.

`ubus call creator generatePsk '{}'`

#### Example response

```json
{
	"pskIdentity": "zyi9HHC4H0C2k5ygYOWSFA",
	"pskSecret": "1988D523D88F480B15A98EBE27E1141CA41F30FD74E52C96ABC75AEC2AA49938"
}
```

## CONTRIBUTING

If you have a contribution to make please follow the processes laid out in [contributor guide](CONTRIBUTING.md).
