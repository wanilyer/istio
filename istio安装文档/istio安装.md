## 安装helm
直接使用官方提供的脚本安装即可
```
https://github.com/helm/helm/blob/release-2.16/scripts/get
```

## 安装tiller
```
kubectl apply -f install/kubernetes/helm/helm-service-account.yaml

helm init --service-account tiller -i registry.aliyuncs.com/google_containers/tiller:v2.16.1  --stable-repo-url https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts
```
+ 不使用 -i  参数会拉取镜像tiller失败

## 安装istio

```
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system

## 默认安装
helm install install/kubernetes/helm/istio --name istio --namespace istio-system

## 安装grafana
helm install install/kubernetes/helm/istio --name istio --namespace istio-system --set grafana.enabled=true

## 安装trace
--set tracing.enabled=true
```
在使用--set设置额外参数的时候会报以下错误：
```
Error: release istio failed: Internal error occurred: failed calling webhook "pilot.validation.istio.io": Post https://istio-galley.istio-system.svc:443/admitpilot?timeout=30s: dial tcp 10.97.227.10:443: connect: connection refused
```
解决方法：
```
kubectl get ValidatingWebhookConfiguration
kubectl delete  ValidatingWebhookConfiguration  istio-galley
helm delete --purge istio
helm install install/kubernetes/helm/istio --name istio --namespace istio-system --set enable-validation=false  --set grafana.enabled=true

```
+ 参考：https://github.com/istio/istio/issues/17162



## 删除istio
```
helm delete --purge istio
helm delete --purge istio-init
```

## 升级istio
```
helm upgrade --install istio install/kubernetes/helm/istio --namespace istio-system --set grafana.enabled=true --set tracing.enabled=true
```

## 命名空间自动注入istio边车
```
kubectl label ns nsname istio-injection=enabled
```
