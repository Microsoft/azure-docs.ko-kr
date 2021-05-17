---
title: 고객 관리 키를 사용 하 여 미사용 암호화
titleSuffix: Azure Cognitive Search
description: Azure Key Vault에서 만들고 관리 하는 키를 사용 하 여 인덱스 및 동의어 맵에 대 한 서버 쪽 암호화를 보충 Cognitive Search 합니다.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.custom: references_regions
ms.openlocfilehash: 6b1079797f1a753fa8362d6e920f3394087d7e9f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98119291"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Azure Cognitive Search에서 데이터 암호화를 위해 고객이 관리 하는 키 구성

Azure Cognitive Search [는 서비스 관리 키](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components)를 사용 하 여 미사용 인덱싱된 콘텐츠를 자동으로 암호화 합니다. 더 많은 보호가 필요한 경우 Azure Key Vault에서 만들고 관리 하는 키를 사용 하 여 추가 암호화 계층으로 기본 암호화를 보완할 수 있습니다. 이 문서에서는 고객이 관리 하는 키 암호화를 설정 하는 단계를 안내 합니다.

고객 관리 키 암호화는 [Azure Key Vault](../key-vault/general/overview.md)에 따라 달라 집니다. 사용자 고유의 암호화 키를 만들고 Azure Key Vault에 저장할 수도 있고 Azure Key Vault의 API를 사용하여 암호화 키를 생성할 수도 있습니다. Azure Key Vault를 사용 하 여 [로깅을 사용](../key-vault/general/logging.md)하는 경우 키 사용을 감사할 수도 있습니다.  

고객 관리 키를 사용 하는 암호화는 해당 개체가 생성 될 때 개별 인덱스나 동의어 맵에 적용 되며 검색 서비스 수준 자체에는 지정 되지 않습니다. 새 개체만 암호화할 수 있습니다. 이미 존재 하는 콘텐츠는 암호화할 수 없습니다.

키가 모두 동일한 키 자격 증명 모음에 있을 필요는 없습니다. 단일 검색 서비스는 서로 다른 키 자격 증명 모음에 저장 된 고유한 고객 관리 암호화 키로 암호화 된 여러 암호화 된 인덱스 또는 동의어 맵을 호스트할 수 있습니다. 고객 관리 키를 사용 하 여 암호화 되지 않은 동일한 서비스에 인덱스 및 동의어 맵을 포함할 수도 있습니다.

>[!Important]
> 고객 관리 키를 구현 하는 경우 주요 자격 증명 모음 키를 정기적으로 회전 하 고 응용 프로그램 암호 및 등록을 Active Directory 하는 엄격한 절차를 수행 해야 합니다. 이전 암호 및 키를 삭제 하기 전에 새 비밀 및 키를 사용 하도록 모든 암호화 된 콘텐츠를 항상 업데이트 합니다. 이 단계를 수행 하지 않은 경우 콘텐츠의 암호를 해독할 수 없습니다.

## <a name="double-encryption"></a>이중 암호화

