# Queue-it server-side connectors (KnownUser.V3 SDKs)
Queue-it's connector SDKs are used to ensure that end users cannot bypass the queue by adding a server-side integration to your web server.

## Introduction
When a user is redirected back from the queue to your website, the queue engine can attach a query string parameter (`queueittoken`) containing some information about the user.

The most important fields of the `queueittoken` are:

 - q - the users unique queue identifier (if queue-page was shown)
 - ts - a timestamp of how long this redirect is valid
 - h - a hash of the token

The high level logic is as follows:

![The KnownUser validation flow](https://github.com/queueit/Documentation/blob/main/serverside-connectors/connector-sequence-v4.png)

 1. User requests a page on your server
 2. The validation method sees that user has no Queue-it session cookie and no `queueittoken` and sends him to the correct waitingroom with a valid `enqueuetoken` based on the configuration.
 3. User waits in the queue
 4. User is redirected back to your website, now with a `queueittoken`
 5. The validation method validates the `queueittoken` and creates a Queue-it session cookie
 6. The user browses to a new page and the Queue-it session cookie will let him go there without queuing again

## How to validate a user
To validate that the current user is allowed to enter your website (has been through the queue) these steps are needed:

 1. Providing the queue configuration to the KnownUser validation
 2. Validate the `queueittoken` and store a session cookie

### 1. Providing the integration configuration
The recommended way is to use the Go Queue-it self-service portal to setup the configuration. The configuration specifies a set of Triggers and Actions. A Trigger is an expression matching one, more or all URLs on your website. 
When a user enter your website and the URL matches a Trigger-expression the corresponding Action will be triggered. 
The Action specifies which queue the users should be sent to. 
In this way you can specify which queue(s) should protect which page(s) on the fly without changing the server-side integration. 

If you want to know more about how Triggers and Actions work then go [here](https://github.com/queueit/Documentation/tree/main/serverside-connectors/integration-actions)

Details on getting integration configuration can be found [here](https://github.com/queueit/Documentation/tree/main/serverside-connectors/integration-config)

_Alternatives_

If your application server is not allowed to do external GET requests (maybe due to security reasons), then you have three options:

1. Manually download the integration configuration file from Queue-it Go self-service portal, save it on your application server and load it from local disk.
2. Use an internal gateway server to download the integration configuration file and save to application server.
3. Specify the configuration in code without using the Trigger/Action paradigm. In this case it is important *only to queue-up page requests* and not requests for resources. **For this option please visit specific repository to find example code (inline queue configuration)**.

### 2. Validate the `queueittoken` and store a session cookie
To validate that the user has been through the queue, use the `KnownUser.ValidateRequestByIntegrationConfig()` method. 
This call will validate the timestamp and hash and if valid create a "QueueITAccepted-SDFrts345E-V3_[WaitingRoomId]" cookie with a TTL as specified in the configuration.
If the timestamp or hash is invalid, the user is send back to the queue.

## Implementation
The KnownUser validation must be done on *all requests except requests for static and cached pages, resources like images, css files and ...*. 
So, if you add the KnownUser validation logic to a central place like in Startup.cs, then be sure that the configured Integration Triggers only fire on page requests (including ajax requests) and not on e.g. images.

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


## Getting started
Click a link below to navigate to relevant repository to get started:

- [ASP.NET (IIS)](https://github.com/queueit/KnownUser.V3.ASPNET)
- [ASP.NET Core (IIS, Kestrel)](https://github.com/queueit/KnownUser.V3.ASPNETCORE)
- [Java (Tomcat, GlassFish or other Servlet compatible web server)](https://github.com/queueit/KnownUser.V3.JAVA)
- [PHP](https://github.com/queueit/KnownUser.V3.PHP)
- [Lua (Apache, NGINX)](https://github.com/queueit/KnownUser.V3.Lua)
- [Ruby (Rails)](https://github.com/queueit/KnownUser.V3.RubyOnRails)
- [Python (Django)](https://github.com/queueit/KnownUser.V3.Python)
- [JavaScript (Node.js/Express)](https://github.com/queueit/KnownUser.V3.Javascript)
