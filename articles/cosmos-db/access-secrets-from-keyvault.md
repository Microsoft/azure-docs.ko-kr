---
title: Key Vault를 사용하여 Azure Cosmos DB 키 저장 및 액세스
description: Azure Key Vault를 사용하여 Azure Cosmos DB 연결 문자열, 키, 엔드포인트를 저장 및 액세스합니다.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 36b0a2f18cf2917251a87405456980811af1bc3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60894840"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Azure Key Vault를 사용하여 Azure Cosmos 키 보호 

애플리케이션에 Azure Cosmos DB를 사용하는 경우 앱의 구성 파일 내에 있는 엔드포인트와 키를 사용하여 데이터베이스, 컬렉션, 문서에 액세스할 수 있습니다.  그러나 모든 사용자가 일반 텍스트 형식으로 사용할 수 있으므로 애플리케이션 코드에 키와 URL을 직접 입력하는 것은 안전하지 않습니다. 안전한 메커니즘을 통해 엔드포인트와 키를 사용하도록 만들 수 있습니다. 이 경우 Azure Key Vault가 애플리케이션 비밀을 안전하게 저장하고 관리하는 데 도움을 줄 수 있습니다.

Key Vault에서 Azure Cosmos DB 액세스 키를 저장하고 읽으려면 다음 단계가 필요합니다.

* 주요 자격 증명 모음 만들기  
* Key Vault에 Azure Cosmos DB 액세스 키 추가  
* Azure 웹 애플리케이션 만들기  
* 애플리케이션 등록 및 Key Vault를 읽을 권한 부여  


## <a name="create-a-key-vault"></a>주요 자격 증명 모음 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.  
2. **[리소스 만들기] > [보안] > [Key Vault]** 를 선택합니다.  
3. **Key Vault 만들기** 섹션에서 다음 정보를 제공합니다.  
   * **이름:** Key Vault의 고유한 이름을 제공합니다.  
   * **구독:** 사용할 구독을 선택합니다.  
   * **리소스 그룹**에서 **새로 만들기**를 선택하고 리소스 그룹 이름을 입력합니다.  
   * 위치 풀 다운 메뉴에서 위치를 선택합니다.  
   * 다른 옵션은 기본값으로 그대로 둡니다.  
4. 위의 정보를 제공한 후 **만들기**를 선택합니다.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Key Vault에 Azure Cosmos DB 액세스 키를 추가합니다.
1. 이전 단계에서 만든 Key Vault로 이동하고 **비밀** 탭을 엽니다.  
2. **+생성/가져오기**를 선택합니다. 

   * **업로드 옵션**에 **수동**을 선택합니다.
   * 비밀의 **이름**을 제공합니다.
   * Cosmos DB 계정의 연결 문자열을 **값** 필드에 제공합니다. 그런 다음, **만들기**를 선택합니다.

   ![비밀 만들기](./media/access-secrets-from-keyvault/create-a-secret.png)

4. 비밀이 생성되면 열고 다음 형식의 **비밀 식별자를 복사합니다. 다음 섹션에서 이 식별자를 사용합니다. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Azure 웹 애플리케이션 만들기

1. Azure 웹 애플리케이션을 만들거나 [GitHub 리포지토리](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/Demo/keyvaultdemo)에서 앱을 다운로드할 수 있습니다. 간단한 MVC 애플리케이션입니다.  

2. 다운로드한 애플리케이션의 압축을 풀고 **HomeController.cs** 파일을 엽니다. 다음 줄에서 비밀 ID를 업데이트합니다.

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. 파일을 **저장**하고 솔루션을 **빌드**합니다.  
4. 다음에는 Azure에 애플리케이션을 배포합니다. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다. 새로운 앱 서비스 프로필(앱 이름을 WebAppKeyVault1로 지정할 수 있음)을 만들고 **게시**를 선택합니다.   

5. 일단 애플리케이션이 배포됩니다. Azure Portal에서 배포한 웹앱으로 이동하고 이 애플리케이션의 **관리 서비스 ID**를 설정합니다.  

   ![관리 서비스 ID](./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png)

지금 애플리케이션을 실행하면 Key Vault에서 이 애플리케이션에 대한 권한을 부여하지 않았으므로 다음 오류가 표시됩니다.

![액세스 없이 배포된 앱](./media/access-secrets-from-keyvault/app-deployed-without-access.png)

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>애플리케이션 등록 및 Key Vault를 읽을 권한 부여

이 섹션에서는 Azure Active Directory에 애플리케이션을 등록하고 애플리케이션이 Key Vault를 읽을 수 있는 권한을 부여합니다. 

1. Azure Portal로 이동하고 이전 섹션에서 만든 **Key Vault**를 엽니다.  

2. **액세스 정책**을 열고 **+새로 추가**를 선택하여 배포된 웹앱을 찾은 다음, 권한을 선택하고 **확인**을 선택합니다.  

   ![액세스 정책 추가](./media/access-secrets-from-keyvault/add-access-policy.png)

이제 애플리케이션을 실행하는 경우 Key Vault에서 비밀을 읽을 수 있습니다.

![비밀로 배포된 앱](./media/access-secrets-from-keyvault/app-deployed-with-access.png)
 
마찬가지로, 키 자격 증명 모음에 액세스할 사용자를 추가할 수 있습니다. **액세스 정책**을 선택하여 자신을 Key Vault에 추가한 다음, Visual Studio에서 애플리케이션을 실행하는 데 필요한 모든 권한을 부여해야 합니다. 이 애플리케이션이 데스크톱에서 실행될 때 사용자의 ID를 사용합니다.

## <a name="next-steps"></a>다음 단계

* Azure Cosmos DB에 대한 방화벽을 구성하려면 [방화벽 지원](firewall-support.md) 문서를 참조하세요.
* 가상 네트워크 서비스 엔드포인트를 구성하려면 [VNet 서비스 엔드포인트를 사용하여 보안 액세스](vnet-service-endpoint.md)를 참조하세요.
