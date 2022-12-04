# 文件說明

此套件是依據以下範本產生後重新調整的套件，並移除 istio 相關的內容。

* https://github.com/salesforce/helm-starter
* https://github.com/salesforce/helm-starter-istio

# 原始說明

## Installation

```sh
> helm template --namespace=[namespace] [chartname] | kubectl apply -f -
```

## Values.yaml

All configuration for this installation is managed in `values.yaml`. Configuration
values can be overriden individually at installation using Helm's `--set` command
line option.

### Service Identity

These three values control the names of generated Kubernetes and Istio objects,
and are used to ensure commont Kubernetes labeling. These values are used to populate
labels that allow for selecting all components of a particular system or service.

* `system`, `service`, `version` - These values describe _what_ this service and
  what it should be named. For example: `my-website`, `web-server`, `2`.

### Container Values

These settings control from where and how your service's docker image is acquired.

* `image.repository` - The docker repo and image to pull.
* `image.tag` - The docker image tag to pull.
* `image.imagePullPolicy` - Kubernetes image pull policy.

### Service Account Values

Istio request authorization requires that each service have a unique service account
identity to functuion correctly.

* `serviceAccount.name` - The Kubernetes service account your service will run under.
* `serviceAccount.create` - Optionally, this chart can generate the service account.
  If false, the service's service account must be pre-existing.

### Replica Values

These settings control service replicas, disruption budgets, and autoscaling.

* `replicaCount` - The initial number of replicas to start after installing this
  chart.
* `maxUnavailable` - The maximum number of intentionally unavailable pods as
  controlled by a `PodDisruptionBudget`.
* `autoscaling.minReplicas` - The minimum number of replicas to run under the
  control of a `HorizontalPodAutoscaler`.
* `autoscaling.maxReplicas` - The maximum number of replicas to run under the
  control of a `HorizontalPodAutoscaler`.
* `autoscaling.targetAverageCpuUtilization` - The CPU utilization target
  used by the `HorizontalPodAutoscaler` to make autoscale decisions.

### Kubernetes Pod Values

These settings configure your service's resource constraints and health check
probes. They ensure your service is a well behaved consumer of shared Kubernetes
resources.

* `resources.*` - Kubernetes resource request and limit configuration. See
  [Kubernetes resource documentation](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) for values.
* `probes.*` - Kubernetes probe configuration. See [Kubernetes probe documentation](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/) for values.

### ConfigMap Values

These optional settings are used to populate and mount a configmap for your
service. When the generated config map changes, the associated service is automatically
resterted using a rolling restart. Generating the configmap from Helm chart values
is useful because it allows you to modify config map values durring installation
using Helm `--set` directives.

* `configMap.mountPath` - The directory inside your pod to mount the config map.
* `configMap.fileName` - The file name of the config map, when mounted in the pod.
* `configMap.content.*` - YAML keys and values under `content` are copied verbatim
  into the configmap's content.
