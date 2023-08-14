# vxlan-ovs-docker-overlay
<img src="https://cdn-images-1.medium.com/v2/resize:fit:1600/1*LRWNP9Lna0P4QkXXVfyCvA.png"/>

Here are the Ubuntu Codes used in VM1<br/><br/>
Install necessary packages (net-tools, docker.io, openvswitch-switch):<br/>
sudo apt -y install net-tools docker.io openvswitch-switch<br/>
Update the package repository:<br/>
sudo apt update<br/>
Install packages again (repeated):<br/>
sudo apt -y install net-tools docker.io openvswitch-switch<br/>
Edit a Dockerfile using the Vim text editor:<br/>
vim Dockerfile<br/><br/>
/**************************/
 
FROM ubuntu<br/>
RUN apt-get update && apt-get install -y \<br/>
iputils-ping \<br/>
iproute2<br/>
CMD ["bash"]<br/>
<br/>
/************************/<br/>
List files in the current directory:<br/><br/>
ls<br/><br/>
Run a Docker container named "docker1" with specified settings and using the "ubuntu-docker" image:<br/><br/>
sudo docker run -dit - name docker1 - net=none - privileged ubuntu-docker<br/><br/>
Build a Docker image named "ubuntu-docker" from the current directory:<br/><br/>
sudo docker build . -t ubuntu-docker<br/>
Run Docker container "docker1" again (repeated):<br/>
sudo docker run -dit - name docker1 - net=none - privileged ubuntu-docker<br/>
Run another Docker container named "docker2" with similar settings:<br/>
sudo docker run -dit - name docker2 - net=none - privileged ubuntu-docker<br/>
Check the list of running Docker containers:<br/>
sudo docker ps<br/>
Display network interface information:<br/>
ip a<br/>
Add a port to Open vSwitch and associate it with a Docker container ("docker1"):<br/><br/>
sudo ovs-docker add-port ovs-br1 eth0 docker1 - ipaddress=192.168.1.11/24<br/><br/>
Add another port to Open vSwitch and associate it with a different Docker container ("docker2"):<br/><br/>
sudo ovs-docker add-port ovs-br2 eth0 docker2 - ipaddress=192.168.2.11/24<br/><br/>
Show Open vSwitch configuration:<br/><br/>
sudo ovs-vsctl show<br/><br/>
Start an interactive shell session inside "docker1" container:<br/><br/>
sudo docker exec -it docker1 bash<br/><br/>
Configure networking within "docker1" container (add default route, update packages, ping):<br/><br/>
root@97260ccd3ab1:/# ip route add default via 192.168.1.1<br/><br/>
root@97260ccd3ab1:/# apt update<br/><br/>
root@97260ccd3ab1:/# ping 192.168.1.1<br/><br/>
PING 192.168.1.1 (192.168.1.1) 56(84) bytes of data.<br/><br/>
From 192.168.1.11 icmp_seq=1 Destination Host Unreachable<br/><br/>

root@97260ccd3ab1:/# exit<br/>
Configure Open vSwitch internal port and set IP address:<br/><br/>
sudo ovs-vsctl add-port ovs-br1 veth1 - set interface veth1 type=internal<br/>
sudo ovs-vsctl add-port ovs-br2 veth2 - set interface veth2 type=internal<br/>
sudo ip address add 192.168.1.1/24 dev veth1<br/>
sudo ip address add 192.168.2.1/24 dev veth2<br/>
Set network interfaces up and adjust the MTU:<br/>
sudo ip link set dev veth1 up mtu 1450<br/>
sudo ip link set dev veth2 up mtu 1450<br/>
Add a port to Open vSwitch and associate it with the "docker1" container (repeated):<br/>
sudo ovs-docker add-port ovs-br1 eth0 docker1 - ipaddress=192.168.1.11/24<br/>
Add a port to Open vSwitch and associate it with the "docker2" container (repeated):<br/>
sudo ovs-docker add-port ovs-br2 eth0 docker2 - ipaddress=192.168.2.11/24<br/>
Run a ping command from within the "docker1" container:<br/>
sudo docker exec docker1 ping 192.168.1.1<br/>
PING 192.168.1.1 (192.168.1.1) 56(84) bytes of data.<br/>
64 bytes from 192.168.1.1: icmp_seq=1 ttl=64 time=0.495 ms<br/><br/>

