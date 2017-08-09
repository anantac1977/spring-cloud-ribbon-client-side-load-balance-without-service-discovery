This project demonstrates the client side load balancing without service discovery.
 
Load balancing is to distribute the workload across multiple computing resources.

In a cloud native architecture, load balancing plays a very important role, as compared to that in a non cloud native architecture. Typically, in a non cloud based architecture, we have multiple instances of the application with a single load balancer. But in a cloud native architecture we have multiple instances of multiple services having multiple load balancers.

The load balancing can be categorized into server-side load balancing and client-side load balancing.
In a server-side load balancing, the request goes to a server(load balancer), that decides to which instance of the application, the request should be sent. With Client side load balancing there is no intermediary. The client itself decides to which of the multiple application instances or services a request needs to be sent. With client side load balancing there is no extra hop as it does with the server side load balancing. Both of the load balancing strategies can implement various algorithms. A server side load balancing can be centralized or distributed. But a client side load balancing is always distributed in nature. With the above analysis, client side load balancing is a natural fit for cloud native architectures.

Ribbon is an inter process communication library, that has got built in software load balancers. Spring cloud adds full integration with Netflix Ribbon to Spring's RestTemplate class.
Spring cloud's Netflix Ribbon support adds two new annotations, "@LoadBalanced" and "@RibbonClient". "@LoadBalanced", is used while creating a RestTemplate to support load balancing. It marks the RestTemplate as a load balanced RestTemplate. "@RibbonClient", is mainly used for configuration purposes, when there is no service discovery, involved.

In the @Configuration class we define @RibbonClient(name="serviceName"). The value of the name attribute of the @RibbonClient annotation should be same as in the configuration as well as in the URL of the RestTemplate. In the application.properties/yml, define two new properties 

#This property indicates ribbon to disable service discovery support.
<ribbon_client_name>.ribbon.eureka.enabled=false

#This property sets the service instances distributed locations 
<ribbon_client_name>.ribbon.listOfServers=http://<host1>:<port1>,http://<host2>:<port2>

The <ribbon_client_name> is the same as the name attribute as set for the @RibbonClient.

Suppose my-service is a service which is running on port 9000 at mycompany.com, and is discoverable via service discovery. Further, assume that there are two instances of the service running on two different servers. Instead of using restTemplate.getForEntity("http://mycompany.com:9000/u/1",...) or restTemplate.getForEntity("http://192.168.1.98:9000/u/1",...), we would rather use restTemplate.getForEntity("http://my-service/u/1",...), which is a logical identifier to represent the service. This logical identifier is the same name with which the service is registered with the service discovery server. At runtime the RestTemplate will function as the client-side load balancer and it would use the service discovery to resolve the relocation of my-service instances and will use the configured load balancing algorithm to distribute the load, between them.

