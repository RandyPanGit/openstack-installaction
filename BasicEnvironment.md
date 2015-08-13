# Basic environment 

## **Install NTP**

安裝NTP(Network Time Protocol)確保各節點間的服務可以正確同步

 - **Controller node**

    To install the NTP service

		 # apt-get install ntp
    To configure the NTP service
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
	
	
 - **Other nodes**

    To install the NTP service
    	
    		 # apt-get install ntp
    
	To configure the NTP service
	
	先修改/etc/ntp.conf，指向controller節點

		 # server controller iburst
		 
	>**Note:**
	Remove the /var/lib/ntp/ntp.conf.dhcp file if it exists.
	
	**restart ntp service**

		 # service ntp restart
	

 - **Verify operation**
  
 	Run this command on the controller node: 

		# ntpq -c peers 
  		remote  refid      st t when poll reach   delay   offset  jitter 
		============================================================== 
		*ntp-server1     192.0.2.11       2 u  169 1024  377    1.901   -0.611   5.483 
		+ntp-server2     192.0.2.12       2 u  887 1024  377    0.922   -0.246   2.864
	
	Run this command on the controller node:
	
		# ntpq -c assoc
		ind assid status  conf reach auth condition  last_event cnt
		==============================================================
		1 20487  961a   yes   yes  none  sys.peer    sys_peer  1
		2 20488  941a   yes   yes  none candidate    sys_peer  1
		
	
	Run this command on all other nodes:
	
		# ntpq -c peers
		remote           refid      st t when poll reach   delay   offset  jitter
		==============================================================================
		*controller      192.0.2.21       3 u   47   64   37    0.308   -0.251   0.079
		
	Run this command on all other nodes:
	
		# ntpq -c assoc
		ind assid status  conf reach auth condition  last_event cnt
		===========================================================
		1 21181  963a   yes   yes  none  sys.peer    sys_peer  3
		
## OpenStack packages

在所有節點執行以下步驟:

- **To enable the OpenStack repository**

	安裝Ubuntu Cloud的keyring和repository

		# apt-get install ubuntu-cloud-keyring
		# echo "deb http://ubuntu-cloud.archive.canonical.com/ubuntu" \
		  "trusty-updates/kilo main" > /etc/apt/sources.list.d/cloudarchive-kilo.list
- **To finalize installation**

	Upgrade the packages on your system:
	
		# apt-get update && apt-get dist-upgrade
		
## SQL database

- **To install and configure the database server**

	1. Install the packages:

			# apt-get install mariadb-server python-mysqldb
		
	2. Choose a suitable password for the database root account.
	3. Edit the /etc/mysql/mysqld.cnf file and complete the following actions:
	
			a.In the [mysqld] section, set the bind-address key to the management IP address of the controller node to enable access by other nodes via the management network:

Select Text

[mysqld]
...
bind-address = 10.0.0.11
In the [mysqld] section, set the following keys to enable useful options and the UTF-8 character set:

Select Text

[mysqld]
...
default-storage-engine = innodb
innodb_file_per_table
collation-server = utf8_general_ci
init-connect = 'SET NAMES utf8'
character-set-server = utf8
	

