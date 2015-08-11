## Basic environment ##
----------
**Install NTP**
安裝NTP(Network Time Protocol)確保各節點間的服務可以正確同步

 - Controller node

    **To install the NTP service**

		 # apt-get install ntp
    **To configure the NTP service**
    vi /etc/ntp.conf
> server NTP_SERVER iburst <br />
> restrict -4 default kod notrap nomodify <br />
> restrict -6 default kod notrap nomodify <br />

	NTP_SERVER 可以替換成其他的時間伺服器
	>**Note:**
	對restrict選項，移除 the nopeer and noquery options.
	
	>**Note:**
	Remove the /var/lib/ntp/ntp.conf.dhcp file if it exists.
	
	
	Restart the NTP service:
	
		# service ntp restart
	
	
 - Other nodes

    **To install the NTP service**
    	
    		 # apt-get install ntp
    
	**To configure the NTP service**
	
	先修改/etc/ntp.conf，指向controller節點

		 # server controller iburst
		 
	>**Note:**
	Remove the /var/lib/ntp/ntp.conf.dhcp file if it exists.
	
	**restart ntp service**

		 # service ntp restart
	

 - Verify operation
  
 	**Run this command on the controller node:** <br />

		# ntpq -c peers <br />
  		remote  refid      st t when poll reach   delay   offset  jitter <br />
		**==============================================================** <br />
		*ntp-server1     192.0.2.11       2 u  169 1024  377    1.901   -0.611   5.483 <br />
		+ntp-server2     192.0.2.12       2 u  887 1024  377    0.922   -0.246   2.864 <br />

