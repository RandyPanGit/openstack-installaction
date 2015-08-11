## Basic environment ##
----------
**Install NTP**
安裝NTP(Network Time Protocol)確保各節點間的服務可以正確同步

 - Controller node

    **To install the NTP service**

		> # apt-get install ntp
    **To configure the NTP service**
> server NTP_SERVER iburst
> restrict -4 default kod notrap nomodify
> restrict -6 default kod notrap nomodify

	NTP_SERVER 可以替換成其他的時間伺服器
	>**Note:**
	對restrict選項，移除 the nopeer and noquery options.
	
	>**Note:**
	Remove the /var/lib/ntp/ntp.conf.dhcp file if it exists.

 - Other nodes

    **To install the NTP service**
    > # apt-get install ntp
    
	**To configure the NTP service**
	先修改/etc/ntp.conf，指向controller節點

	> server controller iburst
restart ntp

# service ntp restart

