---
title: 역할 할당 만들기 및 관리-Azure Digital Twins | Microsoft Docs
description: Azure Digital Twins 내에서 역할 할당을 만들고 관리 하는 방법에 대해 알아봅니다.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 1c83ca0abfd17db873bec62f0a0d052703862a45
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110413"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Azure Digital Twins에서 역할 할당 만들기 및 관리

Azure Digital Twins는 [RBAC](./security-role-based-access-control.md)(역할 기반 액세스 제어)를 사용하여 리소스 액세스를 관리합니다.

## <a name="role-assignments-overview"></a>역할 할당 개요

각 역할 할당은 다음 정의를 준수합니다.

```JSON
{
  "roleId": "00e00ad7-00d4-4007-853b-b9968ad000d1",
  "objectId": "be2c6daa-a3a0-0c0a-b0da-c000000fbc5f",
  "objectIdType": "ServicePrincipalId",
  "path": "/",
  "tenantId": "00f000bf-86f1-00aa-91ab-2d7cd000db47"
}
```

아래 표에서는 각 특성을 설명합니다.

| attribute | 속성 | 필수 | Type | Description |
| --- | --- | --- | --- | --- |
| roleId | 역할 정의 식별자 | yes | String | 원하는 역할 할당의 고유 ID입니다. 시스템 API를 쿼리하거나 아래 표를 검토하여 역할 정의와 해당 식별자를 찾습니다. |
| objectId | 개체 식별자 | yes | String | Azure Active Directory ID, 서비스 주체 개체 ID 또는 도메인 이름입니다. 새로운 역할이 할당되는 대상 또는 사람입니다. 연결된 형식에 따라 역할 할당의 형식을 지정해야 합니다. `DomainName` objectIdType의 경우 objectId가 `“@”` 문자로 시작해야 합니다. |
| objectIdType | 개체 식별자 유형 | yes | String | 사용되는 개체 식별자의 종류입니다. 아래의 **지원되는 ObjectIdTypes**를 참조하세요. |
| 경로 | 공간 경로 | yes | String | `Space` 개체의 전체 액세스 경로입니다. 예제는 `/{Guid}/{Guid}`입니다. 식별자에 전체 그래프에 대한 역할 할당이 필요한 경우 `"/"`를 지정합니다. 이 문자는 루트를 지정하지만 사용하지 않는 것이 좋습니다. 항상 최소 권한 원칙을 준수합니다. |
| tenantId | 테넌트 식별자 | 다양함 | String | 대부분의 경우 Azure Active Directory 테넌트 ID입니다. `DeviceId` 및 `TenantId` ObjectIdTypes에 허용되지 않습니다. `UserId` 및 `ServicePrincipalId` ObjectIdTypes에는 필수입니다. DomainName ObjectIdType에는 선택 사항입니다. |

### <a name="supported-role-definition-identifiers"></a>지원되는 역할 정의 식별자

각 역할 할당은 Azure Digital Twins 환경에서 엔터티에 역할 정의를 연결합니다.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>지원되는 개체 식별자 유형

이전에는 **objectIdType** 특성이 도입되었습니다.

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>역할 할당 작업

Azure Digital Twins는 역할 할당에 대해 모든 *CREATE*, *READ*, 및 *DELETE* 작업을 지원합니다. *UPDATE* 작업은 역할 할당을 추가하거나, 역할 할당을 제거하거나, 역할 할당이 액세스 권한을 부여하는 [공간 인텔리전스 그래프](./concepts-objectmodel-spatialgraph.md) 노드를 수정하여 처리됩니다.

