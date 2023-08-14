# vxlan-ovs-docker-overlay
<img src="https://cdn-images-1.medium.com/v2/resize:fit:1600/1*LRWNP9Lna0P4QkXXVfyCvA.png"/>

Here are the Ubuntu Codes used in VM1
Install necessary packages (net-tools, docker.io, openvswitch-switch):
sudo apt -y install net-tools docker.io openvswitch-switch
Update the package repository:
sudo apt update
Install packages again (repeated):
sudo apt -y install net-tools docker.io openvswitch-switch
Edit a Dockerfile using the Vim text editor:
vim Dockerfile
/**************************/
# Use the official Ubuntu base image
FROM ubuntu
# Install necessary packages
RUN apt-get update && apt-get install -y \
iputils-ping \
iproute2
# (Optional) You can install other packages here if needed.
# Set the default command to run when the container starts (bash in this case)
CMD ["bash"]
/************************/
List files in the current directory:
ls
Run a Docker container named "docker1" with specified settings and using the "ubuntu-docker" image:
sudo docker run -dit - name docker1 - net=none - privileged ubuntu-docker
Build a Docker image named "ubuntu-docker" from the current directory:
sudo docker build . -t ubuntu-docker
Run Docker container "docker1" again (repeated):
sudo docker run -dit - name docker1 - net=none - privileged ubuntu-docker
Run another Docker container named "docker2" with similar settings:
sudo docker run -dit - name docker2 - net=none - privileged ubuntu-docker
Check the list of running Docker containers:
sudo docker ps
Display network interface information:
ip a
Add a port to Open vSwitch and associate it with a Docker container ("docker1"):
sudo ovs-docker add-port ovs-br1 eth0 docker1 - ipaddress=192.168.1.11/24
Add another port to Open vSwitch and associate it with a different Docker container ("docker2"):
sudo ovs-docker add-port ovs-br2 eth0 docker2 - ipaddress=192.168.2.11/24
Show Open vSwitch configuration:
sudo ovs-vsctl show
Start an interactive shell session inside "docker1" container:
sudo docker exec -it docker1 bash
Configure networking within "docker1" container (add default route, update packages, ping):
root@97260ccd3ab1:/# ip route add default via 192.168.1.1
root@97260ccd3ab1:/# apt update
root@97260ccd3ab1:/# ping 192.168.1.1
PING 192.168.1.1 (192.168.1.1) 56(84) bytes of data.
From 192.168.1.11 icmp_seq=1 Destination Host Unreachable

root@97260ccd3ab1:/# exit
Configure Open vSwitch internal port and set IP address:
sudo ovs-vsctl add-port ovs-br1 veth1 - set interface veth1 type=internal
sudo ovs-vsctl add-port ovs-br2 veth2 - set interface veth2 type=internal
sudo ip address add 192.168.1.1/24 dev veth1
sudo ip address add 192.168.2.1/24 dev veth2
Set network interfaces up and adjust the MTU:
sudo ip link set dev veth1 up mtu 1450
sudo ip link set dev veth2 up mtu 1450
Add a port to Open vSwitch and associate it with the "docker1" container (repeated):
sudo ovs-docker add-port ovs-br1 eth0 docker1 - ipaddress=192.168.1.11/24
Add a port to Open vSwitch and associate it with the "docker2" container (repeated):
sudo ovs-docker add-port ovs-br2 eth0 docker2 - ipaddress=192.168.2.11/24
Run a ping command from within the "docker1" container:
sudo docker exec docker1 ping 192.168.1.1
PING 192.168.1.1 (192.168.1.1) 56(84) bytes of data.
64 bytes from 192.168.1.1: icmp_seq=1 ttl=64 time=0.495 ms

Start an interactive shell session inside the "docker2" container:
sudo docker exec -it docker2 bash
Configure networking within the "docker2" container (add default route, update packages):
root@e231278f5336:/# ip route add default via 192.168.2.1
root@e231278f5336:/# apt update
root@e231278f5336:/# exit
Run a ping command from within the "docker2" container:
sudo docker exec docker2 ping 192.168.2.1
PING 192.168.2.1 (192.168.2.1) 56(84) bytes of data.
64 bytes from 192.168.2.1: icmp_seq=1 ttl=64 time=0.309 ms
^C
Display network statistics using netstat:
netstat -ntulp
Add a VXLAN port to Open vSwitch and set options:
sudo ovs-vsctl add-port ovs-br1 vxlan1 - set interface vxlan1 type=vxlan options:remote_ip=172.31.32.116 options:key=1000
sudo ovs-vsctl add-port ovs-br2 vxlan2 - set interface vxlan2 type=vxlan options:remote_ip=172.31.32.116 options:key=2000
Display network statistics again and filter for port 4789 (VXLAN):
netstat -ntulp | grep 4789

