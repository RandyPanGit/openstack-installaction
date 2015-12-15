# 新增Image Service(glance)

## 建立glance資料庫

- **登入mysqql**

        # mysql -u root -p
        
- **建立glance db**

        > CREATE DATABASE glance;
        
- **授權使用者可使用glance資料庫**

        > GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'localhost' \
          IDENTIFIED BY 'GLANCE_DBPASS';
          
        >  GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'%' \
          IDENTIFIED BY 'GLANCE_DBPASS'; 
          
      >**Note**:GLANCE_DBPASS請自行替換，此處替換為openstack
      
## 建立glance使用者

- **配置admin環境變數

        $ source admin-openrc.sh
        
- **建立openstack的glance使用者**

        $ keystone user-create --name glance --pass GLANCE_PASS
        +----------+----------------------------------+
        | Property |              Value               |
        +----------+----------------------------------+
        |  email   |                                  |
        | enabled  |               True               |
        |    id    | f89cca5865dc42b18e2421fa5f5cce66 |
        |   name   |              glance              |
        | username |              glance              |
        +----------+----------------------------------+
        
      >**Note**:GLANCE_DBPASS請自行替換，此處替換為openstack
      
- **將glance使用者賦予admin角色**

        $ keystone user-role-add --user glance --tenant service --role admin
        
- **建立glance service**

        $ keystone service-create --name glance --type image \
                --description "OpenStack Image Service"
        +-------------+----------------------------------+
        |   Property  |              Value               |
        +-------------+----------------------------------+
        | description |     OpenStack Image Service      |
        |   enabled   |               True               |
        |      id     | 23f409c4e79f4c9e9d23d809c50fbacf |
        |     name    |              glance              |
        |     type    |              image               |
        +-------------+----------------------------------+
        
- **建立glance API endpoint**

        $ keystone endpoint-create \
        --service-id $(keystone service-list | awk '/ image / {print $2}') \
        --publicurl http://controller:9292 \
        --internalurl http://controller:9292 \
        --adminurl http://controller:9292 \
        --region regionOne
        +-------------+----------------------------------+
        |   Property  |             Value                |
        +-------------+----------------------------------+
        |   adminurl  |   http://controller:9292         |
        |      id     | a2ee818c69cb475199a1ca108332eb35 |
        | internalurl |   http://controller:9292         |
        |  publicurl  |   http://controller:9292         |
        |    region   |           regionOne              |
        |  service_id | 23f409c4e79f4c9e9d23d809c50fbacf |
        +-------------+----------------------------------+

- **安裝glance套件**

        # apt-get install glance python-glanceclient

- **修改/etc/glance/glance-api.conf**

    1. 在[database]處修改DB連線
        
                [database]
                ...
                connection = mysql://glance:GLANCE_DBPASS@controller/glance

        >**Note**:GLANCE_DBPASS為之前資料庫設定之值，此處替換為openstack
