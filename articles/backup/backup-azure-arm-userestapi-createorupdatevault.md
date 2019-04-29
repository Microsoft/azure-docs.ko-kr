---
title: 'Azure Backup: REST API를 사용하여 Recovery Services 자격 증명 모음 만들기'
description: REST API를 사용하여 Azure VM Backup의 백업 및 복원 작업 관리
services: backup
author: pvrk
manager: shivamg
keywords: REST API; Azure VM 백업; Azure VM 복원;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: pullabhk
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 4f18b10ee3f4148badc8e53a9660c9f5c998aef7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60646710"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>REST API를 사용하여 Azure Recovery Services 자격 증명 모음 만들기

REST API를 사용하여 Azure Recovery Services 자격 증명 모음을 만드는 단계는 [자격 증명 모음 REST API 만들기](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate) 문서에 설명되어 있습니다. 이 문서를 “미국 서부”에 “testVault”라는 자격 증명 모음을 만드는 데 참조로 사용하겠습니다.

Azure Recovery Services 자격 증명 모음을 만들거나 업데이트하려면 다음 *PUT* 작업을 사용합니다.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>요청 만들기

*PUT* 요청을 만들려면 `{subscription-id}` 매개 변수는 필수입니다. 구독이 여러 개인 경우 [여러 구독으로 작업](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)을 참조합니다. 리소스에 대해 `{resourceGroupName}` 및 `{vaultName}`과 함께 `api-version` 매개 변수를 정의합니다. 이 문서에서는 `api-version=2016-06-01`을 사용합니다.

다음과 같은 헤더가 필요합니다.

| 요청 헤더   | 설명 |
|------------------|-----------------|
| *Content-Type:*  | 필수 사항입니다. `application/json`로 설정합니다. |
| *권한 부여* | 필수 사항입니다. 유효한 `Bearer` [액세스 토큰](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)으로 설정합니다. |

요청을 만드는 방법에 대한 자세한 내용은 [REST API 요청/응답의 구성 요소](/rest/api/azure/#components-of-a-rest-api-requestresponse)를 참조하세요.

## <a name="create-the-request-body"></a>요청 본문 만들기

다음과 같은 일반적인 정의가 요청 본문을 빌드하는 데 사용됩니다.

|이름  |필수  |형식  |설명  |
|---------|---------|---------|---------|
|eTag     |         |   String      |  선택적 eTag       |
|location     |  true       |String         |   리소스 위치      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  자격 증명 모음의 속성       |
|sku     |         |  [Sku](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    각 Azure 리소스에 대한 고유한 시스템 식별자를 식별합니다.     |
|tags     |         | Object        |     리소스 태그    |

자격 증명 모음 이름 및 리소스 그룹 이름은 PUT URI에 제공되어 있습니다. 요청 본문은 위치를 정의합니다.

## <a name="example-request-body"></a>요청 본문 예제

다음 예제 본문은 “미국 서부”에 자격 증명 모음을 만드는 데 사용됩니다. 위치를 지정하세요. SKU는 항상 “표준”입니다.

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>응답

Recovery Services 자격 증명 모음을 만들거나 업데이트하는 작업에 대한 성공적인 응답에는 두 가지가 있습니다.

|이름  |형식  |설명  |
|---------|---------|---------|
|200 정상     |   [자격 증명 모음](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | 확인        |
|201 생성됨     | [자격 증명 모음](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   생성일      |

REST API 응답에 대한 자세한 내용은 [응답 메시지 처리](/rest/api/azure/#process-the-response-message)를 참조하세요.

### <a name="example-response"></a>예제 응답

이전 요청 본문 예제에서 압축된 ‘201 생성됨’ 응답은 *id*가 할당되었으며 *provisioningState*가 ‘성공’임을 보여 줍니다.

```json
{
  "location": "westus",
  "name": "testVault",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "id": "/subscriptions/77777777-b0c6-47a2-b37c-d8e65a629c18/resourceGroups/Default-RecoveryServices-ResourceGroup/providers/Microsoft.RecoveryServices/vaults/testVault",
  "type": "Microsoft.RecoveryServices/vaults",
  "sku": {
    "name": "Standard"
  }
}
```

## <a name="next-steps"></a>다음 단계

[이 자격 증명 모음의 Azure VM을 백업하기 위한 백업 정책을 만듭니다](backup-azure-arm-userestapi-createorupdatepolicy.md).

Azure REST API에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Recovery Services 공급자 REST API](/rest/api/recoveryservices/)
- [Azure REST API 시작하기](/rest/api/azure/)
