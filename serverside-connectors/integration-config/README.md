# Downloading the Integration Configuration
The KnownUser library needs the Triggers and Actions to know which pages to protect and which queues to use. 
These Triggers and Actions are specified in the Go Queue-it self-service portal.

There are 3 possible ways you can retrieve the integration config information and provide it for the Known User SDK:

## 1. Time based pulling
![Configuration Provider flow](https://github.com/queueit/Documentation/blob/main/serverside-connectors/integration-config/ConfigProviderExample.png)

In this method, you would have a long running tasks retrieving the latest version of published integration with a sepecified time interval from Queue-it repository with the address **https://[your-customer-id].queue-it.net/status/integrationconfig/secure/[your-customer-id]** then cache and reuse the retrieved value until the next interval. 

You will need to implement this yourself, but we do have example code for some languages, you can look at for inspiration (please check individual repos for details). Your solution should cache the downloaded config file for 5 - 10 minutes to limit number of download requests. Also you should NEVER EVER download the configuration as part of the request handling. It should be a backgroud process downloading this file.

**To prevent unauthorized access to your config file make sure you enable “Secure integration config” setting in the Go Queue-it self-service portal** then provide your API key in the request header to retrieve integration config.

## 2. Pushing from Queue-it Go self-service to your platform
In this method, you will implement an HTTP endpoint to receive and update integration configuration in your infrastructure. Ensure that this endpoint address is entered in the integration settings in the Go Queue-it self-service portal. Then after changing and publishing the integration config using the Go Queue-it self-service portal we will send the new configuration to your specified endpoint.
The push performed when you publish your latest version from the Go Queue-it self-service portal will be a HTTP PUT request containing integration data needed by the KnownUser SDK. This data will be JSON formatted with a hex-encoded version of the integration config and a SHA256 hash of that value based on your secret key.
1st step is to hex-decode the "integrationInfo" field to get the integration configuration. 
2nd step is to verify the authenticity of the request performing a SHA256 hashing of the "integrationInfo" field (the orginal hex-encoded field) using your secret key and then comparing the result with the "hash" field (if identical the request is authentic)

## 3. Manually updating integration configuration
In this method, after changing and publishing your configuration using the Go Queue-it self-service portal, you are able to download the file and then manually copy and paste it to your infrastructure. You can find your configuration file here **https://[your-customer-id].queue-it.net/status/integrationconfig/[your-customer-id]** or via secure link (*) **https://[your-customer-id].queue-it.net/status/integrationconfig/secure/[your-customer-id]** after a successful publish.

## How to download integration config with Api Key
Integration configuration contains valuable information like triggers and actions. Anyone can download the configuration by knowing the URL because it does not require any authentication. You can protect integration configurations by enabling the “**Secure integration config**” setting, so only legitimate systems can download it by providing a valid API key.

1. You need to enable “**Secure integration config**” setting in the Go Queue-it self-service portal.
2. You need to decorate the request by adding API key in the request header. You can get API key in the Go Queue-it self-service portal.
3. Remember to add Host header in the request if your framework doesn't do that automatically. A missing host header will result in a 400 Bad Request response.
   
curl --request GET https://[your-customer-id].queue-it.net/status/integrationconfig/secure/[your-customer-id] --header "api-key: [Customer API-Key]" --header "Host: queue-it.net"