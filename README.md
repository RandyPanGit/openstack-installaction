# openstack-installaction
本文章紀錄利用VirtualBox+Vagrant搭建基本OpenStack環境

 - HostOS: Mac OS X Yosemit
 - VirtualBox:4.3.26
 - Vagrant:
 - OpenStack: KILO
參考資料:http://docs.openstack.org/kilo/install-guide/install/apt/content/index.html

**Example architectures**
----------
本文章依據OpenStack官網所提供之範例架構進行安裝，架構如下：

 - ***The controller node***：runs the Identity service, Image Service, management portions of Compute and Networking, Networking plug-in, and the dashboard.
 - ***The network node***： runs the Networking plug-in and several agents that provision tenant networks and provide switching, routing, NAT, and DHCP services. 
 - ***The compute node***：runs the hypervisor portion of Compute that operates tenant virtual machines or instances. By default, Compute uses KVM as the hypervisor. 
官網架構圖如下（[來源](http://docs.openstack.org/kilo/install-guide/install/apt/content/figures/1/a/common/figures/installguidearch-neutron-hw.png)）：
![enter image description here](http://docs.openstack.org/kilo/install-guide/install/apt/content/figures/1/a/common/figures/installguidearch-neutron-hw.png)

根據OpenStack官網範例架構所提供IP配置如下圖:
![enter image description here](http://docs.openstack.org/kilo/install-guide/install/apt/content/figures/1/a/common/figures/installguidearch-neutron-networks.png)

但是本文章利用Vargant搭配Virtualbox進行安裝，因此IP配置有所差別，Vargant建立VM時會預設建立一張(eth0)網路模式為NAT的網卡，為的是讓使用者可以使用Vargant ssh指令登入ＶＭ，因此每個節點都會多出一張網卡，詳細配置如下：

Controller

    (eth1)
    IP address: 10.0.0.11
    Network mask: 255.255.255.0 (or /24)
    Default gateway: 10.0.0.1
Network

    (eth1)
    IP address: 10.0.0.21
    Network mask: 255.255.255.0 (or /24)
    Default gateway: 10.0.0.1
    (eth2)
    IP address: 10.0.1.21
    Network mask: 255.255.255.0 (or /24)
    (eth3)
Computer1

    (eth1)
    IP address: 10.0.0.31
    Network mask: 255.255.255.0 (or /24)
    Default gateway: 10.0.0.1
    (eth2)
    IP address: 10.0.1.31
    Network mask: 255.255.255.0 (or /24)
    
