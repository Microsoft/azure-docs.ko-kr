---
title: Service Fabric 메시 응용 프로그램을 다른 지역으로 이동
description: 현재 템플릿의 복사본을 새 Azure 지역에 배포 하 여 Service Fabric 메시 리소스를 이동할 수 있습니다.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 376808a6d8f61d4dc03d17061323a473d48053a6
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76908164"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>다른 Azure 지역으로 Service Fabric 메시 응용 프로그램 이동

이 문서에서는 Service Fabric 메시 응용 프로그램 및 해당 리소스를 다른 Azure 지역으로 이동 하는 방법을 설명 합니다. 여러 가지 이유로 리소스를 다른 지역으로 이동할 수 있습니다. 예를 들어 가동 중단에 대 한 응답으로, 특정 지역 에서만 사용할 수 있는 기능 또는 서비스, 내부 정책 및 거 버 넌 스 요구 사항 또는 용량 계획 요구 사항에 대 한 응답으로 얻을 수 있습니다.

 [Service Fabric 메시](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh) 는 Azure 지역에서 리소스를 직접 이동 하는 기능을 지원 하지 않습니다. 그러나 현재 Azure Resource Manager 템플릿의 복사본을 새 대상 영역에 배포한 다음, 수신 트래픽과 종속성을 새로 만든 Service Fabric 메시 응용 프로그램으로 리디렉션하는 방식으로 리소스를 간접적으로 이동할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* 클라이언트와 Service Fabric 메시 응용 프로그램 간의 트래픽 라우팅에 대 한 중개자 역할을 하는 수신 컨트롤러 (예: [Application Gateway](https://docs.microsoft.com/azure/application-gateway/))
* 대상 Azure 지역 (`westus`, `eastus`또는 `westeurope`)에서 메시 (미리 보기) 가용성 Service Fabric

## <a name="prepare"></a>준비

1. 최신 배포에서 Azure Resource Manager 템플릿 및 매개 변수를 내보내 Service Fabric 메시 응용 프로그램의 현재 상태에 대 한 "스냅숏"을 가져옵니다. 이렇게 하려면 Azure Portal를 사용 하 여 [배포 후 템플릿 내보내기](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) 의 단계를 따르세요. [Azure CLI](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)또는 [REST API](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate)를 사용할 수도 있습니다.

2. 해당 하는 경우 대상 지역에서 다시 배포 하기 위해 [동일한 리소스 그룹의 다른 리소스를 내보냅니다](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal#export-template-from-a-resource-group) .

3. 내보낸 템플릿을 검토 하 고 필요한 경우 편집 하 여 기존 속성 값이 대상 지역에서 사용 하려는 값 인지 확인 합니다. 새 `location` (Azure 지역)은 다시 배포 하는 동안 제공 하는 매개 변수입니다.

## <a name="move"></a>이동

1. 대상 지역에서 새 리소스 그룹을 만들거나 기존 리소스 그룹을 사용 합니다.

2. 내보낸 템플릿을 사용 하 여 Azure Portal를 사용 하 여 [사용자 지정 템플릿에서 리소스 배포](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template) 의 단계를 따릅니다. [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli), [Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell)또는 [REST API](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-rest)를 사용할 수도 있습니다.

3. [Azure Storage 계정과](../storage/common/storage-account-move.md)같은 관련 리소스를 이동 하는 방법에 대 한 지침은 [지역 간에 Azure 리소스 이동](../azure-resource-manager/management/move-region.md)항목에 나열 된 개별 서비스에 대 한 지침을 참조 하세요.

## <a name="verify"></a>확인

1. 배포가 완료 되 면 응용 프로그램 엔드포인트을 테스트 하 여 응용 프로그램의 기능을 확인 합니다.

2. 응용 프로그램 상태 ([az 메시 app show](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/app?view=azure-cli-latest#ext-mesh-az-mesh-app-show))를 확인 하 고 [AZURE Service Fabric 메시 CLI](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-quickstart-deploy-container#set-up-service-fabric-mesh-cli)를 사용 하 여 응용 프로그램 로그 및 ([az 메시 code-package](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/code-package-log?view=azure-cli-latest)) 명령을 검토 하 여 응용 프로그램의 상태를 확인할 수도 있습니다.

## <a name="commit"></a>Commit

대상 지역에서 Service Fabric 메시 응용 프로그램의 동일한 기능을 확인 한 후에는 수신 컨트롤러 (예: [Application Gateway](../application-gateway/redirect-overview.md))를 구성 하 여 트래픽을 새 응용 프로그램으로 리디렉션합니다.

## <a name="clean-up-source-resources"></a>원본 리소스 정리

Service Fabric 메시 응용 프로그램의 이동을 완료 하려면 [원본 응용 프로그램 및/또는 부모 리소스 그룹을 삭제](../azure-resource-manager/management/delete-resource-group.md)합니다.

## <a name="next-steps"></a>다음 단계

* [지역 간에 Azure 리소스 이동](../azure-resource-manager/management/move-region.md)
* [지역 간 Azure 리소스 이동에 대 한 지원](../azure-resource-manager/management/region-move-support.md)
* [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [리소스에 대 한 이동 작업 지원](../azure-resource-manager/management/move-support-resources.md
)
