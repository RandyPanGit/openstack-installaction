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

- **配置admin環境變數

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
    
- **將glance使用者賦予admin角色**

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
