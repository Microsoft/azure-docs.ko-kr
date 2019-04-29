---
title: 원격 모니터링 액세스 제어 - Azure | Microsoft Docs
description: 이 문서에서는 원격 모니터링 솔루션 가속기에서 RBAC(역할 기반 액세스 제어)를 구성하는 방법에 대한 정보를 제공합니다.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 9accb41cdb4d780bf137d6872cca022226f902e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61443133"
---
# <a name="configure-role-based-access-controls-in-the-remote-monitoring-solution-accelerator"></a>원격 모니터링 솔루션 가속기에서 역할 기반 액세스 제어 구성

이 문서에서는 원격 모니터링 솔루션 가속기에서 역할 기반 액세스 제어를 구성하는 방법에 대한 정보를 제공합니다. 역할 기반 액세스 제어를 사용하면 개별 사용자의 액세스를 솔루션의 특정 기능으로 제한할 수 있습니다.

## <a name="default-settings"></a>기본 설정

원격 모니터링 솔루션을 처음 배포할 때에 두 가지 역할이 있습니다. **Admin** 하 고 **읽기 전용**합니다.

**관리자** 역할의 모든 사용자는 아래의 다음 권한을 포함하여 솔루션에 대한 전체 액세스 권한이 있습니다. **읽기 전용** 역할의 사용자는 솔루션을 보는 액세스만을 갖습니다.

| 사용 권한            | 관리자 | 읽기 전용 |
|----------------       |-------|-----------|
| 솔루션 보기         | 예   | 예       |
| 알람 업데이트         | 예   | 아닙니다.        |
| 알람 삭제         | 예   | 아닙니다.        |
| 디바이스 만들기        | 예   | 아닙니다.        |
| 디바이스 업데이트        | 예   | 아닙니다.        |
| 디바이스 삭제        | 예   | 아닙니다.        |
| 디바이스 그룹 만들기  | 예   | 아닙니다.        |
| 디바이스 그룹 업데이트  | 예   | 아닙니다.        |
| 디바이스 그룹 삭제  | 예   | 아닙니다.        |
| 규칙 만들기          | 예   | 아닙니다.        |
| 규칙 업데이트          | 예   | 아닙니다.        |
| 규칙 삭제          | 예   | 아닙니다.        |
| 작업 만들기           | 예   | 아닙니다.        |
| SIM 관리 업데이트 | 예   | 아닙니다.        |

기본적으로 솔루션을 배포한 사용자에게는 **관리자** 역할이 자동으로 할당되고 Azure Active Directory 애플리케이션 소유자입니다. 애플리케이션 소유자로서 Azure Portal을 통해 다른 사용자에게 역할을 할당할 수 있습니다. 다른 사용자가 솔루션에서 역할을 할당하게 하려면, Azure Portal에서 애플리케이션 소유자로 설정해야 합니다.

> [!NOTE]
> 솔루션을 배포한 사용자는 만들어진 직후 볼 수 있는 **유일한 사용자**입니다. 읽기 전용, 관리자 또는 사용자 지정 역할 중 하나로 애플리케이션을 보는 다른 액세스 권한을 부여하려면 사용자 추가 또는 제거에 대한 아래의 다음 지침을 참조합니다.

## <a name="add-or-remove-users"></a>사용자 추가 또는 제거

