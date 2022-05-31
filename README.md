# Helm Template with Istio

Kubernetes 部署套件 HELM 的基礎架構

## 注意事項

1. `Application-Base-On-Istio` 使用此範本前請確認以下工具已安裝於 kubernetes 網路叢集中
    1. istio
2. `Application-Base-On-Istio-Use-ArgoRollouts-Canary` 使用此範本前請確認以下工具已安裝於 kubernetes 網路叢集中
    1. istio
    2. Argo Rollout
3. `Application-NginxIngress-Use-ArgoRollouts-Canary` **此範本尚未經過測試**
    1. 使用此範本前請確認以下工具已安裝於 kubernetes 網路叢集中
        1. Argo Rollout
    2. 此範本中的 Rollout 設定可參考
        1. [argo rollout getting started - nginx](https://argoproj.github.io/argo-rollouts/getting-started/nginx/)
        2. [argo rollout traffic management - nginx](https://argoproj.github.io/argo-rollouts/features/traffic-management/nginx/)

## 使用方式：

1. 將 ServiceName 資料夾複製到 ArgoCD (或其他 GitOps 平台) 所使用的部署檔 Git Repo 之中
2. 資料夾更名為你要部署的服務名稱
3. 將資料夾內的 values-*.yaml 檔案開啟
    1. 將 {$ServiceName} 替換為你要部署的服務名稱
    2. 將 {$image-repo} docker image 來源
    3. 將 {$ServiceDomain} 更換為該服務的 Domain

## Kubernetes 部署資源設定

此範本內的資源限制預設如下，可依據服務需求自行調整
設定方式與單位可參照 [此說明文件](kubernetes_overloading.md)

### 各環境建議值

* C# WebApi - Base on 6.0-bullseye-slim / 3.1-buster-slim

```yaml
resources:
  requests:
    memory: "400Mi"
    cpu: "200m"
  limits:
    memory: "800Mi"
    cpu: "250M"
```

* C# WebApi - Base on 6.0-alpine

```yaml
resources:
  requests:
    memory: "100Mi"
    cpu: "200m"
  limits:
    memory: "400Mi"
    cpu: "250M"
```

* Angular SPA - Base on nginx:alpine

```yaml
resources:
  requests:
    memory: "50Mi"
    cpu: "100m"
  limits:
    memory: "200Mi"
    cpu: "200M"
```
## 命名相關建議

1. Helm Package 預設使用資料夾名稱作為套件名稱，因此資料夾名稱建議與服務名稱相符
1. 服務名稱建議使用全小寫，並以 `-` 字元作為分隔符號
1. values 中，以下項目應全小寫，並以 `-` / `_` / `.` 三個字元作為分隔符號 ( `-` 優先 )
    * system
    * service
    * istioIngressGateway
    * serviceAccount.name
    * istioGateway.name

REF
1. [[Kubernetes] 如何管理 Resource Object](https://godleon.github.io/blog/Kubernetes/k8s-CoreConcept-Wokring-with-Objects/)
2. [Kubernetes Doc - Recommended Labels](https://kubernetes.io/docs/concepts/overview/working-with-objects/common-labels/)
3. [9 Best Practices and Examples for Working with Kubernetes Labels](https://www.replex.io/blog/9-best-practices-and-examples-for-working-with-kubernetes-labels)
