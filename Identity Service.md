# 新增Identity Service(keystone)

## 建立keystone資料庫並授權使用者

- **使用mysql root身份登入**
  
        # mysql -u root -p

- **建立keystone資料庫**

        # CREATE DATABASE keystone;
        
- **對keystone資料庫授權使用者**

        GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'localhost' \
        IDENTIFIED BY 'openstack';
        
        GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%' \
        IDENTIFIED BY 'openstack';
        
- **安裝keystone包**
      
        # apt-get install keystone python-keystoneclient
        
- **编辑 /etc/keystone/keystone.conf**

      1.在[DEFAULT]增加admin token，此處修改為openstack
      
          [DEFAULT]
          verbose = True
          ...
          admin_token = openstack
      
      2.修改 [database]部分
      
          [database]
          ...
          connection = mysql://keystone:openstack@controller/keystone
          
      >**Note:**
      記得注釋原有的設定
      connection=sqlite:////var/lib/keystone/keystone.db
      
      3.修改 [token]部分
          
          [token]
          ...
          provider = keystone.token.providers.uuid.Provider
          driver = keystone.token.persistence.backends.sql.Token
      4.修改 [revoke] 部分
      
          [revoke]
          ...
          driver = keystone.contrib.revoke.backends.sql.Revoke
          
- **初始化keystone資料庫**

        # su -s /bin/sh -c "keystone-manage db_sync" keystone
        
- **重啟keystone**

        # service keystone restart
        
- **移除預設SQLite db**

        # rm -f /var/lib/keystone/keystone.db
        
## 建立tenants,users,roles

- **配置環境變數**

        $ export OS_SERVICE_TOKEN=openstack
        $ export OS_SERVICE_ENDPOINT=http://controller:35357/v2.0
        
      >**Note:**
      OS_SERVICE_TOKEN設定成前面所設置的admin_token
      
- **建立admin權限的tenant,user,role**

    1.建立admin tenant
    
        $ keystone tenant-create --name admin --description "Admin Tenant"
        +-------------+----------------------------------+
        |   Property  |              Value               |
        +-------------+----------------------------------+
        | description |           Admin Tenant           |
        |   enabled   |               True               |
        |      id     | 6f4c1e4cbfef4d5a8a1345882fbca110 |
        |     name    |              admin               |
        +-------------+----------------------------------+
        
      >**Note** id是依據系統產生每次都不一樣
      
    2.建立admin user
    
        $ keystone user-create --name admin --pass ADMIN_PASS --email EMAIL_ADDRESS
        +----------+----------------------------------+
        | Property |              Value               |
        +----------+----------------------------------+
        |  email   |        admin@example.com         |
        | enabled  |               True               |
        |    id    | ea8c352d253443118041c9c8b8416040 |
        |   name   |              admin               |
        | username |              admin               |
        +----------+----------------------------------+
        
      >**Note**:自行替換ADMIN_PASS,EMAIL_ADDRESS
      
    3.建立admin role:
    
        $ keystone role-create --name admin
        +----------+----------------------------------+
        | Property |              Value               |
        +----------+----------------------------------+
        |    id    | bff3a6083b714fa29c9344bf8930d199 |
        |   name   |              admin               |
        +----------+----------------------------------+
        
    4.將admin role添加到admin user,admin tenant
    
        $ keystone user-role-add --user admin --tenant admin --role admin
      
      >**Note**:此命令沒有輸出
      
- **建立demo tenant,user,role**

    1.建立demo tenant:
    
        $ keystone tenant-create --name demo --description "Demo Tenant"
        +-------------+----------------------------------+
        |   Property  |              Value               |
        +-------------+----------------------------------+
        | description |           Demo Tenant            |
        |   enabled   |               True               |
        |      id     | 4aa51bb942be4dd0ac0555d7591f80a6 |
        |     name    |               demo               |
        +-------------+----------------------------------+
        
    2.建立demo tenant下的demo使用者
    
        $ keystone user-create --name demo --tenant demo --pass DEMO_PASS --email EMAIL_ADDRESS
        +----------+----------------------------------+
        | Property |              Value               |
        +----------+----------------------------------+
        |  email   |         demo@example.com         |
        | enabled  |               True               |
        |    id    | 7004dfa0dda84d63aef81cf7f100af01 |
        |   name   |               demo               |
        | tenantId | 4aa51bb942be4dd0ac0555d7591f80a6 |
        | username |               demo               |
        +----------+----------------------------------+
      
      >**Note**:自行替換DEMO_PASS,EMAIL_ADDRESS
      
      >**Note**:在建立demo user的同時，demo的role會自動建立
      
- **建立service tenant**

        $ keystone tenant-create --name service --description "Service Tenant"
        +-------------+----------------------------------+
        |   Property  |              Value               |
        +-------------+----------------------------------+
        | description |          Service Tenant          |
        |   enabled   |               True               |
        |      id     | 6b69202e1bf846a4ae50d65bc4789122 |
        |     name    |             service              |
        +-------------+----------------------------------+
        
## service entity and API endpoints

- **建立service entity**

        $ keystone service-create --name keystone --type identity \
          --description "OpenStack Identity"
        +-------------+----------------------------------+
        |   Property  |              Value               |
        +-------------+----------------------------------+
        | description |        OpenStack Identity        |
        |   enabled   |               True               |
        |      id     | 15c11a23667e427e91bc31335b45f4bd |
        |     name    |             keystone             |
        |     type    |             identity             |
        +-------------+----------------------------------+
        
- **建立API endpoints**

        $ keystone endpoint-create \
          --service-id $(keystone service-list | awk '/ identity / {print $2}') \
          --publicurl http://controller:5000/v2.0 \
          --internalurl http://controller:5000/v2.0 \
          --adminurl http://controller:35357/v2.0 \
          --region regionOne
        +-------------+----------------------------------+
        |   Property  |             Value                |
        +-------------+----------------------------------+
        |   adminurl  |   http://controller:35357/v2.0   |
        |      id     | 11f9c625a3b94a3f8e66bf4e5de2679f |
        | internalurl |   http://controller:5000/v2.0    |
        |  publicurl  |   http://controller:5000/v2.0    |
        |    region   |           regionOne              |
        |  service_id | 15c11a23667e427e91bc31335b45f4bd |
        +-------------+----------------------------------+
        
## 驗證keystone安裝

- **建立openrc檔案**

  執行openstack相關命令時，需要設定用戶的環境變數，可將所需的環境變數寫成script檔方便設定
  
  1.建立admin-openrc.sh
  
      export OS_TENANT_NAME=admin
      export OS_USERNAME=admin
      export OS_PASSWORD=ADMIN_PASS
      export OS_AUTH_URL=http://controller:35357/v2.0
      
    >**Note**:自行替換ADMIN_PASS
    
  2.建立demo-openrc.sh
  
      export OS_TENANT_NAME=demo
      export OS_USERNAME=demo
      export OS_PASSWORD=DEMO_PASS
      export OS_AUTH_URL=http://controller:5000/v2.0
      
    >**Note**:自行替換DEMO_PASS
    
- **openrc檔案使用**

        # source admin-openrc.sh
    
- **執行以上命令後即可以admin使用者執行相關命令**

        # keystone service-list