[역할 할당 끝점 ![](media/security-roles/role-assignments.png)](media/security-roles/role-assignments.png#lightbox)

제공된 Swagger 참조 설명서에는 사용 가능한 모든 API 엔드포인트, 요청 작업 및 정의에 대한 추가 정보도 포함되어 있습니다.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="grant-permissions-to-your-service-principal"></a>서비스 주체에 권한 부여

서비스 주체에 대한 권한 부여는 종종 Azure Digital Twins를 사용할 때 수행하는 첫 번째 단계 중 하나입니다. 이 과정에서 다음이 수행됩니다.

1. [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 또는 [PowerShell](https://docs.microsoft.com/powershell/azure/)을 통해 Azure 인스턴스에 로그인 합니다.
1. 서비스 주체 정보 획득
1. 서비스 주체에 원하는 역할 할당

애플리케이션 ID가 Azure Active Directory에서 사용자에게 제공됩니다. Active Directory의 Azure Digital Twins 구성 및 프로비전에 대한 자세한 내용은 [빠른 시작](./quickstart-view-occupancy-dotnet.md)을 읽어보세요.

응용 프로그램 ID가 있으면 다음 명령 중 하나를 실행 합니다. Azure CLI:

```azurecli
az login
az ad sp show --id <ApplicationId>
```

Powershell에서:

```powershell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId <ApplicationId>
```

**관리자** 역할이 있는 사용자는 URL에 대해 인증된 HTTP POST 요청을 수행하여 사용자에게 공간 관리자 역할을 할당할 수 있습니다.

```URL
YOUR_MANAGEMENT_API_URL/roleassignments
```

다음 JSON 본문을 사용합니다.

```JSON
{
  "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
  "objectId": "YOUR_SERVICE_PRINCIPLE_OBJECT_ID",
  "objectIdType": "ServicePrincipalId",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

### <a name="retrieve-all-roles"></a>모든 역할 검색

[![시스템 역할](media/security-roles/system-api.png)](media/security-roles/system-api.png#lightbox)

사용 가능한 모든 역할(역할 정의)을 나열하려면 다음에 대해 인증된 HTTP GET 요청을 수행합니다.

```URL
YOUR_MANAGEMENT_API_URL/system/roles
```

요청이 성공하면 할당될 수 있는 각 역할에 대한 항목을 포함하는 JSON 배열이 반환됩니다.

```JSON
[
    {
        "id": "3cdfde07-bc16-40d9-bed3-66d49a8f52ae",
        "name": "DeviceAdministrator",
        "permissions": [
            {
                "notActions": [],
                "actions": [
                    "Read",
                    "Create",
                    "Update",
                    "Delete"
                ],
                "condition": "@Resource.Type Any_of {'Device', 'DeviceBlobMetadata', 'DeviceExtendedProperty', 'Sensor', 'SensorBlobMetadata', 'SensorExtendedProperty'} || ( @Resource.Type == 'ExtendedType' && (!Exists @Resource.Category || @Resource.Category Any_of { 'DeviceSubtype', 'DeviceType', 'DeviceBlobType', 'DeviceBlobSubtype', 'SensorBlobSubtype', 'SensorBlobType', 'SensorDataSubtype', 'SensorDataType', 'SensorDataUnitType', 'SensorPortType', 'SensorType' } ) )"
            },
            {
                "notActions": [],
                "actions": [
                    "Read"
                ],
                "condition": "@Resource.Type == 'Space' && @Resource.Category == 'WithoutSpecifiedRbacResourceTypes' || @Resource.Type Any_of {'ExtendedPropertyKey', 'SpaceExtendedProperty', 'SpaceBlobMetadata', 'SpaceResource', 'Matcher'}"
            }
        ],
        "accessControlPath": "/system",
        "friendlyPath": "/system",
        "accessControlType": "System"
    }
]
```

### <a name="check-a-specific-role-assignment"></a>특정 역할 할당 확인

특정 역할 할당을 확인하려면 다음에 대해 인증된 HTTP GET 요청을 수행합니다.

```URL
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **매개 변수 값** | **필수** |  **형식** |  **설명** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  True | String |   UserId objectIdType의 objectId입니다. |
| YOUR_PATH | True | String |   액세스를 확인할 선택한 경로입니다. |
| YOUR_ACCESS_TYPE |  True | String |   *읽기*, *만들기*, *업데이트*또는 *삭제* |
| YOUR_RESOURCE_TYPE | True | String |  *장치*, *deviceblobmetadata*, *deviceblobmetadata*, *extendedpropertykey*, *extendedtype*, *Endpoint*, *키 저장소*, *선택기*, *Ontology*, *Report*, *roledefinition*, *센서*, *SensorExtendedProperty*, *Space*, *SpaceBlobMetadata*, *SpaceExtendedProperty*, *SpaceResource*, *SpaceRoleAssignment*, *System*,  *UerDefinedFunction*, *User*, *Userblobmetadata*또는 *userblobmetadata* |

요청이 성공하면 지정된 경로 및 리소스의 사용자에게 액세스 형식이 할당되었는지 여부를 나타내기 위해 부울 `true` 또는 `false`가 반환됩니다.

### <a name="get-role-assignments-by-path"></a>경로별 역할 할당 가져오기

경로의 모든 역할 할당을 가져오려면 다음에 대해 인증된 HTTP GET 요청을 수행합니다.

```URL
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| 값 | 다음 항목으로 교체 |
| --- | --- |
| YOUR_PATH | 공간의 전체 경로 |

요청이 성공하면 선택한 **path** 매개 변수와 연결된 각 역할 할당을 포함하는 JSON 배열이 반환됩니다.

```JSON
[
    {
        "id": "0000c484-698e-46fd-a3fd-c12aa11e53a1",
        "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
        "objectId": "0de38846-1aa5-000c-a46d-ea3d8ca8ee5e",
        "objectIdType": "UserId",
        "path": "/"
    }
]
```

### <a name="revoke-a-permission"></a>권한 해지

받는 사람 으로부터 사용 권한을 취소 하려면 인증 된 HTTP DELETE 요청을 만들어 역할 할당을 삭제 합니다.

```URL
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| 매개 변수 | 다음 항목으로 교체 |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | 제거할 역할 할당의 **id** |

DELETE 요청이 성공하면 204 응답 상태가 반환됩니다. 역할 할당이 아직 유지되는지 여부를 [확인](#check-a-specific-role-assignment)하여 역할 할당 제거를 검토합니다.

### <a name="create-a-role-assignment"></a>역할 할당 만들기

역할 할당을 만들려면 다음과 같이 URL에 대해 인증된 HTTP POST 요청을 수행합니다.

```URL
YOUR_MANAGEMENT_API_URL/roleassignments
```

JSON 본문이 다음 스키마를 준수하는지 확인합니다.

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

요청이 성공하면 새로 만든 역할 할당의 **id**와 함께 201 응답 상태가 반환됩니다.

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>구성 예제

다음 예제에서는 일반적으로 발생하는 몇 가지 역할 할당 시나리오에 JSON 본문을 구성하는 방법을 보여 줍니다.

* **예**: 사용자에 게는 테 넌 트 공간에 대 한 관리자 액세스 권한이 필요 합니다.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **예**: 응용 프로그램에서 테스트 시나리오 모의 장치 및 센서를 실행 합니다.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **예**: 도메인의 일부인 모든 사용자는 공백, 센서 및 사용자에 대 한 읽기 액세스 권한을 받습니다. 이 액세스는 해당 관련 개체를 포함합니다.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>다음 단계

- Azure Digital Twins 역할 기반 액세스 제어를 검토하려면 [Role-base-access-control](./security-authenticating-apis.md)을 읽어보세요.

- Azure Digital Twins API 인증에 대한 자세한 내용은 [API 인증](./security-authenticating-apis.md)을 읽어보세요.
