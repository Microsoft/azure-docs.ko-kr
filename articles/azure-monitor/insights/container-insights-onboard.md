---
title: 컨테이너에 대해 Azure Monitor를 사용 하도록 설정 하는 방법 | Microsoft Docs
description: 이 문서에서는 컨테이너의 작동 방식 및 식별 된 성능 관련 문제를 이해할 수 있도록 컨테이너에 대해 Azure Monitor를 사용 하도록 설정 하 고 구성 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: 7aad7e7dd5ec2569377f9276c2e4793c7afd631a
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198074"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>컨테이너에 대해 Azure Monitor를 사용 하도록 설정 하는 방법

이 문서에서는 Kubernetes 환경에 배포 되 고에서 호스트 되는 워크 로드의 성능을 모니터링 하는 컨테이너에 대 한 설치 Azure Monitor에서 사용할 수 있는 옵션의 개요를 제공 합니다.

- [Azure Kubernetes 서비스](https://docs.microsoft.com/azure/aks/) (AKS)

- [AKS 엔진](https://github.com/Azure/aks-engine)을 사용 하 여 Azure에서 호스트 되는 자체 관리 되는 Kubernetes 클러스터.

- AKS 엔진을 사용 하 여 [Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) 또는 온-프레미스에서 호스트 되는 자체 관리 되는 Kubernetes 클러스터

- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

컨테이너에 대 한 Azure Monitor는 다음과 같은 지원 되는 방법을 사용 하 여 새로운 또는 하나 이상의 기존 Kubernetes 배포를 사용 하도록 설정할 수 있습니다.

- Azure Portal, Azure PowerShell, Azure CLI

- [Terraform 및 AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md) 사용

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 항목이 있는지 확인하십시오.

- **Log Analytics 작업 영역입니다.**

    컨테이너 Azure Monitor는 [지역별 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)에 나열 된 지역에서 Log Analytics 작업 영역을 지원 합니다.

    새 AKS 클러스터의 모니터링을 사용 하도록 설정 하거나 온 보 딩 환경에서 AKS 클러스터 구독의 기본 리소스 그룹에 기본 작업 영역을 만들 때 작업 영역을 만들 수 있습니다. 직접 만들려면 [Azure Resource Manager](../platform/template-workspace-configuration.md)를 통해 만들거나 [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)을 통해 만들거나 [Azure Portal](../learn/quick-create-workspace.md)에서 만들 수 있습니다. 기본 작업 영역에 사용 되는 지원 되는 매핑 쌍 목록은 [컨테이너의 Azure Monitor에 대 한 지역 매핑](container-insights-region-mapping.md)을 참조 하세요.

- 컨테이너 모니터링을 사용 하도록 설정 하는 **Log Analytics 참여자 역할** 의 구성원입니다. Log Analytics 작업 영역에 대한 액세스를 제어하는 방법에 대한 자세한 내용은 [작업 영역 관리](../platform/manage-access.md)를 참조하세요.

- AKS 클러스터 리소스에 대 한 **[소유자](../../role-based-access-control/built-in-roles.md#owner)** 역할의 구성원입니다.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* 기본적으로는 프로메테우스 메트릭이 수집 되지 않습니다. 이를 수집 하도록 [에이전트를 구성](container-insights-prometheus-integration.md) 하기 전에 스크랩 및 메서드가 지원 될 수 있는 것을 이해 하려면 프로메테우스 [설명서](https://prometheus.io/) 를 검토 하는 것이 중요 합니다.

## <a name="supported-configurations"></a>지원되는 구성

다음은 Azure Monitor 컨테이너에 대해 공식적으로 지원 됩니다.

- 환경: azure Red Hat OpenShift, Kubernetes 온-프레미스 및 Azure의 AKS 엔진 및 Azure Stack 자세한 내용은 [Azure Stack AKS Engine](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)을 참조 하세요.
- Kubernetes 및 지원 정책의 버전은 [지원 되는 AKS](../../aks/supported-kubernetes-versions.md)버전과 동일 합니다. 

## <a name="network-firewall-requirements"></a>네트워크 방화벽 요구 사항

다음 표에서는 컨테이너 화 된 에이전트가 컨테이너의 Azure Monitor와 통신 하는 데 필요한 프록시 및 방화벽 구성 정보를 나열 합니다. 에이전트의 모든 네트워크 트래픽은 Azure Monitor로 아웃 바운드 됩니다.

|에이전트 리소스|포트 |
|--------------|------|
| *.ods.opinsights.azure.com | 443 |  
| *.oms.opinsights.azure.com | 443 | 
| \*.blob.core.windows.net | 443 |
| dc.services.visualstudio.com | 443 |
| *.microsoftonline.com | 443 |
| *.monitoring.azure.com | 443 |
| login.microsoftonline.com | 443 |

다음 표의 정보는 Azure 중국에 대 한 프록시 및 방화벽 구성 정보를 나열 합니다.

|에이전트 리소스|포트 |Description | 
|--------------|------|-------------|
| *. ods.opinsights.azure.cn | 443 | 데이터 수집 |
| *. oms.opinsights.azure.cn | 443 | OMS 온 보 딩 |
| \*.blob.core.windows.net | 443 | 아웃 바운드 연결을 모니터링 하는 데 사용 됩니다. |
| microsoft.com | 80 | 네트워크 연결에 사용 됩니다. 에이전트 이미지 버전이 ciprod09262019 이거나 이전 버전인 경우에만 필요 합니다. |
| dc.services.visualstudio.com | 443 | Azure 공용 클라우드 Application Insights를 사용 하는 에이전트 원격 분석의 경우 |

다음 표의 정보는 Azure 미국 정부에 대 한 프록시 및 방화벽 구성 정보를 나열 합니다.

|에이전트 리소스|포트 |Description | 
|--------------|------|-------------|
| *.ods.opinsights.azure.us | 443 | 데이터 수집 |
| *.oms.opinsights.azure.us | 443 | OMS 온 보 딩 |
| \*.blob.core.windows.net | 443 | 아웃 바운드 연결을 모니터링 하는 데 사용 됩니다. |
| microsoft.com | 80 | 네트워크 연결에 사용 됩니다. 에이전트 이미지 버전이 ciprod09262019 이거나 이전 버전인 경우에만 필요 합니다. |
| dc.services.visualstudio.com | 443 | Azure 공용 클라우드 Application Insights를 사용 하는 에이전트 원격 분석의 경우 |

## <a name="components"></a>구성 요소

성능을 모니터링 하는 기능은 컨테이너에 대해 특별히 Azure Monitor 개발 된 Linux 용 컨테이너 화 된 Log Analytics 에이전트에 의존 합니다. 이 특수 에이전트는 클러스터의 모든 노드에서 성능 및 이벤트 데이터를 수집하며, 배포 과정에서 에이전트가 지정된 Log Analytics 작업 영역에 자동으로 배포 및 등록됩니다. 에이전트 버전은 microsoft/oms:ciprod04202018 이상이며, *mmddyyyy* 형식의 날짜로 표시됩니다.

>[!NOTE]
>AKS에 대 한 Windows Server 지원의 미리 보기 릴리스에서 Windows Server 노드가 있는 AKS 클러스터에는 데이터를 수집 하 고 Azure Monitor 전달 하기 위한 에이전트가 설치 되어 있지 않습니다. 대신, 표준 배포의 일부로 클러스터에 자동으로 배포 된 Linux 노드가 클러스터의 모든 Windows 노드를 대신 하 여 Azure Monitor 데이터를 수집 하 고 전달 합니다.  
>

새로운 버전의 에이전트가 릴리스되면 AKS(Azure Kubernetes Service)에 호스트된 관리형 Kubernetes 클러스터에서 에이전트가 자동으로 업그레이드됩니다. 릴리스된 버전을 따르려면 [에이전트 릴리스 알림](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)을 참조하세요.

>[!NOTE]
>AKS 클러스터를 이미 배포한 경우, 이 문서의 뒷부분에 설명된 대로, Azure CLI 또는 제공된 Azure Resource Manager 템플릿을 사용하여 모니터링을 사용하도록 설정할 수 있습니다. `kubectl`을 사용하여 에이전트를 업그레이드, 삭제, 다시 배포 또는 배포할 수 없습니다.
>템플릿을 클러스터와 동일한 리소스 그룹에 배포해야 합니다.

다음 표에서 설명 하는 다음 방법 중 하나를 사용 하 여 컨테이너에 대 한 Azure Monitor를 사용 하도록 설정 합니다.

| 배포 상태 | 방법 | Description |
|------------------|--------|-------------|
| 새 AKS Kubernetes 클러스터 | [Azure CLI를 사용 하 여 AKS 클러스터 만들기](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Azure CLI를 사용 하 여 만든 새 AKS 클러스터의 모니터링을 사용 하도록 설정할 수 있습니다. |
| | [Terraform을 사용 하 여 AKS 클러스터 만들기](container-insights-enable-new-cluster.md#enable-using-terraform)| 오픈 소스 도구인 Terraform을 사용 하 여 만든 새 AKS 클러스터의 모니터링을 사용 하도록 설정할 수 있습니다. |
| | [Azure Resource Manager 템플릿을 사용 하 여 OpenShift 클러스터 만들기](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | 미리 구성 된 Azure Resource Manager 템플릿으로 만든 새 OpenShift 클러스터의 모니터링을 사용 하도록 설정할 수 있습니다. |
| | [Azure CLI를 사용 하 여 OpenShift 클러스터 만들기](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | Azure CLI를 사용 하 여 새 OpenShift 클러스터를 배포 하는 동안 모니터링을 사용 하도록 설정할 수 있습니다. |
| 기존 AKS Kubernetes 클러스터 | [Azure CLI를 사용 하 여 AKS 클러스터에 대해 사용](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Azure CLI를 사용 하 여 이미 배포 된 AKS 클러스터의 모니터링을 사용 하도록 설정할 수 있습니다. |
| |[Terraform을 사용 하 여 AKS 클러스터에 대해 사용](container-insights-enable-existing-clusters.md#enable-using-terraform) | 오픈 소스 도구 Terraform을 사용 하 여 이미 배포 된 AKS 클러스터의 모니터링을 사용 하도록 설정할 수 있습니다. |
| | [Azure Monitor에서 AKS 클러스터 사용](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Azure Monitor의 다중 클러스터 페이지에서 이미 배포 된 하나 이상의 AKS 클러스터에 대 한 모니터링을 사용 하도록 설정할 수 있습니다. |
| | [AKS 클러스터에서 사용](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Azure Portal의 AKS 클러스터에서 직접 모니터링을 사용 하도록 설정할 수 있습니다. |
| | [Azure Resource Manager 템플릿을 사용 하 여 AKS 클러스터에 대해 사용](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| 미리 구성 된 Azure Resource Manager 템플릿을 사용 하 여 AKS 클러스터의 모니터링을 사용 하도록 설정할 수 있습니다. |
| | [하이브리드 Kubernetes 클러스터에 대해 사용](container-insights-hybrid-setup.md) | Azure Stack에서 호스트 되는 AKS 엔진 또는 온-프레미스에 호스트 된 Kubernetes에 대 한 모니터링을 사용 하도록 설정할 수 있습니다. |
| | [Azure Resource Manager 템플릿을 사용 하 여 OpenShift 클러스터 사용](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | 미리 구성 된 Azure Resource Manager 템플릿으로 기존 OpenShift 클러스터의 모니터링을 사용 하도록 설정할 수 있습니다. |
| | [Azure Monitor에서 OpenShift 클러스터 사용](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Azure Monitor의 다중 클러스터 페이지에서 이미 배포 된 하나 이상의 OpenShift 클러스터에 대 한 모니터링을 사용 하도록 설정할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

- 모니터링을 사용 하도록 설정 하면 AKS (Azure Kubernetes Service), Azure Stack 또는 기타 환경에서 호스트 되는 Kubernetes 클러스터의 성능을 분석할 수 있습니다. 컨테이너에 Azure Monitor를 사용 하는 방법에 대 한 자세한 내용은 [View Kubernetes cluster performance](container-insights-analyze.md)을 참조 하세요.
