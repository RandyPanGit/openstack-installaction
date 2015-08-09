
## Basic environment ##
----------
**Install NTP**
安裝NTP(Network Time Protocol)確保各節點間的服務可以正確同步

 - Controller node

    **To install the NTP service**

		> # apt-get install ntp
    **To configure the NTP service**
	可以透過修改/etc/ntp.conf客製化自己所需的時間伺服器，本章節採用預設即可。

