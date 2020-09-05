---
title: REST API를 사용 하 여 Recovery Services 자격 증명 모음 구성 업데이트
description: 이 문서에서는 REST API를 사용 하 여 자격 증명 모음 구성을 업데이트 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 12/06/2019
ms.assetid: 9aafa5a0-1e57-4644-bf79-97124db27aa2
ms.openlocfilehash: 8890cb541e38f8bc8b680fbcfeb821f29723e8c0
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89007114"
---
# <a name="update-azure-recovery-services-vault-configurations-using-rest-api"></a>REST API를 사용 하 여 Azure Recovery Services 자격 증명 모음 구성 업데이트

이 문서에서는 REST API를 사용 하 여 Azure Recovery Services 자격 증명 모음에서 백업 관련 구성을 업데이트 하는 방법을 설명 합니다.

## <a name="soft-delete-state"></a>일시 삭제 상태

보호 된 항목의 백업 삭제는 모니터링 해야 하는 중요 한 작업입니다. 실수로 인 한 삭제를 방지 하기 위해 Azure Recovery Services 자격 증명 모음에는 일시 삭제 기능이 있습니다. 이 기능을 사용 하면 삭제 후 일정 기간 내에 삭제 된 백업 (필요한 경우)을 복원할 수 있습니다.

그러나이 기능이 필요 하지 않은 시나리오는 있습니다. Azure Recovery Services 자격 증명 모음은 일시 삭제 된 경우에도 해당 항목 내에 백업 항목이 있으면 삭제할 수 없습니다. 자격 증명 모음을 즉시 삭제 해야 하는 경우 문제가 발생할 수 있습니다. 예를 들어의 경우 배포 작업은 동일한 워크플로에서 생성 된 리소스를 정리 하는 경우가 많습니다. 배포는 자격 증명 모음을 만들고, 항목에 대 한 백업을 구성 하 고, 테스트 복원을 수행한 후 백업 항목과 자격 증명 모음을 계속 해 서 삭제할 수 있습니다. 자격 증명 모음을 삭제 하지 못하면 전체 배포가 실패할 수 있습니다. 즉시 삭제를 보장 하는 유일한 방법은 일시 삭제를 사용 하지 않도록 설정 하는 것입니다.

따라서 시나리오에 따라 특정 자격 증명 모음에 대해 일시 삭제를 사용 하지 않도록 설정할지 여부를 신중 하 게 선택 해야 합니다. 자세한 내용은 [일시 삭제 문서](backup-azure-security-feature-cloud.md)를 참조 하세요.

### <a name="fetch-soft-delete-state-using-rest-api"></a>REST API를 사용 하 여 일시 삭제 상태 페치

기본적으로 새로 만든 Recovery Services 자격 증명 모음에 대해 일시 삭제 상태가 사용 됩니다. 자격 증명 모음에 대 한 일시 삭제 상태를 인출/업데이트 하려면 백업 자격 증명 모음 관련 [REST API 문서](/rest/api/backup/backupresourcevaultconfigs) 를 사용 합니다.

자격 증명 모음에 대 한 일시 삭제의 현재 상태를 인출 하려면 다음 *가져오기* 작업을 사용 합니다.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

GET URI에는 `{subscriptionId}` , `{vaultName}` `{vaultresourceGroupName}` 매개 변수가 있습니다. 이 예제에서는 `{vaultName}` "testVault"이 고 `{vaultresourceGroupName}` 는 "testVaultRG"입니다. 모든 필수 매개 변수가 URI에 제공 되므로 별도의 요청 본문이 필요 하지 않습니다.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="responses"></a>응답

' GET ' 작업에 대 한 성공적인 응답은 다음과 같습니다.

|Name  |Type  |설명  |
|---------|---------|---------|
|200 정상     |   [BackupResourceVaultConfig](/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | 정상        |

##### <a name="example-response"></a>예제 응답

' GET ' 요청이 제출 되 면 200 (성공) 응답이 반환 됩니다.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Enabled"
  }
}
```

### <a name="update-soft-delete-state-using-rest-api"></a>REST API를 사용 하 여 일시 삭제 상태 업데이트

REST API를 사용 하 여 Recovery Services 자격 증명 모음의 일시 삭제 상태를 업데이트 하려면 다음 *패치* 작업을 사용 합니다.

```http
PATCH https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupconfig/vaultconfig?api-version=2019-05-13
```

패치 URI에는 `{subscriptionId}` , `{vaultName}` , `{vaultresourceGroupName}` 매개 변수가 있습니다. 이 예제에서는 `{vaultName}` "testVault"이 고 `{vaultresourceGroupName}` 는 "testVaultRG"입니다. URI를 위의 값으로 바꾸면 URI가 다음과 같이 표시 됩니다.

```http
PATCH https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupconfig/vaultconfig?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>요청 본문 만들기

다음 일반 정의는 요청 본문을 만드는 데 사용 됩니다.

자세한 내용은 [REST API 설명서](/rest/api/backup/backupresourcevaultconfigs/update#request-body) 를 참조 하세요.

|Name  |필수  |Type  |설명  |
|---------|---------|---------|---------|
|eTag     |         |   문자열      |  선택적 eTag       |
|위치     |  true       |문자열         |   리소스 위치      |
|properties     |         | [VaultProperties](/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  자격 증명 모음의 속성       |
|tags     |         | Object        |     리소스 태그    |

#### <a name="example-request-body"></a>요청 본문 예제

다음 예제는 일시 삭제 상태를 ' 사용 안 함 '으로 업데이트 하는 데 사용 됩니다.

```json
{
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

#### <a name="responses-for-the-patch-operation"></a>패치 작업에 대 한 응답

' PATCH ' 작업에 대 한 성공적인 응답은 다음과 같습니다.

|Name  |Type  |설명  |
|---------|---------|---------|
|200 정상     |   [BackupResourceVaultConfig](/rest/api/backup/backupresourcevaultconfigs/get#backupresourcevaultconfigresource)      | 정상        |

##### <a name="example-response-for-the-patch-operation"></a>패치 작업에 대 한 예제 응답

' PATCH ' 요청이 제출 되 면 200 (성공) 응답이 반환 됩니다.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testvaultRG/providers/Microsoft.RecoveryServices/vaults/testvault/backupconfig/vaultconfig",
  "name": "vaultconfig",
  "type": "Microsoft.RecoveryServices/vaults/backupconfig",
  "properties": {
    "enhancedSecurityState": "Enabled",
    "softDeleteFeatureState": "Disabled"
  }
}
```

## <a name="next-steps"></a>다음 단계

[이 자격 증명 모음의 Azure VM을 백업하기 위한 백업 정책을 만듭니다](backup-azure-arm-userestapi-createorupdatepolicy.md).

Azure REST API에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Recovery Services 공급자 REST API](/rest/api/recoveryservices/)
- [Azure REST API 시작하기](/rest/api/azure/)
