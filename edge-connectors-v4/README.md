# Queue-it edge connectors
The Queue-it edge connectors ensures that end-users are not able to access your online application without first going through the queue for any and all protected areas and paths on your sites. The queue system is implemented by adding a server-side (request-level) integration that protects your online application by redirecting users to a waiting room according to web traffic settings in the Queue-it GO Platform. After the integration is complete, queue system behavior and operations are managed in Queue-it's Go Platform and/or via the Queue-it Admin API.

## Introduction
When a user is redirected back from the queue to your cloud infrastucture, the queue engine have attached a query string parameter (`queueittoken`) containing some information about the user.

The most important fields of the `queueittoken` are:

 - q - the users unique queue identifier (if queue-page was shown)
 - ts - a timestamp of how long this redirect is valid
 - h - a hash of the token

The high level logic is as follows:

![The KnownUser validation flow](https://github.com/queueit/Documentation/blob/main/edge-connectors-v4/connector-sequence-edge-v4.png)

 1. User requests a page.
 2. The validation method sees that user has no Queue-it session cookie and no `queueittoken` and sends him to the correct waitingroom with a valid `enqueuetoken` based on the configuration.
 3. User waits in the queue.
 4. User is navigated back to your website, now with a `queueittoken`.
 5. The validation method validates the `queueittoken` and creates a Queue-it session cookie.
 6. The user browses to a new page and the Queue-it session cookie will let him go there without queuing again.

## Protecting ajax calls
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
Reach out to support@queue-it.com to get the latest relevant repository and get started.