Start an interactive shell session inside the "docker2" container:<br/><br/>
sudo docker exec -it docker2 bash<br/>
Configure networking within the "docker2" container (add default route, update packages):<br/><br/>
root@e231278f5336:/# ip route add default via 192.168.2.1<br/>
root@e231278f5336:/# apt update<br/>
root@e231278f5336:/# exit<br/>
Run a ping command from within the "docker2" container:<br/><br/>
sudo docker exec docker2 ping 192.168.2.1<br/>
PING 192.168.2.1 (192.168.2.1) 56(84) bytes of data.<br/>
64 bytes from 192.168.2.1: icmp_seq=1 ttl=64 time=0.309 ms<br/>
^C<br/>
Display network statistics using netstat:<br/>
netstat -ntulp<br/>
Add a VXLAN port to Open vSwitch and set options:<br/>
sudo ovs-vsctl add-port ovs-br1 vxlan1 - set interface vxlan1 type=vxlan options:remote_ip=172.31.32.116 options:key=1000<br/>
sudo ovs-vsctl add-port ovs-br2 vxlan2 - set interface vxlan2 type=vxlan options:remote_ip=172.31.32.116 options:key=2000<br/>
Display network statistics again and filter for port 4789 (VXLAN):<br/>
netstat -ntulp | grep 4789<br/>

Here are the Ubuntu Commands used in VM2<br/>
1)Installing Required Packages:<br/>
sudo apt -y install net-tools docker.io openvswitch-switch<br/><br/>
This command installs the net-tools, docker.io, and openvswitch-switch packages using the apt package manager. These packages are necessary for networking, Docker, and Open vSwitch.<br/><br/>
2) Updating Package Information:<br/>
sudo apt update<br/><br/>
This command updates the local package repository information to ensure you have the latest package details before installing or upgrading software.<br/><br/>
3) Building a Docker Image:<br/><br/>
vim Dockerfile<br/>
/**************************/<br/>

