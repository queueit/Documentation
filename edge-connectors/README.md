# Queue-it edge connectors
Queue-it's edge connectors are used to ensure that end users cannot bypass the queue by adding integration code to your cloud infrastucture.

## Introduction
When a user is redirected back from the queue to your cloud infrastucture, the queue engine can attach a query string parameter (`queueittoken`) containing some information about the user.

The most important fields of the `queueittoken` are:

 - q - the users unique queue identifier
 - ts - a timestamp of how long this redirect is valid
 - h - a hash of the token

The high level logic is as follows:

![The KnownUser validation flow](https://github.com/queueit/Documentation/blob/main/edge-connectors/connector-sequence-edge.png)

 1. User requests a page via your cloud infrastucture.
 2. The validation method sees that user has no Queue-it session cookie and no `queueittoken` and sends him to the correct queue based on the configuration.
 3. User waits in the queue.
 4. User is redirected back to your website, now with a `queueittoken`.
 5. The validation method validates the `queueittoken` and creates a Queue-it session cookie.
 6. The user browses to a new page and the Queue-it session cookie will let him go there without queuing again.

## Getting started
Click a link below to navigate to relevant repository to get started:

- [Akamai](https://github.com/queueit/KnownUser.V3.Akamai)
- [Cloudflare](https://github.com/queueit/KnownUser.V3.Cloudflare)
- [Cloudfront](https://github.com/queueit/KnownUser.V3.Cloudfront)
- [Fastly](https://github.com/queueit/KnownUser.V3.Fastly)
