![GNS3, gns3topology](Topologies/GNS3.png)

# Overloading Encapsulation with VXLAN by Extending Broadcast domain to remote branch over  MPLS/VPN6 BackBone  
(Reboot with my precedent **VXLAN** project with more details and some improvements).

So ! First question ! Why **WAYNE  ENTREPRISE** ? 
Obvisiouly 'cause i'm DCU Comics fan and ...

#### Software Requirements :


> * GNS3 ( FOR MPLS/VPN6 BACKBONE ) 
> * VMare Workstatio Pro ( For simulate distributed network )
> * Wireshark
> * Docker Container 
> * Ubuntu server 
> * OpenVSwitch 

#### Underlay Topology : 

![Underlay topology](Topologies/underlay2.png  "underlay")

#### Overlay Topology : 

![Underlay topology](Topologies/overlay2.png "overlay")


## So let's go !!! 

### MPLS/VPN6 Deployement :

 ! Find all router config scripts and Addressing Table in "CiscoStartupConfigRouters/" directory 
 
 > > >  > ASSUME THAT YOU HAVE ALREADY YOUR MPLS BACKBONE !!!
 
##  1) STEP 1 : 
 
 Check if HQ(172.16.1.3) and Branch(172.16.2.3) can talk and Check if flows travel through MPLS/VPN6 BACKBONE
 
 ![Checkping ](WiresharkCap/checkping.png)
 
 * 1 Reply from **Branch** to **HQ**
 * 2 Label Tag 
 
## 2) STEP 2 : 

- Building of LAN infrastructure on HQ : 

	* Setup Bridge instance "switch" ( OpenVSwitch instance ) : 
	
	> ` # ovs-vsctl add-br switch `
	
	* Setup Nginx Web server "webserver"  ( docker container instances ) :
	
	> ` # docker run --name webserver --net none -P -d ultron11/nginx  `
	
	* Setup VLANs interfaces on bridge instance as gateway ( vlan1 : 10.10.10.0/24 and vlan2 : 10.1020.0/24 ) :
	> ` # ovs-vsctl add-port switch vlan10 tag=10 -- set interface vlan10 type=internal`
	> ` # ovs-vsctl add-port switch vlan20 tag=20 -- set interface vlan20 type=internal`
	* Setup link between "switch" and "webserver" ( 1.1.1.0/30) :
	> ` # ovs-vsctl add-port switch vlan1 tag=1 -- set interface vlan1 type=internal`
	* Configuration of VLAN1 interface in netplan file.
	* Setup web server on the link : 
	> ` # ovs-docker add-port switch eth1 webserver --ipaddress=1.1.1.2/24 --gateway=1.1.1.1`
	> ` # ovs-docker set-vlan switch eth1 webserver 1`
	
	
	## CHECKING ! 
	 
	 - ip addresses configuration on 'switch' :
	 
	 > ` # ip add `
	 
	 ``` 18: vlan10: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN group default qlen 1000
    link/ether a2:df:3d:8b:74:6c brd ff:ff:ff:ff:ff:ff
    inet 10.10.10.1/24 brd 10.10.10.255 scope global vlan10
       valid_lft forever preferred_lft forever
    inet6 fe80::a0df:3dff:fe8b:746c/64 scope link 
       valid_lft forever preferred_lft forever
19: vlan20: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN group default qlen 1000
    link/ether c6:92:23:07:86:a7 brd ff:ff:ff:ff:ff:ff
    inet 10.10.20.1/24 brd 10.10.20.255 scope global vlan20
       valid_lft forever preferred_lft forever
    inet6 fe80::c492:23ff:fe07:86a7/64 scope link 
       valid_lft forever preferred_lft forever
20: vlan1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN group default qlen 1000
    link/ether 1e:81:60:2a:f3:80 brd ff:ff:ff:ff:ff:ff
    inet 1.1.1.1/24 brd 1.1.1.255 scope global vlan1
       valid_lft forever preferred_lft forever
    inet6 fe80::1c81:60ff:fe2a:f380/64 scope link 
       valid_lft forever preferred_lft forever ```
	 


	
	
	



 
  
 
 
 








 



 
