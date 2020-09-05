---
title: 고객 관리 키를 사용 하 여 미사용 암호화
titleSuffix: Azure Cognitive Search
description: Azure Key Vault에서 만들고 관리 하는 키를 사용 하 여 인덱스 및 동의어 맵에 대 한 서버 쪽 암호화를 보충 Cognitive Search 합니다.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.custom: references_regions
ms.openlocfilehash: 2dc7458dd905ff84455927c81b4ea93765d4f5cb
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928822"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Azure Cognitive Search에서 데이터 암호화를 위해 고객이 관리 하는 키 구성

Azure Cognitive Search [는 서비스 관리 키](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components)를 사용 하 여 미사용 인덱싱된 콘텐츠를 자동으로 암호화 합니다. 더 많은 보호가 필요한 경우 Azure Key Vault에서 만들고 관리 하는 키를 사용 하 여 추가 암호화 계층으로 기본 암호화를 보완할 수 있습니다. 이 문서에서는 CMK 암호화를 설정 하는 단계를 안내 합니다.

CMK 암호화는 [Azure Key Vault](../key-vault/general/overview.md)에 따라 달라 집니다. 사용자 고유의 암호화 키를 만들고 Azure Key Vault에 저장할 수도 있고 Azure Key Vault의 API를 사용하여 암호화 키를 생성할 수도 있습니다. Azure Key Vault를 사용 하 여 [로깅을 사용](../key-vault/general/logging.md)하는 경우 키 사용을 감사할 수도 있습니다.  

고객 관리 키를 사용 하는 암호화는 해당 개체가 생성 될 때 개별 인덱스나 동의어 맵에 적용 되며 검색 서비스 수준 자체에는 지정 되지 않습니다. 새 개체만 암호화할 수 있습니다. 이미 존재 하는 콘텐츠는 암호화할 수 없습니다.

키가 모두 동일한 키 자격 증명 모음에 있을 필요는 없습니다. 단일 검색 서비스는 서로 다른 키 자격 증명 모음에 저장 된 고유한 고객 관리 암호화 키로 암호화 된 여러 암호화 된 인덱스 또는 동의어 맵을 호스트할 수 있습니다. 고객 관리 키를 사용 하 여 암호화 되지 않은 동일한 서비스에 인덱스 및 동의어 맵을 포함할 수도 있습니다. 

## <a name="double-encryption"></a>이중 암호화

