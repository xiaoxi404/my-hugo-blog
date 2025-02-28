---
title: "使用 Kind 和 Helm 部署 OpenWhisk"
date: 2025-02-27T16:14:44+08:00
draft: false
slug: kind-helm-openwisk
---

最近博主需要测试 OpenWhisk，OpenWhisk 官方推荐使用 k8s 部署，并且博主希望使用 rootless podman 完成部署，所以博主正好记录一下这次奇特的部署过程

linux 发行版为 Archlinux，容器使用 rootless 模式的 podman，本文不需要使用任何特权命令(除了 pacman 安装程序)

## 安装 kind 和 helm

~~~
sudo pacman -S kind helm
~~~

## kind 创建 k8s 集群

git clone openwhisk-deploy-kube

~~~
git clone https://github.com/apache/openwhisk-deploy-kube.git
~~~

后续命令均在 openwhisk-deploy-kube 目录下运行

Kind k8s 集群配置文件[deploy/kind/kind-cluster.yaml](https://github.com/apache/openwhisk-deploy-kube/blob/master/deploy/kind/kind-cluster.yaml)

下面的配置文件配置了1个 control-plane，2个 worker，并暴露 localhost:31001 端口作为后续 openwshisk 的 api 访问端口

~~~
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
  extraPortMappings:
    - hostPort: 31001
      containerPort: 31001
- role: worker
~~~

k8s 集群创建

由于博主使用 podman rootless，不能直接运行 `kind create cluster`，需要使用 `systemd-run`,详见[kind rootless document](https://kind.sigs.k8s.io/docs/user/rootless/)

~~~
systemd-run --scope --user -p "Delegate=yes" kind create cluster --config ./deploy/kind/kind-cluster.yaml --name kind-cluster
~~~

## k8s 集群配置

给 worker node 打上 label 标签, kind-cluster-worker 和 kind-cluster-worker2 都是 worker node name，需要根据实际情况修改，详见[oepnwhisk multi worker node clusters docs](https://github.com/apache/openwhisk-deploy-kube#multi-worker-node-clusters)

查看集群 nodes

~~~
kubectl get nodes
~~~

打上 label

~~~
kubectl label node kind-cluster-worker openwhisk-role=core
kubectl label node kind-cluster-worker2 openwhisk-role=invoke
~~~

## helm 部署 Openwhisk

修改 Openwhisk helm chart 配置，配置文件位于[deploy/kind/mycluster.yaml](https://github.com/apache/openwhisk-deploy-kube/blob/master/deploy/kind/kind-cluster.yaml)，主要是将 `apihostName` 修改为 openwhisk-nginx，openwhisk-nginx 是 openwhisk 的 nginx service 的 name。

~~~
whisk:
  ingress:
    type: NodePort
    apiHostName: openwhisk-nginx
    apiHostPort: 31001
    useInternally: false

nginx:
  httpsNodePort: 31001

# disable affinity
affinity:
  enabled: false
toleration:
  enabled: false
invoker:
  options: "-Dwhisk.kubernetes.user-pod-node-affinity.enabled=false"
  # must use KCF as kind uses containerd as its container runtime
  containerFactory:
    impl: "kubernetes"
~~~

helm install OpenWhisk
~~~
helm install openwhisk openwhisk/openwhisk -n openwhisk --create-namespace -f deploy/kind/mycluster.yaml
~~~

部署过程需要 10 min左右，`openwhisk-install-packages` 开头的pod 处于 complete 状态时就证明 Openwhisk 部署好了。

## OpenWhisk 测试

wsk cli 配置
~~~
wsk property set --apihost 127.0.0.1:31001
wsk property set --auth 23bc46b1-71f6-4ed5-8c54-816aa4f8c502:123zO3xZCLrMN6v2BKK1dXYFpXlPkccOFqm12CdAsMgRU4VrNZ9lyGVCGuMDGIwP
~~~

hello.js 测试云函数
~~~
function main() {
    return { msg: 'Hello world' };
}
~~~

上传 hello.js 并测试调用它
~~~
wsk -i action create hello hello.js
wsk -i action invoke -r hello
~~~