---
title: API Management에서 백업 및 복원을 사용 하 여 재해 복구 구현
titleSuffix: Azure API Management
description: Azure API Management에서 백업 및 복원을 사용하여 재해 복구를 수행하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/03/2020
ms.author: apimpm
ms.openlocfilehash: 8f748764d0f61e4932b2d4710f5a6805a5eddf0e
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77047479"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Azure API Management에서 서비스 백업 및 복원을 사용하여 재해 복구를 구현하는 방법

Azure API Management를 통해 API를 게시 및 관리하면 기존에는 수동으로 설계, 구현 및 관리하던 내결함성 및 인프라 기능을 활용할 수 있습니다. Azure 플랫폼은 매우 적은 비용으로 상당한 잠재적 오류를 완화합니다.

API Management 서비스를 호스트하는 지역에 영향을 주는 가용성 문제로부터 서비스를 복구하려면 언제든지 다른 지역에서 서비스를 다시 구축할 준비를 하세요. 복구 시간 목표에 따라 하나 이상의 지역에 대기 서비스를 유지할 수 있습니다. 또한 복구 지점 목표에 따라 해당 구성과 콘텐츠를 활성 서비스와 동기화 상태로 유지 하려고 할 수도 있습니다. 서비스 백업 및 복원 기능은 재해 복구 전략을 구현 하는 데 필요한 구성 요소를 제공 합니다.

운영 환경 (예: 개발 및 스테이징) 간에 API Management 서비스 구성을 복제 하는 데 백업 및 복원 작업을 사용할 수도 있습니다. 사용자 및 구독과 같은 런타임 데이터도 복사 됩니다 .이는 항상 바람직하지 않을 수 있습니다.

이 가이드에서는 백업 및 복원 작업을 자동화 하는 방법 및 Azure Resource Manager 여 백업 및 복원 요청을 성공적으로 인증 하는 방법을 보여 줍니다.

> [!IMPORTANT]
> 복원 작업은 대상 서비스의 사용자 지정 호스트 이름 구성을 변경 하지 않습니다. 활성 및 대기 서비스 모두에 대해 동일한 사용자 지정 호스트 이름 및 TLS 인증서를 사용 하는 것이 좋습니다. 따라서 복원 작업이 완료 된 후에는 간단한 DNS CNAME 변경으로 트래픽을 대기 인스턴스로 다시 지시할 수 있습니다.
>
> 백업 작업은 Azure Portal의 분석 블레이드에 표시 된 보고서에 사용 되는 미리 집계 된 로그 데이터를 캡처하지 않습니다.

> [!WARNING]
> 각 백업은 30일 후에 만료됩니다. 30일의 만료 기간이 만료된 후에 백업을 복원하려고 하면 `Cannot restore: backup expired` 메시지와 함께 복원이 실패합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Azure 리소스 관리자 요청 인증

> [!IMPORTANT]
> 백업 및 복원을 위한 REST API는 Azure Resource Manager를 사용하며 API Management 엔터티를 관리하기 위한 REST API와는 다른 인증 메커니즘입니다. 이 섹션의 단계에는 Azure 리소스 관리자 요청을 인증하는 방법을 설명합니다. 자세한 내용은 [Azure 리소스 관리자 요청 인증](/rest/api/index)을 참조하세요.

Azure Resource Manager를 사용하여 리소스에서 수행하는 모든 작업은 다음 단계를 사용하여 Azure Active Directory에서 인증되어야 합니다.

-   애플리케이션을 Azure Active Directory 테넌트에 추가합니다.
-   추가한 애플리케이션에 대한 권한을 설정합니다.
-   Azure 리소스 관리자에 대한 요청을 인증하기 위한 토큰을 가져옵니다.

### <a name="create-an-azure-active-directory-application"></a>Azure Active Directory 애플리케이션 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. API Management 서비스 인스턴스를 포함하는 구독을 사용하여 **Azure Active Directory**의 **앱 등록** 탭으로 이동합니다(Azure Active Directory > [관리/앱 등록]).

    > [!NOTE]
    > Azure Active Directory 기본 디렉터리에 사용자의 계정이 표시되지 않는 경우, 계정에 필요한 권한을 부여하려면 Azure 구독의 관리자에게 문의하세요.

3. **새 애플리케이션 등록**을 클릭합니다.

    **만들기** 창이 오른쪽에 나타납니다. 여기에 AAD 앱 관련 정보를 입력합니다.

