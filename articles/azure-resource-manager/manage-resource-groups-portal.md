---
title: Azure portal을 사용 하 여 Azure Resource Manager 그룹 관리 | Microsoft Docs
description: Azure Resource Manager 그룹을 관리 하려면 Azure portal을 사용 합니다.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: cb1eb5ac27c53f4c0d48fe3644febc62f848486d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551283"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>Azure portal을 사용 하 여 Azure Resource Manager 리소스 그룹 관리

사용 방법 알아보기 합니다 [Azure portal](https://portal.azure.com) 사용 하 여 [Azure Resource Manager](resource-group-overview.md) Azure 리소스 그룹을 관리 하 합니다. Azure 리소스 관리를 참조 하세요 [Azure portal을 사용 하 여 Azure 관리 리소스](./manage-resources-portal.md)합니다.

리소스 그룹을 관리 하는 방법에 대 한 다른 문서:

- [Azure CLI를 사용 하 여 Azure 리소스 그룹 관리](./manage-resources-cli.md)
- [Azure PowerShell을 사용 하 여 Azure 리소스 그룹 관리](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>리소스 그룹이란?

리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 리소스 그룹에는 솔루션에 대한 모든 리소스 또는 그룹으로 관리하려는 해당 리소스만 포함될 수 있습니다. 사용자의 조직에 가장 적합한 내용에 따라 리소스 그룹에 리소스를 어떻게 할당할지 결정합니다. 일반적으로 쉽게 배포, 업데이트하고 그룹으로 삭제할 수 있도록 동일한 리소스 그룹에 대해 동일한 수명 주기를 공유하는 리소스를 추가합니다.

리소스 그룹은 리소스에 대한 메타데이터를 저장합니다. 따라서 리소스 그룹의 위치를 지정하면 메타데이터가 저장된 위치를 지정하게 됩니다. 규정 준수 때문에 특정 지역에 데이터가 저장되는지 확인해야 합니다.

리소스 그룹은 리소스에 대한 메타데이터를 저장합니다. 리소스 그룹의 위치를 지정 하면 메타 데이터가 저장 된 지정 하는 합니다.

## <a name="create-resource-groups"></a>리소스 그룹 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 선택 **리소스 그룹**

    ![리소스 그룹 추가](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. **추가**를 선택합니다.
4. 다음 값을 입력합니다.

   - **구독**: Azure 구독을 선택합니다. 
   - **리소스 그룹**: 새 리소스 그룹 이름을 입력 합니다. 
   - **지역**: 와 같은 Azure 위치를 선택 **중미**합니다.

     ![리소스 그룹 만들기](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. 선택 **검토 + 만들기**
6. **만들기**를 선택합니다. 리소스 그룹을 만들려면 몇 초 정도가 걸립니다.
7. 선택 **새로 고침** 리소스 그룹 목록을 새로 고치고 열려는 새로 만든된 리소스 그룹을 선택 하려면 위쪽 메뉴에서. 누르거나 **알림**(벨 아이콘)에서 위쪽 및 선택 **리소스 그룹으로 이동** 새로 만든된 리소스 그룹을

    ![리소스 그룹으로 이동](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>리소스 그룹 나열

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 리소스 그룹을 나열 하려면 선택 **리소스 그룹**

    ![리소스 그룹 찾아보기](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. 리소스 그룹에 대해 표시 되는 정보를 사용자 지정 하려면 선택 **열을 편집할**합니다. 다음 스크린 샷에서 표시를 추가할 수 있습니다 추가 열을 보여 줍니다.

## <a name="open-resource-groups"></a>열린 리소스 그룹

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **리소스 그룹**을 선택합니다.
3. 열려는 리소스 그룹을 선택 합니다.

## <a name="delete-resource-groups"></a>리소스 그룹 삭제

1. 삭제 하려는 리소스 그룹을 엽니다.  참조 [리소스 그룹을 열고](#open-resource-groups)합니다.
2. **리소스 그룹 삭제**를 선택합니다.

    ![azure 리소스 그룹 삭제](./media/manage-resource-groups-portal/delete-group.png)

Azure Resource Manager 리소스의 삭제를 정렬 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Resource Manager 리소스 그룹 삭제](./resource-group-delete.md)합니다.

## <a name="deploy-resources-to-a-resource-group"></a>리소스 그룹에 리소스 배포

Resource Manager 템플릿을 만든 후에 Azure 리소스를 배포 하려면 Azure portal을 사용할 수 있습니다. 템플릿을 만들면 참조 [빠른 시작: Azure Portal을 사용하여 Azure Resource Manager 템플릿 만들기 및 배포](./resource-manager-quickstart-create-templates-use-the-portal.md)를 참조하세요. 포털을 사용 하 여 템플릿을 배포 하는 것에 대 한 참조 [Resource Manager 템플릿 및 Azure portal로 리소스 배포](resource-group-template-deploy-portal.md)합니다.

## <a name="move-to-another-resource-group-or-subscription"></a>다른 리소스 그룹 또는 구독으로 이동

다른 리소스 그룹으로 그룹의 리소스를 이동할 수 있습니다. 자세한 내용을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](resource-group-move-resources.md)을 참조하세요.

## <a name="lock-resource-groups"></a>잠금 리소스 그룹

잠그면 실수로 삭제 하거나 Azure 구독, 리소스 그룹 또는 리소스와 같은 중요 한 리소스를 수정 하면 조직의 다른 사용자가 수 없습니다. 

1. 삭제 하려는 리소스 그룹을 엽니다.  참조 [리소스 그룹을 열고](#open-resource-groups)합니다.
2. 왼쪽된 창에서 선택 **잠급니다**합니다.
3. 잠금 리소스 그룹에 추가 하려면 선택 **추가**합니다.
4. 입력 **잠금 이름은**, **잠금 유형이**, 및 **메모**합니다. 잠금 유형은 **읽기 전용**, 및 **삭제**합니다.

    ![azure 리소스 그룹 잠금](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

자세한 내용은 [예기치 않은 변경을 방지 하기 위해 리소스 잠그기](./resource-group-lock-resources.md)합니다.

## <a name="tag-resource-groups"></a>리소스 그룹 태그

리소스 그룹 및 리소스에 태그를 적용하여 논리적으로 자산을 구성할 수 있습니다. 정보를 참조 하세요 [태그를 사용 하 여 Azure 리소스 구성에](./resource-group-using-tags.md#portal)입니다.

## <a name="export-resource-groups-to-templates"></a>리소스 그룹 템플릿 내보내기

리소스 그룹을 성공적으로 설정한 후 리소스 그룹에 대 한 Resource Manager 템플릿을 볼 수는 것이 좋습니다. 템플릿을 내보내면 다음과 같은 두 가지 이점이 있습니다.

- 템플릿에 모든 인프라가 포함 되어 있으므로 향후 솔루션 배포를 자동화 합니다.
- 에 개체 JSON (JavaScript Notation) 솔루션을 나타내는 확인 하 여 템플릿 구문에 알아봅니다.

두 가지 방법으로 템플릿을 내보낼 수 있습니다.

- 배포에 사용 된 실제 템플릿을 내보낼 수 있습니다. 내보낸 템플릿은 원본 템플릿에 나타난 대로 모든 매개 변수 및 변수를 포함합니다. 이 방법은 포털을 통해 리소스를 배포하고 해당 리소스를 만드는 템플릿을 확인하려는 경우에 유용합니다. 이 템플릿은 곧 사용할 수 있습니다. 
- 리소스 그룹의 현재 상태를 나타내는 생성 된 템플릿을 내보낼 수 있습니다. 내보낸 템플릿은 배포에 사용된 템플릿에 기초하지 않습니다. 대신 리소스 그룹의 “스냅숏” 또는 “백업”인 템플릿을 만듭니다. 내보낸 템플릿에는 하드 코드된 값이 많으며 일반적으로 정의된 경우와 같이 매개 변수가 많이 포함되지 않습니다. 동일한 리소스 그룹에 리소스를 다시 배포하려면 이 옵션을 사용합니다. 다른 리소스 그룹에 이 템플릿을 사용하려면 크게 수정해야 할 수 있습니다.

### <a name="export-templates-from-deployment-history"></a>배포 기록에서 템플릿 내보내기

이 메서드는 특정 배포에 대 한 템플릿을 내보냅니다. 여러 배포에서 리소스를 추가/제거를 포털에서 리소스를 변경한 경우 참조 [리소스 그룹에서 템플릿을 내보낼](#export-templates-from-resource-groups)합니다.

1. 내보내려는 리소스 그룹을 엽니다.  참조 [리소스 그룹을 열고](#open-resource-groups)합니다.
2. 왼쪽 창에서 **배포**를 선택하거나 **배포** 아래에 있는 링크를 선택합니다.  다음 스크린샷에서 보여 줍니다 **4 성공** 네 개의 서로 다른 배포 이름으로 구분 된 4 개의 배포 되어 있습니다. 표시 될 수 있습니다 **1 성공**합니다.

    ![azure 리소스 그룹 내보내기 템플릿](./media/manage-resource-groups-portal/manage-resource-groups-export-templates-deployment-history.png)

3. 목록에서 배포 중 하나를 선택 합니다.
4. 왼쪽된 창에서 선택 **템플릿**합니다. Resource Manager는 다음 6개의 파일을 검색합니다.

   - **템플릿** - 솔루션의 인프라를 정의하는 템플릿입니다. 포털을 통해 저장소 계정을 만들 때 Resource Manager는 템플릿을 사용하여 배포하고 나중에 참조할 수 있도록 해당 템플릿을 저장했습니다.
   - **매개 변수** - 배포하는 동안 값을 전달하는 데 사용할 수 있는 매개 변수 파일. 첫 번째 배포 중에 제공되는 값을 포함합니다. 템플릿을 다시 배포할 때 이러한 값을 변경할 수 있습니다.
   - **CLI** - 템플릿 배포에 사용할 수 있는 Azure CLI 스크립트 파일.
   - **PowerShell** - 템플릿 배포에 사용할 수 있는 Azure PowerShell 스크립트 파일.
   - **.NET** - 템플릿 배포에 사용할 수 있는 .NET 클래스.
   - **Ruby** - 템플릿 배포에 사용할 수 있는 Ruby 클래스.

     기본적으로 포털에 템플릿이 표시됩니다.

5. 선택 **다운로드** 로컬 컴퓨터에 템플릿을 내보낼 수 있습니다.

    ![azure 리소스 그룹 내보내기 템플릿](./media/manage-resource-groups-portal/manage-resource-groups-export-templates-deployment-history-download.png)

<a name="export-templates-from-resource-groups"></a>
### <a name="export-templates-from-resource-groups"></a>리소스 그룹에서 템플릿 내보내기

하면 포털에서 리소스를 변경 했으므로, 여러 배포에서 리소스를 추가/제거 하는 경우 배포 기록에서 템플릿을 검색 리소스 그룹의 현재 상태를 반영 하지 않습니다. 이 섹션에서는 리소스 그룹의 현재 상태를 반영하는 템플릿을 내보내는 방법을 보여줍니다. 동일한 리소스 그룹에 다시 배포하는 데 사용할 수 있는 리소스 그룹의 스냅숏으로 사용됩니다. 다른 솔루션에 내보낸 템플릿을 사용하려면 대폭 수정해야 합니다.

1. 내보내려는 리소스 그룹을 엽니다.  참조 [리소스 그룹을 열고](#open-resource-groups)합니다.
2. 왼쪽된 창에서 선택 **템플릿 내보내기**합니다. Resource Manager는 다음 6개의 파일을 검색합니다.

   - **템플릿** - 솔루션의 인프라를 정의하는 템플릿입니다. 포털을 통해 저장소 계정을 만들 때 Resource Manager는 템플릿을 사용하여 배포하고 나중에 참조할 수 있도록 해당 템플릿을 저장했습니다.
   - **매개 변수** - 배포하는 동안 값을 전달하는 데 사용할 수 있는 매개 변수 파일. 첫 번째 배포 중에 제공되는 값을 포함합니다. 템플릿을 다시 배포할 때 이러한 값을 변경할 수 있습니다.
   - **CLI** - 템플릿 배포에 사용할 수 있는 Azure CLI 스크립트 파일.
   - **PowerShell** - 템플릿 배포에 사용할 수 있는 Azure PowerShell 스크립트 파일.
   - **.NET** - 템플릿 배포에 사용할 수 있는 .NET 클래스.
   - **Ruby** - 템플릿 배포에 사용할 수 있는 Ruby 클래스.

     기본적으로 포털에 템플릿이 표시됩니다.
3. 선택 **다운로드** 로컬 컴퓨터에 템플릿을 내보낼 수 있습니다.

일부 내보낸된 템플릿에서 사용할 수 있으려면 먼저 일부 편집 해야 합니다. 템플릿을 개발 하는 방법에 알아보려면 참조를 [단계별 자습서](/azure/azure-resource-manager/)합니다.

### <a name="export-template-before-deploying"></a>배포 하기 전에 템플릿 내보내기

리소스를 정의 하는 포털을 사용할 수 있습니다.  리소스를 배포 하기 전에 볼 수 있으며 템플릿을 내보낼 수 있습니다. 지침은 [빠른 시작: Azure Portal을 사용하여 Azure Resource Manager 템플릿 만들기 및 배포](./resource-manager-quickstart-create-templates-use-the-portal.md)를 참조하세요.

### <a name="fix-export-issues"></a>내보내기 문제 수정

모든 리소스 종류가 내보내기 템플릿 함수를 지원하지는 않습니다. 배포 기록이 아닌 리소스 그룹에서 내보낸 경우 내보내기 문제가 발생합니다. 마지막 배포가 리소스 그룹의 현재 상태를 정확하게 나타내는 경우 리소스 그룹이 아닌 배포 기록에서 템플릿을 내보내야 합니다. 단일 템플릿에서 정의되지 않은 리소스 그룹을 변경한 경우에만 리소스 그룹에서 내보냅니다.

내보내기 문제를 해결하려면 누락된 리소스를 템플릿에 다시 수동으로 추가합니다. 오류 메시지에는 내보낼 수 없는 리소스 종류가 포함됩니다. [템플릿 참조](/azure/templates/)에서 리소스 종류를 찾습니다. 예를 들어 가상 네트워크 게이트웨이를 수동으로 추가하려면 [Microsoft.Network/virtualNetworkGateways 템플릿 참조](/azure/templates/microsoft.network/virtualnetworkgateways)를 참조하세요. 템플릿 참조는 템플릿에 리소스를 추가하도록 JSON을 제공합니다.

리소스에 대한 JSON 형식을 가져온 후 리소스 값을 가져와야 합니다. 리소스 종류에 대한 REST API에서 GET 작업을 사용하여 리소스에 대한 값을 확인할 수 있습니다. 예를 들어 가상 네트워크 게이트웨이에 대한 값을 가져오려면 [가상 네트워크 게이트웨이 - 가져오기](/rest/api/network-gateway/virtualnetworkgateways/get)를 참조하세요.

## <a name="manage-access-to-resource-groups"></a>리소스 그룹에 대 한 액세스 관리

[RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)는 Azure에서 리소스에 대한 액세스를 관리하는 방법입니다. 자세한 내용은 [RBAC 및 Azure Portal을 사용하여 액세스 관리](../role-based-access-control/role-assignments-portal.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure Resource Manager에 알아보려면 [Azure Resource Manager 개요](./resource-group-overview.md)합니다.
- Resource Manager 템플릿 구문에 알아보려면 [구조 및 Azure Resource Manager 템플릿의 구문 이해](./resource-group-authoring-templates.md)합니다.
- 템플릿을 개발 하는 방법에 알아보려면 참조를 [단계별 자습서](/azure/azure-resource-manager/)합니다.
- Azure Resource Manager 템플릿 스키마를 보려면 [템플릿 참조](/azure/templates/)합니다.