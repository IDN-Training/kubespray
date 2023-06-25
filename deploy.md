# on deployment ansible

Note: Use deploy user

### Copy ``inventory/sample`` as ``inventory/mycluster``
```
cp -rfp inventory/sample inventory/mycluster
```

### Update Ansible inventory file with inventory builder
```
declare -a IPS=(192.168.212.1 192.168.212.2 192.168.212.3)
CONFIG_FILE=inventory/mycluster/hosts.yaml python3 contrib/inventory_builder/inventory.py ${IPS[@]}
```

Config file inventory
```
vim inventory/mycluster/hosts.yaml
```

edit
```
all:
  hosts:
    master-jkt:
      ansible_host: 192.168.212.1
      ansible_user: deploy
      ip: 192.168.212.1
      access_ip: 192.168.212.1
    worker1-jkt:
      ansible_host: 192.168.212.2
      ansible_user: deploy
      ip: 192.168.212.2
      access_ip: 192.168.212.2
    worker2-jkt:
      ansible_host: 192.168.212.3
      ansible_user: deploy
      ip: 192.168.212.3
      access_ip: 192.168.212.3
  children:
    kube_control_plane:
      hosts:
        master-jkt:
    kube_node:
      hosts:
        worker1-jkt:
        worker2-jkt:
    etcd:
      hosts:
        master-jkt:
    k8s_cluster:
      children:
        kube_control_plane:
        kube_node:
    calico_rr:
      hosts: {}
```

### Review and change parameters under ``inventory/mycluster/group_vars``
Set configuration NTP Server
```
vim inventory/mycluster/group_vars/all/all.yml
```

edit this
```
## NTP Settings
# Start the ntpd or chrony service and enable it at system boot.
ntp_enabled: false
ntp_manage_config: false
ntp_servers:
  - "0.id.pool.ntp.org iburst"
  - "1.id.pool.ntp.org iburst"
  - "2.id.pool.ntp.org iburst"
  - "3.id.pool.ntp.org iburst"
```

Set configuration service kubernetes
```
vim inventory/mycluster/group_vars/k8s_cluster/k8s-cluster.yml
```

edit this
```
kube_version: v1.26.3
kube_network_plugin: calico
kube_service_addresses: 10.233.0.0/18
kube_pods_subnet: 10.233.64.0/18
cluster_name: jkt-cluster
container_manager: containerd
```

### Test inventory
```
ansible all -i inventory/mycluster/hosts.yaml -m ping
```

### Deploy kubernetes cluster
```
ansible-playbook -i inventory/mycluster/hosts.yaml cluster.yml --become
```

### (Opsional) Reset kubernetes cluster
```
ansible-playbook -i inventory/mycluster/hosts.yaml reset.yml --become
```