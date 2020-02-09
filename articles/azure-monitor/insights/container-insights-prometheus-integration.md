---
title: 컨테이너에 대 한 Azure Monitor 구성 프로메테우스 통합 | Microsoft Docs
description: 이 문서에서는 Kubernetes 클러스터와 함께 프로메테우스의 메트릭을 스크랩 컨테이너 에이전트에 대 한 Azure Monitor를 구성 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: b774bf042778ca9118a7bc9f051655b200d87659
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75931414"
---
# <a name="configure-scraping-of-prometheus-metrics-with-azure-monitor-for-containers"></a>컨테이너에 대 한 Azure Monitor를 사용 하 여 프로메테우스 메트릭의 스크랩 구성

[프로메테우스](https://prometheus.io/) 는 널리 사용 되는 오픈 소스 메트릭 모니터링 솔루션으로, [클라우드 기본 계산 파운데이션](https://www.cncf.io/)의 일부입니다. 컨테이너에 대 한 Azure Monitor는 원활한 등록 환경을 제공 하 여 프로메테우스 메트릭을 수집 합니다. 일반적으로 프로메테우스를 사용 하려면 저장소를 사용 하 여 프로메테우스 서버를 설정 하 고 관리 해야 합니다. Azure Monitor와 통합 하면 프로메테우스 서버가 필요 하지 않습니다. 내보내기 또는 pod (응용 프로그램)을 통해 프로메테우스 메트릭 엔드포인트을 노출 하 고, 컨테이너에 대 한 Azure Monitor 용 컨테이너 화 된 에이전트가 메트릭을 스크랩 수 있습니다. 

![프로메테우스 용 컨테이너 모니터링 아키텍처](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>스크랩 프로메테우스 메트릭에 대해 지원 되는 최소 에이전트 버전은 ciprod07092019 이상 이며 `KubeMonAgentEvents` 테이블에서 구성 및 에이전트 오류를 기록 하는 데 지원 되는 에이전트 버전은 ciprod10112019입니다. 에이전트 버전 및 각 릴리스에 포함 되는 내용에 대 한 자세한 내용은 [에이전트 릴리스 정보](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod)를 참조 하세요. 에이전트 버전을 확인 하려면 **노드** 탭에서 노드를 선택 하 고 속성 창에서 **에이전트 이미지 태그** 속성의 값을 확인 합니다.

스크랩에서 호스트 되는 Kubernetes 클러스터에서 지원 되는 프로메테우스 메트릭은 다음과 같습니다.

- Azure Kubernetes Service(AKS)
- Azure Container Instances
- Azure Stack 또는 온-프레미스
- Azure Red Hat OpenShift

>[!NOTE]
>Azure Red Hat OpenShift의 경우 *openshift-Azure-로깅* 네임 스페이스에 템플릿 configmap 파일이 만들어집니다. 에이전트에서 메트릭 또는 데이터 수집을 적극적으로 스크랩 구성 되어 있지 않습니다.
>

## <a name="azure-red-hat-openshift-prerequisites"></a>Azure Red Hat OpenShift 필수 조건

시작 하기 전에 컨테이너 화 된 agent 및 프로메테우스 스크랩 설정을 구성 하려면 Azure Red Hat OpenShift 클러스터의 고객 클러스터 관리자 역할의 구성원 인지 확인 합니다. 사용자가 *osa-고객-admins* 그룹의 구성원 인지 확인 하려면 다음 명령을 실행 합니다.

``` bash
  oc get groups
```

출력은 다음과 유사합니다.

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

*Osa-고객-admins* 그룹의 구성원 인 경우 다음 명령을 사용 하 여 `container-azm-ms-agentconfig` configmap을 나열할 수 있습니다.

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

출력은 다음과 유사합니다.

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="prometheus-scraping-settings"></a>프로메테우스 스크랩 설정

프로메테우스의 활성 메트릭 스크랩는 다음 두 가지 관점 중 하나에서 수행 됩니다.

* 클러스터 전반의 HTTP URL로, 서비스의 나열 된 엔드포인트에서 대상을 검색 합니다. 예를 들어 kube와 같은 k8s 서비스 및 응용 프로그램과 관련 된 pod 주석을 사용할 수 있습니다. 이 컨텍스트에서 수집 된 메트릭은 ConfigMap 섹션 *[프로메테우스 data_collection_settings. cluster]* 에 정의 됩니다.
* 노드 전반의 HTTP URL로, 서비스의 나열 된 엔드포인트에서 대상을 검색 합니다. 이 컨텍스트에서 수집 된 메트릭은 ConfigMap 섹션 *[Prometheus_data_collection_settings. node]* 에 정의 됩니다.

| 엔드포인트 | 범위 | 예 |
|----------|-------|---------|
| Pod 주석 | 클러스터 전체 | 달 <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Kubernetes 서비스 | 클러스터 전체 | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| url/엔드포인트 | 노드당 및/또는 클러스터 전체 | `http://myurl:9101/metrics` |

URL을 지정 하면 Azure Monitor 컨테이너의 경우에만 엔드포인트을 스크랩 합니다. Kubernetes service를 지정 하면 서비스 이름이 클러스터 DNS 서버를 통해 확인 되어 IP 주소를 가져온 다음 확인 된 서비스가 스크랩 됩니다.

|범위 | 키 | 데이터 형식 | 값 | Description |
|------|-----|-----------|-------|-------------|
| 클러스터 전체 | | | | 다음 세 가지 방법 중 하나를 지정 하 여 메트릭에 대 한 엔드포인트을 스크랩. |
| | `urls` | String | 쉼표로 구분 된 배열 | HTTP 엔드포인트 (IP 주소 또는 올바른 URL 경로 중 하나). 예: `urls=[$NODE_IP/metrics]` $NODE _IP는 컨테이너 매개 변수에 대 한 특정 Azure Monitor 이며 노드 IP 주소 대신 사용할 수 있습니다. 모두 대문자 여야 합니다. |
| | `kubernetes_services` | String | 쉼표로 구분 된 배열 | Kube에서 메트릭을 스크랩 하는 Kubernetes services의 배열입니다. 예를 들면 `kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics", http://my-service-dns.my-namespace:9100/metrics]`입니다.|
| | `monitor_kubernetes_pods` | 부울 | true 또는 false | 클러스터 전체 설정에서 `true`로 설정 된 경우 컨테이너 에이전트에 대 한 Azure Monitor는 다음 프로메테우스 주석을 위해 전체 클러스터에서 Kubernetes pod를 스크랩 합니다.<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | 부울 | true 또는 false | Pod의 스크랩를 사용 하도록 설정 합니다. `monitor_kubernetes_pods`은 `true`로 설정해야 합니다. |
| | `prometheus.io/scheme` | String | HTTP 또는 HTTPS | 기본값은 HTTP over scrapping입니다. 필요한 경우 `https`으로 설정 합니다. | 
| | `prometheus.io/path` | String | 쉼표로 구분 된 배열 | 메트릭을 페치할 HTTP 리소스 경로입니다. 메트릭 경로가 `/metrics`되지 않은 경우이 주석을 사용 하 여 정의 합니다. |
| | `prometheus.io/port` | String | 9102 | 스크랩 포트를 지정 합니다. 포트가 설정 되지 않은 경우 기본값은 9102입니다. |
| | `monitor_kubernetes_pods_namespaces` | String | 쉼표로 구분 된 배열 | Kubernetes pod에서 메트릭을 스크랩 네임 스페이스 목록을 허용 합니다.<br> 예를 들어 `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| 노드 차원 | `urls` | String | 쉼표로 구분 된 배열 | HTTP 엔드포인트 (IP 주소 또는 올바른 URL 경로 중 하나). 예: `urls=[$NODE_IP/metrics]` $NODE _IP는 컨테이너 매개 변수에 대 한 특정 Azure Monitor 이며 노드 IP 주소 대신 사용할 수 있습니다. 모두 대문자 여야 합니다. |
| 노드 전체 또는 클러스터 전체 | `interval` | String | 60 초 | 컬렉션 간격 기본값은 1 분 (60 초)입니다. *[Prometheus_data_collection_settings]* 및/또는 *[prometheus_data_collection_settings]* 에 대 한 컬렉션을 시간 단위 (예: s, m, h)로 수정할 수 있습니다. |
| 노드 전체 또는 클러스터 전체 | `fieldpass`<br> `fielddrop`| String | 쉼표로 구분 된 배열 | 허용 (`fieldpass`) 및 허용 안 함 (`fielddrop`) 목록을 설정 하 여 엔드포인트에서 수집할 특정 메트릭을 지정할 수 있습니다. 먼저 허용 목록을 설정 해야 합니다. |

ConfigMaps는 전역 목록이 며 에이전트에 하나의 Configmaps만 적용 될 수 있습니다. 컬렉션에서 다른 ConfigMaps을 과도 하 게 사용할 수 없습니다.

## <a name="configure-and-deploy-configmaps"></a>ConfigMaps 구성 및 배포

Kubernetes 클러스터에 대 한 ConfigMap 구성 파일을 구성 하려면 다음 단계를 수행 합니다.

1. 템플릿 ConfigMap yaml 파일을 [다운로드](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) 하 고 azm로 저장 합니다.

   >[!NOTE]
   >ConfigMap 템플릿이 클러스터에 이미 있기 때문에 Azure Red Hat OpenShift를 사용 하는 경우에는이 단계가 필요 하지 않습니다.

2. 사용자 지정 항목을 사용 하 여 스크랩 프로메테우스 메트릭에 대 한 ConfigMap yaml 파일을 편집 합니다. Azure Red Hat OpenShift에 대 한 ConfigMap yaml 파일을 편집 하는 경우 먼저 명령 `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`를 실행 하 여 텍스트 편집기에서 파일을 엽니다.

    >[!NOTE]
    >조정을 방지 하려면 *azm-agentconfig* configmap의 메타 데이터 아래에 다음 주석 `openshift.io/reconcile-protect: "true"` 추가 해야 합니다. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Kubernetes services 클러스터 전체를 수집 하려면 다음 예제를 사용 하 여 ConfigMap 파일을 구성 합니다.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - 클러스터의 특정 URL에서 스크랩의 프로메테우스 메트릭을 구성 하려면 다음 예제를 사용 하 여 ConfigMap 파일을 구성 합니다.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - 클러스터의 모든 개별 노드에 대해 에이전트의 DaemonSet에서 스크랩의 프로메테우스 메트릭을 구성 하려면 ConfigMap에서 다음을 구성 합니다.
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE _IP는 컨테이너 매개 변수에 대 한 특정 Azure Monitor 이며 노드 IP 주소 대신 사용할 수 있습니다. 모두 대문자 여야 합니다. 

    - Pod 주석을 지정 하 여 스크랩의 프로메테우스 메트릭을 구성 하려면 다음 단계를 수행 합니다.

       1. ConfigMap에서 다음을 지정 합니다.

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Pod 주석에 대해 다음 구성을 지정 합니다.

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http:" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Pod에 대 한 특정 네임 스페이스 (예: 프로덕션 워크 로드 전용 pod 포함)로 모니터링을 제한 하려는 경우 ConfigMap에서 `true`로 `monitor_kubernetes_pod`를 설정 하 고 네임 스페이스 `monitor_kubernetes_pods_namespaces` 필터를 추가 하 여 네임 스페이스를 스크랩로 지정 합니다. 예를 들어 `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. Azure Red Hat OpenShift 이외의 클러스터의 경우 다음 kubectl 명령을 실행 합니다. `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    예: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Azure Red Hat OpenShift의 경우 편집기에서 변경 내용을 저장 합니다.

구성 변경 내용을 적용 하기 전에 완료 하는 데 몇 분 정도 걸릴 수 있으며, 클러스터의 모든 omsagent pod가 다시 시작 됩니다. 다시 시작은 모든 omsagent pod에 대 한 롤링 다시 시작 이지만 동시에 다시 시작 되지 않습니다. 다시 시작이 완료 되 면 다음과 유사한 메시지가 표시 되 고 결과에 `configmap "container-azm-ms-agentconfig" created`포함 됩니다.

`oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging`명령을 실행 하 여 Azure Red Hat OpenShift에 대 한 업데이트 된 ConfigMap을 볼 수 있습니다. 

## <a name="applying-updated-configmap"></a>업데이트 된 ConfigMap 적용

클러스터에 ConfigMap을 이미 배포 했 고 최신 구성으로 업데이트 하려는 경우 이전에 사용한 ConfigMap 파일을 편집한 다음 이전과 동일한 명령을 사용 하 여 적용할 수 있습니다.

Azure Red Hat OpenShift 이외의 Kubernetes 클러스터의 경우 `kubectl apply -f <configmap_yaml_file.yaml`명령을 실행 합니다. 

Azure Red Hat OpenShift 클러스터의 경우 `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` 명령을 실행 하 여 기본 편집기에서 파일을 열고 수정 하 고 저장 합니다.

구성 변경 내용을 적용 하기 전에 완료 하는 데 몇 분 정도 걸릴 수 있으며, 클러스터의 모든 omsagent pod가 다시 시작 됩니다. 다시 시작은 모든 omsagent pod에 대 한 롤링 다시 시작 이지만 동시에 다시 시작 되지 않습니다. 다시 시작이 완료 되 면 다음과 유사한 메시지가 표시 되 고 결과에 `configmap "container-azm-ms-agentconfig" updated`포함 됩니다.

## <a name="verify-configuration"></a>구성 확인

구성이 클러스터에 성공적으로 적용 되었는지 확인 하려면 다음 명령을 사용 하 여 에이전트 pod: `kubectl logs omsagent-fdf58 -n=kube-system`에서 로그를 검토 합니다. 

>[!NOTE]
>이 명령은 Azure Red Hat OpenShift 클러스터에 적용 되지 않습니다.
> 

Omsagent pod의 구성 오류가 있으면 출력에 다음과 유사한 오류가 표시 됩니다.

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

구성 변경 내용을 적용 하는 것과 관련 된 오류도 검토할 수 있습니다. 다음 옵션을 사용 하 여 스크랩의 구성 변경 및의 추가 문제 해결을 수행할 수 있습니다.

- 동일한 `kubectl logs` 명령을 사용 하 여 에이전트 pod 로그에서 
    >[!NOTE]
    >이 명령은 Azure Red Hat OpenShift 클러스터에 적용 되지 않습니다.
    > 

- 라이브 데이터 (미리 보기)를 기반으로 합니다. 라이브 데이터 (미리 보기) 로그에는 다음과 비슷한 오류가 표시 됩니다.

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- Log Analytics 작업 영역에 있는 **KubeMonAgentEvents** 테이블의 스크랩 오류에 대 한 *경고* 심각도와 구성 오류에 대 한 *오류* 심각도를 사용 하 여 매시간 데이터가 전송 됩니다. 오류가 없는 경우 테이블의 항목에는 오류를 보고 하지 않는 심각도 *정보*를 포함 하는 데이터가 포함 됩니다. **Tags** 속성에는 지난 1 시간 동안 오류가 발생 한 pod 및 컨테이너 ID와 처음 발생 한 항목, 마지막 항목 및 개수에 대 한 자세한 정보가 포함 되어 있습니다.

- Azure Red Hat OpenShift의 경우 **ContainerLog** 테이블을 검색 하 여 omsagent 로그를 확인 하 여 openshift-Azure-logging의 로그 수집이 사용 되는지 확인 합니다.

오류가 발생 하면 omsagent에서 파일을 구문 분석 하 여 다시 시작 되 고 기본 구성을 사용 하 게 됩니다. Azure Red Hat OpenShift 이외의 클러스터에서 ConfigMap의 오류를 수정한 후에는 yaml 파일을 저장 하 고 `kubectl apply -f <configmap_yaml_file.yaml`명령을 실행 하 여 업데이트 된 Configmap을 적용 합니다. 

Azure Red Hat OpenShift의 경우 `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`명령을 실행 하 여 업데이트 된 ConfigMaps을 편집 하 고 저장 합니다.

## <a name="query-prometheus-metrics-data"></a>프로메테우스 메트릭 데이터 쿼리

Azure Monitor에서 스크랩 하는 프로메테우스 메트릭과 에이전트에서 보고 한 모든 configuration/스크랩 오류를 보려면 [프로메테우스 메트릭 데이터 쿼리](container-insights-log-search.md#query-prometheus-metrics-data) 및 [쿼리 구성 또는 스크랩 오류](container-insights-log-search.md#query-config-or-scraping-errors)를 검토 합니다.

## <a name="view-prometheus-metrics-in-grafana"></a>Grafana에서 프로메테우스 메트릭 보기

컨테이너 Azure Monitor는 Grafana 대시보드의 Log Analytics 작업 영역에 저장 된 메트릭 보기를 지원 합니다. 사용자 지정 Grafana 대시보드를 시각화 하기 위해 모니터링 되는 클러스터의 추가 데이터를 쿼리 하는 방법을 배우는 데 도움이 되는 Grafana의 [대시보드 리포지토리에서](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) 다운로드할 수 있는 템플릿을 제공 했습니다. 

## <a name="review-prometheus-data-usage"></a>프로메테우스 데이터 사용 검토

각 메트릭의 수집 볼륨 크기를 매일 GB 단위로 식별 하 여 높은 경우에는 다음 쿼리를 제공 합니다.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```
출력은 다음과 유사한 결과를 표시 합니다.

![데이터 수집 볼륨의 쿼리 결과를 기록 합니다.](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

작업 영역에서 수신 되는 데이터 수집의 양이 높은 경우에는 한 달에 각 메트릭 크기 (GB)가 무엇 인지 파악 하기 위해 다음 쿼리가 제공 됩니다.

```
InsightsMetrics 
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

출력은 다음과 유사한 결과를 표시 합니다.

![데이터 수집 볼륨의 쿼리 결과를 기록 합니다.](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

데이터 사용량을 모니터링 하 고 비용을 분석 하는 방법에 대 한 자세한 내용은 [Azure Monitor 로그를 사용 하 여 사용량 및 비용 관리](../platform/manage-cost-storage.md)에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[여기](container-insights-agent-config.md)에서 컨테이너 워크 로드의 stdout, stderr 및 환경 변수에 대 한 에이전트 컬렉션 설정 구성에 대해 자세히 알아보세요. 
