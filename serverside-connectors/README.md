# Queue-it server-side connectors (KnownUser.V3 SDKs)
Queue-it's connector SDKs are used to ensure that end users cannot bypass the queue by adding a server-side integration to your webserver.

## Introduction
When a user is redirected back from the queue to your website, the queue engine can attach a query string parameter (`queueittoken`) containing some information about the user.

The most important fields of the `queueittoken` are:

 - q: the users unique queue identifier
 - ts: a timestamp of how long this redirect is valid
 - h: a hash of the token

The high level logic is as follows:

![The KnownUser validation flow](https://github.com/queueit/Documentation/blob/master/serverside-connector/KnownUserFlow.png)

 1. User requests a page on your server
 2. The validation method sees that the has no Queue-it session cookie and no `queueittoken` and sends him to the correct queue based on the configuration
 3. User waits in the queue
 4. User is redirected back to your website, now with a `queueittoken`
 5. The validation method validates the `queueittoken` and creates a Queue-it session cookie
 6. The user browses to a new page and the Queue-it session cookie will let him go there without queuing again

## How to validate a user
To validate that the current user is allowed to enter your website (has been through the queue) these steps are needed:

 1. Providing the queue configuration to the KnownUser validation
 2. Validate the `queueittoken` and store a session cookie

### 1. Providing the queue configuration
The recommended way is to use the Go Queue-it self-service portal to setup the configuration. 
The configuration specifies a set of Triggers and Actions. A Trigger is an expression matching one, more or all URLs on your website. 
When a user enter your website and the URL matches a Trigger-expression the corresponding Action will be triggered. 
The Action specifies which queue the users should be sent to. 
In this way you can specify which queue(s) should protect which page(s) on the fly without changing the server-side integration.
More details can be found under [Documentation](https://github.com/queueit/Documentation/blob/master/Documentation/)

### 2. Validate the `queueittoken` and store a session cookie
To validate that the user has been through the queue, use the `KnownUser.ValidateRequestByIntegrationConfig()` method. 
This call will validate the timestamp and hash and if valid create a "QueueITAccepted-SDFrts345E-V3_[WaitingRoomId]" cookie with a TTL as specified in the configuration.
If the timestamp or hash is invalid, the user is send back to the queue.

## Implementation
The KnownUser validation must be done on *all requests except requests for static and cached pages, resources like images, css files and ...*. 
So, if you add the KnownUser validation logic to a central place like in Startup.cs, then be sure that the Triggers only fire on page requests (including ajax requests) and not on e.g. image.

Click link below to find example code for your webserver:

- IIS (ASP.NET)
- IIS, Kerstel (ASP.NET Core)
- Tomcat or other Servlet compatible webserver (Java)
- Apache as application (PHP)
- Apache as load balancer (Lua)
- Rails (Ruby)
- Django (Python)
- NGINX (Lua)

### Protecting ajax calls
If you need to protect AJAX calls beside page loads you need to add the below JavaScript tags to your pages:
```
<script type="text/javascript" src="//static.queue-it.net/script/queueclient.min.js"></script>
<script
  data-queueit-intercept-domain="{YOUR_CURRENT_DOMAIN}"
  data-queueit-intercept="true"
  data-queueit-c="{YOUR_CUSTOMER_ID}"
  type="text/javascript"
  src="//static.queue-it.net/script/queueconfigloader.min.js">
</script>
```

## Alternative Implementation

### Queue configuration

If your application server (maybe due to security reasons) is not allowed to do external GET requests, then you have three options:

1. Manually download the configuration file from Queue-it Go self-service portal, save it on your application server and load it from local disk
2. Use an internal gateway server to download the configuration file and save to application server
3. Specify the configuration in code without using the Trigger/Action paradigm. In this case it is important *only to queue-up page requests* and not requests for resources.

The following are examples of how to specify the configuration in code:

- IIS (ASP.NET)
- IIS, Kerstel (ASP.NET Core)
- Tomcat or other Servlet compatible webserver (Java)
- Apache as application (PHP)
- Apache as load balancer (Lua)
- Rails (Ruby)
- Django (Python)
- NGINX (Lua)