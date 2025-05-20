# AKS 快速配置和安装指南

本文档提供了快速配置和安装 Azure Kubernetes Service (AKS) 的步骤和最佳实践。

## 目录

- [前提条件](#前提条件)
- [快速安装](#快速安装)
- [基本配置](#基本配置)
- [高级配置选项](#高级配置选项)
- [常见问题排查](#常见问题排查)
- [参考资源](#参考资源)

## 前提条件

在安装 AKS 之前，请确保您具备以下条件：

1. **Azure 订阅** - 如果没有，可以[创建免费账户](https://azure.microsoft.com/zh-cn/free/)
2. **安装 Azure CLI** - 用于管理 Azure 资源
   ```bash
   # 在 Windows 上安装
   winget install -e --id Microsoft.AzureCLI
   
   # 在 macOS 上安装
   brew install azure-cli
   
   # 在 Linux 上安装
   curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
   ```
3. **安装 kubectl** - Kubernetes 命令行工具
   ```bash
   # 在 Windows 上安装
   az aks install-cli
   
   # 在 macOS 上安装
   brew install kubernetes-cli
   
   # 在 Linux 上安装
   sudo az aks install-cli
   ```
4. **登录到 Azure**
   ```bash
   az login
   ```

## 快速安装

### 1. 创建资源组

```bash
# 创建资源组
az group create --name myAKSResourceGroup --location eastus
```

### 2. 创建 AKS 集群

```bash
# 创建单节点 AKS 集群（最快速的配置）
az aks create \
    --resource-group myAKSResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --enable-addons monitoring \
    --generate-ssh-keys
```

### 3. 连接到集群

```bash
# 获取凭据
az aks get-credentials --resource-group myAKSResourceGroup --name myAKSCluster

# 验证连接
kubectl get nodes
```

## 基本配置

### 配置节点池

```bash
# 添加节点池
az aks nodepool add \
    --resource-group myAKSResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --node-vm-size Standard_DS2_v2
```

### 配置网络

```bash
# 创建高级网络配置的集群
az aks create \
    --resource-group myAKSResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.0.0.10 \
    --service-cidr 10.0.0.0/16
```

### 启用监控

```bash
# 启用监控插件
az aks enable-addons \
    --resource-group myAKSResourceGroup \
    --name myAKSCluster \
    --addons monitoring
```

## 高级配置选项

### 自动缩放

```bash
# 配置集群自动缩放
az aks update \
    --resource-group myAKSResourceGroup \
    --name myAKSCluster \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 5
```

### 部署 Azure CNI

```bash
# 使用 Azure CNI 创建集群
az aks create \
    --resource-group myAKSResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --node-count 3
```

### 配置 RBAC

```bash
# 启用 RBAC
az aks create \
    --resource-group myAKSResourceGroup \
    --name myAKSCluster \
    --enable-aad \
    --aad-admin-group-object-ids <AAD-ADMIN-GROUP-ID> \
    --aad-tenant-id <AAD-TENANT-ID>
```

## 常见问题排查

### 无法连接到集群

1. 检查凭据是否正确：
   ```bash
   az aks get-credentials --resource-group myAKSResourceGroup --name myAKSCluster --overwrite-existing
   ```

2. 检查 Azure CLI 是否已登录：
   ```bash
   az account show
   ```

3. 验证网络连接：
   ```bash
   kubectl get nodes
   ```

### 部署失败

1. 检查资源限制：
   ```bash
   az vm list-usage --location eastus -o table
   ```

2. 检查错误日志：
   ```bash
   az aks show --resource-group myAKSResourceGroup --name myAKSCluster
   ```

3. 检查 Kubernetes 事件：
   ```bash
   kubectl get events --sort-by='.metadata.creationTimestamp'
   ```

### 节点未就绪

1. 检查节点状态：
   ```bash
   kubectl describe node <node-name>
   ```

2. 检查节点池运行状况：
   ```bash
   az aks nodepool list --resource-group myAKSResourceGroup --cluster-name myAKSCluster -o table
   ```

## 参考资源

- [Azure Kubernetes Service 文档](https://docs.microsoft.com/zh-cn/azure/aks/)
- [AKS 最佳实践](https://docs.microsoft.com/zh-cn/azure/aks/best-practices)
- [Kubernetes 官方文档](https://kubernetes.io/zh/docs/home/)
- [AKS 定价](https://azure.microsoft.com/zh-cn/pricing/details/kubernetes-service/)
- [AKS 路线图](https://github.com/Azure/AKS/projects/1)