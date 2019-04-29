---
title: 'Azure Backup: REST API를 사용 하 여 백업 정책 만들기'
description: REST API를 사용하여 백업 정책(일정 및 보존) 관리
services: backup
author: pvrk
manager: shivamg
keywords: REST API, Azure VM 백업, Azure VM 복원,
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: pullabhk
ms.assetid: 5ffc4115-0ae5-4b85-a18c-8a942f6d4870
ms.openlocfilehash: 657a777da0e984a145c1c617a6194bf4ef56306e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648808"
---
# <a name="create-azure-recovery-services-backup-policies-using-rest-api"></a>REST API를 사용하여 Azure Recovery Services 백업 정책 만들기

Azure Recovery Services 자격 증명 모음에 대한 백업 정책을 만드는 단계는 [백업 정책 REST API 문서](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate)에 간략하게 설명돼 있습니다. Azure VM 백업에 대한 정책을 만들려면 이 문서를 참조로 사용하겠습니다.

## <a name="backup-policy-essentials"></a>백업 정책 기본 정보

- 자격 증명 모음당 백업 정책을 만듭니다.
- 다음 워크로드의 백업에 대한 백업 정책을 만들 수 있음
  - Azure VM
  - Azure VM의 SQL
  - Azure 파일 공유
- 다양한 리소스에 정책을 할당할 수 있습니다. 여러 Azure VM을 보호하는 데 Azure VM 백업 정책을 사용할 수 있습니다.
- 두 구성 요소로 이루어진 정책
  - 일정: 백업을 수행 하는 경우
  - 보존: 기간에 대 한 각 백업을 보관 해야 합니다.
- 특정 시점을 사용하여 "매일" 또는 "매주"로 일정을 정의할 수 있습니다.
- "매일", "매주", "매월", "매년" 단위로 백업 지점에 대한 보존을 정의할 수 있습니다.
- "매주"는 그 주의 특정 날짜의 백업을 참조하고, "매월"은 그 달의 특정 날짜의 백업을 의미하며, "매년"은 그 해의 특정 날짜의 백업을 참조합니다.
- "매월", "매년" 백업 지점의 보존은 "LongTermRetention"이라고 합니다.
- 자격 증명 모음을 만들면 "DefaultPolicy"라는 Azure VM 백업 정책이 만들어지고 Azure VM 백업에도 사용할 수 있습니다.

Azure Backup 정책을 만들거나 업데이트하려면 다음 *PUT* 작업을 사용합니다.

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupPolicies/{policyName}?api-version=2016-12-01
```

URI에서 `{policyName}` 및 `{vaultName}`을 제공합니다. 요청 본문에 추가 정보를 제공합니다.

## <a name="create-the-request-body"></a>요청 본문 만들기

예를 들어 Azure VM 백업의 백업을 만들려면 요청 본문의 구성 요소는 다음과 같습니다.

|이름  |필수  |형식  |설명  |
|---------|---------|---------|---------|
|properties     |   True       |  ProtectionPolicy:[AzureIaaSVMProtectionPolicy](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate#azureiaasvmprotectionpolicy)      | ProtectionPolicyResource 속성        |
|tags     |         | Object        |  리소스 태그       |

요청 본문의 전체 정의 목록은 [백업 정책 REST API 문서](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate)를 참조하세요.

### <a name="example-request-body"></a>요청 본문 예제

다음 요청 본문에서는 Azure VM 백업에 대한 백업 정책을 정의합니다.

정책은 다음과 같습니다.

- 매주 백업은 매주 월요일, 수요일, 목요일 오전 10시(태평양 표준시)에 수행합니다.
- 한 주간 동안 매주 월요일, 수요일, 목요일에 수행된 백업을 보존합니다.
- 두 달 동안 매달 첫 번째 수요일 및 세 번째 목요일에 수행된 백업을 보존합니다(이전 보존 조건이 있는 경우 재정의합니다).
- 4년 동안 2월 및 11월의 네 번째 월요일 및 목요일에 수행된 백업을 보존합니다(이전 보존 조건이 있는 경우 재정의합니다).

```json
{
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "timeZone": "Pacific Standard Time",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ]
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    }
  }
}
```

> [!IMPORTANT]
> 일정 및 보존에 대한 시간 형식은 날짜/시간만 지원하고 시간 형식만 따로 지원하지 않습니다.

## <a name="responses"></a>응답

백업 정책 만들기/업데이트는 [비동기 작업](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

두 응답을 반환합니다. 다른 작업을 만드는 경우 202(수락됨) 및 해당 작업이 완료되는 경우 200(정상)

|이름  |형식  |설명  |
|---------|---------|---------|
|200 정상     |    [보호 PolicyResource](https://docs.microsoft.com/rest/api/backup/protectionpolicies/createorupdate#protectionpolicyresource)     |  확인       |
|202 수락됨     |         |     수락됨    |

### <a name="example-responses"></a>예제 응답

정책 만들기 또는 업데이트를 위한 *PUT* 요청을 제출하면 초기 응답은 위치 헤더 또는 Azure-async-header를 사용한 202(수락됨)입니다.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operations/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
X-Powered-By: ASP.NET
```

