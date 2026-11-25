# flannel 和 calico网络插件互换重装

kubeadm\_path: "/opt/kubeadm-files"

### 使用 calico

```plaintext
kkubectl delete -f {{ kubeadm_path }}/kube-flannel.yaml
ifconfig cni0 down
ip link delete flannel.1
sudo rm -rf /var/lib/cni/
sudo rm -rf /etc/cni/net.d/*

  systemctl daemon-reload
  systemctl restart kubelet
kubectl apply -f {{ kubeadm_path }}/kube-calico.yaml

```

# 使用flannel

```plaintext
kubectl delete -f {{ kubeadm_path }}/kube-calico.yaml
for i in `ifconfig |grep cali |awk {'print $1'} |tr -d ':'`;do ifconfig $i down;done
for i in `ifconfig |grep cali |awk {'print $1'} |tr -d ':'`;do ip link delete $i;done
sudo rm -rf /var/lib/cni/
sudo rm -rf /etc/cni/net.d/*

systemctl daemon-reload
systemctl restart kubelet

kubectl apply -f {{ kubeadm_path }}/kube-flannel.yaml
```