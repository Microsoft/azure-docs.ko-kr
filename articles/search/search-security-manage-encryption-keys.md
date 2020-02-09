---
title: 고객 관리 키를 사용 하 여 미사용 암호화
titleSuffix: Azure Cognitive Search
description: Azure Key Vault에서 만들고 관리 하는 키를 사용 하 여 인덱스 및 동의어 맵에 대 한 서버 쪽 암호화를 보충 Cognitive Search 합니다.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: cb17fe24339ad618229b3456ece15c206f79bdb7
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899939"
---
# <a name="encryption-at-rest-of-content-in-azure-cognitive-search-using-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault에서 고객이 관리 하는 키를 사용 하 여 Azure Cognitive Search 콘텐츠에 대 한 미사용 암호화

기본적으로 Azure Cognitive Search [는 서비스 관리 키](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models)를 사용 하 여 미사용 인덱싱된 콘텐츠를 암호화 합니다. Azure Key Vault에서 만들고 관리 하는 키를 사용 하 여 추가 암호화 계층으로 기본 암호화를 보완할 수 있습니다. 이 문서에서는 단계를 안내 합니다.

서버 쪽 암호화는 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)와의 통합을 통해 지원 됩니다. 사용자 고유의 암호화 키를 만들고 Azure Key Vault에 저장할 수도 있고 Azure Key Vault의 API를 사용하여 암호화 키를 생성할 수도 있습니다. Azure Key Vault를 사용 하 여 키 사용을 감사할 수도 있습니다. 

고객 관리 키를 사용 하는 암호화는 검색 서비스 수준이 아닌 해당 개체가 생성 될 때 인덱스 또는 동의어 맵 수준에서 구성 됩니다. 이미 존재 하는 콘텐츠는 암호화할 수 없습니다. 

키가 모두 같은 Key Vault에 있을 필요는 없습니다. 단일 검색 서비스는 서로 다른 키 자격 증명 모음에 저장 된 고유한 고객 관리 암호화 키를 사용 하 여 암호화 된 여러 인덱스 또는 동의어 맵을 호스트할 수 있습니다.  고객 관리 키를 사용 하 여 암호화 되지 않은 동일한 서비스에 인덱스 및 동의어 맵을 포함할 수도 있습니다. 

> [!IMPORTANT] 
> 이 기능은 [REST API 버전 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/) 및 [.net SDK 버전 8.0-미리 보기](search-dotnet-sdk-migration-version-9.md)에서 사용할 수 있습니다. 현재 Azure Portal에는 고객 관리 암호화 키를 구성할 수 있는 기능이 없습니다. 검색 서비스는 2019 년 1 월 1 일 이후에 생성 되어야 하며 무료 (공유) 서비스 일 수 없습니다.

## <a name="prerequisites"></a>필수 조건

이 예제에서 사용 되는 서비스는 다음과 같습니다. 

+ [Azure Cognitive Search 서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 

+ [Azure Key Vault 리소스를 만들거나](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) 구독에서 기존 자격 증명 모음을 찾습니다.

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 또는 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) 는 구성 작업에 사용 됩니다.

