# 麒麟v10 sp3 k8s安装失败

### 1 k8s安装失败

```shell
  "stderr": "Error: \n Problem 1: package kubelet-1.28.0-0.x86_64 requires socat, but none of the providers can be installed\n  - conflicting requests\n  - nothing provides libreadline.so.7()(64bit) needed by socat-1.7.3.2-8.ky10.x86_64\n Problem 2: package kubeadm-1.28.0-0.x86_64 requires kubelet >= 1.19.0, but none of the providers can be installed\n  - package kubelet-1.28.0-0.x86_64 requires socat, but none of the providers can be installed\n  - conflicting requests\n  - nothing provides libreadline.so.7()(64bit) needed by socat-1.7.3.2-8.ky10.x86_64\n Problem 3: package kubernetes-cni-1.2.0-0.x86_64 requires kubelet, but none of the providers can be installed\n  - package kubelet-1.28.0-0.x86_64 requires socat, but none of the providers can be installed\n  - conflicting requests\n  - nothing provides libreadline.so.7()(64bit) needed by socat-1.7.3.2-8.ky10.x86_64",

```

临时方案：

> 将/data/tes-repos/localyum/rpms/socat-1.7.3.2-8.ky10.x86\_64.rpm 复制到所有机器上

强制安装

rpm -ivh --nodeps socat-1.7.3.2-8.ky10.aarch64.rpm

rpm -ivh --nodeps socat-1.7.3.2-8.ky10.x86\_64.rpm

然后用tes3重试安装

### 防火墙禁用后，安装停止

> 停止firewalld后，firewalld 中 DOCKER 链 被删除了（操作系统自身原因）

解决方案：

重启tes-mc docker 所在的docker 服务

systemctl restart docker