2020 년 8 월 1 일 이후에 생성 된 서비스의 경우, 특정 지역에서 고객이 관리 하는 키 암호화의 범위는 임시 디스크를 포함 하 여 [전체 이중 암호화](search-security-overview.md#double-encryption)를 제공 합니다 .이 지역에서는 현재 

+ 미국 서부 2
+ 미국 동부
+ 미국 중남부
+ US Gov 버지니아
+ US Gov 애리조나

다른 지역 또는 8 월 1 일 이전에 만든 서비스를 사용 하는 경우 관리 키 암호화는 서비스에서 사용 하는 임시 디스크를 제외 하 고 데이터 디스크로만 제한 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

이 시나리오에서 사용 되는 도구 및 서비스는 다음과 같습니다.

+ [Azure Cognitive Search](search-create-service-portal.md) 는 [청구 가능한 계층](search-sku-tier.md#tier-descriptions) (기본 이상, 모든 지역)에 있습니다.
+ [Azure Key Vault](../key-vault/general/overview.md) [Azure Portal](../key-vault//general/quick-create-portal.md), [Azure CLI](../key-vault//general/quick-create-cli.md)또는 [Azure PowerShell](../key-vault//general/quick-create-powershell.md)를 사용 하 여 주요 자격 증명 모음을 만들 수 있습니다. Azure Cognitive Search와 동일한 구독에 있습니다. 키 자격 증명 모음에는 **일시 삭제** 및 **보호 제거** 를 사용 하도록 설정 해야 합니다.
+ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). 새 테 넌 트를 설치 하지 않은 경우에 [는 새 테 넌 트를 설정](../active-directory/develop/quickstart-create-new-tenant.md)합니다.

암호화 된 개체를 만들 수 있는 검색 응용 프로그램이 있어야 합니다. 이 코드에는 주요 자격 증명 모음 키를 참조 하 고 등록 정보를 Active Directory 합니다. 이 코드는 작업 중인 앱 또는 [c # 코드 샘플 DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)같은 프로토타입 코드 일 수 있습니다.

> [!TIP]
> [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)또는 [Azure PowerShell](./search-get-started-powershell.md)를 사용 하 여 암호화 키 매개 변수를 포함 하는 인덱스 및 동의어 맵을 만드는 REST api를 호출할 수 있습니다. 현재는 인덱스 또는 동의어 맵에 키를 추가할 수 있는 포털이 지원 되지 않습니다.

## <a name="1---enable-key-recovery"></a>1-키 복구 사용

고객 관리 키를 사용 하는 암호화의 특성으로 인해 Azure Key vault 키가 삭제 되 면 아무도 데이터를 검색할 수 없습니다. Key Vault 실수로 키 삭제로 인 한 데이터 손실을 방지 하려면 키 자격 증명 모음에 대해 일시 삭제 및 제거 보호를 사용 하도록 설정 해야 합니다. 일시 삭제는 기본적으로 사용 하도록 설정 되어 있으므로 의도적으로 사용 하지 않도록 설정한 경우에만 문제가 발생 합니다. 제거 보호는 기본적으로 사용 하도록 설정 되어 있지 않지만 Cognitive Search에서 고객이 관리 하는 키 암호화에 필요 합니다. 자세한 내용은 [일시 삭제](../key-vault/general/soft-delete-overview.md) 및 [보호 제거](../key-vault/general/soft-delete-overview.md#purge-protection) 개요를 참조 하세요.

Portal, PowerShell 또는 Azure CLI 명령을 사용 하 여 두 속성을 모두 설정할 수 있습니다.

### <a name="using-azure-portal"></a>Azure Portal 사용

1. [Azure Portal에 로그인](https://portal.azure.com) 하 고 키 자격 증명 모음 개요 페이지를 엽니다.

1. **개요** 페이지의 **Essentials** 에서 **일시 삭제** 및 **보호 제거** 를 사용 하도록 설정 합니다.

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

+ [Azure CLI](/cli/azure/install-azure-cli)을 설치한 경우 다음 명령을 실행 하 여 필수 속성을 사용 하도록 설정할 수 있습니다.

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2-Key Vault에서 키 만들기

Azure Key Vault에 키가 이미 있는 경우이 단계를 건너뜁니다.

1. [Azure Portal에 로그인](https://portal.azure.com) 하 고 키 자격 증명 모음 개요 페이지를 엽니다.

1. 왼쪽에서 **키** 를 선택 하 고 **+ 생성/가져오기** 를 선택 합니다.

1. **키 만들기** 창에 있는 **옵션** 목록에서 키를 만드는 데 사용할 방법을 선택 합니다. 새 키를 **생성** 하거나 기존 키를 **업로드** 하거나 **복원 백업을** 사용 하 여 키 백업을 선택할 수 있습니다.

1. 키의 **이름을** 입력 하 고 필요에 따라 다른 키 속성을 선택 합니다.

1. **만들기** 를 선택 하 여 배포를 시작 합니다.

1. 키 식별자를 적어둡니다. 키 **값은 Uri**, **키 이름** 및 키 **버전** 으로 구성 됩니다. Azure Cognitive Search에서 암호화 된 인덱스를 정의 하려면 식별자가 필요 합니다.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="새 key vault 키 만들기":::

## <a name="3---register-an-app-in-active-directory"></a>3-Active Directory에 앱 등록

1. [Azure Portal](https://portal.azure.com)에서 구독에 대 한 Azure Active Directory 리소스를 찾습니다.

1. 왼쪽의 **관리** 에서 **앱 등록** 를 선택 하 고 **새 등록** 을 선택 합니다.

1. 검색 응용 프로그램 이름과 비슷한 이름을 등록 이름을 지정 합니다. **등록** 을 선택합니다.

1. 앱 등록을 만든 후 응용 프로그램 ID를 복사 합니다. 응용 프로그램에이 문자열을 제공 해야 합니다. 

   [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)를 단계별로 실행 하는 경우이 값을 파일 **의appsettings.js** 에 붙여넣습니다.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="Essentials 섹션의 응용 프로그램 ID":::

1. 그런 다음 왼쪽에서 **인증서 & 암호** 를 선택 합니다.

1. **새 클라이언트 비밀** 을 선택합니다. 비밀에 표시 이름을 지정 하 고 **추가** 를 선택 합니다.

1. 응용 프로그램 암호를 복사 합니다. 샘플을 단계별로 실행 하는 경우이 값을 파일 **의appsettings.js** 에 붙여넣습니다.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="애플리케이션 암호":::

## <a name="4---grant-key-access-permissions"></a>4-키 액세스 권한 부여

이 단계에서는 Key Vault에서 액세스 정책을 만듭니다. 이 정책은 사용자가 등록 한 응용 프로그램에 고객이 관리 하는 키를 사용할 수 있는 권한을 부여 Active Directory 합니다.

지정 된 시간에 액세스 권한이 취소 될 수 있습니다. 해지 되 면 해당 키 자격 증명 모음을 사용 하는 모든 검색 서비스 인덱스 또는 동의어 맵을 사용할 수 없게 됩니다. 나중에 Key vault 액세스 권한을 복원 하면 index\synonym 맵 액세스가 복원 됩니다. 자세한 내용은 [key vault에 대 한 보안 액세스](../key-vault/general/secure-your-key-vault.md)를 참조 하세요.

1. 계속 Azure Portal에서 키 자격 증명 모음 **개요** 페이지를 엽니다. 

1. 왼쪽에서 **액세스 정책을** 선택 하 고 **+ 액세스 정책 추가** 를 선택 합니다.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="새 키 자격 증명 모음 액세스 정책 추가":::

1. **보안 주체 선택** 을 선택 하 고 Active Directory에 등록 한 응용 프로그램을 선택 합니다. 이름을 기준으로 검색할 수 있습니다.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="키 자격 증명 모음 액세스 정책 주체를 선택 합니다.":::

1. **키 권한** 에서 *가져오기*, *키 래핑* 및 *키 래핑* 을 선택 합니다.

1. **비밀 권한** 에서 *가져오기* 를 선택 합니다.

1. **인증서 사용 권한** 에서 *가져오기* 를 선택 합니다.

1. **추가** 를 선택한 다음 **저장** 을 선택 합니다.

> [!Important]
> Azure Cognitive Search의 암호화 된 콘텐츠는 특정 **버전** 의 특정 Azure Key Vault 키를 사용 하도록 구성 됩니다. 키 또는 버전을 변경 하는 경우 이전 key\versiona를 삭제 **하기 전에** 새 key\version을 사용 하도록 인덱스 또는 동의어 맵을 업데이트 해야 합니다. 이렇게 하지 않으면 인덱스 또는 동의어 맵을 사용할 수 없게 되며 키 액세스가 손실 된 후에는 콘텐츠의 암호를 해독할 수 없습니다.

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5-콘텐츠 암호화

인덱스, 데이터 원본, 기술, 인덱서 또는 동의어 맵에 고객이 관리 하는 키를 추가 하려면 [검색 REST API](/rest/api/searchservice/) 또는 SDK를 사용 해야 합니다. 포털은 동의어 맵 또는 암호화 속성을 노출 하지 않습니다. 유효한 API 인덱스를 사용 하는 경우 데이터 원본, 기술력과, 인덱서 및 동의어 맵이 최상위 **encryptionKey** 속성을 지원 합니다.

이 예에서는 Azure Key Vault 및 Azure Active Directory에 대 한 값과 함께 REST API를 사용 합니다.

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

> [!Note]
> 이러한 주요 자격 증명 모음 세부 정보는 비밀로 간주 되지 않으며 Azure Portal의 관련 Azure Key Vault 키 페이지로 이동 하 여 쉽게 검색할 수 있습니다.

## <a name="example-index-encryption"></a>예: 인덱스 암호화

[Create Index Azure Cognitive Search REST API](/rest/api/searchservice/create-index)를 사용 하 여 암호화 된 인덱스를 만듭니다. `encryptionKey`사용할 암호화 키를 지정 하려면 속성을 사용 합니다.
> [!Note]
> 이러한 주요 자격 증명 모음 세부 정보는 비밀로 간주 되지 않으며 Azure Portal의 관련 Azure Key Vault 키 페이지로 이동 하 여 쉽게 검색할 수 있습니다.

## <a name="rest-examples"></a>REST 예제

이 섹션에서는 암호화 된 인덱스 및 동의어 맵에 대 한 전체 JSON을 보여 줍니다.

### <a name="index-encryption"></a>인덱스 암호화

REST API를 통해 새 인덱스를 만드는 방법에 대 한 자세한 내용은 인덱스 [만들기 (REST API)](/rest/api/searchservice/create-index)에서 찾을 수 있습니다. 여기서 유일한 차이점은 인덱스 정의의 일부로 암호화 키 세부 정보를 지정 하는 것입니다.

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
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

이제 인덱스 생성 요청을 보낸 다음 인덱스를 정상적으로 사용 하기 시작할 수 있습니다.

### <a name="synonym-map-encryption"></a>동의어 맵 암호화

[동의어 맵 만들기 Azure Cognitive Search REST API](/rest/api/searchservice/create-synonym-map)를 사용 하 여 암호화 된 동의어 맵을 만듭니다. `encryptionKey`사용할 암호화 키를 지정 하려면 속성을 사용 합니다.

```json
{
  "name" : "synonymmap1",
  "format" : "solr",
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
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

이제 동의어 맵 만들기 요청을 보낸 다음 일반적으로 사용을 시작할 수 있습니다.

## <a name="example-data-source-encryption"></a>예: 데이터 원본 암호화

[데이터 원본 만들기 (Azure Cognitive Search REST API)](/rest/api/searchservice/create-data-source)를 사용 하 여 암호화 된 데이터 원본을 만듭니다. `encryptionKey`사용할 암호화 키를 지정 하려면 속성을 사용 합니다.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
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

이제 데이터 원본 만들기 요청을 보낸 다음 정상적으로 사용을 시작할 수 있습니다.

## <a name="example-skillset-encryption"></a>예: 기술 encryption

[Create 기술 Azure Cognitive Search REST API](/rest/api/searchservice/create-skillset)를 사용 하 여 암호화 된 기술를 만듭니다. `encryptionKey`사용할 암호화 키를 지정 하려면 속성을 사용 합니다.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
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

이제 기술 만들기 요청을 보낸 다음 정상적으로 사용을 시작할 수 있습니다.

## <a name="example-indexer-encryption"></a>예: 인덱서 암호화

[Create 인덱서 Azure Cognitive Search REST API](/rest/api/searchservice/create-indexer)를 사용 하 여 암호화 된 인덱서를 만듭니다. `encryptionKey`사용할 암호화 키를 지정 하려면 속성을 사용 합니다.

```json
{
  "name": "indexer1",
  "dataSourceName": "datasource1",
  "skillsetName": "skillset1",
  "parameters": {
      "configuration": {
          "imageAction": "generateNormalizedImages"
      }
  },
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

이제 인덱서 생성 요청을 보낸 다음 정상적으로 사용을 시작할 수 있습니다.

>[!Important]
> 는 `encryptionKey` 기존 검색 인덱스 또는 동의어 맵에 추가할 수 없지만 세 가지 주요 자격 증명 모음 세부 정보 (예: 키 버전 업데이트)에 대해 다른 값을 제공 하 여 업데이트할 수 있습니다. 새 Key Vault 키 또는 새 키 버전으로 변경 하는 경우 이전 key\versiona를 삭제 **하기 전에** 먼저 새 key\version을 사용 하도록 해당 키를 사용 하는 모든 검색 인덱스나 동의어 맵을 업데이트 해야 합니다. 이렇게 하지 않으면 키 액세스가 손실 된 후 콘텐츠를 해독할 수 없으므로 인덱스나 동의어 맵을 사용할 수 없게 됩니다. 키 자격 증명 모음 액세스 권한을 나중에 복원 해도 콘텐츠 액세스가 복원 됩니다.

## <a name="simpler-alternative-trusted-service"></a>더 간단한 대안: 신뢰할 수 있는 서비스

테 넌 트 구성 및 인증 요구 사항에 따라 주요 자격 증명 모음 키에 액세스 하기 위한 간단한 방법을 구현할 수 있습니다. Active Directory 응용 프로그램을 만들고 사용 하는 대신 시스템 관리 id를 사용 하도록 설정 하 여 검색 서비스를 신뢰할 수 있는 서비스로 만들 수 있습니다. 그런 다음 키 자격 증명 모음 키에 액세스 하기 위해 AD에 등록 된 응용 프로그램이 아닌 보안 원칙으로 신뢰할 수 있는 검색 서비스를 사용 합니다.

이 방법을 사용 하면 응용 프로그램 등록 및 응용 프로그램 암호에 대 한 단계를 생략 하 고 키 자격 증명 모음 구성 요소 (URI, 자격 증명 모음 이름, 키 버전)에만 암호화 키 정의를 단순화할 수 있습니다.

일반적으로 관리 되는 id를 사용 하면 검색 서비스에서 코드에 자격 증명 (ApplicationID 또는 ApplicationSecret)을 저장 하지 않고 Azure Key Vault에 인증할 수 있습니다. 이러한 유형의 관리 되는 id의 수명 주기는 관리 id를 하나만 포함할 수 있는 검색 서비스의 수명 주기에 연결 됩니다. 관리 id의 작동 방식에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 되는 Id 란?](../active-directory/managed-identities-azure-resources/overview.md)을 참조 하세요.

1. 검색 서비스를 신뢰할 수 있는 서비스로 설정 합니다.
   ![시스템 할당 관리 ID 켜기](./media/search-managed-identities/turn-on-system-assigned-identity.png "시스템 할당 관리 ID 켜기")

1. Azure Key Vault에서 액세스 정책을 설정할 때 AD에 등록 된 응용 프로그램 대신 신뢰 된 검색 서비스를 보안 주체로 선택 합니다. 액세스 키 권한 부여 단계에 설명 된 것 처럼 동일한 권한 (여러 개의 가져오기, 래핑, 래핑 해제)을 할당 합니다.

1. `encryptionKey`Active Directory 속성을 생략 하는의 단순화 된 생성을 사용 합니다.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

이 단순화 된 방법을 채택할 수 없게 되는 조건은 다음과 같습니다.

+ 검색 서비스가 Azure Key Vault와 다른 Active Directory 테 넌 트에 있는 경우와 같이 검색 서비스에 대 한 액세스 권한을 키 자격 증명 모음에 직접 부여할 수는 없습니다.

+ 단일 검색 서비스는 각각 다른 키 자격 증명 모음에서 다른 키를 사용 하 여 각각 다른 키 자격 증명 모음에서 다른 키를 사용 해야 하는 여러 암호화 된 indexes\synonym 맵을 호스트 해야 합니다. 여기서 각 키 자격 증명 모음은 인증에 **다른 id** 검색 서비스에는 관리 id가 하나만 있을 수 있기 때문에 여러 id에 대 한 요구 사항은 시나리오에 대 한 단순화 된 접근 방식을 규정 하지 않습니다.  

## <a name="work-with-encrypted-content"></a>암호화 된 콘텐츠 작업

고객 관리 키 암호화를 사용 하 여 추가 암호화/암호 해독 작업으로 인해 인덱싱 및 쿼리 모두에 대 한 대기 시간을 확인할 수 있습니다. Azure Cognitive Search는 암호화 작업을 기록 하지 않지만 key vault 로깅을 통해 키 액세스를 모니터링할 수 있습니다. 키 자격 증명 모음 구성의 일부로 [로깅을 사용](../key-vault/general/logging.md) 하는 것이 좋습니다.

키 회전은 시간이 지남에 따라 발생 합니다. 키를 회전할 때마다 다음 순서를 따르는 것이 중요 합니다.

1. [인덱스 또는 동의어 맵에 사용 되는 키를 확인](search-security-get-encryption-keys.md)합니다.
1. [키 자격 증명 모음에 새 키](../key-vault/keys/quick-create-portal.md)를 만들지만 원래 키를 사용할 수 있는 상태로 둡니다.
1. 인덱스 또는 동의어 맵의 [encryptionKey 속성을 업데이트](/rest/api/searchservice/update-index) 하 여 새 값을 사용 합니다. 원래이 속성을 사용 하 여 만든 개체만 다른 값을 사용 하도록 업데이트할 수 있습니다.
1. 키 자격 증명 모음에서 이전 키를 사용 하지 않도록 설정 하거나 삭제 합니다. 키 액세스를 모니터링 하 여 새 키가 사용 되 고 있는지 확인 합니다.

성능상의 이유로 검색 서비스는 최대 몇 시간 동안 키를 캐시 합니다. 새 키를 제공 하지 않고 키를 사용 하지 않도록 설정 하거나 삭제 한 경우에는 캐시가 만료 될 때까지 쿼리가 일시적으로 계속 작동 합니다. 그러나 검색 서비스에서 콘텐츠 암호를 해독할 수 없는 경우 다음 메시지가 표시 됩니다. "액세스가 금지 되었습니다. 사용 된 쿼리 키가 해지 되었을 수 있습니다. 다시 시도 하십시오. " 

## <a name="next-steps"></a>다음 단계

Azure 보안 아키텍처에 익숙하지 않은 경우 [Azure 보안 설명서](../security/index.yml)를 검토 합니다. 특히이 문서는 다음과 같습니다.

> [!div class="nextstepaction"]
> [저장소 데이터 암호화](../security/fundamentals/encryption-atrest.md)