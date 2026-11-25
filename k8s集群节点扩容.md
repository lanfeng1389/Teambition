# ADD_NODE

## 0. 节点准备

1. 修改主机名为 `nodeX.teambition.local`
    - 如：`node1.teambition.local`
2. 执行系统初始化

    ``` bash
    bash <(curl -s http://apt.cncicore.com:9101/ops-utils/run_allinone.sh)
    ```

3. 更新内核以修复安全问题。更新后需重启

    ``` bash
    CURRENT_KERNEL_VERSION=$(uname -r)
    TARGET_KERNEL_VERSION=5.10.134-19.3.1.an8.x86_64
    if [ "${CURRENT_KERNEL_VERSION}" != "${TARGET_KERNEL_VERSION}" ]; then
        PKGS=$(rpm -qa | grep "${CURRENT_KERNEL_VERSION}" | sed "s@${CURRENT_KERNEL_VERSION}@${TARGET_KERNEL_VERSION}@")
        yum install -y ${PKGS}
    fi
    ```

4. 挂载数据盘

    - fstab：

        ``` fstab
        /dev/mapper/datavg-docker     /var/lib/docker                   ext4    defaults,noatime,nofail 0 0
        /dev/mapper/datavg-containerd /var/lib/containerd               ext4    defaults,noatime,nofail 0 0
        /dev/mapper/datavg-kubelet    /var/lib/kubelet                  ext4    defaults,noatime,nofail 0 0
        ```

    - lsblk：

        ``` paintext
        [root@node1 ~]# lsblk
        NAME                MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
        vda                 253:0    0   80G  0 disk 
        ├─vda1              253:1    0    2M  0 part 
        ├─vda2              253:2    0  200M  0 part /boot/efi
        └─vda3              253:3    0 79.8G  0 part /
        vdb                 253:16   0  500G  0 disk 
        ├─datavg-docker     252:0    0  100G  0 lvm  /var/lib/docker
        ├─datavg-containerd 252:1    0  200G  0 lvm  /var/lib/containerd
        └─datavg-kubelet    252:2    0  200G  0 lvm  /var/lib/kubelet
        ```

5. 新节点需允许访问公网，用于节点启动时拉取镜像
    - ECS 虚拟机通过添加 SNAT 允许访问公网
    - 底座镜像仓库：`registry.teambition.net`
    - 应用镜像仓库：`private-doc-registry.cn-qingdao.cr.aliyuncs.com`

## 1. 配置文件中添加节点

1. 通过 git，分别在 `data/inventory/hosts` 和 `data/inventory/all` 文件中的 `[k8snode]` 添加节点信息。
2. 通过 git，在 `data/inventory/host_vars/` 目录中添加上述节点的变量配置。

## 2. 执行节点扩容

### 2.1. 获取 tes-mc容器id 并进入容器环境

``` bash
docker exec -it $(docker ps | grep tesmc_worker_1 | awk '{print $1}' | head -n 1) sh
```

> **以下操作在此容器环境执行**

### 2.2. 切换执行目录

``` bash
cd /code/data/apps/base
```

### 2.3. 执行ansible playbook安装

> 执行 `ansible-playbook` 时，需使用 `-l` 参数指定要操作的节点
> 可指定一个组（如 `k8snode`），也可指定一个节点（如 `node1`）

把容器里面的 /code/data/.ssh/id_rsa.pub 添加到 node1 的 `.ssh/authorized_keys` 中。

#### 2.3.1. 执行install-common

``` bash
ANSIBLE_HOST_KEY_CHECKING=False ansible-playbook ./ansible/common.yaml -i /code/data/inventory -e"@/code/data/inventory/teambition_vars.yaml" -l node1
```

#### 2.3.2. 执行安装容器运行时

- 若运行时使用 docker 则执行install-docker（本环境不执行）

``` bash
ANSIBLE_HOST_KEY_CHECKING=False ansible-playbook ./ansible/install-docker.yaml -i /code/data/inventory -e"@/code/data/inventory/teambition_vars.yaml" -l node1
```

- 若运行时使用 containerd 则执行install-containerd **（本环境执行这个）**

``` bash
ANSIBLE_HOST_KEY_CHECKING=False ansible-playbook ./ansible/install-containerd.yaml -i /code/data/inventory -e"@/code/data/inventory/teambition_vars.yaml" -l node1
```

#### 2.3.3. 执行install-k8s

``` bash
ANSIBLE_HOST_KEY_CHECKING=False ansible-playbook ./ansible/install-k8s.yaml -i /code/data/inventory -e"@/code/data/inventory/teambition_vars.yaml" -l node1
```

### 2.4. 查看节点加入状态

- master 中执行：

``` bash
kubectl get nodes
```

## 3. 从节点驱除

1. 驱除节点POD

    ``` bash
    kubectl cordon node1.teambition.local
    kubectl drain node1.teambition.local --delete-emptydir-data --force --ignore-daemonsets
    ```

2. 删除从节点

    ``` bash
    kubectl delete node node1.teambition.local
    ```
