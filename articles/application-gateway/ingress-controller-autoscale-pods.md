---
title: Azure 애플리케이션 게이트웨이 메트릭을 사용 하 여 자동 크기 조정 AKS pod
description: 이 문서에서는 Application Gateway 메트릭과 Azure Kubernetes Metric 어댑터를 사용 하 여 AKS 백엔드 pod 크기를 조정 하는 방법에 대한 지침을 제공 합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: b98ab8d3c4d03115ea689b4dfd3d8dee753f019d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715087"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>Application Gateway 메트릭을 사용 하 여 AKS pod 자동 크기 조정 (베타)

들어오는 트래픽이 늘어나면 수요에 따라 응용 프로그램을 확장 하는 것이 중요 합니다.

다음 자습서에서는 Application Gateway의 `AvgRequestCountPerHealthyHost` 메트릭을 사용 하 여 응용 프로그램을 확장 하는 방법을 설명 합니다. `AvgRequestCountPerHealthyHost`는 특정 백 엔드 풀 및 백 엔드 HTTP 설정 조합으로 전송 된 평균 요청을 측정 합니다.

다음 두 구성 요소를 사용할 예정입니다.

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) -메트릭 어댑터를 사용 하 여 메트릭 서버를 통해 Application Gateway 메트릭을 노출 합니다. Azure Kubernetes 메트릭 어댑터는 Application Gateway 수신 컨트롤러와 유사 하 게 Azure에서 오픈 소스 프로젝트입니다. 
* [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) -HPA를 사용 하 여 Application Gateway 메트릭을 사용 하 고 크기 조정을 위한 배포를 대상으로 합니다.

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>Azure Kubernetes 메트릭 어댑터 설정

1. 먼저 Azure AAD 서비스 주체를 만들고 Application Gateway의 리소스 그룹에 대한 액세스 `Monitoring Reader` 할당 합니다. 

    ```bash
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. 이제 위에서 만든 AAD 서비스 사용자를 사용 하 여 [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) 를 배포 합니다.

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principle created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. 이름 `appgw-request-count-metric`를 사용 하 여 `ExternalMetric` 리소스를 만듭니다. 이 리소스는 `myResourceGroup` 리소스 그룹의 `myApplicationGateway` 리소스에 대한 `AvgRequestCountPerHealthyHost` 메트릭을 노출 하도록 메트릭 어댑터에 지시 합니다. `filter` 필드를 사용 하 여 Application Gateway에서 특정 백 엔드 풀 및 백 엔드 HTTP 설정을 대상으로 지정할 수 있습니다.

    ```yaml
    apiVersion: azure.com/v1alpha2
    kind: ExternalMetric
    metadata:
    name: appgw-request-count-metric
    spec:
        type: azuremonitor
        azure:
            resourceGroup: myResourceGroup # replace with your application gateway's resource group name
            resourceName: myApplicationGateway # replace with your application gateway's name
            resourceProviderNamespace: Microsoft.Network
            resourceType: applicationGateways
        metric:
            metricName: AvgRequestCountPerHealthyHost
            aggregation: Average
            filter: BackendSettingsPool eq '<backend-pool-name>~<backend-http-setting-name>' # optional
    ```

이제 메트릭 서버에 대한 요청을 수행 하 여 새 메트릭이 노출 되는지 확인할 수 있습니다.
```bash
kubectl get --raw "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric"
# Sample Output
# {
#   "kind": "ExternalMetricValueList",
#   "apiVersion": "external.metrics.k8s.io/v1beta1",
#   "metadata":
#     {
#       "selfLink": "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric",
#     },
#   "items":
#     [
#       {
#         "metricName": "appgw-request-count-metric",
#         "metricLabels": null,
#         "timestamp": "2019-11-05T00:18:51Z",
#         "value": "30",
#       },
#     ],
# }
```

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>새 메트릭을 사용 하 여 배포 확장

메트릭 서버를 통해 `appgw-request-count-metric`을 노출할 수 있게 되 면 [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) 를 사용 하 여 대상 배포를 확장할 수 있습니다.

다음 예제에서는 `aspnet`샘플 배포를 대상으로 합니다. 최대 `10` Pod에 대한 Pod 당 200 > `appgw-request-count-metric` 때 Pod를 확장 합니다.

대상 배포 이름을 바꾸고 다음 자동 크기 조정 구성을 적용 합니다.
```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: deployment-scaler
spec:
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: aspnet # replace with your deployment's name
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: External
    external:
      metricName: appgw-request-count-metric
      targetAverageValue: 200
```

Apache 도구와 같은 부하 테스트 도구를 사용 하 여 설치를 테스트 합니다.
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>다음 단계
- [**수신 컨트롤러 문제 해결**](ingress-controller-troubleshoot.md): 수신 컨트롤러와 관련 된 문제를 해결 합니다.