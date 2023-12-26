# namespace-networking
## what is network namespace?
network namespace is making aprivate virtual network that is isolated from another network 
## goal of this toturial is to make connection between differant namespace and make them connected to the internet.
### 1- craeting namespaces <br />
    `sudo ip netns add namespace-name `

2- to list all network namespace  <br />
    `ip netns `  

 3- to view the network inetrface within the namespace  <br />
     **ip netns excec namespace_name ip link**

4- to make coonectivity between namespaces
   * we need to craete virtual switch inside the host ( using differnt solution here we will use the native solution linux bridge )
      to create the virtual switch **ip link add v-net-0 type bridge**  <br />
      to bring this interface up **ip link set v-net-0 up**  <br />
      to make alink between namespace and virtual switch **ip link add veth-red type veth peer name veth-red-br**  <br />
      to attach the link to the namespace **ip link set veth-red red**  <br />
      to attach the link to the virtual switch **ip link set veth-red-br master v-net-0**  <br />
      to attach ip address to the interface of the namespace **ip -n red addr add 192.168.15.1 dev veth-red**  <br />
      turn the virual ethernet of red namespace app **ip -n red link set veth-red up**  <br />
      follow the same procedure to connect the reset namespace to the virtual network we have craeted   <br />
      now all namespaces can connect to each others  <br />

     5- we want to reach to the namespaces from our local host  <br />
       remeber that namespaces are in differnet network rather than the local host network so what we want is to make link between the two networks
       in the local host **ip addr add 192.168.15.5/24 dev v-net-0**  <br />
     
