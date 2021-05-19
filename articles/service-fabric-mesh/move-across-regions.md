---
title: Service Fabric Mesh 애플리케이션을 다른 지역으로 이동
description: 현재 템플릿의 복사본을 새 Azure 지역에 배포하여 Service Fabric Mesh 리소스를 이동할 수 있습니다.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 1b59d482b8b88e37da2d61636ff3f254a46ba5c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626090"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Service Fabric Mesh 애플리케이션을 다른 지역으로 이동

> [!IMPORTANT]
> Azure Service Fabric Mesh의 미리 보기가 사용 중지되었습니다. 새 배포는 더이상 Service Fabric Mesh API를 통해 허용되지 않습니다. 기존 배포에 대한 지원은 2021년 4월 28일까지 계속됩니다.
> 
> 자세한 내용은 [Azure Service Fabric Mesh 미리 보기 사용 중지](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)를 참조하세요.

이 문서에서는 Service Fabric Mesh 애플리케이션 및 해당 리소스를 다른 Azure 지역으로 이동하는 방법을 설명합니다. 여러 가지 이유로 리소스를 다른 지역으로 이동시킬 수도 있습니다. 예를 들어, 정전에 대응하거나, 특정 지역에서만 사용할 수 있는 기능 또는 서비스를 얻거나, 내부 정책 및 거버넌스 요구사항을 충족하거나, 용량 계획 요구사항에 대응하기 위해서입니다.

 [Service Fabric Mesh](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh) 는 Azure 지역에서 리소스를 직접 이동하는 기능을 지원하지 않습니다. 그러나 현재 Azure Resource Manager 템플릿의 복사본을 새 대상 영역에 배포한 다음, 수신 트래픽과 종속성을 새로 만든 Service Fabric Mesh 애플리케이션으로 리디렉션하여 리소스를 간접적으로 이동할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* 클라이언트와 Service Fabric Mesh 애플리케이션 간의 트래픽 라우팅에 대한 중개자 역할을 하는 수신 컨트롤러 (예: [Application Gateway](../application-gateway/index.yml))
* 대상 Azure 지역 (`westus`, `eastus` 또는 `westeurope`)에서 Service Fabric Mesh (미리 보기) 가용성

## <a name="prepare"></a>준비

1. 가장 최신 배포에서 Azure Resource Manager 템플릿 및 매개 변수를 내보내 Service Fabric Mesh 애플리케이션의 현재 상태에 대한 "스냅샷"을 가져옵니다. 이렇게 하려면 Azure Portal을 사용하여 [배포 후 템플릿 내보내기](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment)의 단계를 따르세요. [Azure CLI](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates), 또는 [REST API](/rest/api/resources/resourcegroups/exporttemplate)도 사용할 수 있습니다.

2. 해당하는 경우 대상 지역에서 다시 배포하기 위해 [동일한 리소스 그룹에서 다른 리소스를 내보냅니다](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource-group).

3. 내보낸 템플릿을 검토하고 (필요한 경우 편집하여) 기존 속성 값이 대상 지역에서 사용하려는 값인지 확인합니다. 새 `location` (Azure 지역)은 다시 배포하는 동안 제공하는 매개 변수입니다.

## <a name="move"></a>이동

1. 대상 지역에 새 리소스 그룹을 만들거나 기존 그룹을 선택합니다.

2. 내보낸 템플릿으로 Azure Portal을 사용하여 [사용자 지정 템플릿에서 리소스 배포](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) 의 단계를 따릅니다. [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md), [REST API](../azure-resource-manager/templates/deploy-rest.md)도 사용할 수 있습니다.

3. [Azure Storage 계정과](../storage/common/storage-account-move.md)같은 관련 리소스를 이동하는 방법에 대한 참고 자료는 [지역 간에 Azure 리소스 이동](../azure-resource-manager/management/move-region.md) 항목에 나열된 개별 서비스에 대한 지침을 참조하세요.

## <a name="verify"></a>확인

1. 배포가 완료되면 애플리케이션 엔드포인트를 테스트하여 애플리케이션의 기능을 확인합니다.

2. 애플리케이션 상태 ([az mesh app show](/cli/azure/ext/mesh/mesh/app#ext-mesh-az-mesh-app-show))를 확인하고 [Azure Service Fabric Mesh CLI](./service-fabric-mesh-quickstart-deploy-container.md#set-up-service-fabric-mesh-cli)를 사용하여 애플리케이션 로그 및 ([az mesh code-package-log](/cli/azure/ext/mesh/mesh/code-package-log)) 명령을 검토하여 애플리케이션의 상태를 확인할 수도 있습니다.

## <a name="commit"></a>Commit

대상 지역에서 Service Fabric Mesh 애플리케이션의 동일한 기능을 확인한 후에는 수신 컨트롤러(예: [Application Gateway](../application-gateway/redirect-overview.md))를 구성하여 트래픽을 새 애플리케이션으로 리디렉션합니다.

## <a name="clean-up-source-resources"></a>원본 리소스 정리

Service Fabric Mesh 애플리케이션의 이동을 완료하려면 [원본 애플리케이션 및/또는 부모 리소스 그룹을 삭제](../azure-resource-manager/management/delete-resource-group.md)합니다.

## <a name="next-steps"></a>다음 단계

* [지역 간에 Azure 리소스 이동](../azure-resource-manager/management/move-region.md)
* [지역 간에 Azure 리소스 이동을 위한 지원](../azure-resource-manager/management/region-move-support.md)
* [리소스를 새 리소스 그룹 또는 구독으로 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [리소스에 대한 이동 작업 지원](../azure-resource-manager/management/move-support-resources.md
)
