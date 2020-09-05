---
title: 암호화 키 정보 가져오기
titleSuffix: Azure Cognitive Search
description: Azure Key Vault에서 키를 관리할 수 있도록 인덱스나 동의어 맵에 사용 되는 암호화 키 이름 및 버전을 검색 합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.openlocfilehash: 37ff94608e9756142f70a4f3c64d0a6f7eeea685
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88932902"
---
# <a name="get-customer-managed-key-information-from-indexes-and-synonym-maps"></a>인덱스 및 동의어 맵에서 고객이 관리 하는 키 정보 가져오기

Azure Cognitive Search에서는 고객이 관리 하는 암호화 키가 Azure Key Vault에서 생성, 저장 및 관리 됩니다. 개체가 암호화 되었는지 또는 사용 된 키 이름 또는 버전을 확인 해야 하는 경우 REST API 또는 SDK를 사용 하 여 인덱스 또는 동의어 맵 정의에서 **encryptionKey** 속성을 검색 합니다. 

키 사용을 모니터링할 수 있도록 Key Vault에 대 한 [로깅을 사용 하도록 설정](../key-vault/general/logging.md) 하는 것이 좋습니다.

## <a name="get-the-admin-api-key"></a>관리 API 키 가져오기

검색 서비스에서 개체 정의를 가져오려면 관리자 권한으로 인증 해야 합니다. 관리 API 키를 가져오는 가장 쉬운 방법은 포털을 통하는 것입니다.

1. [Azure Portal](https://portal.azure.com/) 에 로그인 하 고 search service 개요 페이지를 엽니다.

1. 왼쪽에서 **키** 를 클릭 하 고 관리 API를 복사 합니다. 인덱스 및 동의어 맵 검색에는 관리자 키가 필요 합니다.

나머지 단계를 수행 하려면 PowerShell 및 REST API로 전환 합니다. 포털에는 동의어 맵과 인덱스의 암호화 키 속성이 표시 되지 않습니다.

## <a name="use-powershell-and-rest"></a>PowerShell 및 REST 사용

다음 명령을 실행 하 여 변수를 설정 하 고 개체 정의를 가져옵니다.

```powershell
<# Connect to Azure #>
$Connect-AzAccount

<# Provide the admin API key used for search service authentication  #>
$headers = @{
'api-key' = '<YOUR-ADMIN-API-KEY>'
'Content-Type' = 'application/json'
'Accept' = 'application/json' }

<# List all existing synonym maps #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# List all existing indexes #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific synonym map definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms/<YOUR-SYNONYM-MAP-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific index definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/<YOUR-INDEX-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json
```

## <a name="next-steps"></a>다음 단계

사용 되는 암호화 키와 버전을 파악 했으므로 Azure Key Vault에서 키를 관리 하거나 다른 구성 설정을 확인할 수 있습니다.

+ [빠른 시작: PowerShell을 사용하여 Azure Key Vault에서 비밀을 설정하고 검색](../key-vault/secrets/quick-create-powershell.md)

+ [Azure Cognitive Search에서 데이터 암호화를 위해 고객이 관리 하는 키 구성](search-security-manage-encryption-keys.md)