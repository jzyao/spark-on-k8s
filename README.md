# spark on CCE - spark operator

## 准备
- 创建CCE集群，2台工作节点，为了试验方便，每台节点都配了公网IP
  ```
  # kubectl get node
  NAME            STATUS    ROLES     AGE       VERSION
  192.168.0.135   Ready     <none>    14m       v1.11.7-r0-CCE2.0.20.B001
  192.168.0.34    Ready     <none>    14m       v1.11.7-r0-CCE2.0.20.B001
  ```
- 创建两块文件存储卷，安装Prometheus时需要
   ![sfs](/pic/sfs.png?raw=true "sfs")
- 工作节点安装 `yum install -y socat`
- 下载kubeconfig文件到本地





## 安装Helm
```
wget -O helm.tar.gz https://storage.googleapis.com/kubernetes-helm/helm-v2.11.0-linux-amd64.tar.gz
tar -zxvf helm.tar.gz
mv linux-amd64/helm /usr/local/bin/helm
helm version
helm repo add stable https://kubernetes.oss-cn-hangzhou.aliyuncs.com/
helm init --tiller-image=sapcc/tiller:v2.11.0 --service-account tiller

```

## 安装Spark operator
官方的文档是通过Helm Chart进行安装的，由于很多开发者的环境无法连通google的repo，因此此处我们通过标准的yaml进行安装。
```
git clone https://github.com/jzyao/spark-on-k8s-operator.git
cd spark-on-k8s-operator

##安装crd
kubectl apply -f manifest/spark-operator-crds.yaml 
## 安装operator的服务账号与授权策略
kubectl apply -f manifest/spark-operator-rbac.yaml 
## 安装spark任务的服务账号与授权策略
kubectl apply -f manifest/spark-rbac.yaml 
## 安装spark-on-k8s-operator 
kubectl apply -f manifest/spark-operator-with-metrics.yaml
```


##  安装Prometheus
用helm chart安装Pormetheus
```
helm install --name CCE-prom stable/prometheus
```
在这一步，有两个容器会报错，报错为，原因是
解决方法
修改deployment的yaml文件，用之前准备的两块文件存储卷名替换

```
kubectl expose deployment --type=NodePort  --name=prometheus-webui
```
## 测试用例

## 安装Spark History Server

