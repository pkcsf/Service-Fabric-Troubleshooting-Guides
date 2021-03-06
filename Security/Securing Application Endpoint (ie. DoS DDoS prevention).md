## Securing Application Endpoint (ie. DoS DDoS prevention)

The first thing to clarify is what you are trying to protect.  Are you trying to protect your application endpoints, or are you trying to protect the Service Fabric Management endpoint (ie. Service Fabric Explorer)?  Below assumes you are talking about your application endpoints since the management endpoint is secured and potential attackers couldn’t complete the initial handshake.


For protecting your application this really isn’t a Service Fabric question per-se.  Service Fabric manages the deployment, monitoring, failover, etc of your applications running in the cluster, but it has very little to do with HTTP communication coming into your application (other than doing the initial HTTP.sys binding).  SF can’t help mitigate HTTP type attacks because it is not injected anywhere in the communication path between the client and your application.

The protection you are looking for will come in a few layers:

1.  Azure platform.  We have various DoS protections built into the Azure platform itself.  You can find information at https://azure.microsoft.com/en-us/blog/microsoft-azure-network-security-whitepaper-version-3-is-now-available/ and https://azure.microsoft.com/en-us/documentation/videos/azurecon-2015-building-secure-virtual-networks-in-azure/. 

2. Web Application Firewall (WAF).  Application Gateway provides an optional Web Application Firewall layer which adds automatic protection for the most common types of vulnerabilities.  You can read more at https://azure.microsoft.com/en-us/documentation/articles/application-gateway-webapplicationfirewall-overview/.  There are also 3rd party web application firewalls such as Barracuda or Kemp (ie. https://azure.microsoft.com/en-us/marketplace/partners/barracudanetworks/waf/).  This type of protection can be implemented into your deployment template for your cluster, and it adds a lot of extra functionality, but keep in mind that it also adds extra cost and complexity.

3. Application layer.  With your Web Roles you are hosted in IIS which provides a lot of easy configuration of the various request limits to mitigate slow HTTP POST, but with Service Fabric your web server is probably self-hosted OWIN.  You will get a lot of good default values to protect against most attacks from http.sys and the HTTPListener, but you can check out https://blogs.msdn.microsoft.com/tilovell/2015/03/11/request-and-connection-throttling-when-self-hosting-with-owinhttplistener/ for more information and additional tweaks you can make.  This type of protection would be built into your application itself rather than in the template during cluster deployment. 
