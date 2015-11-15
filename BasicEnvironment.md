# Basic environment 
## **網路設定**
- **controller**
	![enter image description here](https://goo.gl/EJxci5)
- **network**
	![enter image description here](https://goo.gl/Zfxv7E)
- **compute**
	![enter image description here](https://goo.gl/VNSjdd)

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
  			"trusty-updates/juno main" > /etc/apt/sources.list.d/cloudarchive-juno.list
- **To finalize installation**

	Upgrade the packages on your system:
	
		# apt-get update && apt-get dist-upgrade
		
## SQL database

- **To install and configure the database server**

	1. Install the packages:

			# apt-get install mariadb-server python-mysqldb
		
	2. 安裝過程中會出現輸入mysql root密碼，密碼：openstack.
	3. 編輯/etc/mysql/mysqld.cnf:
	
		1. 在[mysqld]欄位下更改ip設定:

				[mysqld]
				...
				bind-address = 10.10.10.10
				
		2. 然後新增以下內容:
		 
				[mysqld]
				...
				default-storage-engine = innodb
				innodb_file_per_table
				collation-server = utf8_general_ci
				init-connect = 'SET NAMES utf8'
				character-set-server = utf8
	4. 重啟mysql
	
			# service mysql restart
	5 執行以下命令，依據提示輸入即可：

			# mysql_secure_installation
			
## Messaging Server

- **在controller節點安裝RabbitMQ**

		apt-get install rabbitmq-server
- **修改密碼為openstack**

		# rabbitmqctl change_password guest RABBIT_PASS
		Changing password for user "guest" ...
		...done.
- **重啟mq**

		# service rabbitmq-server restart
- **檢查RabbittMQ版本**

	如果版本為3.3.0以上則需配置允許guest帳號
	
		# rabbitmqctl status | grep rabbit
	

