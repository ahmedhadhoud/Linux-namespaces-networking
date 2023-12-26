# namespace-networking
## what is network namespace?
network namespace is making aprivate virtual network that is isolated from another network 
## goal of this toturial is to make connection between differant namespace and make them connected to the internet.
1- craeting namespaces <br />
    **ip netns add namespace-name** 

2- to list all network namespace  <br />
     **ip netns**

 3- to view the network inetrface within the namespace  <br />
     **ip netns excec namespace_name ip link**
