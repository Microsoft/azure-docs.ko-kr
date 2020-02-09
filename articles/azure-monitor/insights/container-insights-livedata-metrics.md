---
title: 컨테이너에 대 한 Azure Monitor를 실시간으로 메트릭 보기 | Microsoft Docs
description: 이 문서에서는 컨테이너의 Azure Monitor with kubectl를 사용 하지 않고 메트릭의 실시간 보기에 대해 설명 합니다.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: fbb08a8ed3deeff061065916241ee2d724603be3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75404942"
---
# <a name="how-to-view-metrics-in-real-time"></a>실시간으로 메트릭을 보는 방법

컨테이너 라이브 데이터 (미리 보기) 기능에 대 한 Azure Monitor를 사용 하면 클러스터의 노드 및 pod 상태에 대 한 메트릭을 실시간으로 시각화할 수 있습니다. 이 정보에 포함 된 성능 차트에서 데이터를 호출 하 고, 구문 분석 하 고, 시각화 하기 위해 `kubectl top nodes`, `kubectl get pods –all-namespaces`및 `kubectl get nodes` 명령에 직접 액세스 하는 것을 에뮬레이트합니다. 

이 문서에서는 자세한 개요를 제공 하 고이 기능을 사용 하는 방법을 이해 하는 데 도움을 줍니다.  

>[!NOTE]
>[개인 클러스터](https://azure.microsoft.com/updates/aks-private-cluster/) 로 설정 된 AKS 클러스터는이 기능에서 지원 되지 않습니다. 이 기능은 브라우저에서 프록시 서버를 통해 Kubernetes API에 직접 액세스 하는 데 의존 합니다. 이 프록시의 Kubernetes API를 차단 하도록 네트워킹 보안을 사용 하도록 설정 하면이 트래픽이 차단 됩니다. 

>[!NOTE]
>이 기능은 Azure 중국을 비롯 한 모든 Azure 지역에서 사용할 수 있습니다. 현재는 Azure 미국 정부에서 사용할 수 없습니다.

라이브 데이터 (미리 보기) 기능 설정 또는 문제 해결에 대 한 도움말은 [설치 가이드](container-insights-livedata-setup.md)를 참조 하세요.

## <a name="how-it-works"></a>적용 방법 

라이브 데이터 (미리 보기) 기능은 Kubernetes API에 직접 액세스할 수 있으며 인증 모델에 대 한 추가 정보는 [여기](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)에서 찾을 수 있습니다. 

이 기능은 메트릭 엔드포인트 (`/api/v1/nodes`, `/apis/metrics.k8s.io/v1beta1/nodes`및 `/api/v1/pods`포함)에 대해 폴링 작업을 수행 합니다 .이 작업은 기본적으로 5 초 마다 수행 됩니다. 이 데이터는 브라우저에 캐시 되 고 **클러스터** 탭의 컨테이너에 대 한 Azure Monitor에 포함 된 4 개의 성능 차트에서 **라이브 이동 (미리 보기)** 을 선택 하 여 차트에 포함 됩니다. 각 후속 폴링은 5 분의 롤링 시각화 창에 차트로 작성 됩니다. 

![클러스터 뷰에서 라이브로 전환 옵션](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

폴링 간격은 1, 5, 15 및 30 초 마다 새 데이터에 대 한 폴링을 설정할 수 있도록 **설정 된 간격** 드롭다운에서 구성 됩니다. 

![라이브 이동 드롭다운 폴링 간격](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.ping.png)

>[!IMPORTANT]
>짧은 시간 동안 문제를 해결 하는 동안 폴링 간격을 1 초로 설정 하는 것이 좋습니다. 이러한 요청은 클러스터의 Kubernetes API에 대 한 가용성 및 제한에 영향을 줄 수 있습니다. 그런 다음 더 긴 폴링 간격으로 다시 구성 합니다. 

>[!IMPORTANT]
>이 기능을 수행 하는 동안 데이터가 영구적으로 저장 되지 않습니다. 브라우저를 닫거나 기능에서 벗어나 이동할 때이 세션 중에 캡처된 모든 정보가 즉시 삭제 됩니다. 데이터는 5 분 내에 시각화에 대해서만 표시 됩니다. 5 분 보다 오래 된 메트릭도 영구적으로 삭제 됩니다.

이러한 차트는 라이브 모드에서 본 마지막 Azure 대시보드에 고정할 수 없습니다.

## <a name="metrics-captured"></a>캡처된 메트릭

### <a name="node-cpu-utilization---node-memory-utilization-"></a>노드 CPU 사용률%/노드 메모리 사용률 (%) 

이러한 두 성능 차트는 `kubectl top nodes`를 호출 하 고 **CPU%** 및 **메모리%** 열의 결과를 해당 차트에 캡처하는 것과 동일한에 매핑됩니다. 

![Kubectl top nodes 예제 결과](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![노드 CPU 사용률 비율 차트](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![노드 메모리 사용률 비율 차트](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

백분위 수 계산은 클러스터의 이상 값 노드를 식별 하는 데 도움이 되도록 대규모 클러스터에서 작동 합니다. 예를 들어 노드가 규모 축소를 위해 미달 사용 되는지 이해 하기 위해 **Min** aggregation를 활용 하면 클러스터에서 사용률이 낮은 노드를 확인할 수 있습니다. 자세히 조사 하려면 **노드** 탭을 선택 하 고 CPU 또는 메모리 사용률을 기준으로 그리드를 정렬 합니다.

이를 통해 제한에 푸시되는 노드를 파악 하 고 수평 확장이 필요할 수도 있습니다. **Max** 및 **P95** 집계를 모두 사용 하면 리소스 사용률이 높은 클러스터에 노드가 있는지 확인 하는 데 도움이 될 수 있습니다. 추가 조사를 위해 다시 **노드** 탭으로 전환 합니다.

### <a name="node-count"></a>노드 수

이 성능 차트는 `kubectl get nodes`를 호출 하 고 상태 열을 상태 유형별로 **그룹화 된 차트** 에 매핑하는 것과 동일한에 매핑됩니다.

![Kubectl get 노드 예제 결과](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![노드 수 차트](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

노드가 **준비** 됨 또는 **준비 되지 않음** 상태로 보고 됩니다. 이러한 집계는 계산 되어 총 개수를 생성 하 고 이러한 두 집계의 결과는 차트로 표현 됩니다.
예를 들어 노드가 실패 상태를 벗어난 경우를 이해 합니다. **준비 안** 됨 집계를 활용 하면 현재 클러스터에서 **준비 되지 않음** 상태로 노드 수를 빠르게 확인할 수 있습니다.

### <a name="active-pod-count"></a>활성 pod 수

이 성능 차트는 `kubectl get pods –all-namespaces`를 호출 하는 것과 동일 하 게 매핑되고, 차트에서 상태 유형을 기준으로 그룹화 된 **상태** 열을 매핑합니다.

![Kubectl get pod 예제 결과](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![노드 pod 개수 차트](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>`kubectl` 해석 된 상태 이름은 차트에서 정확 하 게 일치 하지 않을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

[로그 쿼리 예](container-insights-log-search.md#search-logs-to-analyze-data) 를 확인 하 여 미리 정의 된 쿼리 및 예제를 확인 하 여 경고, 시각화를 만들거나 클러스터에 대 한 추가 분석을 수행 합니다.
