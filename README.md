# AKS 快速配置和安装指南

本文档提供了 Azure Kubernetes Service (AKS) 的快速安装指南。

## 目录

- [前提条件](#前提条件)
- [快速安装](#快速安装)
- [基本配置](#基本配置)
- [高级配置](#高级配置)
- [常见问题](#常见问题)
- [参考资源](#参考资源)

## 前提条件

1. **Azure 订阅** - 如果没有，可以[创建免费账户](https://azure.microsoft.com/zh-cn/free/)
2. **安装工具**
   ```bash
   # 安装 Azure CLI
   curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash  # Linux
   brew install azure-cli  # macOS
   winget install -e --id Microsoft.AzureCLI  # Windows
   
   # 安装 kubectl
   az aks install-cli
   
   # 登录 Azure
   az login
   ```

## 快速安装

```bash
# 1. 创建资源组
az group create --name myResourceGroup --location eastus

# 2. 创建 AKS 集群
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys

# 3. 连接到集群
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster

# 4. 验证连接
kubectl get nodes
```

## 基本配置

```bash
# 添加节点池
az aks nodepool add --resource-group myResourceGroup --cluster-name myAKSCluster --name mynodepool --node-count 3

# 启用监控
az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons monitoring
```

## 高级配置

```bash
# 配置自动缩放
az aks update --resource-group myResourceGroup --name myAKSCluster --enable-cluster-autoscaler --min-count 1 --max-count 5

# 使用 Azure CNI
az aks create --resource-group myResourceGroup --name myAKSCluster --network-plugin azure --node-count 3

# 启用 RBAC
az aks create --resource-group myResourceGroup --name myAKSCluster --enable-aad --aad-admin-group-object-ids <管理组ID>
```

## 常见问题

```bash
# 重新获取凭据
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing

# 检查节点状态
kubectl get nodes
kubectl describe node <节点名称>

# 查看事件和日志
kubectl get events
az aks show --resource-group myResourceGroup --name myAKSCluster
```

## 参考资源

- [Azure Kubernetes Service 文档](https://docs.microsoft.com/zh-cn/azure/aks/)
- [AKS 最佳实践](https://docs.microsoft.com/zh-cn/azure/aks/best-practices)
- [Kubernetes 官方文档](https://kubernetes.io/zh/docs/home/)