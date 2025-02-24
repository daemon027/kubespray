# kubespray
Forked from [kubespray](https://github.com/kubernetes-sigs/kubespray)

Setup:

Due to the GFW in China, firstly we need to install kubernetes and related packages(packages.txt) in a VM,
 then make a VM image for all the kubernetes nodes, the kubernetes related docker images can also
be included in the VM image for fast kubernetes installation.

```ShellSession
# update docker default IP pool if need
cat /etc/docker/daemon.json
{
  "bip": "169.254.123.1/24"
}
# update the host lists to inventory/XXX/inventory.ini first
vi inventory/XXX/inventory.ini
# update the network interface on host the flannel will use
vi inventory/XXX/group_vars/k8s-cluster/k8s-net-flannel.yml
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


Issues:

- fix coredns crash with the doc [loop](https://coredns.io/plugins/loop/#troubleshooting): for ubuntu, just comment 'dns=dnsmasq' in /etc/NetworkManager/NetworkManager.conf

Others:

- only K8S v1.16.* tested