그런 다음, 간단한 *GET* 명령으로 위치 헤더 또는 Azure-AsyncOperation 헤더를 사용하여 결과 작업을 추적합니다.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1/operationResults/00000000-0000-0000-0000-000000000000?api-version=2016-06-01
```

작업이 완료되면 응답 본문에서 정책 콘텐츠를 사용하여 200(정상)을 반환합니다.

```json
{
  "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SwaggerTestRg/providers/Microsoft.RecoveryServices/vaults/testVault/backupPolicies/testPolicy1",
  "name": "testPolicy1",
  "type": "Microsoft.RecoveryServices/vaults/backupPolicies",
  "properties": {
    "backupManagementType": "AzureIaasVM",
    "schedulePolicy": {
      "schedulePolicyType": "SimpleSchedulePolicy",
      "scheduleRunFrequency": "Weekly",
      "scheduleRunDays": [
        "Monday",
        "Wednesday",
        "Thursday"
      ],
      "scheduleRunTimes": [
        "2018-01-24T10:00:00Z"
      ],
      "scheduleWeeklyFrequency": 0
    },
    "retentionPolicy": {
      "retentionPolicyType": "LongTermRetentionPolicy",
      "weeklySchedule": {
        "daysOfTheWeek": [
          "Monday",
          "Wednesday",
          "Thursday"
        ],
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 1,
          "durationType": "Weeks"
        }
      },
      "monthlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Wednesday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "First",
            "Third"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 2,
          "durationType": "Months"
        }
      },
      "yearlySchedule": {
        "retentionScheduleFormatType": "Weekly",
        "monthsOfYear": [
          "February",
          "November"
        ],
        "retentionScheduleWeekly": {
          "daysOfTheWeek": [
            "Monday",
            "Thursday"
          ],
          "weeksOfTheMonth": [
            "Fourth"
          ]
        },
        "retentionTimes": [
          "2018-01-24T10:00:00Z"
        ],
        "retentionDuration": {
          "count": 4,
          "durationType": "Years"
        }
      }
    },
    "timeZone": "Pacific Standard Time",
    "protectedItemsCount": 0
  }
}
```

항목을 보호하는 데 이미 정책을 사용하는 경우 정책의 모든 업데이트는 결국 이러한 모든 연결 항목에 대한 [보호를 수정](backup-azure-arm-userestapi-backupazurevms.md#changing-the-policy-of-protection)하게 됩니다.

## <a name="next-steps"></a>다음 단계

[보호되지 않는 Azure VM에 대한 보호를 사용하도록 설정합니다](backup-azure-arm-userestapi-backupazurevms.md).

Azure Backup REST API에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Recovery Services 공급자 REST API](/rest/api/recoveryservices/)
- [Azure REST API 시작하기](/rest/api/azure/)