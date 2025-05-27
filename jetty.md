<!-- jetty, jetty.xml, standalone, jetty configuration, extend jetty, extending jetty, custom jetty, customize jetty, jetty context, context files, jetty context files -->

# 1 Jetty

## 1.1 Jetty XML

### 1.1.1 Introducting Jetty X-Powered-By, Version, and Date Headers

Here is an example Jetty Context XML file that introduces a few additional headers into each of your HTTP responses. 

> [!NOTE]
> I'm using ORDS in standalone mode, so if you are using Apache Tomcat or Weblogic, this will not work.

```xml
<?xml version="1.0"?>
<!DOCTYPE Configure PUBLIC "-//Jetty//Configure//EN" "http://www.eclipse.org/jetty/configure_10_0.dtd">
<Configure id="httpConfig" class="org.eclipse.jetty.server.HttpConfiguration">
    <Set name="sendXPoweredBy">
      <Property name= "jetty.send.xpoweredBy" default="true" />
    </Set>
    <Set name="sendServerVersion">
      <Property name="jetty.send.server.version" default="true" />
    </Set>
    <Set name="sendDateHeader">
      <Property name="jetty.send.date.header" default="true" />
    </Set>
</Configure>
```

First, create a Jetty `XML` file. Next, place it in your `/etc`[^1.1.1a] folder, prior to starting ORDS back up. In this example I've named the file `jetty_headers.xml`. I have installed and deployed ORDS locally (i.e., `localhost`), with my configuration folder named `configuration`. With the full path being:

```sh
/Users/choina/ords/configuration/global/standalone/etc/jetty_headers.xml
```

And here is a view of how my ORDS configuration folder is structured:

```sh
.
├── databases
│   └── default
│       ├── pool.xml
│       └── wallet
│           └── cwallet.sso
└── global
    ├── settings.xml
    └── standalone
        ├── access_logs
        ├── etc
        │   └── jetty_headers.xml
        ├── self-signed.key
        └── self-signed.pem
```

[^1.1.1a]: You need to create this `/etc` folder manually. As of ORDS 25.1 this folder isn't something that already exists. You might have a path similar to the one I shared above.



I can issue an HTTP `GET` request to one of my endpoints, like this one (a cURL command in this example):

```sh
curl http://localhost:8080/ords/hr/employees/
```

I see three new response headers displayed in a console output:

- `Server: Jetty(12.0.18)`
- `X-Powered-By: Jetty(12.0.18)`
- `Date: Sat, 24 2025 11:46:12 GMT`[^1.1.1b]

[^1.1.1b]: As of ORDS 25.1, and possibly all versions, ORDS will always show a request date

```sh
* Preparing request to http://localhost:8080/ords/hr/employees/
* Current time is 2025-05-24T11:46:12.857Z
* Enable automatic URL encoding
* Using default HTTP version
* Enable timeout of 30000ms
* Enable SSL validation
* Connection 9 seems to be dead
* Closing connection 9
* Hostname in DNS cache was stale, zapped
*   Trying 127.0.0.1:8080...
* Connected to localhost (127.0.0.1) port 8080 (#10)

> GET /ords/hr/employees/ HTTP/1.1
> Host: localhost:8080
> User-Agent: insomnia/11.1.0
> Accept: */*

* Mark bundle as not supporting multiuse

< HTTP/1.1 200 OK
< Server: Jetty(12.0.18)
< X-Powered-By: Jetty(12.0.18)
< Date: Sat, 24 May 2025 11:46:12 GMT
< Content-Type: application/json
< X-ORDS_DEBUG: true
< ETag: "yGRRTja1S+tUjM5cGRS/a6WnUiHoqAczoKJ0J5Ty9sAGg1QeeXN3G+BtUukHu9DWgxrpOAOaapoBAd++rVdl6g=="
< Transfer-Encoding: chunked


* Received 8.2 KB chunk
* Received 7 B chunk
* Connection #10 to host localhost left intact
```
