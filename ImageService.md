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
