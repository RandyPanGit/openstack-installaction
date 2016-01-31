# 新增Compute Service(nova)

## 建立nova資料庫

- **登入mysqql**

        # mysql -u root -p
        
- **建立nova db**

         CREATE DATABASE nova;
         
- **授權使用者可使用nova資料庫**

        GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'localhost' \
        IDENTIFIED BY 'openstac';
        GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'%' \
        IDENTIFIED BY 'openstack';
        
      >**Note**:NOVA_DBPASS請自行替換，此處替換為openstack
      
## 建立nova使用者

- **配置admin環境變數**

        $ source admin-openrc.sh
        
- **建立openstack的nova使用者**
        
          $ keystone user-create --name nova --pass NOVA_PASS
          +----------+----------------------------------+
          | Property |              Value               |
          +----------+----------------------------------+
          |  email   |                                  |
          | enabled  |               True               |
          |    id    | 387dd4f7e46d4f72965ee99c76ae748c |
          |   name   |               nova               |
          | username |               nova               |
          +----------+----------------------------------+

    >**Note**: --pass後請自行替換，此處替換為openstack
    
- **將nova使用者賦予admin角色**

        $ keystone user-role-add --user nova --tenant service --role admin
        
## 建立nova service

    $ keystone service-create --name nova --type compute \
      --description "OpenStack Compute"
    +-------------+----------------------------------+
    |   Property  |              Value               |
    +-------------+----------------------------------+
    | description |        OpenStack Compute         |
    |   enabled   |               True               |
    |      id     | 6c7854f52ce84db795557ebc0373f6b9 |
    |     name    |               nova               |
    |     type    |             compute              |
    +-------------+----------------------------------+
   
## 建立nova service的endpoint

    $ keystone endpoint-create \
    --service-id $(keystone service-list | awk '/ compute / {print $2}') \
    --publicurl http://controller:8774/v2/%\(tenant_id\)s \
    --internalurl http://controller:8774/v2/%\(tenant_id\)s \
    --adminurl http://controller:8774/v2/%\(tenant_id\)s \
    --region regionOne
    +-------------+-----------------------------------------+
    |   Property  |                  Value                  |
    +-------------+-----------------------------------------+
    |   adminurl  | http://controller:8774/v2/%(tenant_id)s |
    |      id     |    c397438bd82c41198ec1a9d85cb7cc74     |
    | internalurl | http://controller:8774/v2/%(tenant_id)s |
    |  publicurl  | http://controller:8774/v2/%(tenant_id)s |
    |    region   |                regionOne                |
    |  service_id |    6c7854f52ce84db795557ebc0373f6b9     |
    +-------------+-----------------------------------------+
    
## 安裝nova service控制組件

- **Install the packages:**

        # apt-get install nova-api nova-cert nova-conductor nova-consoleauth \
           nova-novncproxy nova-scheduler python-novaclient
           
- **編輯/etc/nova/nova.conf**

    1. 在[database]部分，配置database連接
        
            [database]
            ...
            connection = mysql://nova:NOVA_DBPASS@controller/nova
        
        >**Note**:NOVA_DBPASS請自行替換，此處替換為openstack
        
    2. 在[DEFAULT]部分配置RabbitMQ 設定
    
            [DEFAULT]
            ...
            rpc_backend = rabbit
            rabbit_host = controller
            rabbit_password = RABBIT_PASS
            
         >**Note**: Replace RABBIT_PASS with the password you chose for the guest account in RabbitMQ.
         
    3. 在[DEFAULT] 和 [keystone_authtoken]部分配置訪問認證
    
            [DEFAULT]
            ...
            auth_strategy = keystone
 
            [keystone_authtoken]
            ...
            auth_uri = http://controller:5000/v2.0
            identity_uri = http://controller:35357
            admin_tenant_name = service
            admin_user = nova
            admin_password = NOVA_PASS
            
        >**Note**:NOVA_DBPASS請自行替換，此處替換為openstack
        
        >**Note**:註釋掉auth_host, auth_port, 和 auth_protocol
        
    4. 在 [DEFAULT]部分修改my_ip，配置為控制節點IP
    
            [DEFAULT]
            ...
            my_ip = 10.0.0.11
    
    5. 在 [DEFAULT] 部分修改VNC設定
    
            [DEFAULT]
            ...
            vncserver_listen = 10.0.0.11
            vncserver_proxyclient_address = 10.0.0.11
   
    6. 在[glance]部分 配置image服務
    
            [glance]
            ...
            host = controller
    
    7. 啟動Log詳細記錄
    
            [DEFAULT]
            ...
            verbose = True
    
- **初始化nova資料庫**

        # su -s /bin/sh -c "nova-manage db sync" nova
        
- **Restart the Compute services:**

        # service nova-api restart
        # service nova-cert restart
        # service nova-consoleauth restart
        # service nova-scheduler restart
        # service nova-conductor restart
        # service nova-novncproxy restart
        
- **移除預設SQLite**

        # rm -f /var/lib/nova/nova.sqlite