2020 년 8 월 1 일 이후에 만들어진 서비스의 경우 CMK 암호화의 범위에는 다음 지역에서 현재 사용할 수 있는 [전체 이중 암호화](search-security-overview.md#double-encryption)를 얻는 임시 디스크가 포함 됩니다. 

+ 미국 서부 2
+ 미국 동부
+ 미국 중남부
+ US Gov 버지니아
+ US Gov 애리조나

다른 지역 또는 8 월 1 일 이전에 만든 서비스를 사용 하는 경우 CMK 암호화는 서비스에서 사용 하는 임시 디스크를 제외 하 고 데이터 디스크로만 제한 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

이 예제에서는 다음 서비스와 서비스를 사용 합니다. 

+ [Azure Cognitive Search 서비스를 만들](search-create-service-portal.md)거나 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 

+ [Azure Key Vault 리소스를 만들거나](../key-vault/secrets/quick-create-portal.md#create-a-vault) Azure Cognitive Search와 동일한 구독에서 기존 자격 증명 모음을 찾습니다. 이 기능에는 동일한 구독 요구 사항이 있습니다.

+ [Azure PowerShell](/powershell/azure/) 또는 [Azure CLI](/cli/azure/install-azure-cli) 는 구성 작업에 사용 됩니다.

+ [Postman](search-get-started-postman.md), [AZURE POWERSHELL](./search-get-started-powershell.md) 및 [.net SDK 미리 보기](https://aka.ms/search-sdk-preview) 를 사용 하 여 암호화 키 매개 변수를 포함 하는 인덱스 및 동의어 맵을 만드는 REST API를 호출할 수 있습니다. 현재는 인덱스 또는 동의어 맵에 키를 추가할 수 있는 포털이 지원 되지 않습니다.

>[!Note]
> Azure 키 자격 증명 모음 키가 삭제 된 경우 Azure Cognitive Search는 고객 관리 키를 사용 하 여 암호화의 특성으로 인해 데이터를 검색할 수 없습니다. Key Vault 실수로 키 삭제로 인 한 데이터 손실을 방지 하려면 키 자격 증명 모음에 대해 일시 삭제 및 제거 보호를 사용 하도록 설정 해야 합니다. 일시 삭제는 기본적으로 사용 하도록 설정 되어 있으므로 의도적으로 사용 하지 않도록 설정한 경우에만 문제가 발생 합니다. 제거 보호는 기본적으로 사용 하도록 설정 되어 있지 않지만 Azure Cognitive Search CMK 암호화에 필요 합니다. 자세한 내용은 [일시 삭제](../key-vault/general/soft-delete-overview.md) 및 [보호 제거](../key-vault/general/soft-delete-overview.md#purge-protection) 개요를 참조 하세요.

## <a name="1---enable-key-recovery"></a>1-키 복구 사용

키 자격 증명 모음에는 **일시 삭제** 및 **보호 제거** 를 사용 하도록 설정 해야 합니다. 이러한 기능은 포털 또는 다음 PowerShell 또는 Azure CLI 명령을 사용 하 여 설정할 수 있습니다.

### <a name="using-powershell"></a>PowerShell 사용

1. `Connect-AzAccount`을 실행 하 여 Azure 자격 증명을 설정 합니다.

1. 다음 명령을 실행 하 여 키 자격 증명 모음에 연결 하 고를 `<vault_name>` 유효한 이름으로 바꿉니다.

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. 일시 삭제를 사용 하 여 Azure Key Vault를 만듭니다. 자격 증명 모음에서 사용 하지 않도록 설정 된 경우 다음 명령을 실행 합니다.

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. 보호 제거 사용:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. 업데이트를 저장 합니다.

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Azure CLI 사용

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2-새 키 만들기

기존 키를 사용 하 여 Azure Cognitive Search 콘텐츠를 암호화 하는 경우이 단계를 건너뜁니다.

1. [Azure Portal에 로그인](https://portal.azure.com) 하 고 키 자격 증명 모음 개요 페이지를 엽니다.

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

 일반적으로 관리 되는 id를 사용 하면 검색 서비스에서 코드에 자격 증명을 저장 하지 않고 Azure Key Vault에 인증할 수 있습니다. 이러한 유형의 관리 되는 id의 수명 주기는 관리 id를 하나만 포함할 수 있는 검색 서비스의 수명 주기에 연결 됩니다. [관리 id에 대해 자세히 알아보세요](../active-directory/managed-identities-azure-resources/overview.md).

1. [Azure Portal에 로그인](https://portal.azure.com) 하 고 search 서비스 개요 페이지를 엽니다. 

1. 왼쪽 탐색 창에서 **id** 를 클릭 하 고 상태를 **켜기**로 변경 하 고 **저장**을 클릭 합니다.

![관리 id 사용](./media/search-enable-msi/enable-identity-portal.png "관리 되 id 사용")

## <a name="4---grant-key-access-permissions"></a>4-키 액세스 권한 부여

검색 서비스에서 Key Vault 키를 사용 하도록 설정 하려면 검색 서비스에 특정 액세스 권한을 부여 해야 합니다.

지정 된 시간에 액세스 권한이 취소 될 수 있습니다. 해지 되 면 해당 키 자격 증명 모음을 사용 하는 모든 검색 서비스 인덱스 또는 동의어 맵을 사용할 수 없게 됩니다. 나중에 Key vault 액세스 권한을 복원 하면 index\synonym 맵 액세스가 복원 됩니다. 자세한 내용은 [key vault에 대 한 보안 액세스](../key-vault/general/secure-your-key-vault.md)를 참조 하세요.

1. [Azure Portal에 로그인](https://portal.azure.com) 하 고 키 자격 증명 모음 개요 페이지를 엽니다. 

1. 왼쪽 탐색 창에서 **액세스 정책** 설정을 선택 하 고 **+ 새로 추가**를 클릭 합니다.

   ![새 키 자격 증명 모음 액세스 정책 추가](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "새 키 자격 증명 모음 액세스 정책 추가")

1. **보안 주체 선택** 을 클릭 하 고 Azure Cognitive Search 서비스를 선택 합니다. 이름 또는 관리 id를 사용 하도록 설정한 후 표시 된 개체 ID를 기준으로 검색할 수 있습니다.

   ![키 자격 증명 모음 액세스 정책 주체를 선택 합니다.](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "키 자격 증명 모음 액세스 정책 주체를 선택 합니다.")

1. **키 사용 권한** 을 클릭 하 고 *가져오기*, *키 래핑* 및 *키 래핑*을 선택 합니다. *Azure Data Lake Storage 또는 Azure Storage* 템플릿을 사용 하 여 필요한 사용 권한을 빠르게 선택할 수 있습니다.

   Azure Cognitive Search는 다음 [액세스 권한](../key-vault/keys/about-keys.md#key-operations)으로 부여 되어야 합니다.

   * *Get* -검색 서비스를 사용 하 여 Key Vault에서 키의 공개 부분을 검색할 수 있습니다.
   * *키 래핑* -검색 서비스에서 키를 사용 하 여 내부 암호화 키를 보호할 수 있습니다.
   * *래핑 해제 키* -검색 서비스에서 키를 사용 하 여 내부 암호화 키를 래핑 해제할 수 있습니다.

   ![키 자격 증명 모음 액세스 정책 키 사용 권한 선택](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "키 자격 증명 모음 액세스 정책 키 사용 권한 선택")

1. **비밀 사용 권한에**대해 *가져오기*를 선택 합니다.

1. **인증서 사용 권한에**대해 *가져오기*를 선택 합니다.

1. **확인** 을 클릭 하 고 액세스 정책 변경 내용을 **저장** 합니다.

> [!Important]
> Azure Cognitive Search의 암호화 된 콘텐츠는 특정 **버전**의 특정 Azure Key Vault 키를 사용 하도록 구성 됩니다. 키 또는 버전을 변경 하는 경우 이전 key\versiona를 삭제 **하기 전에** 새 key\version을 사용 하도록 인덱스 또는 동의어 맵을 업데이트 해야 합니다. 이렇게 하지 않으면 인덱스 또는 동의어 맵을 사용할 수 없게 되며 키 액세스가 손실 된 후에는 콘텐츠의 암호를 해독할 수 없습니다.   

## <a name="5---encrypt-content"></a>5-콘텐츠 암호화

인덱스 또는 동의어 맵에 고객이 관리 하는 키를 추가 하려면 [검색 REST API](/rest/api/searchservice/) 또는 SDK를 사용 해야 합니다. 포털은 동의어 맵 또는 암호화 속성을 노출 하지 않습니다. 유효한 API를 사용 하는 경우 인덱스와 동의어 맵이 모두 최상위 **encryptionKey** 속성을 지원 합니다. 

주요 자격 증명 모음 키의 주요 **자격 증명 모음 Uri**, **키 이름** 및 **키 버전** 을 사용 하 여 다음과 같이 **encryptionKey** 정의를 만듭니다.

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
REST API를 통해 새 인덱스를 만드는 방법에 대 한 자세한 내용은 [인덱스 만들기 (Azure Cognitive Search REST API)](/rest/api/searchservice/create-index)에서 찾을 수 있습니다. 여기서 유일한 차이점은 인덱스 정의의 일부로 암호화 키 세부 정보를 지정 하는 것입니다. 

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

REST API를 통해 새 동의어 맵을 만드는 방법에 대 한 자세한 내용은 동의어 맵 [만들기 (Azure Cognitive Search REST API)](/rest/api/searchservice/create-synonym-map)에서 찾을 수 있습니다. 여기서 유일한 차이점은 동의어 맵 정의의 일부로 암호화 키 세부 정보를 지정 하는 것입니다. 

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

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a> 고급: 외부에서 관리 되는 Azure Active Directory 응용 프로그램 사용

관리 id를 사용할 수 없는 경우 Azure Cognitive Search 서비스의 보안 주체를 사용 하 여 Azure Active Directory 응용 프로그램을 만들 수 있습니다. 특히 관리 되는 id는 다음과 같은 경우에는 실행 되지 않습니다.

* 검색 서비스가 Azure Key Vault와 다른 Active Directory 테 넌 트에 있는 경우와 같이 검색 서비스에 대 한 액세스 권한을 키 자격 증명 모음에 직접 부여할 수는 없습니다.

* 단일 검색 서비스는 각각 다른 키 자격 증명 모음에서 다른 키를 사용 하 여 각각 다른 키 자격 증명 모음에서 다른 키를 사용 해야 하는 여러 암호화 된 indexes\synonym 맵을 호스트 해야 합니다. 여기서 각 키 자격 증명 모음은 인증에 **다른 id** 다른 id를 사용 하 여 다른 키 자격 증명 모음을 관리 하는 것은 요구 사항이 아닙니다. 위의 관리 id 옵션을 사용 하십시오.  

이러한 토폴로지를 수용 하기 위해 Azure Cognitive Search는 Azure Active Directory (AAD) 응용 프로그램을 사용 하 여 검색 서비스와 Key Vault 간의 인증을 지원 합니다.    
포털에서 AAD 응용 프로그램을 만들려면 다음을 수행 합니다.

1. [Azure Active Directory 응용 프로그램을 만듭니다](../active-directory/develop/howto-create-service-principal-portal.md).

1. 암호화 된 인덱스를 만드는 데 필요한 [응용 프로그램 ID 및 인증 키를 가져옵니다](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) . 제공 해야 하는 값에는 **응용 프로그램 ID** 및 **인증 키**가 포함 됩니다.

>[!Important]
> 관리 되는 id 대신 AAD 응용 프로그램 인증을 사용 하기로 결정 하는 경우 Azure Cognitive Search 사용자 대신 aad 응용 프로그램을 관리할 수 있는 권한이 없다는 사실을 고려 하 고, 응용 프로그램 인증 키의 주기적인 회전과 같은 AAD 응용 프로그램을 관리 하는 것이 좋습니다.
> AAD 응용 프로그램 또는 해당 인증 키를 변경 하는 경우 이전 응용 프로그램 또는 해당 권한 부여 키를 삭제 **하기 전에** 새 응용 프로그램 ID\key를 사용 하도록 먼저 업데이트 하 고이에 대 한 Key Vault 액세스를 취소 하기 전에 해당 응용 프로그램을 사용 하는 모든 Azure Cognitive Search 인덱스 또는 동의어 맵을 업데이트 해야 합니다.
> 이렇게 하지 않으면 키 액세스가 손실 된 후 콘텐츠를 해독할 수 없으므로 인덱스나 동의어 맵을 사용할 수 없게 됩니다.

## <a name="work-with-encrypted-content"></a>암호화 된 콘텐츠 작업

CMK 암호화를 사용 하는 경우 추가 암호화/암호 해독 작업으로 인해 인덱싱 및 쿼리 모두에 대 한 대기 시간을 알 수 있습니다. Azure Cognitive Search는 암호화 작업을 기록 하지 않지만 key vault 로깅을 통해 키 액세스를 모니터링할 수 있습니다. 키 자격 증명 모음의 일부로 [로깅을 사용 하도록](../key-vault/general/logging.md) 설정 하는 것이 좋습니다.

키 회전은 시간이 지남에 따라 발생 합니다. 키를 회전할 때마다 다음 순서를 따르는 것이 중요 합니다.

1. [인덱스 또는 동의어 맵에 사용 되는 키를 확인](search-security-get-encryption-keys.md)합니다.
1. [키 자격 증명 모음에 새 키](../key-vault/keys/quick-create-portal.md)를 만들지만 원래 키를 사용할 수 있는 상태로 둡니다.
1. 인덱스 또는 동의어 맵의 [encryptionKey 속성을 업데이트](/rest/api/searchservice/update-index) 하 여 새 값을 사용 합니다. 원래이 속성을 사용 하 여 만든 개체만 다른 값을 사용 하도록 업데이트할 수 있습니다.
1. 키 자격 증명 모음에서 이전 키를 사용 하지 않도록 설정 하거나 삭제 합니다. 키 액세스를 모니터링 하 여 새 키가 사용 되 고 있는지 확인 합니다.

성능상의 이유로 검색 서비스는 최대 몇 시간 동안 키를 캐시 합니다. 새 키를 제공 하지 않고 키를 사용 하지 않도록 설정 하거나 삭제 한 경우에는 캐시가 만료 될 때까지 쿼리가 일시적으로 계속 작동 합니다. 그러나 검색 서비스에서 콘텐츠 암호를 해독할 수 없는 경우 다음 메시지가 표시 됩니다. "액세스가 금지 되었습니다. 사용 된 쿼리 키가 해지 되었을 수 있습니다. 다시 시도 하십시오. " 

## <a name="next-steps"></a>다음 단계

Azure 보안 아키텍처에 익숙하지 않은 경우 [Azure 보안 설명서](../security/index.yml)를 검토 합니다. 특히이 문서는 다음과 같습니다.

> [!div class="nextstepaction"]
> [미사용 데이터 암호화](../security/fundamentals/encryption-atrest.md)