---
title: Azure Portal에서 Kubernetes 리소스에 액세스 (미리 보기)
description: Kubernetes 리소스와 상호 작용 하 여 Azure Portal에서 AKS (Azure Kubernetes Service) 클러스터를 관리 하는 방법에 대해 알아봅니다.
services: container-service
author: laurenhughes
ms.topic: article
ms.date: 08/11/2020
ms.author: lahugh
ms.openlocfilehash: 4a0acf284475f3c9119f3b9d012debad656b1faa
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661353"
---
# <a name="access-kubernetes-resources-from-the-azure-portal-preview"></a>Azure Portal에서 Kubernetes 리소스에 액세스 (미리 보기)

Azure Portal에는 AKS (Azure Kubernetes Service) 클러스터의 Kubernetes 리소스에 쉽게 액세스할 수 있는 Kubernetes 리소스 뷰어 (미리 보기)가 포함 되어 있습니다. Azure Portal에서 Kubernetes 리소스를 보면 Azure Portal와 명령줄 도구 간의 컨텍스트 전환이 줄어들어 `kubectl` Kubernetes 리소스를 보고 편집 하는 환경이 간소화 됩니다. 리소스 뷰어에는 현재 여러 리소스 유형 (예: 배포, pod 및 복제본 집합)이 포함 되어 있습니다.

Azure Portal의 Kubernetes 리소스 뷰가 사용 중단에 대해 설정 된 [AKS dashboard 추가 기능][kubernetes-dashboard]을 대체 합니다.