4. 애플리케이션의 이름을 입력합니다.
5. 애플리케이션 형식에서 **네이티브**를 선택합니다.
6. `http://resources`리디렉션 URI**로** 와 같은 자리 표시자 URL을 입력하고, 필수 필드지만 값은 나중에 사용되지 않습니다. 애플리케이션을 저장하려면 이 확인란을 클릭합니다.
7. **만들기**를 클릭합니다.

### <a name="add-an-application"></a>애플리케이션 추가

1. 응용 프로그램을 만든 후 **API 사용 권한**을 클릭 합니다.
2. **+ 권한 추가를**클릭 합니다.
4. **Microsoft Api 선택**을 누릅니다.
5. **Azure 서비스 관리**를 선택 합니다.
6. **선택**을 누릅니다.

    ![권한 추가](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. 새로 추가된 애플리케이션 옆에 있는 **위임된 권한**을 클릭하고, **Azure 서비스 관리 액세스(미리 보기)** 에서 상자를 선택합니다.
8. **선택**을 누릅니다.
9. **권한 부여**를 클릭합니다.

### <a name="configuring-your-app"></a>앱 구성

백업을 생성하고 복원하는 API를 호출하기 전에 토큰을 가져와야 합니다. 다음 예제는 [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet 패키지를 사용하여 토큰을 검색합니다.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

다음 지침을 사용하여 `{tenant id}`, `{application id}` 및 `{redirect uri}`을 바꿉니다.

1. `{tenant id}`를 사용자가 만든 Azure Active Directory 애플리케이션의 테넌트 ID로 바꿉니다. **앱 등록** -> **엔드포인트**를 클릭하여 ID에 액세스할 수 있습니다.

    ![엔드포인트][api-management-endpoint]

2. `{application id}`설정**페이지로 이동하여**를 가져온 값으로 바꿉니다.
3. `{redirect uri}`를 Azure Active Directory 애플리케이션의 **리디렉션 URI** 탭에 있는 값으로 바꿉니다.

    값이 지정되면 코드 예제에서는 다음 예제와 유사한 토큰을 반환해야 합니다.

    ![토큰][api-management-arm-token]

    > [!NOTE]
    > 토큰은 특정 기간 후에 만료될 수 있습니다. 코드 샘플을 다시 실행하여 새 토큰을 생성합니다.

## <a name="calling-the-backup-and-restore-operations"></a>백업 및 복원 작업 호출

REST API는 [API Management 서비스 - 백업](/rest/api/apimanagement/2019-01-01/apimanagementservice/backup) 및 [API Management 서비스 - 복원](/rest/api/apimanagement/2019-01-01/apimanagementservice/restore)입니다.

다음 섹션에서 설명한 "백업 및 복원 작업"을 호출하기 전에 REST 호출에 대한 권한 부여 요청 헤더를 설정합니다.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"> </a>API Management 서비스 백업

API Management 서비스를 백업하려면 다음 HTTP 요청을 실행합니다.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

각 항목이 나타내는 의미는 다음과 같습니다.

-   `subscriptionId` - 백업하려는 API Management 서비스를 포함하는 구독의 ID입니다.
-   `resourceGroupName` - Azure API Management 서비스의 리소스 그룹 이름입니다.
-   `serviceName` - 백업을 만드는 API Management 서비스를 만들 때 지정하는 이름입니다.
-   `api-version` `2018-06-01-preview`으로 바꾸기

요청 본문에서 대상 Azure Storage 계정 이름, 액세스 키, Blob 컨테이너 이름 및 백업 이름을 지정합니다.

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

`Content-Type` 요청 헤더의 값을 `application/json`으로 설정합니다.

백업은 완료하는 데 1분 이상 걸릴 수 있는 장기 실행 작업입니다. 요청이 성공하고 백업 프로세스가 시작된 경우 `202 Accepted` 헤더가 포함된 `Location` 응답 상태 코드를 받게 됩니다. `Location` 헤더에서 URL에 대한 'GET' 요청을 수행하면 작업 상태를 확인할 수 있습니다. 백업이 진행 중인 동안에는 '202 수락됨' 상태 코드가 계속 수신됩니다. 응답 코드가 `200 OK` 이면 백업 작업이 정상적으로 완료된 것입니다.

백업 또는 복원 요청을 만들 때 다음 제약 조건을 확인 합니다.

-   요청 본문에 지정된 **Container**가 **있어야 합니다**.
-   백업이 진행 중인 동안에는 SKU 업그레이드 또는 다운 그레이드, 도메인 이름 변경 등의 **서비스에서 관리를 변경 하지 않습니다** .
-   백업 복원은 생성 시점부터 **30일 동안만 보장**됩니다.
-   분석 보고서를 만드는 데 사용되는 **사용량 현황 데이터**는 백업에 **포함되지 않습니다**. [Azure API Management REST API][azure api management rest api] 를 사용하여 분석 보고서를 주기적으로 검색한 다음 안전하게 보관하세요.
-   또한 다음 항목은 백업 데이터에 포함 되지 않습니다. 사용자 지정 도메인 SSL 인증서 및 고객, 개발자 포털 콘텐츠 및 가상 네트워크 통합 설정에서 업로드 하는 중간 또는 루트 인증서입니다.
-   서비스 백업을 수행하는 빈도는 복구 지점 목표에 영향을 줍니다. 영향을 최소화하려면 정기 백업을 구현함과 동시에 API Management 서비스에 대한 변경을 수행한 후 요청 시 백업도 수행하는 것이 좋습니다.
-   백업 작업이 진행되는 동안 API, 정책 및 개발자 포털 모양 등의 서비스 구성을 **변경**하는 경우 **해당 내용이 백업에서 제외되고 손실될 수 있습니다**.
-   [방화벽이][azure-storage-ip-firewall] 사용 하도록 설정 된 경우 제어 평면에서 Azure Storage 계정으로의 액세스를 **허용** 합니다. 고객은 저장소 계정에서 백업 또는 복원에 대한 [Azure API Management 제어 평면 IP 주소][control-plane-ip-address] 집합을 열어야 합니다. 

> [!NOTE]
> 동일한 Azure 지역에서 [방화벽이][azure-storage-ip-firewall] 사용 하도록 설정 된 저장소 계정을 사용 하 여 API Management 서비스에서 백업/복원을 수행 하려고 하면이 작업이 작동 하지 않습니다. 이는 Azure Storage에 대한 요청이 계산 > (Azure Api Management 제어 평면)의 공용 IP에는 없는 것 이기 때문입니다. 지역 간 저장소 요청은 SNATed

### <a name="step2"> </a>API Management 서비스 복원

이전에 만든 백업에서 API Management 서비스를 복원하려면 다음 HTTP 요청을 실행합니다.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

각 항목이 나타내는 의미는 다음과 같습니다.

-   `subscriptionId` - 백업을 복원할 API Management 서비스를 포함하는 구독의 ID입니다.
-   `resourceGroupName` - 백업을 복원할 Azure API Management 서비스를 포함하는 리소스 그룹의 이름입니다.
-   `serviceName` - 백업을 복원할 API Management 서비스를 만들 때 지정한 이름입니다.
-   `api-version` `2018-06-01-preview`으로 바꾸기

요청 본문에서 백업 파일 위치를 지정합니다. 즉, Azure Storage 계정 이름, 액세스 키, Blob 컨테이너 이름 및 백업 이름을 추가합니다.

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

`Content-Type` 요청 헤더의 값을 `application/json`으로 설정합니다.

복원은 오랫동안 실행되는 작업으로, 완료되려면 30분 이상이 걸릴 수 있습니다. 요청이 성공하고 복원 프로세스가 시작된 경우 `202 Accepted` 헤더가 포함된 `Location` 응답 상태 코드를 받게 됩니다. `Location` 헤더에서 URL에 대한 'GET' 요청을 수행하면 작업 상태를 확인할 수 있습니다. 복원이 진행 중인 동안에는 '202 수락됨' 상태 코드가 계속 수신됩니다. 응답 코드가 `200 OK` 이면 복원 작업이 정상적으로 완료된 것입니다.

> [!IMPORTANT]
> 백업을 복원할 서비스의 **SKU**는 복원하려는 백업된 서비스의 SKU와 **일치해야** 합니다.
>
> 복원 작업이 진행되는 동안 API, 정책, 개발자 포털 모양 등의 서비스 구성에 적용된 **변경 내용**을 **덮어쓸 수 있습니다**.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> 각각 PowerShell [_AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) 및 [_AzApiManagement_](/powershell/module/az.apimanagement/restore-azapimanagement) 명령을 사용 하 여 백업 및 복원 작업을 수행할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

백업/복원 프로세스의 다른 연습을 위해 다음 리소스를 확인합니다.

-   [Azure API Management 계정 복제](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Logic Apps로 API Management 백업 및 복원 자동화](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
-   [Azure API Management: 구성 백업 및 복원](https://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
    _Stuart에서 구체화된 접근 방식은 공식 지침과 일치하지 않지만 흥미롭습니다._

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
[control-plane-ip-address]: api-management-using-with-vnet.md#control-plane-ips
[azure-storage-ip-firewall]: ../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range