Azure Active Directory 애플리케이션 소유자로서 Azure Portal을 사용하여 원격 모니터링 솔루션에서 역할에 사용자를 추가하거나 제거할 수 있습니다. 다음 단계에서는 원격 모니터링 솔루션을 배포할 때 만들어진 [Azure Active Directory 엔터프라이즈 애플리케이션](../active-directory/manage-apps/add-application-portal.md#find-your-azure-ad-tenant-application)을 사용합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 사용 중인[디렉터리에 사용자가 있는지](../active-directory/fundamentals/add-users-azure-active-directory.md) 확인합니다. 사용할 디렉터리는 [Microsoft Azure IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators) 사이트에 로그인할 때 선택합니다. 디렉터리 이름은 해당 [페이지](https://www.azureiotsolutions.com/Accelerators)의 오른쪽 위 모서리에서 볼 수 있습니다.

1. Azure Portal에서 솔루션에 대한 **엔터프라이즈 애플리케이션**을 찾습니다. 찾으면 **애플리케이션 유형**을 **모든 애플리케이션**으로 설정하여 목록을 필터링합니다. 애플리케이션 이름으로 애플리케이션을 검색합니다. 애플리케이션 이름은 원격 모니터링 솔루션의 이름입니다. 다음 스크린샷에서 솔루션과 애플리케이션 표시 이름은 **contoso-rm4**입니다.

    ![엔터프라이즈 애플리케이션](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. 애플리케이션을 클릭한 다음, **소유자**를 클릭하여 내가 애플리케이션의 소유자인지 확인합니다. 다음 스크린샷에서 **Contoso admin**은 **contoso-rm4** 애플리케이션의 소유자입니다.

    ![소유자](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    소유자가 아닌 경우에는 기존 소유자에게 나를 목록에 추가하도록 요청해야 합니다. 소유자만 **관리자**나 **읽기 전용**과 같은 애플리케이션 역할을 다른 사용자에게 할당할 수 있습니다.

1. 애플리케이션에서 역할에 할당된 사용자 목록을 보려면 **사용자 및 그룹**을 클릭합니다.

1. 사용자를 추가하려면 **+ 사용자 추가**를 클릭한 다음, **사용자 및 그룹, 선택된 항목 없음**을 클릭하여 디렉터리에서 사용자를 선택합니다.

1. 사용자를 역할에 할당하려면 **역할 선택, 선택된 항목 없음**을 클릭하고 사용자에 대해 **관리자**나 **읽기 전용** 역할 중 하나를 선택합니다. **선택**을 클릭한 다음, **할당**을 클릭합니다.

    ![역할 선택](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. 이제 사용자는 역할에 의해 정의된 권한으로 원격 모니터링 솔루션에 액세스할 수 있습니다.

1. 포털의 **사용자 및 그룹** 페이지에서는 애플리케이션에서 사용자를 삭제할 수 있습니다.

## <a name="create-a-custom-role"></a>사용자 지정 역할 만들기

원격 모니터링 솔루션은 처음 배포될 때 **관리자**와 **읽기 전용** 역할을 포함합니다. 다른 권한 집합으로 사용자 지정 역할을 추가할 수 있습니다. 사용자 지정 역할을 정의하려면 다음을 수행해야 합니다.

- Azure Portal에서 애플리케이션에 새 역할을 추가합니다.
- 인증 및 권한 부여 마이크로 서비스에서 새 역할에 대한 정책을 정의합니다.
- 솔루션의 웹 UI를 업데이트합니다.

### <a name="define-a-custom-role-in-the-azure-portal"></a>Azure Portal에서 사용자 지정 역할 정의

다음 단계에서는 Azure Active Directory에서 애플리케이션에 역할을 추가하는 방법을 설명합니다. 이 예제에서는 원격 모니터링 솔루션에서 멤버가 디바이스를 만들고 업데이트하고 삭제할 수 있는 새 역할을 만듭니다.

1. Azure Portal에서 솔루션에 대한 **앱 등록**을 찾습니다. 애플리케이션 이름은 원격 모니터링 솔루션의 이름입니다. 다음 스크린샷에서 솔루션과 애플리케이션 표시 이름은 **contoso-rm4**입니다.

    ![앱 등록](media/iot-accelerators-remote-monitoring-rbac/appregistration2.png)

1. 애플리케이션을 선택한 다음, **매니페스트**를 클릭합니다. 애플리케이션에 대해 정의된 기존 [앱 역할](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) 두 개가 표시됩니다.

    ![매니페스트 보기](media/iot-accelerators-remote-monitoring-rbac/viewmanifest.png)

1. 다음 코드 조각처럼 매니페스트를 편집하여 **ManageDevices**라는 역할을 추가합니다. 새 역할 ID에 대한 GUID와 같은 고유 문자열이 필요합니다. 새 GUID는 [Online GUID Generator](https://www.guidgenerator.com/)와 같은 서비스를 사용하여 생성할 수 있습니다.

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Admin",
        "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "isEnabled": true,
        "description": "Administrator access to the application",
        "value": "Admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Read Only",
        "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "isEnabled": true,
        "description": "Read only access to device information",
        "value": "ReadOnly"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "ManageDevices",
        "id": "ADD A NEW GUID HERE",
        "isEnabled": true,
        "description": "A new role for the application.",
        "value": "ManageDevices"
      }
    ],
    ```

    변경 내용을 저장합니다.

### <a name="define-a-policy-for-the-new-role"></a>새 역할에 대한 정책 정의

Azure Portal에서 앱에 역할을 추가한 후에는, 디바이스 관리에 필요한 권한을 할당하는 역할에 대해 [roles.json](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/auth/Services/data/policies/roles.json)에 정책을 정의해야 합니다.

1. GitHub의 [원격 모니터링 마이크로 서비스](https://github.com/Azure/remote-monitoring-services-dotnet) 리포지토리를 로컬 머신으로 복제합니다.

1. 다음 코드 조각처럼 **auth/Services/data/policies/roles.json** 파일을 편집하여 **ManageDevices** 역할에 대한 정책을 추가합니다. **ID**와 **역할** 값은 이전 섹션의 앱 매니페스트에 있는 역할 정의와 일치해야 합니다. 허용된 작업 목록을 통해 **ManageDevices** 역할에 해당하는 사람이 솔루션에 연결된 디바이스를 만들고, 업데이트하고 삭제할 수 있습니다.

    ```json
    {
    "Items": [
      {
        "Id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "Role": "admin",
        "AllowedActions": [
          "UpdateAlarms",
          "DeleteAlarms",
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices",
          "CreateDeviceGroups",
          "UpdateDeviceGroups",
          "DeleteDeviceGroups",
          "CreateRules",
          "UpdateRules",
          "DeleteRules",
          "CreateJobs",
          "UpdateSimManagement"
        ]
      },
      {
        "Id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "Role": "readOnly",
        "AllowedActions": []
      },
      {
        "Id": "GUID FROM APP MANIFEST",
        "Role": "ManageDevices",
        "AllowedActions": [
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices"
        ]
      }
    ]
    }
    ```

1. **Services/data/policies/roles.json** 파일 편집을 마치면 인증 및 권한 부여 마이크로 서비스를 다시 작성하여 솔루션 가속기에 다시 배포합니다.

### <a name="how-the-web-ui-enforces-permissions"></a>웹 UI가 권한을 적용하는 방법

웹 UI는 [인증 및 권한 부여 마이크로 서비스](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth)를 사용하여 사용자가 수행하도록 허용된 작업이 무엇인지, UI에 어떤 컨트롤이 표시되는지를 확인합니다. 예를 들어 솔루션 이름이 **contoso-rm4**인 경우 웹 UI는 다음 요청을 보내서 현재 사용자에게 허용된 작업 목록을 검색합니다.

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

**ManageDevices** 역할의 **디바이스 관리자**라는 사용자는 응답의 본문에 다음 JSON이 포함됩니다.

```json
{
  "Id": "userIdExample",
  "Email": "devicemanager@contoso.com",
  "Name": "Device Manager",
  "AllowedActions": [
    "CreateDevices",
    "UpdateDevices",
    "DeleteDevices"
  ]
}
```

[웹 UI](https://github.com/Azure/pcs-remote-monitoring-webui/)의 [deviceDelete.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js)에서 가져온 다음, 코드 조각은 권한을 선언적으로 적용하는 방법을 보여 줍니다.

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

자세한 내용은 [보호된 구성 요소](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md)를 참조하세요. [authModel.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js) 파일에서 추가 권한을 정의할 수 있습니다.

### <a name="how-the-microservices-enforce-permissions"></a>마이크로 서비스가 권한을 적용하는 방법

마이크로 서비스는 인증되지 않은 API 요청으로부터 보호하기 위해 권한을 확인합니다. 마이크로 서비스가 API 요청을 받으면 JWT 토큰을 디코딩하여 유효성을 검사하고 사용자의 역할과 관련된 사용자 ID 및 권한을 가져옵니다.

[IoTHub 관리자 마이크로 서비스](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager)의 [DevicesController.cs](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/iothub-manager/WebService/v1/Controllers/DevicesController.cs) 파일의 다음 코드 조각은 권한이 적용되는 방법을 보여 줍니다.

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 원격 모니터링 솔루션 가속기에서 역할 기반 액세스 제어를 구현하는 방법을 알아보았습니다.

원격 모니터링 솔루션 액셀러레이터에서 Time Series Insights 탐색기에 대한 액세스 권한을 관리하는 방법에 대한 자세한 내용은 [Time Series Insights Explorer에 대한 액세스 제어 구성](iot-accelerators-remote-monitoring-rbac-tsi.md)을 참조하세요.

원격 모니터링 솔루션 가속기에 대한 자세한 개념 정보는 [원격 모니터링 아키텍처](iot-accelerators-remote-monitoring-sample-walkthrough.md)를 참조하세요.

원격 모니터링 솔루션 사용자 지정에 대한 자세한 내용은 [마이크로 서비스 사용자 지정 및 재배포](iot-accelerators-microservices-example.md)를 참조하세요.
<!-- Next tutorials in the sequence -->