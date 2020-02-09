---
title: 컨테이너 배포에 대 한 Azure Monitor 보기 (미리 보기) | Microsoft Docs
description: 이 문서에서는 컨테이너에 Azure Monitor kubectl을 사용 하지 않고 Kubernetes 배포에 대 한 실시간 보기를 설명 합니다.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 7d0344851e1db8c014a1bb16b228a0c2f76444d5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75404766"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>실시간으로 배포 (미리 보기)를 보는 방법

컨테이너에 대 한 Azure Monitor를 사용 하 여 배포 보기 (미리 보기) 기능은 `kubeclt get deployments` 및 `kubectl describe deployment {your deployment}` 명령을 노출 하 여 Kubernetes 배포 개체에 대 한 직접 액세스를 에뮬레이트합니다. 

>[!NOTE]
>[개인 클러스터](https://azure.microsoft.com/updates/aks-private-cluster/) 로 설정 된 AKS 클러스터는이 기능에서 지원 되지 않습니다. 이 기능은 브라우저에서 프록시 서버를 통해 Kubernetes API에 직접 액세스 하는 데 의존 합니다. 이 프록시의 Kubernetes API를 차단 하도록 네트워킹 보안을 사용 하도록 설정 하면이 트래픽이 차단 됩니다. 

>[!NOTE]
>이 기능은 Azure 중국을 비롯 한 모든 Azure 지역에서 사용할 수 있습니다. 현재는 Azure 미국 정부에서 사용할 수 없습니다.

자세히 알아보려면 [배포](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)에 대 한 Kubernetes 설명서를 검토 하세요. 

## <a name="how-it-works"></a>작동 원리

라이브 데이터 (미리 보기) 기능은 Kubernetes API에 직접 액세스할 수 있으며 인증 모델에 대 한 추가 정보는 [여기](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)에서 찾을 수 있습니다. 

배포 (미리 보기) 기능은 배포 엔드포인트 `/apis/apps/v1/deployments`에 대해 한 번 (새로 고칠 때) 부하를 수행 합니다. 지정 된 배포를 선택 하 고 배포 엔드포인트 `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}`에 대 한 특정 배포에 대 한 설명 세부 정보를 로드할 수 있습니다. 

페이지의 왼쪽 위에 있는 **새로 고침** 을 선택 하면 배포 목록이 새로 고쳐집니다. 이는 `kubectl` 명령을 다시 실행 하는 것을 시뮬레이션 합니다. 

>[!IMPORTANT]
>이 기능을 수행 하는 동안 데이터가 영구적으로 저장 되지 않습니다. 세션 중에 캡처된 모든 정보는 브라우저를 닫거나 다른 곳으로 이동 하면 삭제 됩니다.  

>[!NOTE]
>콘솔에서 Azure 대시보드에 라이브 데이터 (미리 보기) 데이터를 고정할 수 없습니다.

## <a name="deployments-describe"></a>배포 설명

`kubectl describe deployment`에 해당 하는 배포에 대 한 세부 정보를 보려면 다음 단계를 수행 합니다.

1. Azure Portal에서 AKS cluster 리소스 그룹으로 이동 하 여 AKS 리소스를 선택 합니다.

2. AKS 클러스터 대시보드의 왼쪽에 있는 **모니터링** 에서 **Insights**를 선택 합니다. 

3. **배포 (미리 보기)** 탭을 선택 합니다.

    ![Azure Portal의 배포 보기](./media/container-insights-livedata-deployments/deployment-view.png)

이 보기는 실행 중인 모든 배포 목록과 네임 스페이스 및 기타 세부 정보를 표시 하 여 명령 `kubectl get deployments –all-namespaces`의 실행을 에뮬레이션 합니다. 열 중 하나를 선택 하 여 결과를 정렬할 수 있습니다. 

![배포 속성 창 세부 정보](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

목록에서 배포를 선택 하면 속성 창이 페이지의 오른쪽에 자동으로 표시 됩니다. `kubectl describe deployment {deploymentName}`명령을 실행 한 경우 표시 되는 선택 된 배포와 관련 된 정보를 표시 합니다. 설명 정보에 몇 가지 세부 정보가 누락 된 것을 알 수 있습니다. 가장 특히 **템플릿이** 없습니다. **원시** 탭을 선택 하면 구문 분석 되지 않은 설명 세부 정보로 이동할 수 있습니다.  

![배포 속성 창 원시 정보](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

배포 세부 정보를 검토 하는 동안 컨테이너 로그 및 이벤트를 실시간으로 볼 수 있습니다. 연속 스트림에서 라이브 로그 데이터를 볼 수 있는 배포 데이터 그리드 아래에 **라이브 콘솔** 및 라이브 데이터 표시 (미리 보기) 콘솔 창이 표시 됩니다. 인출 상태 표시기가 창의 맨 오른쪽에 있는 녹색 확인 표시를 표시 하는 경우 데이터를 검색 하 고 콘솔에 대 한 스트리밍을 시작 하는 것을 의미 합니다.

네임 스페이스 또는 클러스터 수준 이벤트를 기준으로 필터링 할 수도 있습니다. 콘솔에서 실시간으로 데이터를 보는 방법에 대 한 자세한 내용은 [컨테이너의 Azure Monitor 사용 하 여 라이브 데이터 보기 (미리 보기)](container-insights-livedata-overview.md)를 참조 하세요. 

![배포 콘솔에서 라이브 데이터 보기](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>다음 단계

- Azure Monitor를 사용하고 AKS 클러스터의 다른 측면을 모니터링하는 방법을 계속 알아보려면 [Azure Kubernetes 서비스 상태](container-insights-analyze.md)를 참조하세요.

- [로그 쿼리 예](container-insights-log-search.md#search-logs-to-analyze-data) 를 확인 하 여 미리 정의 된 쿼리 및 예제를 확인 하 여 경고, 시각화를 만들거나 클러스터에 대 한 추가 분석을 수행 합니다.
