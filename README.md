# Helm Template with Istio

Kubernetes 部署套件 HELM 的基礎架構

## 範本說明

| 名稱                                      | 說明                                                                                                                                                                                                                                                                                                                              |
|-------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [nginx-server](./nginx-server)            | 沒有使用 istio 的基礎服務範本，也可以另外開啟 nginx ingress 與 argo rollout 支援，適合用在純前端或是不想用 istio 的後端服務。 **argo rollout 部分尚未進行測試**                                                                                                                                                                   |
| [queue-consumer](./queue-consumer)        | 搭配 keda 的服務部署設定，有倚賴 istio，但是 istio 的倚賴有考慮移除，因為 consumer 通常不需要外部連入，也比較沒有藍綠部署、金絲雀部署等需求 (因為部署上去就會開始接收 Queue，分流控制通常不在 ingress 上)。</br>目前仍有 istio 相關設定的原因是，保留 health 等外部直接查看服務狀態的頁面，但若追蹤、Log 等機制完善後就不需要了。 |
| [web-application](./web-application)      | 適合用在 .net MVC 的 Web Server，有倚賴 istio，可以開啟 argo rollout 支援。</br>有在 istio virtual service 中的 httpHeader 上加上 'x-forwarded-proto: https'                                                                                                                                                                      |
| [webApi](./webapi)                        | 適合用在純後端服務，有倚賴 istio，可以開啟 argo rollout 支援。                                                                                                                                                                                                                                                                    |
| [yarp gateway](./yarp-gateway-with-istio) | 大多數設定都與 [web-application](./web-application) 相同，但是因為主要要給 Yarp 做的 Gateway 使用，所以資源值沒有像 web application 那麼高。</br>有倚賴 istio，可以開啟 argo rollout 支援。                                                                                                                                       |

## Kubernetes 部署資源設定

此範本內的資源限制預設如下，可依據服務需求自行調整
設定方式與單位可參照 [此說明文件](kubernetes_overloading.md)

### 各環境建議值
`此處相關數值僅供參考，limits 的部份請依據服務所需與可用資源定義最大值，requests 的部分須確認服務在中低負載下的基礎需求定義，過高可能會導致 kubernetes 認為目前叢集內無資源可部署服務而部署失敗，過低可能會在資源極度不足的狀況下仍部署該服務而導致整個叢集的效能低落。`

* C# WebApi/Application - Base on bullseye-slim / buster-slim

```yaml
resources:
  requests:
    memory: "400Mi"
    cpu: "200m"
  limits:
    memory: "800Mi"
    cpu: "2000m"
```

* C# Application (Yarp Gateway) - Base on alpine

```yaml
resources:
  requests:
    memory: "200Mi"
    cpu: "100m"
  limits:
    memory: "600Mi"
    cpu: "1000m"
```

* Angular SPA - Base on nginx:alpine

**如果有設定 nginx 上進行 gzip 壓縮的話，CPU 可以拉高一些**

```yaml
resources:
  requests:
    memory: "100Mi"
    cpu: "100m"
  limits:
    memory: "200Mi"
    cpu: "500m"
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
