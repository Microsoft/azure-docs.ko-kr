---
title: 자습서`:` 관리 ID를 사용하여 Azure Resource Manager에 액세스 - Windows - Azure AD
description: Windows VM 시스템 할당 관리 ID를 사용하여 Azure Resource Manager에 액세스하는 프로세스를 단계별로 안내하는 자습서입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.custom: subject-rbac-steps
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/24/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66624f0304065c21ecde9de261bebad3300bbd26
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112077991"
---
# <a name="use-a-windows-vm-system-assigned-managed-identity-to-access-resource-manager"></a>Windows VM 시스템 할당 관리 ID를 사용하여 Resource Manager에 액세스

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

이 자습서에서는 시스템 할당 관리 ID를 사용하도록 설정된 Windows 가상 머신을 사용하여 Azure Resource Manager API에 액세스하는 방법을 보여줍니다. Azure 리소스에 대한 관리 ID는 Azure에서 자동으로 관리되며 이를 사용하면 Azure AD 인증을 지원하는 서비스에 인증할 수 있으므로 코드에 자격 증명을 삽입할 필요가 없습니다. 다음 방법을 알아봅니다.

> [!div class="checklist"] 
> * VM에 Azure Resource Manager의 리소스 그룹 액세스 권한 부여 
> * VM ID를 사용하여 액세스 토큰을 가져온 다음 Azure Resource Manager를 호출하는 데 사용

## <a name="prerequisites"></a>사전 요구 사항

- 관리 ID에 대한 기본 이해. Azure 리소스에 대한 관리 ID 기능이 익숙하지 않은 경우 [개요](overview.md)를 참조하세요.
- Azure 계정, [체험 계정에 등록](https://azure.microsoft.com/free/)합니다.
- 적절한 범위(사용자 구독 또는 리소스 그룹)에서 필요한 리소스 생성 및 역할 관리 단계를 수행할 수 있는 "소유자" 권한. 역할 할당에 관한 도움이 필요한 경우 [Azure 역할을 할당하여 Azure 구독 리소스에 대한 액세스 관리](../../role-based-access-control/role-assignments-portal.md)를 참조하세요.
- 시스템 할당 관리 ID가 활성화된 Windows 가상 머신도 필요합니다.
  - 이 자습서에 대한 가상 머신을 만들어야 하는 경우 [시스템 할당 ID가 설정된 가상 머신 만들기](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)라는 제목의 문서를 수행하면 됩니다.

## <a name="grant-your-vm-access-to-a-resource-group-in-resource-manager"></a>VM에 Resource Manager의 리소스 그룹 액세스 권한 부여

Azure 리소스에 대한 관리 ID를 사용하면 코드에서 액세스 토큰을 가져와 Azure AD 인증을 지원하는 리소스에 인증할 수 있으며 Azure Resource Manager는 Azure AD 인증을 지원합니다.  이 VM의 시스템이 할당한 관리 ID에 Resource Manager의 리소스(이 경우 VM을 만든 리소스 그룹)에 대한 액세스 권한을 부여해야 합니다. **Windows VM** 에 대해 만든 리소스 그룹의 범위에서 관리 ID에 [읽기 권한자](../../role-based-access-control/built-in-roles.md#reader) 역할을 할당합니다.
 
세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

## <a name="get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-resource-manager"></a>VM의 시스템 할당 관리 ID를 통해 액세스 토큰을 가져오고 Azure Resource Manager를 호출하는 데 사용합니다. 

이 부분에서는 **PowerShell** 을 사용해야 합니다.  **PowerShell** 이 설치되어 있지 않으면 [여기](/powershell/azure/)서 다운로드하세요. 

1.  Portal에서 **Virtual Machines** -> Windows Virtual Machines로 이동한 다음 **개요** 에서 **연결** 을 클릭합니다. 
2.  Windows VM을 만들 때 추가한 **사용자 이름** 과 **암호** 를 입력합니다. 
3.  이제 가상 머신에 대한 **원격 데스크톱 연결** 을 만들었으므로 원격 세션에서 **PowerShell** 을 엽니다. 
4.  Invoke-WebRequest cmdlet을 사용하여 Azure Resource Manager에 대한 액세스 토큰을 가져오도록 Azure 리소스 엔드포인트의 로컬 관리 ID에 요청합니다.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > "resource" 매개 변수의 값은 Azure AD에 필요한 값과 정확하게 일치해야 합니다. Azure Resource Manager 리소스 ID를 사용할 때는 URI에 후행 슬래시를 포함해야 합니다.
    
    다음으로 $response 개체에서 JSON(JavaScript Object Notation) 형식의 문자열로 저장된 전체 응답을 추출합니다. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    다음으로는 응답에서 액세스 토큰을 추출합니다.
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    마지막으로 액세스 토큰을 사용하여 Azure Resource Manager를 호출합니다. 또한 이 예제에서는 Invoke-WebRequest cmdlet을 사용하여 Azure Resource Manager를 호출하고, 인증 헤더에 액세스 토큰을 포함합니다.
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE] 
    > URL은 대/소문자를 구분하므로 앞서 리소스 그룹의 이름을 지정할 때 사용했던 것과 정확히 동일한 대/소문자를 사용해야 하며, “resourceGroups”와 같이 대문자 “G”를 사용해야 합니다.
        
    다음 명령은 리소스 그룹의 세부 정보를 반환합니다.

    ```powershell
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}}
    ```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 시스템 할당 관리 ID를 사용하여 Azure Resource Manager API에 액세스하는 방법을 알아보았습니다.  Azure Resource Manager에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
>[Azure Resource Manager](../../azure-resource-manager/management/overview.md)