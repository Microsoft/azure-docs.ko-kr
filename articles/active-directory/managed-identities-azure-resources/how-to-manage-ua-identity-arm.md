---
title: Azure Resource Manager를 사용 하 여 사용자 할당 관리 id 만들기 & 삭제
description: Azure Resource Manager를 사용 하 여 사용자 할당 관리 id를 만들고 삭제 하는 방법에 대 한 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd49c60a23a0ad58f0907d3c1d7c38178ac08c97
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266360"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Azure Resource Manager를 사용하여 사용자 할당 관리 ID 생성, 나열 및 삭제


Azure 리소스에 대 한 관리 id는 Azure Active Directory에서 관리 id를 사용 하 여 Azure 서비스를 제공 합니다. 이 ID를 사용하면 코드에 자격 증명을 포함할 필요 없이 Azure AD 인증을 지원하는 서비스에 인증할 수 있습니다. 

이 문서에서는 Azure Resource Manager를 사용하여 사용자 할당 관리 ID를 만듭니다.

Azure Resource Manager 템플릿을 사용하여 사용자 할당 관리 ID를 나열하고 삭제할 수 없습니다.  사용자 할당 관리 ID를 만들고 나열하려면 아래 문서를 참조하세요.

- [사용자 할당 관리 ID 나열](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [사용자 할당 관리 ID 삭제](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
  ## <a name="prerequisites"></a>필수 구성 요소

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 관리 ID의 차이점](overview.md#managed-identity-types)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.

## <a name="template-creation-and-editing"></a>템플릿 만들기 및 편집

Azure Portal 및 스크립팅을 사용할 때와 마찬가지로, Azure Resource Manager 템플릿에서도 Azure 리소스 그룹으로 정의된 새 리소스 또는 수정된 리소스를 배포하는 기능을 제공합니다. 다음을 비롯한 로컬 및 포털 기반 템플릿 편집 및 배포에 여러 가지 옵션이 제공됩니다.

- [Azure Marketplace에서 사용자 지정 템플릿을](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)사용 하 여 템플릿을 처음부터 만들거나 기존 공통 또는 [빠른 시작 템플릿에서](https://azure.microsoft.com/documentation/templates/)기반으로 할 수 있습니다.
- [원본 배포](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates) 또는 [배포의 현재 상태](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)에서 템플릿을 내보내 기존 리소스 그룹에서 템플릿을 파생합니다.
- 로컬 [JSON 편집기(예: VS Code)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)를 사용하는 경우 PowerShell 또는 CLI를 사용하여 템플릿을 업로드하고 배포합니다.
- Visual Studio [Azure 리소스 그룹 프로젝트](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)를 사용하여 템플릿을 만들고 배포합니다. 

## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기 

사용자 할당 관리 ID를 만들려면 계정에 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

사용자 할당 관리 ID를 만들려면 다음 템플릿을 사용하세요. `<USER ASSIGNED IDENTITY NAME>` 값을 원하는 값으로 바꿉니다.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2018-11-30",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="next-steps"></a>다음 단계

Azure Resource Manager 템플릿을 사용하여 Azure VM에 사용자 할당 관리 ID를 할당하는 방법에 대한 자세한 내용은 [A템플릿을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-template-windows-vm.md)을 참조하세요.


