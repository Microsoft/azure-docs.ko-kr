---
title: Azure portal을 사용 하 여 Azure 리소스 관리 | Microsoft Docs
description: 리소스를 관리 하는 Azure portal 및 Azure 리소스 관리를 사용 합니다.
services: azure-resource-manager,azure-portal
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 20bf38b87ce29f8506a5611ecd25cf38f6d4ed61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550703"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Azure portal을 사용 하 여 Azure 리소스 관리

사용 방법 알아보기 합니다 [Azure portal](https://portal.azure.com) 사용 하 여 [Azure Resource Manager](resource-group-overview.md) Azure 리소스를 관리 하 합니다. 리소스 그룹 관리에 대 한 참조 [Azure portal을 사용 하 여 관리 하는 Azure 리소스 그룹](./manage-resource-groups-portal.md)합니다.

리소스를 관리 하는 방법에 대 한 다른 문서:

- [Azure CLI를 사용 하 여 Azure 리소스 관리](./manage-resources-cli.md)
- [Azure PowerShell을 사용 하 여 Azure 리소스 관리](./manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>리소스 그룹에 리소스 배포

Resource Manager 템플릿을 만든 후에 Azure 리소스를 배포 하려면 Azure portal을 사용할 수 있습니다. 템플릿을 만들면 참조 [빠른 시작: Azure Portal을 사용하여 Azure Resource Manager 템플릿 만들기 및 배포](./resource-manager-quickstart-create-templates-use-the-portal.md)를 참조하세요. 포털을 사용 하 여 템플릿을 배포 하는 것에 대 한 참조 [Resource Manager 템플릿 및 Azure portal로 리소스 배포](resource-group-template-deploy-portal.md)합니다.

## <a name="open-resources"></a>이 리소스

Azure 리소스는 Azure 서비스에서 리소스 그룹으로 구성 됩니다. 다음 절차에 라는 저장소 계정 키를 여는 방법을 보여 줍니다 **mystorage0207**합니다. 가상 컴퓨터 라는 리소스 그룹에 상주 **mystorage0207rg**합니다.

서비스 형식에서 리소스를 열려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽된 창에서 Azure 서비스를 선택 합니다. 이 예에서 **저장소 계정**합니다.  서비스 목록이 표시 되지 않으면, 선택 **모든 서비스**, 서비스 종류를 선택 합니다.

    ![포털에서 azure 리소스를 엽니다.](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. 열려는 리소스를 선택 합니다.

    ![포털에서 azure 리소스를 엽니다.](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    저장소 계정에는 다음과 같습니다.

    ![포털에서 azure 리소스를 엽니다.](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

리소스에서 리소스 그룹을 열려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽된 창에서 선택 **리소스 그룹** 그룹 내의 리소스를 나열 합니다.
3. 열려는 리소스를 선택 합니다. 

## <a name="manage-resources"></a>리소스 관리

포털에서 리소스를 볼 때 특정 리소스 관리 옵션이 표시됩니다.

![Azure 리소스 관리](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

스크린샷에서 Azure 가상 머신에 대 한 관리 옵션을 보여 줍니다. 시작, 다시 시작 및 가상 머신 중지 등의 작업을 수행할 수 있습니다.

## <a name="delete-resources"></a>리소스 삭제

1. 포털에서 리소스를 엽니다. 단계를 참조 하세요 [리소스를 열려면](#open-resources)합니다.
2. **삭제**를 선택합니다. 다음 스크린 샷에서 가상 머신에 대 한 관리 옵션을 보여 줍니다.

    ![azure 리소스를 삭제 합니다.](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. 삭제를 확인 하 고 선택한 리소스의 이름을 입력 **삭제**합니다.

Azure Resource Manager 리소스의 삭제를 정렬 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Resource Manager 리소스 그룹 삭제](./resource-group-delete.md)합니다.

## <a name="move-resources"></a>리소스 이동

1. 포털에서 리소스를 엽니다. 단계를 참조 하세요 [리소스를 열려면](#open-resources)합니다.
2. **이동**을 선택합니다. 다음 스크린샷은 저장소 계정에 대 한 관리 옵션을 보여 줍니다.

    ![azure 리소스를 이동 합니다.](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. 선택 **다른 리소스 그룹으로 이동** 또는 **다른 구독으로 Moeve** 필요에 따라 합니다.

자세한 내용을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](resource-group-move-resources.md)을 참조하세요.

## <a name="lock-resources"></a>리소스 잠금

잠그면 실수로 삭제 하거나 Azure 구독, 리소스 그룹 또는 리소스와 같은 중요 한 리소스를 수정 하면 조직의 다른 사용자가 수 없습니다. 

1. 포털에서 리소스를 엽니다. 단계를 참조 하세요 [리소스를 열려면](#open-resources)합니다.
2. 선택 **잠급니다**합니다. 다음 스크린샷은 저장소 계정에 대 한 관리 옵션을 보여 줍니다.

    ![잠금 azure 리소스](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. 선택 **추가**, 잠금 속성을 지정 합니다.

자세한 내용은 [Azure 리소스 관리자를 사용하여 리소스 잠그기](resource-group-lock-resources.md)를 참조하세요.

## <a name="tag-resources"></a>리소스 태그 지정

리소스 그룹 및 리소스를 논리적으로 구성 하는 데 도움이 됩니다. 태그를 지정 합니다. 

1. 포털에서 리소스를 엽니다. 단계를 참조 하세요 [리소스를 열려면](#open-resources)합니다.
2. **태그**를 선택합니다. 다음 스크린샷은 저장소 계정에 대 한 관리 옵션을 보여 줍니다.

    ![azure 태그 리소스](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. 태그 속성을 지정 하 고 선택한 **저장할**합니다.

정보를 참조 하세요 [태그를 사용 하 여 Azure 리소스 구성에](./resource-group-using-tags.md#portal)입니다.

## <a name="monitor-resources"></a>리소스 모니터링

리소스를 열면 기본 그래프 및 표가 리소스 종류를 모니터링 하는 것에 대 한 포털에 표시 됩니다. 다음 스크린샷에서 가상 컴퓨터에 대 한 그래프를 보여 줍니다.

![azure monitor 리소스](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

그래프를 대시보드에 고정 하려면 그래프의 오른쪽 위 모서리의 핀 고정 아이콘을 선택할 수 있습니다. 대시보드를 사용하는 방법을 알아보려면 [Azure 포털에서 대시보드 만들기 및 공유](../azure-portal/azure-portal-dashboards.md)를 참조하세요.

## <a name="manage-access-to-resources"></a>리소스에 대한 액세스 관리

[RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)는 Azure에서 리소스에 대한 액세스를 관리하는 방법입니다. 자세한 내용은 [RBAC 및 Azure Portal을 사용하여 액세스 관리](../role-based-access-control/role-assignments-portal.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure Resource Manager에 알아보려면 [Azure Resource Manager 개요](./resource-group-overview.md)합니다.
- Resource Manager 템플릿 구문에 알아보려면 [구조 및 Azure Resource Manager 템플릿의 구문 이해](./resource-group-authoring-templates.md)합니다.
- 템플릿을 개발 하는 방법에 알아보려면 참조를 [단계별 자습서](/azure/azure-resource-manager/)합니다.
- Azure Resource Manager 템플릿 스키마를 보려면 [템플릿 참조](/azure/templates/)합니다.