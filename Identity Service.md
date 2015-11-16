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