FROM ubuntu<br/>
RUN apt-get update && apt-get install -y \<br/>
iputils-ping \<br/>
iproute2<br/>
CMD ["bash"]<br/>
/************************/<br/>
ls<br/>
Dockerfile<br/>
sudo docker run -dit - name docker3 - net=none - privileged ubuntu-docker<br/><br/>
Unable to find image 'ubuntu-docker:latest' locally 
docker: Error response from daemon: pull access denied for ubuntu-docker, repository does not exist or may require 'docker login': denied: requested access to the resource is denied.<br/>
See 'docker run - help'.<br/><br/>
4)Building a Docker Image:<br/><br/>
sudo docker build . -t ubuntu-docker<br/><br/>
These commands create a Dockerfile using the vim text editor and then build a Docker image named ubuntu-docker from the current directory. A Docker image is a lightweight, standalone, and executable software package that includes everything needed to run a piece of software.<br/><br/>
5)Creating Docker Containers:<br/><br/>
sudo docker run -dit - name docker3 - net=none - privileged ubuntu-docker<br/>
sudo docker run -dit - name docker4 - net=none - privileged ubuntu-docker<br/><br/>
These commands create two Docker containers named docker3 and docker4 from the ubuntu-docker image. They are started in detached mode (-d) and without network access (--net=none). The containers are also granted privileges (--privileged) for certain system operations.<br/><br/>
sudo docker ps<br/>
ubuntu@ip-172–31–32–116:~$ ip a<br/><br/>
6) Adding Containers to Open vSwitch Bridges:<br/>
sudo ovs-docker add-port ovs-br1 eth0 docker3 - ipaddress=192.168.1.12/24<br/>
sudo ovs-docker add-port ovs-br2 eth0 docker4 - ipaddress=192.168.2.12/24<br/>
sudo ovs-vsctl show<br/>
sudo docker exec -it docker3 bash<br/>
root@857bf7a322b4:/# ip route add default via 192.168.1.1<br/>
root@857bf7a322b4:/# apt update<br/>
0% [Connecting to archive.ubuntu.com] [Connecting to security.ubuntu.com]^C<br/><br/>
root@857bf7a322b4:/# ping 192.168.1.1<br/>
PING 192.168.1.1 (192.168.1.1) 56(84) bytes of data.<br/>
From 192.168.1.12 icmp_seq=1 Destination Host Unreachable<br/>
From 192.168.1.12 icmp_seq=2 Destination Host Unreachable<br/>
From 192.168.1.12 icmp_seq=3 Destination Host Unreachable<br/>
^C<br/><br/>
7)Setting Up Internal Interfaces and IP Addresses:<br/><br/>
sudo ovs-vsctl add-port ovs-br1 veth1 - set interface veth1 type=internal<br/>
sudo ovs-vsctl add-port ovs-br2 veth2 - set interface veth2 type=internal<br/>
sudo ip address add 192.168.1.1/24 dev veth1<br/>
sudo ip address add 192.168.2.1/24 dev veth2<br/>
sudo ip link set dev veth1 up mtu 1450<br/>
sudo ip link set dev veth2 up mtu 1450<br/><br/>
These commands create internal interfaces (veth1 and veth2) on Open vSwitch bridges (ovs-br1 and ovs-br2) and assign IP addresses to them. These interfaces are used for communication between the host and the containers.<br/><br/>
sudo ovs-docker add-port ovs-br1 eth0 docker3 - ipaddress=192.168.1.12/24<br/>
ovs-docker: Port already attached for CONTAINER=docker3 and INTERFACE=eth0<br/>
sudo ovs-docker add-port ovs-br2 eth0 docker4 - ipaddress=192.168.2.12/24<br/>
ovs-docker: Port already attached for CONTAINER=docker4 and INTERFACE=eth0<br/>
sudo docker exec docker3 ping 192.168.1.1<br/><br/>
PING 192.168.1.1 (192.168.1.1) 56(84) bytes of data.<br/>
64 bytes from 192.168.1.1: icmp_seq=1 ttl=64 time=0.425 ms<br/>
64 bytes from 192.168.1.1: icmp_seq=2 ttl=64 time=0.054 ms<br/>
64 bytes from 192.168.1.1: icmp_seq=3 ttl=64 time=0.055 ms<br/>
64 bytes from 192.168.1.1: icmp_seq=4 ttl=64 time=0.054 ms<br/>
^C
sudo docker exec -it docker4 bash<br/>
root@a980b0226cd3:/# ip route add default via 192.168.2.1<br/>
root@a980b0226cd3:/# apt update<br/>
0% [Connecting to archive.ubuntu.com] [Connecting to security.ubuntu.com]<br/>^C
root@a980b0226cd3:/# exit<br/>
exit<br/>
sudo docker exec docker2 ping 192.168.2.1<br/>
Error: No such container: docker2<br/><br/>
sudo docker exec docker4 ping 192.168.2.1<br/><br/>
PING 192.168.2.1 (192.168.2.1) 56(84) bytes of data.<br/>
64 bytes from 192.168.2.1: icmp_seq=1 ttl=64 time=0.239 ms<br/>
64 bytes from 192.168.2.1: icmp_seq=2 ttl=64 time=0.056 ms<br/>
64 bytes from 192.168.2.1: icmp_seq=3 ttl=64 time=0.057 ms<br/>
^C<br/>
 
sudo ovs-vsctl add-port ovs-br1 vxlan1 - set interface vxlan1 type=vxlan options:remote_ip=172.31.38.170 options:key=1000<br/>
sudo ovs-vsctl add-port ovs-br2 vxlan2 - set interface vxlan2 type=vxlan options:remote_ip=172.31.38.170 options:key=2000<br/>
