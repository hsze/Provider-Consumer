# Provider-Consumer Connectivity 
In this era of digitization, it has become increasingly common for a businesses to be a provider of a cloud-based service to other businesses, the consumers of the service.  There are several models to interconnect the provider and consumer. 
## Option 1: PrivateLink Service
In this model, both the provider and consumer have Azure presence with subscriptions and Virtual Networks. The provider builds a service which sits behind an Azure Standard Load Balancer or Application Gateway. The provider then creates a Private Link service and publishes the service URI to the consumer. The consumer creates a Private Endpoint by specifying the Private Link service ID. An approval workflow is initiated, and connectivity is established.

The configuration is illustrated below, with the Provider Network, and the Consumer Network.

![PLS](/Diagrams/Option1.png)

Benefits: 
-	Recommended approach for scale and security
-	Any NAT issues automatically handled
Drawbacks:
-	A unidirectional service.  If provider needs to initiate connection back to consumer, another Private Link service would need to be built in reverse direction

## Option 2: VNET Peering
In this model, the provider and consumer also have presence in Azure, with VNETs in each subscription.  Peering is established across subscription and tenants.  Once a peering is established, the provider and consumer will have reachability with each other.  Network Security Groups may be used at the subnet level to provide traffic flow, and a combination of Firewall and Web Application Firewall may be used to protect ingress and egress flows, on either side. 

The VNET peering configuration between provider and consumer is illustrated below.  Note, there is a variation of the configuration where the VNETs are not connected by peering but by IPSec tunnel overlay.

![Peering](/Diagrams/Option2.png)

Benefits:
-	Cross subscription peering is easy to set up
Drawbacks:
-	There are no inter-VNET route filtering capabilities, any prefixes added to each VNET and even OnPrem – even by accident – will propagate across peers
-	Address Overlap considerations:  need to ensure the peered VNETs and the onprem networks do not have overlapping addresses

## Option 3: Cross subscription/tenant ExpressRoute Connection 
In this model, the consumer has an ExpressRoute circuit which connects to their On Prem.  Rather than building a connection to their subscription’s VNET, an authorization key is created and given to the Provider.  The Provider build the ExpressRoute Gateway in their subscription and redeems the authorization to build a Connection object to the circuit.  

As with the VNET Peering Option, NSGs, Firewalls and Web Application Firewalls may be used to protect traffic.   

![ER](/Diagrams/Option3.png)

Benefits:  
-	Easy to configure
Drawbacks: 
-	No route filtering with ExpressRoute Private Peering. Not only are prefixes from OnPrem propagated from Consumer to Provider, any other VNET prefixes which may also be connected to the same circuit would also be reflected by ExpressRoute
-	Need to be careful with any IP address overlap

