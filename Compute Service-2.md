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
      
     >**Note**: 注釋其他 auth_host, auth_port, 和 auth_protocol，因為identity_uri已經配置
    
    3.在[DEFAULT]部分配置，配置計算節點網路IP，在my_ip選項

        [DEFAULT]
        ...
        my_ip = 10.0.0.31
        
    4.在[DEFAULT]部分，啟用配置遠程訪問
    
        [DEFAULT]
        ...
        vnc_enabled = True
        vncserver_listen = 0.0.0.0
        vncserver_proxyclient_address = MANAGEMENT_INTERFACE_IP_ADDRESS
        novncproxy_base_url = http://controller:6080/vnc_auto.html
        
     >**Note**:MANAGEMENT_INTERFACE_IP_ADDRESS替換為計算節點管理網路IP位址
     
    5.在[glance]部分，配置Image服務的host
    
        [glance]
        ...
        host = controller

    6.在[DEFAULT]部分，開啟log紀錄設定
    
        [DEFAULT]
        ...
        verbose = True
        

- **完成安裝**

    1.測試虛擬機是否支援硬體加速
        
        egrep -c '(vmx|svm)' /proc/cpuinfo
        
     >**Note**:如果输出的不是0，那么不需要额外配置，如果输出的是0.则使用QEMU 代替KVM．
    
    1.1编辑文件/etc/nova/nova-compute.conf，在 [libvirt]部分，修改如下
    
        [libvirt]
        ...
        virt_type = qemu

    2.重啟Nova Service
    
        # service nova-compute restart
        
    3.如果存在SQLite 資料庫，則刪除
    
        rm -f /var/lib/nova/nova.sqlite
        
-**驗證安裝**

    1.輸入環境變數
    
        source admin-openrc.sh
        
    2.列出nova service套件列表
    
        $ nova service-list
        +----+------------------+------------+----------+---------+-------+----------------------------+-----------------+
        | Id | Binary           | Host       | Zone     | Status  | State | Updated_at                 | Disabled Reason |
        +----+------------------+------------+----------+---------+-------+----------------------------+-----------------+
        | 1  | nova-conductor   | controller | internal | enabled | up    | 2014-09-16T23:54:02.000000 | -               |
        | 2  | nova-consoleauth | controller | internal | enabled | up    | 2014-09-16T23:54:04.000000 | -               |
        | 3  | nova-scheduler   | controller | internal | enabled | up    | 2014-09-16T23:54:07.000000 | -               |
        | 4  | nova-cert        | controller | internal | enabled | up    | 2014-09-16T23:54:00.000000 | -               |
        | 5  | nova-compute     | compute1   | nova     | enabled | up    | 2014-09-16T23:54:06.000000 | -               |
        +----+------------------+------------+----------+---------+-------+----------------------------+-----------------+
        
   >**Note**:建立虛擬機前須確認上述服務皆有正常啟動

