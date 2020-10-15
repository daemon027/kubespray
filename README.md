# kubespray
Forked from [kubespray](https://github.com/kubernetes-sigs/kubespray)

setup:
```ShellSession
# update the host lists to inventory/XXX/inventory.ini first
vi inventory/XXX/inventory.ini
# run the command to setup K8S cluster
ansible-playbook -i inventory/XXX/inventory.ini cluster.yml -b --become-user=root -v
# add/remove K8S node, add/remove node in inventory.ini file first
ansible-playbook -i inventory/XXX/inventory.ini scale.yml -b --become-user=root -v
# reset
ansible-playbook -i inventory/XXX/inventory.ini reset.yml -b --become-user=root -v
ansible -i inventory/XXX/inventory.ini -m shell -a 'kubeadm reset -f' all
ansible -i inventory/XXX/inventory.ini -m shell -a 'rm -rf /etc/kubernetes' all
# debug
ansible -i inventory/XXX/inventory.ini -m setup -c local all > /dev/null
ansible -i inventory/XXX/inventory.ini -m debug -a "var=ansible_default_ipv4" kube-master
```

You will get get the K8S cluster:

- flannel as K8S CNI
- 2 nodes HA for master
- 3 nodes cluster for etcd


issues:
- fix coredns crash with the doc [loop](https://coredns.io/plugins/loop/#troubleshooting)