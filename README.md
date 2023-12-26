# namespace-networking
![digram image](https://github.com/ahmedhadhoud/namespace-networking/blob/2600434af414339b995175c641504354f35e25b7/network-ns.drawio%20(1).png))
## what is network namespace?
network namespace is making aprivate virtual network that is isolated from another network 
## goal of this toturial is to make connection between differant namespace and make them connected to the internet.
### 1- craeting namespaces <br />
       sudo ip netns add namespace-name

### 2- to list all network namespace  <br />
       sudo ip netns

### 3- to view the network inetrface within the namespace  <br />
 `ip netns excec namespace_name ip link `

### 4- to make coonectivity between namespaces
#### - we need to craete virtual switch inside the host ( using differnt solution here we will use the native solution linux bridge )
-  to create the virtual switch  <br />

     `ip link add v-net-0 type bridge`  <br />

-   to bring this interface up   <br />

     `ip link set v-net-0 up` <br />

-  to make alink between namespace and virtual switch <br />

     `ip link add veth-red type veth peer name veth-red-br`    <br />

-  to attach the link to the namespace <br />

     `ip link set veth-red red`  <br />
 
-  to attach the link to the virtual switch <br />

    `ip link set veth-red-br master v-net-0`  <br />

-  to attach ip address to the interface of the namespace <br />

    `ip -n red addr add 192.168.15.1 dev veth-red` <br />

-  turn the virual ethernet of red namespace app <br />

     `ip -n red link set veth-red up`  <br />

-  follow the same procedure to connect the reset namespace to the virtual network we have craeted   <br />

-  now all namespaces can connect to each others  <br />


  ### 5- we want to reach to the namespaces from our local host  <br />
  
remeber that namespaces are in differnet network rather than the local host network so what we want is to make link between the two networks
in the local host <br />
`ip addr add 192.168.15.5/24 dev v-net-0` <br />

### 6- if you want the red namespace to reach another host in the local network <br />

first we need to make an gateway for the red namespace to be reachable from outside it's network where the local host is the gateway of our namespaces <br /> 

   `ip netns exec red route add 192.168.1.0/24 via 192.168.15.5` <br />
   
now red namespace can reach the other host but no reply will go from red namespace to outer hosts as in the ip table of namespace has no route to outside the local network so we will use NAT to forward traffic to outside   <br />  

  `iptables -t nat -A POSTROUTING -s 192.168.15.0/24 -j MASQUERADE`  <br />   

 ### 7- we need the red namespace to connect to the internet  <br />

first thing we want the red namespace to reach the internet so we will add route in the route table of red namespace <br /> 

   `ip netns exec red ip route add default via 192.168.15.5` <br />

 next we need the internet to access the app in the namespace via pot 80 using portforward method  <br />  

   ` iptables -t nat -A PREROUTTING --dport 80 --to-destination 192.168.15.1:80 -j DNAT`

 
 