Here are the Ubuntu Commands used in VM2
1)Installing Required Packages:
sudo apt -y install net-tools docker.io openvswitch-switch
This command installs the net-tools, docker.io, and openvswitch-switch packages using the apt package manager. These packages are necessary for networking, Docker, and Open vSwitch.
2) Updating Package Information:
sudo apt update
This command updates the local package repository information to ensure you have the latest package details before installing or upgrading software.
3) Building a Docker Image:
vim Dockerfile
/**************************/
# Use the official Ubuntu base image
FROM ubuntu
# Install necessary packages
RUN apt-get update && apt-get install -y \
iputils-ping \
iproute2
# (Optional) You can install other packages here if needed.
# Set the default command to run when the container starts (bash in this case)
CMD ["bash"]
/************************/
ls
Dockerfile
sudo docker run -dit - name docker3 - net=none - privileged ubuntu-docker
Unable to find image 'ubuntu-docker:latest' locally
docker: Error response from daemon: pull access denied for ubuntu-docker, repository does not exist or may require 'docker login': denied: requested access to the resource is denied.
See 'docker run - help'.
4)Building a Docker Image:
sudo docker build . -t ubuntu-docker
These commands create a Dockerfile using the vim text editor and then build a Docker image named ubuntu-docker from the current directory. A Docker image is a lightweight, standalone, and executable software package that includes everything needed to run a piece of software.
5)Creating Docker Containers:
sudo docker run -dit - name docker3 - net=none - privileged ubuntu-docker
sudo docker run -dit - name docker4 - net=none - privileged ubuntu-docker
These commands create two Docker containers named docker3 and docker4 from the ubuntu-docker image. They are started in detached mode (-d) and without network access (--net=none). The containers are also granted privileges (--privileged) for certain system operations.
sudo docker ps
ubuntu@ip-172–31–32–116:~$ ip a
6) Adding Containers to Open vSwitch Bridges:
sudo ovs-docker add-port ovs-br1 eth0 docker3 - ipaddress=192.168.1.12/24
sudo ovs-docker add-port ovs-br2 eth0 docker4 - ipaddress=192.168.2.12/24
sudo ovs-vsctl show
sudo docker exec -it docker3 bash
root@857bf7a322b4:/# ip route add default via 192.168.1.1
root@857bf7a322b4:/# apt update
0% [Connecting to archive.ubuntu.com] [Connecting to security.ubuntu.com]^C
root@857bf7a322b4:/# ping 192.168.1.1
PING 192.168.1.1 (192.168.1.1) 56(84) bytes of data.
From 192.168.1.12 icmp_seq=1 Destination Host Unreachable
From 192.168.1.12 icmp_seq=2 Destination Host Unreachable
From 192.168.1.12 icmp_seq=3 Destination Host Unreachable
^C
7)Setting Up Internal Interfaces and IP Addresses:
sudo ovs-vsctl add-port ovs-br1 veth1 - set interface veth1 type=internal
sudo ovs-vsctl add-port ovs-br2 veth2 - set interface veth2 type=internal
sudo ip address add 192.168.1.1/24 dev veth1
sudo ip address add 192.168.2.1/24 dev veth2
sudo ip link set dev veth1 up mtu 1450
sudo ip link set dev veth2 up mtu 1450
These commands create internal interfaces (veth1 and veth2) on Open vSwitch bridges (ovs-br1 and ovs-br2) and assign IP addresses to them. These interfaces are used for communication between the host and the containers.
sudo ovs-docker add-port ovs-br1 eth0 docker3 - ipaddress=192.168.1.12/24
ovs-docker: Port already attached for CONTAINER=docker3 and INTERFACE=eth0
sudo ovs-docker add-port ovs-br2 eth0 docker4 - ipaddress=192.168.2.12/24
ovs-docker: Port already attached for CONTAINER=docker4 and INTERFACE=eth0
sudo docker exec docker3 ping 192.168.1.1
PING 192.168.1.1 (192.168.1.1) 56(84) bytes of data.
64 bytes from 192.168.1.1: icmp_seq=1 ttl=64 time=0.425 ms
64 bytes from 192.168.1.1: icmp_seq=2 ttl=64 time=0.054 ms
64 bytes from 192.168.1.1: icmp_seq=3 ttl=64 time=0.055 ms
64 bytes from 192.168.1.1: icmp_seq=4 ttl=64 time=0.054 ms
^C
sudo docker exec -it docker4 bash
root@a980b0226cd3:/# ip route add default via 192.168.2.1
root@a980b0226cd3:/# apt update
0% [Connecting to archive.ubuntu.com] [Connecting to security.ubuntu.com]^C
root@a980b0226cd3:/# exit
exit
sudo docker exec docker2 ping 192.168.2.1
Error: No such container: docker2
sudo docker exec docker4 ping 192.168.2.1
PING 192.168.2.1 (192.168.2.1) 56(84) bytes of data.
64 bytes from 192.168.2.1: icmp_seq=1 ttl=64 time=0.239 ms
64 bytes from 192.168.2.1: icmp_seq=2 ttl=64 time=0.056 ms
64 bytes from 192.168.2.1: icmp_seq=3 ttl=64 time=0.057 ms
^C
 
sudo ovs-vsctl add-port ovs-br1 vxlan1 - set interface vxlan1 type=vxlan options:remote_ip=172.31.38.170 options:key=1000
sudo ovs-vsctl add-port ovs-br2 vxlan2 - set interface vxlan2 type=vxlan options:remote_ip=172.31.38.170 options:key=2000