+ [Postman](search-get-started-postman.md), [Azure PowerShell](search-create-index-rest-api.md) 및 [Azure Cognitive Search SDK](https://aka.ms/search-sdk-preview) 를 사용 하 여 REST API를 호출할 수 있습니다. 지금은 고객이 관리 하는 암호화에 대 한 포털이 지원 되지 않습니다.

>[!Note]
> Azure 키 자격 증명 모음 키가 삭제 된 경우 Azure Cognitive Search는 고객 관리 키 기능을 사용 하 여 암호화의 특성으로 인해 데이터를 검색할 수 없습니다. 실수로 Key Vault 키 삭제로 인 한 데이터 손실을 방지 하려면 Key Vault에서 일시 삭제 및 제거 보호를 사용 하도록 설정 **해야** 합니다. 자세한 내용은 [Azure Key Vault 일시 삭제](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)를 참조 하세요.   

## <a name="1---enable-key-recovery"></a>1-키 복구 사용

Azure Key Vault 리소스를 만든 후 다음 PowerShell 또는 Azure CLI 명령을 실행 하 여 선택한 Key Vault에서 **일시 삭제** 및 **보호 제거** 를 사용 하도록 설정 합니다.   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2-새 키 만들기

기존 키를 사용 하 여 Azure Cognitive Search 콘텐츠를 암호화 하는 경우이 단계를 건너뜁니다.

1. [Azure Portal에 로그인](https://portal.azure.com) 하 고 키 자격 증명 모음 대시보드로 이동 합니다.

1. 왼쪽 탐색 창에서 **키** 설정을 선택 하 고 **+ 생성/가져오기**를 클릭 합니다.

1. **키 만들기** 창에 있는 **옵션**목록에서 키를 만드는 데 사용할 방법을 선택 합니다. 새 키를 **생성** 하거나 기존 키를 **업로드** 하거나 **복원 백업을** 사용 하 여 키 백업을 선택할 수 있습니다.

1. 키의 **이름을** 입력 하 고 필요에 따라 다른 키 속성을 선택 합니다.

1. **만들기** 단추를 클릭 하 여 배포를 시작 합니다.

키 식별자를 적어 둡니다 .이는 키 **값 Uri**, **키 이름**및 **키 버전**으로 구성 됩니다. Azure Cognitive Search에서 암호화 된 인덱스를 정의 하는 데 필요 합니다.
 
![새 key vault 키 만들기](./media/search-manage-encryption-keys/create-new-key-vault-key.png "새 key vault 키 만들기")

## <a name="3---create-a-service-identity"></a>3-서비스 id 만들기

검색 서비스에 id를 할당 하면 검색 서비스에 대 한 Key Vault 액세스 권한을 부여할 수 있습니다. 검색 서비스는 해당 id를 사용 하 여 Azure Key vault에 인증 합니다.

Azure Cognitive Search는 id를 할당 하는 두 가지 방법, 즉 관리 되는 id 또는 외부에서 관리 되는 Azure Active Directory 응용 프로그램을 지원 합니다. 

가능 하면 관리 되는 id를 사용 합니다. 검색 서비스에 id를 할당 하는 가장 간단한 방법은 대부분의 시나리오에서 작동 해야 합니다. 인덱스 및 동의어 맵에 대해 여러 키를 사용 하는 경우 또는 솔루션이 id 기반 인증을 정규화 하지 않는 분산 아키텍처에 있는 경우이 문서의 끝에 설명 된 대로 [외부에서 관리 되는 고급 Azure Active Directory 방법을](#aad-app) 사용 합니다.

 일반적으로 관리 되는 id를 사용 하면 검색 서비스에서 코드에 자격 증명을 저장 하지 않고 Azure Key Vault에 인증할 수 있습니다. 이러한 유형의 관리 되는 id의 수명 주기는 관리 id를 하나만 포함할 수 있는 검색 서비스의 수명 주기에 연결 됩니다. [관리 id에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. 관리 id를 만들려면 [azure portal에 로그인](https://portal.azure.com) 하 고 검색 서비스 대시보드를 엽니다. 

1. 왼쪽 탐색 창에서 **id** 를 클릭 하 고 상태를 **켜기**로 변경 하 고 **저장**을 클릭 합니다.

![관리 id 사용](./media/search-enable-msi/enable-identity-portal.png "관리 되 id 사용")

## <a name="4---grant-key-access-permissions"></a>4-키 액세스 권한 부여

검색 서비스에서 Key Vault 키를 사용 하도록 설정 하려면 검색 서비스에 특정 액세스 권한을 부여 해야 합니다.

지정 된 시간에 액세스 권한이 취소 될 수 있습니다. 해지 되 면 해당 키 자격 증명 모음을 사용 하는 모든 검색 서비스 인덱스 또는 동의어 맵을 사용할 수 없게 됩니다. 나중에 Key vault 액세스 권한을 복원 하면 index\synonym 맵 액세스가 복원 됩니다. 자세한 내용은 [key vault에 대 한 보안 액세스](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)를 참조 하세요.

1. [Azure Portal에 로그인](https://portal.azure.com) 하 고 키 자격 증명 모음 개요 페이지를 엽니다. 

1. 왼쪽 탐색 창에서 **액세스 정책** 설정을 선택 하 고 **+ 새로 추가**를 클릭 합니다.

   ![새 키 자격 증명 모음 액세스 정책 추가](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "새 키 자격 증명 모음 액세스 정책 추가")

1. **보안 주체 선택** 을 클릭 하 고 Azure Cognitive Search 서비스를 선택 합니다. 이름 또는 관리 id를 사용 하도록 설정한 후 표시 된 개체 ID를 기준으로 검색할 수 있습니다.

   ![키 자격 증명 모음 액세스 정책 주체를 선택 합니다.](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "키 자격 증명 모음 액세스 정책 주체를 선택 합니다.")

1. **키 사용 권한** 을 클릭 하 고 *가져오기*, *키 래핑* 및 *키 래핑*을 선택 합니다. *Azure Data Lake Storage 또는 Azure Storage* 템플릿을 사용 하 여 필요한 사용 권한을 빠르게 선택할 수 있습니다.

   Azure Cognitive Search는 다음 [액세스 권한](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations)으로 부여 되어야 합니다.

   * *Get* -검색 서비스를 사용 하 여 Key Vault에서 키의 공개 부분을 검색할 수 있습니다.
   * *키 래핑* -검색 서비스에서 키를 사용 하 여 내부 암호화 키를 보호할 수 있습니다.
   * *래핑 해제 키* -검색 서비스에서 키를 사용 하 여 내부 암호화 키를 래핑 해제할 수 있습니다.

   ![키 자격 증명 모음 액세스 정책 키 사용 권한 선택](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "키 자격 증명 모음 액세스 정책 키 사용 권한 선택")

1. **확인** 을 클릭 하 고 액세스 정책 변경 내용을 **저장** 합니다.

> [!Important]
> Azure Cognitive Search의 암호화 된 콘텐츠는 특정 **버전**의 특정 Azure Key Vault 키를 사용 하도록 구성 됩니다. 키 또는 버전을 변경 하는 경우 이전 key\versiona를 삭제 **하기 전에** 새 key\version을 사용 하도록 인덱스 또는 동의어 맵을 업데이트 해야 합니다. 이렇게 하지 않으면 인덱스 또는 동의어 맵을 사용할 수 없게 되며 키 액세스가 손실 된 후에는 콘텐츠의 암호를 해독할 수 없습니다.   

## <a name="5---encrypt-content"></a>5-콘텐츠 암호화

Azure Portal를 사용 하 여 고객 관리 키로 암호화 된 인덱스 또는 동의어 맵을 만들 수는 없습니다. Azure Cognitive Search REST API를 사용 하 여 인덱스 또는 동의어 맵을 만들 수 있습니다.

인덱스와 동의어 맵은 모두 키를 지정 하는 데 사용 되는 새 최상위 **encryptionKey** 속성을 지원 합니다. 

주요 자격 증명 모음 키의 주요 **자격 증명 모음 Uri**, **키 이름** 및 **키 버전** 을 사용 하 여 **encryptionKey** 정의를 만들 수 있습니다.

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
> [!Note] 
> 이러한 주요 자격 증명 모음 세부 정보는 비밀로 간주 되지 않으며 Azure Portal의 관련 Azure Key Vault 키 페이지로 이동 하 여 쉽게 검색할 수 있습니다.

관리 id를 사용 하는 대신 Key Vault 인증을 위해 AAD 응용 프로그램을 사용 하는 경우 AAD 응용 프로그램 **액세스 자격 증명** 을 암호화 키에 추가 합니다. 
```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

## <a name="example-index-encryption"></a>예: 인덱스 암호화
REST API를 통해 새 인덱스를 만드는 방법에 대 한 자세한 내용은 [인덱스 만들기 (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)에서 찾을 수 있습니다. 여기서 유일한 차이점은 인덱스 정의의 일부로 암호화 키 세부 정보를 지정 하는 것입니다. 

```json
{
 "name": "hotels",  
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
 "encryptionKey": {
   "keyVaultUri": "https://demokeyvault.vault.azure.net",
   "keyVaultKeyName": "myEncryptionKey",
   "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
 }
}
```
이제 인덱스 생성 요청을 보낸 다음 인덱스를 정상적으로 사용 하기 시작할 수 있습니다.

## <a name="example-synonym-map-encryption"></a>예: 동의어 맵 암호화

REST API를 통해 새 동의어 맵을 만드는 방법에 대 한 자세한 내용은 동의어 맵 [만들기 (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map)에서 찾을 수 있습니다. 여기서 유일한 차이점은 동의어 맵 정의의 일부로 암호화 키 세부 정보를 지정 하는 것입니다. 

```json
{   
  "name" : "synonymmap1",  
  "format" : "solr",  
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
이제 동의어 맵 만들기 요청을 보낸 다음 일반적으로 사용을 시작할 수 있습니다.

>[!Important] 
> **EncryptionKey** 는 기존 Azure Cognitive Search 인덱스 또는 동의어 맵에 추가할 수 없지만 세 가지 주요 자격 증명 모음 세부 정보 (예: 키 버전 업데이트)에 대해 다른 값을 제공 하 여 업데이트할 수 있습니다. 새 Key Vault 키 또는 새 키 버전으로 변경 하는 경우 이전 key\versiona를 삭제 **하기 전에** 먼저 새 key\version을 사용 하도록 해당 키를 사용 하는 모든 Azure Cognitive Search 인덱스나 동의어 맵을 업데이트 해야 합니다. 이렇게 하지 않으면 키 액세스가 손실 된 후 콘텐츠를 해독할 수 없으므로 인덱스나 동의어 맵을 사용할 수 없게 됩니다.   
> 나중에 Key vault 액세스 권한을 복원 하면 콘텐츠 액세스가 복원 됩니다.

## <a name="aad-app"></a>고급: 외부에서 관리 되는 Azure Active Directory 응용 프로그램 사용

관리 id를 사용할 수 없는 경우 Azure Cognitive Search 서비스의 보안 주체를 사용 하 여 Azure Active Directory 응용 프로그램을 만들 수 있습니다. 특히 관리 되는 id는 다음과 같은 경우에는 실행 되지 않습니다.

* 검색 서비스가 Azure Key Vault와 다른 Active Directory 테넌트에 있는 경우와 같이 검색 서비스에 대 한 액세스 권한을 키 자격 증명 모음에 직접 부여할 수는 없습니다.

* 단일 검색 서비스는 각각 다른 키 자격 증명 모음에서 다른 키를 사용 하 여 각각 다른 키 자격 증명 모음에서 다른 키를 사용 해야 하는 여러 암호화 된 indexes\synonym 맵을 호스트 해야 합니다. 여기서 각 키 자격 증명 모음은 인증에 **다른 id** 다른 id를 사용 하 여 다른 키 자격 증명 모음을 관리 하는 것은 요구 사항이 아닙니다. 위의 관리 id 옵션을 사용 하십시오.  

이러한 토폴로지를 수용 하기 위해 Azure Cognitive Search는 Azure Active Directory (AAD) 응용 프로그램을 사용 하 여 검색 서비스와 Key Vault 간의 인증을 지원 합니다.    
포털에서 AAD 응용 프로그램을 만들려면 다음을 수행 합니다.

1. [Azure Active Directory 애플리케이션을 만듭니다](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. 암호화 된 인덱스를 만드는 데 필요한 [응용 프로그램 ID 및 인증 키를 가져옵니다](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) . 제공 해야 하는 값에는 **응용 프로그램 ID** 및 **인증 키**가 포함 됩니다.

>[!Important]
> 관리 되는 id 대신 AAD 응용 프로그램 인증을 사용 하기로 결정 하는 경우 Azure Cognitive Search 사용자를 대신 하 여 AAD 응용 프로그램을 관리할 권한이 없고 정기적으로 AAD 응용 프로그램을 관리 하는 것이 좋습니다. 응용 프로그램 인증 키의 회전입니다.
> AAD 응용 프로그램 또는 해당 인증 키를 변경 하는 경우 이전 응용 프로그램 또는 해당 권한 부여 키를 삭제 **하기 전에** 새 응용 프로그램 ID\key를 사용 하도록 먼저 업데이트 하 고이에 대 한 Key Vault 액세스를 취소 하기 전에 해당 응용 프로그램을 사용 하는 모든 Azure Cognitive Search 인덱스 또는 동의어 맵을 업데이트 해야 합니다.
> 이렇게 하지 않으면 키 액세스가 손실 된 후 콘텐츠를 해독할 수 없으므로 인덱스나 동의어 맵을 사용할 수 없게 됩니다.   

## <a name="next-steps"></a>다음 단계

Azure 보안 아키텍처에 익숙하지 않은 경우 [Azure 보안 설명서](https://docs.microsoft.com/azure/security/)를 검토 합니다. 특히이 문서는 다음과 같습니다.

> [!div class="nextstepaction"]
> [미사용 데이터 암호화](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
