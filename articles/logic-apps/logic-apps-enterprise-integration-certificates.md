---
title: 인증서를 사용하여 B2B 메시지 보호
description: 엔터프라이즈 통합 팩를 사용 하 여 Azure Logic Apps에서 B2B 메시지를 보호 하는 데 도움이 되는 인증서 추가
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: c1b48ae8191e2e5313d9037c791eca73c8a55691
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191393"
---
# <a name="improve-security-for-b2b-messages-by-using-certificates"></a>인증서를 사용 하 여 B2B 메시지에 대 한 보안 강화

B2B 통신을 기밀로 유지 해야 하는 경우 통합 계정에 인증서를 추가 하 여 엔터프라이즈 통합 앱, 특히 논리 앱에서 B2B 통신에 대 한 보안을 강화할 수 있습니다. 인증서는 전자 통신에서 참가자의 ID를 확인하고 다음과 같은 방식으로 전자 통신을 보호하는 디지털 문서입니다.

* 메시지 콘텐츠를 암호화합니다.
* 디지털로 메시지에 서명합니다.

엔터프라이즈 통합 앱에서 다음과 같은 인증서를 사용할 수 있습니다.

* [공용 인증서](https://en.wikipedia.org/wiki/Public_key_certificate): 공용 인터넷 [CA(인증 기관)](https://en.wikipedia.org/wiki/Certificate_authority)에서 구입해야 하지만 키가 필요하지 않습니다. 

* 프라이빗 인증서 또는 [*자체 서명된 인증서*](https://en.wikipedia.org/wiki/Self-signed_certificate): 직접 만들고 발급하며 프라이빗 키도 필요합니다. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>공용 인증서 업로드

B2B 기능이 포함된 논리 앱에서 *공용 인증서*를 사용하려면 먼저 통합 계정에 인증서를 업로드해야 합니다. 사용자가 만드는 [규약](logic-apps-enterprise-integration-agreements.md)에서 해당 속성을 정의한 후에 인증서를 사용하여 B2B 메시지를 안전하게 보호할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. Azure 주 메뉴에서 **모든 리소스**를 선택합니다. 검색 상자에 통합 계정 이름을 입력한 후 원하는 통합 계정을 선택합니다.

   ![통합 계정 찾기 및 선택](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. **구성 요소** 아래에서 **인증서** 타일을 선택합니다.

   !["인증서" 선택](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. **인증서** 아래에서 **추가**를 선택합니다. **인증서 추가** 아래에서 인증서에 대한 세부 정보를 입력합니다. 작업을 완료하면 **확인**을 선택합니다.

   | 속성 | 값 | Description | 
   |----------|-------|-------------|
   | **이름** | <*certificate-name*> | 사용자의 인증서 이름(이 예제의 "publicCert")입니다. | 
   | **인증서 종류** | 공용 | 인증서의 종류입니다. |
   | **MSSQLSERVER에 대한 프로토콜 속성** | <*certificate-file-name*> | 업로드하려는 인증서 파일을 찾아 선택하려면 **인증서** 상자 옆에 있는 폴더 아이콘을 선택합니다. |
   ||||

   !["추가" 선택, 인증서 세부 정보 제공](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   Azure에서는 사용자의 선택 사항이 유효한지 확인한 후 인증서를 업로드합니다.

   ![Azure에서 새 인증서 표시](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>프라이빗 인증서 업로드

B2B 기능이 포함된 논리 앱에서 *프라이빗 인증서*를 사용하려면 먼저 통합 계정에 인증서를 업로드해야 합니다. 먼저 [Azure Key Vault](../key-vault/key-vault-get-started.md)에 추가할 프라이빗 키가 있어야 합니다. 

사용자가 만드는 [규약](logic-apps-enterprise-integration-agreements.md)에서 해당 속성을 정의한 후에 인증서를 사용하여 B2B 메시지를 안전하게 보호할 수 있습니다.

> [!NOTE]
> 개인 인증서의 경우 메시지 서명 및 암호화에 대 한 [AS2 규약의](logic-apps-enterprise-integration-as2.md) **송신 및 수신** 설정에 표시 되는 해당 공용 인증서를 추가 해야 합니다.

1. [프라이빗 키를 Azure Key Vault에 추가](../key-vault/certificate-scenarios.md#import-a-certificate)하고 **키 이름**을 입력합니다.
   
2. Azure Logic Apps에 Azure Key Vault에서 작업을 수행할 수 있는 권한을 부여합니다. Logic Apps 서비스 주체에게 액세스 권한을 부여하려면 다음 예제처럼 [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) PowerShell 명령을 사용합니다.

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. [Azure Portal](https://portal.azure.com)에 로그인합니다. Azure 주 메뉴에서 **모든 리소스**를 선택합니다. 검색 상자에 통합 계정 이름을 입력한 후 원하는 통합 계정을 선택합니다.

   ![통합 계정 찾기](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. **구성 요소** 아래에서 **인증서** 타일을 선택합니다.  

   ![인증서 타일 선택](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. **인증서** 아래에서 **추가**를 선택합니다. **인증서 추가** 아래에서 인증서에 대한 세부 정보를 입력합니다. 작업을 완료하면 **확인**을 선택합니다.

   | 속성 | 값 | Description | 
   |----------|-------|-------------|
   | **이름** | <*certificate-name*> | 사용자의 인증서 이름(이 예제의 "privateCert")입니다. | 
   | **인증서 종류** | 프라이빗 | 인증서의 종류입니다. |
   | **MSSQLSERVER에 대한 프로토콜 속성** | <*certificate-file-name*> | 업로드하려는 인증서 파일을 찾아 선택하려면 **인증서** 상자 옆에 있는 폴더 아이콘을 선택합니다. 개인 키에 대해 키 자격 증명 모음을 사용 하는 경우 업로드 된 파일은 공용 인증서가 됩니다. | 
   | **리소스 그룹** | <*integration-account-resource-group*> | 통합 계정의 리소스 그룹(이 예제의 "MyResourceGroup")입니다. | 
   | **Key Vault** | <*key-vault-name*> | Azure Key Vault의 이름입니다. |
   | **키 이름** | <*key-name*> | 키의 이름입니다. |
   ||||

   !["추가" 선택, 인증서 세부 정보 제공](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   Azure에서는 사용자의 선택 사항이 유효한지 확인한 후 인증서를 업로드합니다.

   ![Azure에서 새 인증서 표시](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>다음 단계

* [B2B 규약 만들기](logic-apps-enterprise-integration-agreements.md)
