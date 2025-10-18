# Kubernetes 全栈监控解决方案

##  项目概述

基于 Prometheus Stack 的完整 Kubernetes 监控解决方案，实现云原生应用和非云原生应用的全面监控、告警和可视化。

##  架构设计

### 核心组件

- **Prometheus** - 时序数据收集与存储
- **Grafana** - 数据可视化与仪表盘
- **Alertmanager** - 智能告警管理
- **各类 Exporters** - 监控数据采集
- **Prometheus Operator** - Kubernetes 原生部署与管理

### 监控流程

```
云原生应用 → Service → ServiceMonitor → Prometheus
非云原生应用 → Exporter → Service → ServiceMonitor → Prometheus
```

##  功能特性

###  已实现功能

- [x] Kubernetes 集群基础监控
- [x] 云原生应用监控（Etcd 实战）
- [x] 非云原生应用监控（MySQL 实战）
- [x] 黑盒监控（网站可用性检测）
- [x] 自定义告警规则与路由
- [x] 邮件告警通知
- [x] 自定义告警模板
- [x] 多维度数据可视化

###  技术栈

- **容器编排**: Kubernetes 1.28.7
- **监控采集**: Prometheus 2.36.1
- **可视化**: Grafana 8.5.5
- **告警管理**: Alertmanager 0.24.0
- **服务发现**: ServiceMonitor CRD
- **导出器**: Node Exporter, MySQL Exporter, Blackbox Exporter

##  项目结构

```
kube-prometheus/
├── manifests/
│   ├── setup/                    # CRD 和 Operator 安装
│   ├── prometheus-operator/      # Operator 相关配置
│   ├── prometheus/              # Prometheus 主配置
│   ├── grafana/                 # Grafana 配置
│   ├── alertmanager/            # 告警配置
│   └── exporters/               # 各类导出器
├── custom-rules/                # 自定义告警规则
├── dashboards/                  # Grafana 仪表盘
└── documentation/               # 部署文档
```

## 🔧 快速开始

### 1. 环境要求

- Kubernetes 1.19+
- Helm 3.0+
- 至少 4GB 可用内存

### 2. 部署监控栈

```bash
# 克隆项目
git clone -b release-0.14 https://github.com/prometheus-operator/kube-prometheus.git

# 安装 CRD 和 Operator
kubectl create -f manifests/setup/

# 部署监控组件
kubectl create -f manifests/
```

### 3. 访问服务

```bash
# 将服务类型改为 NodePort
kubectl patch svc grafana -n monitoring -p '{"spec": {"type": "NodePort"}}'
kubectl patch svc prometheus-k8s -n monitoring -p '{"spec": {"type": "NodePort"}}'

# 获取访问端口
kubectl get svc -n monitoring
```

## 📈 监控案例

### 云原生应用监控 - Etcd

- 配置 Service 和 Endpoints
- 创建 TLS 证书 Secret
- 部署 ServiceMonitor
- 实现完整的监控数据流

### 非云原生应用监控 - MySQL

- 部署 MySQL Exporter
- 配置数据库权限
- 创建监控 Service
- 设置数据采集规则

### 黑盒监控

- 网站可用性检测
- HTTP 响应时间监控
- SSL 证书过期检查
- 自定义探测模块

## 🔔 告警系统

### 告警配置

- **路由规则**: 基于标签的分组和路由
- **抑制规则**: 防止告警风暴
- **静默配置**: 临时屏蔽特定告警
- **模板定制**: 自定义告警通知格式

### 通知渠道

- 📧 邮件通知
- 💬 企业微信
- 🔔 PagerDuty
- 🌐 Webhook

## 🎯 核心技能展示

### Kubernetes 运维

- 熟练使用 Prometheus Operator 管理监控栈
- 精通 ServiceMonitor 和 PodMonitor 配置
- 掌握 Kubernetes 服务发现机制
- 熟悉 CRD 和自定义资源管理

### 监控体系建设

- 设计完整的监控数据流水线
- 实现多维度指标采集和存储
- 构建可视化监控仪表盘
- 建立智能告警管理体系

### 云原生技术

- 容器化部署和管理
- 微服务监控方案
- 自动扩缩容监控
- 分布式系统追踪

### 故障排查能力

- 监控目标发现问题诊断
- 数据采集链路排查
- 告警路由问题分析
- 性能瓶颈定位

## 📊 监控指标覆盖

### 基础设施层

- 节点资源使用率（CPU、内存、磁盘、网络）
- 容器运行状态和资源限制
- 存储卷使用情况
- 网络连接和带宽

### 应用层

- 应用业务指标
- 服务响应时间
- 错误率和异常检测
- 数据库性能指标

### Kubernetes 组件

- API Server 性能
- Controller Manager 状态
- Scheduler 调度指标
- Etcd 集群健康度

## 🚨 告警规则示例

```yaml
- alert: DomainAccessDelayExceeds1s
  expr: sum(probe_http_duration_seconds{job=~"blackbox"}) by (instance) > 0.5
  for: 1m
  labels:
    severity: warning
  annotations:
    description: "域名 {{ $labels.instance }} 访问延迟超过 0.5s，当前值: {{ $value }}"
    summary: "网站访问延迟告警"
```

## 📝 使用说明

### 添加新监控目标

1. 为应用创建 Service
2. 配置对应的 ServiceMonitor
3. 验证指标采集状态
4. 在 Grafana 创建仪表盘

### 自定义告警

1. 创建 PrometheusRule 资源
2. 配置告警路由规则
3. 设置通知模板
4. 测试告警触发
