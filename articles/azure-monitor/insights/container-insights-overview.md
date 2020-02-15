---
title: 컨테이너용 Azure Monitor 개요 | Microsoft Docs
description: 이 문서에서는 AKS Container Insights 솔루션을 모니터링하는 컨테이너용 Azure Monitor 및 Azure Monitor가 Azure의 AKS 클러스터 및 Container Instances 상태를 모니터링하여 제공하는 가치에 대해 설명합니다.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 3ff2c35ae9f5838447ce90e2a020649427920a43
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198040"
---
# <a name="azure-monitor-for-containers-overview"></a>컨테이너용 Azure Monitor 개요

컨테이너에 대 한 Azure Monitor는에 배포 된 컨테이너 워크 로드의 성능을 모니터링 하도록 설계 된 기능입니다.

- [AKS (Azure Kubernetes Service)](../../aks/intro-kubernetes.md) 에서 호스트 되는 Managed Kubernetes 클러스터
- [AKS 엔진](https://github.com/Azure/aks-engine) 을 사용 하 여 Azure에서 호스트 되는 자체 관리 되는 Kubernetes 클러스터
- [Azure Container Instances](../../container-instances/container-instances-overview.md)
- [Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) 또는 온-프레미스에서 호스트 되는 자체 관리 되는 Kubernetes 클러스터
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

컨테이너 Azure Monitor Linux 및 Windows Server 2019 운영 체제를 실행 하는 클러스터를 지원 합니다. 

컨테이너 모니터링은 특히 여러 애플리케이션을 사용하여 대규모의 프로덕션 클러스터를 실행하는 경우에 중요합니다.

컨테이너용 Azure Monitor는 Metrics API를 통해 Kubernetes에서 사용할 수 있는 컨트롤러, 노드 및 컨테이너의 메모리 및 프로세서 메트릭을 수집하여 성능을 시각적으로 표시합니다. 컨테이너 로그도 수집됩니다.  Kubernetes 클러스터에서 모니터링을 사용 하도록 설정 하면 메트릭 및 로그가 Linux 용 Log Analytics 에이전트의 컨테이너 화 된 버전을 통해 자동으로 수집 됩니다. 메트릭은 메트릭 저장소에 기록 되 고 로그 데이터는 [Log Analytics](../log-query/log-query-overview.md) 작업 영역과 연결 된 로그 저장소에 기록 됩니다. 

![컨테이너 아키텍처에 대 한 Azure Monitor](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>컨테이너용 Azure Monitor에서 제공하는 기능은 무엇인가요?

컨테이너 Azure Monitor은 Azure Monitor의 다양 한 기능을 사용 하 여 포괄적인 모니터링 환경을 제공 합니다. 이러한 기능을 사용 하 여 Linux 및 Windows Server 2019 운영 체제 및 컨테이너 워크 로드를 실행 하는 Kubernetes 클러스터의 성능 및 상태를 이해할 수 있습니다. 컨테이너에 대 한 Azure Monitor를 사용 하 여 다음을 수행할 수 있습니다.

* 노드에서 실행 중인 AKS 컨테이너와 평균 프로세서 및 메모리 사용률을 확인합니다. 이 정보를 통해 리소스 병목 상태를 파악할 수 있습니다.
* Azure Container Instances에 호스트된 컨테이너 그룹 및 해당 컨테이너의 프로세서 및 메모리 사용률을 식별합니다.  
* 컨트롤러 또는 Pod에서 컨테이너가 상주하는 위치를 확인합니다. 이 정보를 통해 컨트롤러 또는 Pod의 전반적인 성능을 볼 수 있습니다. 
* Pod를 지원하는 표준 프로세스와 관련이 없는 호스트에서 실행되는 워크로드의 리소스 사용률을 검토합니다.
* 평균 부하 및 최고 부하 상태에서.클러스터의 동작을 이해합니다. 이 정보를 통해 용량 요구 사항을 파악하고 클러스터를 유지할 수 있는 최대 부하를 확인할 수 있습니다. 
* 노드 또는 컨테이너의 CPU 및 메모리 사용률이 임계값을 초과 하거나 인프라 또는 노드 상태 롤업에서 클러스터의 상태 변경이 발생 하는 경우 사전에 알리도록 경고를 구성 하거나 기록 합니다.
* [프로메테우스](https://prometheus.io/docs/introduction/overview/) 와 통합 하 여 사용자 지정 경고, 대시보드 및 자세한 분석을 만드는 [쿼리](container-insights-log-search.md) 를 사용 하 여 노드 및 Kubernetes에서 수집한 응용 프로그램 및 워크 로드 메트릭을 볼 수 있습니다.
* Azure Stack에서 AKS 엔진 온-프레미스 및 [AKS engine](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908) [에 배포](https://github.com/Azure/aks-engine) 된 컨테이너 워크 로드를 모니터링 합니다.
* [Azure Red Hat OpenShift에 배포](../../openshift/intro-openshift.md)된 컨테이너 워크 로드를 모니터링 합니다.

    >[!NOTE]
    >Azure Red Hat OpenShift에 대 한 지원은 현재 공개 미리 보기의 기능입니다.
    >

Linux 클러스터와 비교 하 여 Windows Server 클러스터 모니터링의 주요 차이점은 다음과 같습니다.

- Windows 노드 및 컨테이너에는 메모리 RSS 메트릭을 사용할 수 없습니다.
- Windows 노드에는 디스크 저장소 용량 정보를 사용할 수 없습니다.
- Windows 노드에서 실행 중인 컨테이너에 대해 컨테이너 로그를 사용할 수 없습니다.
- 라이브 데이터 (미리 보기) 기능 지원은 Windows 컨테이너 로그를 제외 하 고 사용할 수 있습니다.
- Pod 환경을 모니터링 하 고 Docker 환경만 모니터링 합니다.
- Preview 릴리스를 사용 하는 경우 최대 30 개의 Windows Server 컨테이너가 지원 됩니다. 이 제한은 Linux 컨테이너에는 적용 되지 않습니다. 

컨테이너에 대 한 Azure Monitor를 사용 하 여 AKS 클러스터를 모니터링 하는 방법을 알아보는 데 도움이 되는 중간 수준의 심층 정보를 제공 하는 다음 비디오를 확인 하세요.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>이 기능에 액세스하려면 어떻게 하나요?

컨테이너용 Azure Monitor는 Azure Monitor에서 액세스하거나 선택한 AKS 클러스터에서 직접 액세스할 수 있습니다. Azure Monitor에서 모니터링 되 고, 그렇지 않은 모든 컨테이너의 글로벌 큐브 뷰를 통해 구독 및 리소스 그룹을 검색 하 고 필터링 한 다음, 다음에서 컨테이너에 대 한 Azure Monitor을 드릴 수 있습니다. 컨테이너를 선택 했습니다.  그렇지 않으면 AKS 페이지에서 선택한 AKS 컨테이너에서 직접 기능에 액세스할 수 있습니다.  

![컨테이너용 Azure Monitor에 액세스하는 방법에 대한 개요](./media/container-insights-overview/azmon-containers-experience.png)

AKS 외부에서 실행 되는 Docker 및 Windows 컨테이너 호스트를 모니터링 하 고 관리 하는 데 관심이 있는 경우 구성, 감사 및 리소스 사용률을 보려면 [컨테이너 모니터링 솔루션](../../azure-monitor/insights/containers.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

Kubernetes 클러스터 모니터링을 시작 하려면 [컨테이너에 대해 Azure Monitor를 사용 하도록 설정](container-insights-onboard.md) 하 여 모니터링을 사용 하는 요구 사항 및 사용 가능한 방법을 이해 하는 방법을 검토 합니다. 
