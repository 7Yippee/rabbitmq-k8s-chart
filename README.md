# RabbitMQ Helm Chart

这个 Helm Chart 用于在 Kubernetes 上部署 RabbitMQ。

## 前提条件

- Kubernetes 1.12+
- Helm 3.0+
- PV 供应器支持您选择的存储类（默认使用 huawei-sc）

## 安装

```bash
# 使用默认配置安装
helm install my-rabbitmq ./rabbitmq-chart

# 使用自定义值文件安装
helm install my-rabbitmq ./rabbitmq-chart -f custom-values.yaml

# 指定命名空间安装
helm install my-rabbitmq ./rabbitmq-chart --namespace rabbitmq-system

# 如果命名空间不存在，需要先创建
kubectl create namespace rabbitmq-system
```

## 配置

下表列出了 RabbitMQ chart 的可配置参数及其默认值：

| 参数                                 | 描述                                | 默认值                          |
| ----------------------------------- | ----------------------------------- | ------------------------------- |
| `nameOverride`                      | 覆盖chart名称                        | `""`                           |
| `fullnameOverride`                  | 覆盖完整名称                         | `""`                           |
| `namespace`                         | 部署的命名空间                       | `default`                      |
| `rabbitmq.image.repository`         | RabbitMQ 镜像仓库                   | `192.168.3.53/base/rabbitmq`   |
| `rabbitmq.image.tag`                | RabbitMQ 镜像标签                   | `3.9.12-debian-10-r0`          |
| `rabbitmq.replicaCount`             | 副本数量                            | `1`                            |
| `rabbitmq.resources.limits.cpu`     | CPU 资源限制                        | `1`                            |
| `rabbitmq.resources.limits.memory`  | 内存资源限制                        | `2Gi`                          |
| `rabbitmq.persistence.enabled`      | 是否启用持久化                      | `true`                         |
| `rabbitmq.persistence.storageClass` | 存储类                             | `huawei-sc`                    |
| `rabbitmq.persistence.size`         | 持久卷大小                          | `10Gi`                         |
| `rabbitmq.auth.username`            | RabbitMQ 用户名                     | `guest`                        |
| `rabbitmq.auth.existingSecret`      | 含有密码和Erlang Cookie的现有Secret | `""`                           |
| `securityContext.fsGroup`           | Pod安全上下文文件系统组             | `1001`                         |
| `securityContext.runAsUser`         | Pod安全上下文用户                   | `1001`                         |
| `serviceAccount.create`             | 是否创建服务账号                     | `true`                         |
| `serviceAccount.name`               | 服务账号名称                        | `rabbitmq-cluster`             |

## 部署示例

### 单节点部署

使用默认配置即可部署单个 RabbitMQ 节点。

### 集群部署

创建一个名为 `cluster-values.yaml` 的文件：

```yaml
rabbitmq:
  replicaCount: 3
  resources:
    limits:
      cpu: "2"
      memory: 4Gi
  persistence:
    size: 20Gi
```

然后使用以下命令部署：

```bash
helm install rabbitmq-cluster ./rabbitmq-chart -f cluster-values.yaml
```

## 访问 RabbitMQ

### 管理界面

默认情况下，RabbitMQ 管理界面在端口 15672 上提供服务。

对于 ClusterIP 类型的服务，您可以使用端口转发来访问：

```bash
kubectl port-forward svc/my-rabbitmq 15672:15672
```

然后在浏览器中访问 http://localhost:15672。

### 客户端连接

对于 AMQP 连接，您可以使用以下地址：

```
my-rabbitmq.namespace.svc.cluster.local:5672
```

## 卸载

```bash
# 卸载 Chart
helm uninstall my-rabbitmq
```

请注意，这不会删除持久卷。如果您想删除持久卷，您需要手动删除它们。 