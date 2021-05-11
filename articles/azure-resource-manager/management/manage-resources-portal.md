---
title: 리소스 관리 - Azure Portal
description: Azure Portal 및 Azure Resource Manager를 사용하여 리소스를 관리합니다. 리소스를 배포하고 삭제하는 방법을 보여 줍니다.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 12d704b0a3d92aa0585fb120f969000def282396
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092190"
---
# <a name="manage-azure-resources-by-using-the-azure-portal"></a>Azure Portal를 사용하여 Azure 리소스 관리

[Azure Resource Manager](overview.md)와 함께 [Azure Portal](https://portal.azure.com)을 사용하여 Azure 리소스를 관리하는 방법을 알아봅니다. 리소스 그룹 관리에 대한 자세한 내용은 [Azure Portal를 사용하여 Azure 리소스 그룹 관리](manage-resource-groups-portal.md)를 참조하세요.

리소스 관리에 대한 다른 문서:

- [Azure CLI를 사용하여 Azure 리소스를 관리](manage-resources-cli.md)
- [Azure PowerShell을 사용하여 Azure 리소스 관리](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="deploy-resources-to-a-resource-group"></a>리소스 그룹에 리소스 배포

Resource Manager 템플릿을 만든 후에는 Azure Portal을 사용하여 Azure 리소스를 배포할 수 있습니다. 템플릿을 만들려면 [빠른 시작: Azure Portal을 사용하여 Azure Resource Manager 템플릿 만들기 및 배포](../templates/quickstart-create-templates-use-the-portal.md)를 참조하세요. 포털을 사용하여 템플릿을 배포하려면 [Resource Manager 템플릿 및 Azure Portal을 사용한 리소스 배포](../templates/deploy-portal.md)를 참조하세요.

## <a name="open-resources"></a>리소스 열기

Azure 리소스는 Azure 서비스 및 리소스 그룹별로 구성됩니다. 다음 절차에서는 **mystorage0207** 이라는 스토리지 계정을 여는 방법을 보여 줍니다. 가상 머신은 **mystorage0207rg** 라는 리소스 그룹에 상주합니다.

서비스 유형별로 리소스를 열려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 창에서 Azure 서비스를 선택합니다. 이 경우 **스토리지 계정** 입니다.  나열된 서비스가 표시되지 않으면 **모든 서비스** 를 선택한 다음 서비스 유형을 선택합니다.

    ![포털에서 azure 리소스 열기](./media/manage-resources-portal/manage-azure-resources-portal-open-service.png)

3. 열려는 리소스 선택

    ![선택한 리소스를 강조 표시하는 스크린샷](./media/manage-resources-portal/manage-azure-resources-portal-open-resource.png)

    스토리지 계정은 다음과 같습니다.

    ![스토리지 계정이 표시되는 모습을 보여 주는 스크린샷](./media/manage-resources-portal/manage-azure-resources-portal-open-resource-storage.png)

리소스 그룹별 리소스를 열려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 창에서 **리소스 그룹** 을 선택하여 그룹 내의 리소스를 나열합니다.
3. 열려는 리소스 선택 

## <a name="manage-resources"></a>리소스 관리

포털에서 리소스를 볼 때 특정 리소스 관리 옵션이 표시됩니다.

![Azure 리소스 관리](./media/manage-resources-portal/manage-azure-resources-portal-manage-resource.png)

스크린샷에서는 Azure 가상 머신에 대한 관리 옵션을 보여 줍니다. 가상 머신을 시작, 재시작 및 중지하는 등의 작업을 수행할 수 있습니다.

## <a name="delete-resources"></a>리소스 삭제

1. 포털에서 리소스를 엽니다. 이 단계는 [리소스 열기](#open-resources)를 참조하세요.
2. **삭제** 를 선택합니다. 다음 스크린샷에서는 가상 머신에 대한 관리 옵션을 보여 줍니다.

    ![azure 리소스 삭제](./media/manage-resources-portal/manage-azure-resources-portal-delete-resource.png)
3. 삭제를 확인하려면 리소스의 이름을 입력한 후 **삭제** 를 선택합니다.

Azure Resource Manager가 리소스 삭제를 지시하는 방법에 대한 자세한 내용은 [Azure Resource Manager 리소스 그룹 삭제](delete-resource-group.md)를 참조하세요.

## <a name="move-resources"></a>리소스 이동

1. 포털에서 리소스를 엽니다. 이 단계는 [리소스 열기](#open-resources)를 참조하세요.
2. **이동** 을 선택합니다. 다음 스크린샷에서는 스토리지 계정에 대한 관리 옵션을 보여 줍니다.

    ![azure 리소스 이동](./media/manage-resources-portal/manage-azure-resources-portal-move-resource.png)
3. 필요에 따라 **다른 리소스 그룹으로 이동** 또는 **다른 구독으로 이동** 을 선택합니다.

자세한 내용을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](move-resource-group-and-subscription.md)을 참조하세요.

## <a name="lock-resources"></a>리소스 잠금

잠금은 조직의 다른 사용자가 실수로 Azure 구독, 리소스 그룹 또는 리소스와 같은 중요한 리소스를 삭제하거나 수정하는 것을 방지합니다. 

1. 포털에서 리소스를 엽니다. 이 단계는 [리소스 열기](#open-resources)를 참조하세요.
2. **잠금** 을 선택합니다. 다음 스크린샷에서는 스토리지 계정에 대한 관리 옵션을 보여 줍니다.

    ![azure 리소스 잠금](./media/manage-resources-portal/manage-azure-resources-portal-lock-resource.png)
3. **추가** 를 선택한 후 잠금 속성을 지정합니다.

자세한 내용은 [Azure 리소스 관리자를 사용하여 리소스 잠그기](lock-resources.md)를 참조하세요.

## <a name="tag-resources"></a>리소스 태그 지정

태그를 지정하면 리소스 그룹 및 리소스를 논리적으로 구성할 수 있습니다. 

1. 포털에서 리소스를 엽니다. 이 단계는 [리소스 열기](#open-resources)를 참조하세요.
2. **태그** 를 선택합니다. 다음 스크린샷에서는 스토리지 계정에 대한 관리 옵션을 보여 줍니다.

    ![azure 리소스 태그](./media/manage-resources-portal/manage-azure-resources-portal-tag-resource.png)
3. 태그 속성을 지정하고 **저장** 을 선택합니다.

자세한 내용은 [태그를 사용하여 Azure 리소스 구성](tag-resources.md#portal)을 참조하세요.

## <a name="monitor-resources"></a>리소스 모니터링

리소스를 열면 리소스 유형을 모니터링하기 위한 기본 그래프 및 표가 포털에 표시됩니다. 다음 스크린샷에서는 가상 머신에 대한 그래프를 보여 줍니다.

![azure 리소스 모니터링](./media/manage-resources-portal/manage-azure-resources-portal-monitor-resource.png)

그래프의 오른쪽 위 모퉁이에 있는 고정 아이콘을 선택하여 그래프를 대시보드에 고정할 수 있습니다. 대시보드를 사용하는 방법을 알아보려면 [Azure 포털에서 대시보드 만들기 및 공유](../../azure-portal/azure-portal-dashboards.md)를 참조하세요.

## <a name="manage-access-to-resources"></a>리소스에 대한 액세스 관리

[Azure RBAC](../../role-based-access-control/overview.md)(Azure 역할 기반 액세스 제어)는 Azure 리소스에 대한 액세스를 관리하는 방법입니다. 자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure Resource Manager에 대한 자세한 내용은 [Azure Resource Manager 개요](overview.md)를 참조하세요.
- Resource Manager 템플릿 구문에 대해 알아보려면 [Azure Resource Manager 템플릿의 구조 및 구문 이해](../templates/template-syntax.md)를 참조하세요.
- 템플릿을 개발하는 방법을 알아보려면 [단계별 자습서](../index.yml)를 참조하세요.
- Azure Resource Manager 템플릿 스키마를 보려면 [템플릿 참조](/azure/templates/)를 참조하세요.
