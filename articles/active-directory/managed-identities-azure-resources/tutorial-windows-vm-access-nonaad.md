---
title: 자습서`:` 관리 ID를 사용하여 Azure Key Vault에 액세스 - Windows - Azure AD
description: Windows VM 시스템 할당 관리 ID를 사용하여 Azure Key Vault에 액세스하는 프로세스를 단계별로 안내하는 자습서입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2890eb2211ac0a105363742a0e900e52a577ed27
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89255828"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>자습서: Windows VM 시스템 할당 관리 ID를 사용하여 Azure Key Vault에 액세스 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

이 자습서에서는 Windows VM(가상 머신)에 대한 시스템 할당 관리 ID를 사용하여 Azure Key Vault에 액세스하는 방법을 보여 줍니다. 부트스트랩 역할을 하는 Key Vault를 사용하면 클라이언트 애플리케이션에서 비밀을 사용하여 Azure AD(Active Directory)로 보호되지 않는 리소스에 액세스할 수 있습니다. Azure에서 자동으로 관리되는 관리 서비스 ID를 사용하면 Azure AD 인증을 지원하는 서비스에 인증할 수 있으므로 코드에 자격 증명을 삽입할 필요가 없습니다. 

다음 방법을 알아봅니다.


> [!div class="checklist"]
> * Key Vault에 저장된 비밀 액세스 권한을 VM에 부여 
> * VM ID를 사용하여 액세스 토큰을 가져온 다음 Key Vault에서 비밀을 검색하는 데 사용 

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]


## <a name="enable"></a>사용

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>액세스 권한 부여  
 
이 섹션에서는 Key Vault에 저장된 비밀에 대한 액세스 권한을 VM에 부여하는 방법을 보여줍니다. Azure 리소스에 대한 관리 ID를 사용하면 코드에서 Azure AD 인증을 지원하는 리소스에 인증하기 위한 액세스 토큰을 가져올 수 있습니다.  그러나 모든 Azure 서비스가 Azure AD 인증을 지원하지는 않습니다. 이러한 서비스에서 Azure 리소스에 대한 관리 ID를 사용하려면 Azure Key Vault에 서비스 자격 증명을 저장하고 VM의 관리 ID로 Key Vault에 액세스하여 자격 증명을 검색합니다. 

먼저 Key Vault를 만들고, VM의 시스템 할당 관리 ID 액세스 권한을 Key Vault에 부여해야 합니다.   

1. 왼쪽 탐색 모음 맨 위에서 **리소스 만들기** > **보안 + ID** > **Key Vault**를 차례로 선택합니다.  
2. 새 Key Vault의 **이름**을 입력합니다. 
3. 앞에서 만든 VM과 같은 구독 및 리소스 그룹에 Key Vault를 배치합니다. 
4. **액세스 정책**을 선택하고 **새로 추가**를 클릭합니다. 
5. 템플릿에서 구성에서 **비밀 관리**를 선택합니다. 
6. **주체 선택**을 선택하고 검색 필드에 앞에서 만든 VM의 이름을 입력합니다.  결과 목록에서 VM을 선택하고 **선택** 을 클릭합니다. 
7. **확인**을 클릭하여 새 액세스 정책 추가를 완료하고, **확인**을 클릭하여 액세스 정책 선택을 완료합니다. 
8. **만들기**를 클릭하여 Key Vault 만들기를 완료합니다. 

    ![대체 이미지 텍스트](./media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


다음으로는 VM에서 실행되는 코드를 사용하여 나중에 비밀을 검색할 수 있도록 Key Vault에 암호를 추가합니다. 

1. **모든 리소스**를 선택하고 만든 Key Vault를 찾아서 선택합니다. 
2. **비밀**을 선택하고 **추가**를 클릭합니다. 
3. **업로드 옵션**에서 **수동**을 선택합니다. 
4. 비밀의 이름과 값을 입력합니다.  원하는 어떤 값이나 입력할 수 있습니다. 
5. 활성화 날짜와 만료 날짜는 비워 두고 **사용 가능**은 **예**로 유지합니다. 
6. **만들기**를 클릭하여 비밀을 만듭니다. 
 
## <a name="access-data"></a>데이터 액세스  

이 섹션에서는 VM ID를 사용하여 액세스 토큰을 가져오고 이를 사용하여 Key Vault에서 비밀을 검색하는 방법을 보여줍니다. PowerShell 4.3.1 이상이 설치되어 있지 않으면 [최신 버전을 다운로드하고 설치](/powershell/azure/)해야 합니다.

먼저 VM의 시스템 할당 관리 ID를 사용하여 Key Vault에 인증하기 위한 액세스 토큰을 가져옵니다.
 
1. Portal에서 **Virtual Machines** -> Windows Virtual Machines로 이동한 다음 **개요**에서 **연결**을 클릭합니다.
2. **Windows VM**을 만들 때 추가한 **사용자 이름**과 **암호**를 입력합니다.  
3. 이제 가상 머신에 대한 **원격 데스크톱 연결**을 만들었으므로 원격 세션에서 PowerShell을 엽니다.  
4. PowerShell에서 테넌트에 대해 웹 요청을 호출하여 VM의 특정 포트에서 로컬 호스트용 토큰을 가져옵니다.  

    PowerShell 요청은 다음과 같습니다.
    
    ```powershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
    ```
    
    다음으로 $response 개체에서 JSON(JavaScript Object Notation) 형식의 문자열로 저장된 전체 응답을 추출합니다.  
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json 
    ```
    
    다음으로는 응답에서 액세스 토큰을 추출합니다.  
    
    ```powershell
    $KeyVaultToken = $content.access_token 
    ```
    
    마지막으로 PowerShell의 Invoke-WebRequest 명령을 사용하여 앞에서 Key Vault에 만든 비밀을 검색하고 인증 헤더에서 액세스 토큰을 전달합니다.  Key Vault **개요** 페이지의 **기본 정보** 섹션에 있는 Key Vault의 URL이 필요합니다.  
    
    ```powershell
    (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    응답은 다음과 같습니다. 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Key Vault에서 비밀을 검색한 후에는 이름과 암호가 필요한 서비스에 인증하는 데 비밀을 사용할 수 있습니다. 


## <a name="disable"></a>사용 안 함

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>다음 단계

이 자습서에서는 Windows VM 시스템 할당 관리 ID를 사용하여 Azure Key Vault에 액세스하는 방법을 알아보았습니다.  Azure Key Vault에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)