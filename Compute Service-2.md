# 安裝及配置Compute hypervisor組件(計算節點)

- **安裝**

        # apt-get install nova-compute sysfsutils
        
- **編輯/etc/nova/nova.conf**

  1.在[DEFAULT]部分 配置RabbitMQ設定
  
      [DEFAULT]
      ...
      rpc_backend = rabbit
      rabbit_host = controller
      rabbit_password = RABBIT_PASS
      
    >**Note**: Replace RABBIT_PASS with the password you chose for the guest account in RabbitMQ.
    
  2.在 [DEFAULT] 和 [keystone_authtoken] 部分 配置Identity service允許訪問
  
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
