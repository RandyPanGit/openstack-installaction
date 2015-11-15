# openstack-installaction
本文章紀錄利用VirtualBox+Vagrant搭建基本OpenStack環境

 - HostOS: Mac OS X Yosemit
 - VirtualBox:5.0.6
 - OpenStack: JUNO
參考資料:http://docs.openstack.org/kilo/install-guide/install/apt/content/index.html

**Example architectures**
----------
本文章依據OpenStack官網所提供之範例架構進行安裝，架構如下：

 - ***The controller node***：runs the Identity service, Image Service, management portions of Compute and Networking, Networking plug-in, and the dashboard.
 - ***The network node***： runs the Networking plug-in and several agents that provision tenant networks and provide switching, routing, NAT, and DHCP services. 
 - ***The compute node***：runs the hypervisor portion of Compute that operates tenant virtual machines or instances. By default, Compute uses KVM as the hypervisor. 
官網架構圖如下（[來源](http://docs.openstack.org/juno/install-guide/install/apt/content/figures/1/a/common/figures/installguidearch-neutron-hw.png)）：
![enter image description here](http://docs.openstack.org/juno/install-guide/install/apt/content/figures/1/a/common/figures/installguidearch-neutron-hw.png)

根據OpenStack官網範例架構所提供IP配置如下圖:
![enter image description here](http://docs.openstack.org/juno/install-guide/install/apt/content/figures/1/a/common/figures/installguidearch-neutron-networks.png)

VirtualBox網路設定如下:
  新增三張「僅限主機網卡」，如下圖：
  ![enter image description here](https://goo.gl/Tkanuv)
  三張網卡設定分別如下：
  vboxnet0
  ![enter image description here](https://goo.gl/NkEm7v)
  vboxnet1
  ![enter image description here](https://goo.gl/9zJk4S)
  vboxnet2
  ![enter image description here](https://goo.gl/EEqMYA)