>[!NOTE]
>Capabilty는 현재 [개인 Azure Kubernetes Service 클러스터](https://docs.microsoft.com/azure/aks/private-clusters)에서 지원 되지 않습니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>필수 구성 요소

Azure Portal에서 Kubernetes 리소스를 보려면 AKS 클러스터가 필요 합니다. 모든 클러스터가 지원 되지만 Azure Active Directory (Azure AD) 통합을 사용 하는 경우 클러스터에서 [AKS로 관리 되는 AZURE ad 통합][aks-managed-aad]을 사용 해야 합니다. 클러스터에서 레거시 Azure AD를 사용 하는 경우 포털에서 또는 [Azure CLI][cli-aad-upgrade]를 사용 하 여 클러스터를 업그레이드할 수 있습니다.

## <a name="view-kubernetes-resources"></a>Kubernetes 리소스 보기

Kubernetes 리소스를 보려면 Azure Portal AKS 클러스터로 이동 합니다. 왼쪽의 탐색 창은 리소스에 액세스 하는 데 사용 됩니다. 리소스에는 다음이 포함됩니다.

- **네임 스페이스** 클러스터의 네임 스페이스를 표시 합니다. 네임 스페이스 목록의 맨 위에 있는 필터는 네임 스페이스 리소스를 신속 하 게 필터링 및 표시 하는 방법을 제공 합니다.
- **워크 로드** 에는 클러스터에 배포 된 배포, pod, 복제본 집합 및 데몬 집합에 대 한 정보가 표시 됩니다. 아래 스크린샷에서는 예제 AKS 클러스터의 기본 시스템 pod를 보여 줍니다.
- **서비스 및 조절기** 는 클러스터의 서비스와 수신 리소스를 모두 표시 합니다.

:::image type="content" source="media/kubernetes-portal/workloads.png" alt-text="Azure Portal에 표시 되는 Kubernetes pod 정보입니다." lightbox="media/kubernetes-portal/workloads.png":::

### <a name="deploy-an-application"></a>애플리케이션 배포

이 예제에서는 샘플 AKS 클러스터를 사용 하 여 [AKS 빠른][portal-quickstart]시작에서 Azure 투표 응용 프로그램을 배포 합니다.

1. 리소스 뷰 (네임 스페이스, 작업, 서비스 및 조절기)에서 **추가** 를 선택 합니다.
1. [AKS 빠른][portal-quickstart]시작에서 Azure 투표 응용 프로그램에 대 한 yaml을 붙여넣습니다.
1. YAML 편집기의 맨 아래에 있는 **추가** 를 선택 하 여 응용 프로그램을 배포 합니다. 

YAML 파일이 추가 되 면 리소스 뷰어에는 생성 된 Kubernetes 서비스가 모두 표시 됩니다. 내부 서비스 (azure-응답) 및 외부 서비스 (azure-투표)는 Azure 투표 응용 프로그램에 액세스 합니다. 외부 서비스는 브라우저에서 응용 프로그램을 쉽게 볼 수 있도록 연결 된 외부 IP 주소를 포함 합니다.

:::image type="content" source="media/kubernetes-portal/portal-services.png" alt-text="Azure Portal에 표시 되는 Azure 투표 응용 프로그램 정보입니다." lightbox="media/kubernetes-portal/portal-services.png":::

### <a name="monitor-deployment-insights"></a>Deployment insights 모니터링

[컨테이너에 대해 Azure Monitor][enable-monitor] 를 사용 하는 AKS 클러스터는 deployment insights를 빠르게 볼 수 있습니다. Kubernetes 리소스 보기에서 사용자는 CPU 및 메모리 사용량을 비롯 한 개별 배포의 라이브 상태와 Azure monitor로 전환 하 여 자세한 정보를 확인할 수 있습니다. 다음은 샘플 AKS 클러스터의 배포 정보에 대 한 예입니다.

:::image type="content" source="media/kubernetes-portal/deployment-insights.png" alt-text="Azure Portal에 표시 되는 배포 정보입니다." lightbox="media/kubernetes-portal/deployment-insights.png":::

## <a name="edit-yaml"></a>YAML 편집

Kubernetes 리소스 뷰에는 YAML 편집기도 포함 되어 있습니다. 기본 제공 되는 YAML 편집기는 포털 내에서 서비스 및 배포를 업데이트 하거나 만들고 변경 내용을 즉시 적용할 수 있음을 의미 합니다.

:::image type="content" source="media/kubernetes-portal/service-editor.png" alt-text="Azure Portal에 표시 되는 Kubernetes 서비스에 대 한 YAML 편집기입니다.":::

YAML을 편집한 후에는 **검토 + 저장**을 선택 하 고 변경 내용을 확인 한 후 다시 저장 하 여 변경 내용을 적용 합니다.

>[!WARNING]
> UI 나 CLI를 통해 직접 프로덕션 변경을 수행 하지 않는 것이 좋습니다. [CI (지속적인 통합) 및 CD (지속적인 배포) 모범 사례](kubernetes-action.md)를 활용 하는 것이 좋습니다. Azure Portal Kubernetes 관리 기능 및 YAML 편집기는 개발 및 테스트 설정에서 새 배포를 학습 하 고이를 위한 새 배포를 위해 작성 되었습니다.

## <a name="troubleshooting"></a>문제 해결

이 섹션에서는 일반적인 문제 및 문제 해결 단계를 다룹니다.

### <a name="unauthorized-access"></a>무단 액세스

Kubernetes 리소스에 액세스 하려면 AKS 클러스터, Kubernetes API 및 Kubernetes 개체에 대 한 액세스 권한이 있어야 합니다. 클러스터 관리자 또는 AKS 클러스터에 액세스할 수 있는 적절 한 권한이 있는 사용자 인지 확인 합니다. 클러스터 보안에 대 한 자세한 내용은 [AKS의 Access and identity options][concepts-identity]를 참조 하세요.

### <a name="enable-resource-view"></a>리소스 뷰 사용

기존 클러스터의 경우 Kubernetes 리소스 뷰를 사용 하도록 설정 해야 할 수 있습니다. 리소스 보기를 사용 하도록 설정 하려면 포털에서 클러스터에 대 한 메시지를 따릅니다.

:::image type="content" source="media/kubernetes-portal/enable-resource-view.png" alt-text="Kubernetes 리소스 뷰를 사용 하도록 설정 하는 메시지를 Azure Portal 합니다." lightbox="media/kubernetes-portal/enable-resource-view.png":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터에 대 한 Kubernetes 리소스에 액세스 하는 방법을 살펴보았습니다. Kubernetes 리소스 뷰어를 사용 하 여 액세스 하는 클러스터 리소스 및 YAML 파일에 대 한 자세한 내용은 [배포 및 yaml 매니페스트][deployments] 를 참조 하세요.

<!-- LINKS - internal -->
[kubernetes-dashboard]: kubernetes-dashboard.md
[concepts-identity]: concepts-identity.md
[portal-quickstart]: kubernetes-walkthrough-portal.md#run-the-application
[deployments]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[aks-managed-aad]: managed-aad.md
[cli-aad-upgrade]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
[enable-monitor]: ../azure-monitor/insights/container-insights-enable-existing-clusters.md
