<!-- jetty, jetty.xml, standalone, jetty configuration, extend jetty, extending jetty, custom jetty, customize jetty, jetty context, context files, jetty context files -->

# 1 Jetty

## 1.1 Jetty XML

### 1.1.1 Introducting Jetty X-Powered-By, Version, and Date Headers

Here is an example of a Jetty Context XML file that adds additional headers to your HTTP responses.

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

> [!NOTE]
> I'm using ORDS in standalone mode, so if you are using Apache Tomcat or Weblogic, this will not work.

To test this out, first, create a Jetty `XML` file and populate the body with the contents in the above example. In this example, I've named the file `jetty_headers.xml`.

Next, place the file in your `/etc`[^1.1.1a] folder. Once there, start ORDS back up (i.e., `ords serve`). I have installed and deployed ORDS locally (i.e., `localhost`), with my configuration folder named `configuration`. With the full path being:

```sh
/Users/choina/ords/configuration/global/standalone/etc/jetty_headers.xml
```

For reference, here is a view of how my ORDS configuration folder is structured:

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

[^1.1.1a]: You need to create this `/etc` folder manually. As of ORDS 25.1, this folder isn't something that already exists. You might have a path similar to the one I shared above.

With everything in place, I can issue a request to one of my ORDS endpoints. In this case, I've created a simple `GET` Handler that queries the `EMP` table. The actual response isn't the focus, but rather the response headers.

I used cURL for this:

```sh
curl http://localhost:8080/ords/hr/employees/
```

In the response, I now see three new response headers displayed in a console output[^1.1.1b]:

- `Server: Jetty(12.0.18)`
- `X-Powered-By: Jetty(12.0.18)`
- `Date: Sat, 24 2025 11:46:12 GMT`

[^1.1.1b]: You may notice the Date header is returned in GMT/Zulu. This will always be the case with ORDS. ORDS will always return data and times in GMT/Zulu format. For reference, if you independently alter the `user.timezone` JVM Option, this would only change how ORDS interprets dates and times where no timezone is attached. The Oracle Database `Date` and `Timestamp` datatypes are two examples where the timezone is not indicated.

Below is a console log printout showing the full details of the request and response (excluding the response body):

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

Arguably, the `Server`, `X-Powered-By`, and `Date` response headers could be considered functionally useless. However, for logging purposes, especially when it comes to huge applications or microservices more data can aid in quicker root-cause analyses.

In any case, pretty neat how you can easily add enhanced functionality to ORDS standalone with a few lines of `XML`.

#### 1.1.1 References

- [Deploying Jetty Context XML Files](https://jetty.org/docs/jetty/12/operations-guide/deploy/index.html)
- [About the HttpConfiguration Class](https://javadoc.jetty.org/jetty-12/org/eclipse/jetty/server/HttpConfiguration.html) (used in this example)
- ORDS Installation and Configuration Guide: [Using Jetty XML Configuration Files](https://docs.oracle.com/en/database/oracle/oracle-rest-data-services/25.1/ordig/miscellaneous-configuration-options-of-ORDS.html#GUID-A3798C5A-C6C6-40A2-826F-CE1CF0B8DDE2)
