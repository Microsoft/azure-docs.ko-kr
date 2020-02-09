---
title: Azure Service Bus 엔터티에 액세스 하는 응용 프로그램 인증
description: 이 문서에서는 Azure Service Bus 엔터티 (큐, 토픽 등)에 액세스 하 Azure Active Directory 응용 프로그램을 인증 하는 방법에 대 한 정보를 제공 합니다.
services: service-bus-messaging
ms.service: event-hubs
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 6a78e4d81921fae8dcb325e9d72df1eee7b99a3b
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996998"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Azure Service Bus 엔터티에 액세스 하 Azure Active Directory 응용 프로그램 인증 및 권한 부여
Azure Service Bus은 Azure Active Directory (Azure AD)를 사용 하 여 엔터티 (큐, 토픽, 구독 또는 필터)에 대 한 요청 Service Bus에 권한을 부여 하는 것을 지원 합니다. Azure AD에서는 RBAC (역할 기반 액세스 제어)를 사용 하 여 사용자, 그룹 또는 응용 프로그램 서비스 사용자 일 수 있는 보안 주체에 권한을 부여할 수 있습니다. 역할 및 역할 할당에 대해 자세히 알아보려면 [다른 역할 이해](../role-based-access-control/overview.md)를 참조 하세요.

## <a name="overview"></a>개요
보안 주체 (사용자, 그룹 또는 응용 프로그램)가 Service Bus 엔터티에 대 한 액세스를 시도 하는 경우 요청에 권한이 부여 되어야 합니다. Azure AD를 사용 하 여 리소스에 대 한 액세스는 2 단계 프로세스입니다. 

 1. 먼저, 보안 주체의 id가 인증 되 고 OAuth 2.0 토큰이 반환 됩니다. 토큰을 요청 하는 리소스 이름은 `https://servicebus.azure.net`입니다.
 1. 그런 다음 토큰은 지정 된 리소스에 대 한 액세스 권한을 부여 하기 위해 Service Bus 서비스에 대 한 요청의 일부로 전달 됩니다.

인증 단계를 수행 하려면 응용 프로그램 요청이 런타임에 OAuth 2.0 액세스 토큰을 포함 해야 합니다. 응용 프로그램이 azure VM, 가상 머신 확장 집합 또는 azure 함수 앱과 같은 Azure 엔터티 내에서 실행 되는 경우 관리 id를 사용 하 여 리소스에 액세스할 수 있습니다. 관리 id에서 Service Bus 서비스에 대 한 요청을 인증 하는 방법을 알아보려면 [Azure 리소스에 대 한 Azure Active Directory 및 관리 id를 사용 하 여 Azure Service Bus 리소스에 대 한 액세스 인증](service-bus-managed-service-identity.md)을 참조 하세요. 

권한 부여 단계를 수행 하려면 하나 이상의 RBAC 역할을 보안 주체에 할당 해야 합니다. Azure Service Bus는 Service Bus 리소스에 대 한 권한 집합을 포함 하는 RBAC 역할을 제공 합니다. 보안 주체에 할당 된 역할에 따라 보안 주체에 부여 되는 사용 권한이 결정 됩니다. Azure Service Bus에 RBAC 역할을 할당 하는 방법에 대 한 자세한 내용은 [Azure Service Bus 기본 제공 rbac 역할](#built-in-rbac-roles-for-azure-service-bus)을 참조 하세요. 

Service Bus에 대 한 요청을 하는 네이티브 응용 프로그램 및 웹 응용 프로그램은 Azure AD를 사용 하 여 권한을 부여할 수도 있습니다. 이 문서에서는 액세스 토큰을 요청 하 고이를 사용 하 여 Service Bus 리소스에 대 한 요청에 권한을 부여 하는 방법을 보여 줍니다. 


## <a name="assigning-rbac-roles-for-access-rights"></a>액세스 권한에 대 한 RBAC 역할 할당
Azure AD(Azure Active Directory)에서는 [RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 통해 보호된 리소스에 액세스 권한을 부여합니다. Azure Service Bus는 Service Bus 엔터티에 액세스 하는 데 사용 되는 일반 권한 집합을 포함 하는 기본 제공 RBAC 역할 집합을 정의 하 고 데이터에 액세스 하기 위한 사용자 지정 역할을 정의할 수도 있습니다.

RBAC 역할이 Azure AD 보안 주체에 할당 되 면 Azure는 해당 보안 주체에 대 한 해당 리소스에 대 한 액세스 권한을 부여 합니다. 액세스 범위는 구독, 리소스 그룹 또는 Service Bus 네임 스페이스의 수준으로 지정할 수 있습니다. Azure AD 보안 주체는 사용자, 그룹, 응용 프로그램 서비스 주체 또는 [azure 리소스에 대 한 관리 되는 id](../active-directory/managed-identities-azure-resources/overview.md)일 수 있습니다.

## <a name="built-in-rbac-roles-for-azure-service-bus"></a>Azure Service Bus에 대 한 기본 제공 RBAC 역할
Azure Service Bus의 경우 Azure Portal 및 Azure 리소스 관리 API를 통한 네임스페이스 및 관련된 모든 리소스의 관리는 이미 RBAC(*역할 기반 액세스 제어*) 모델을 사용하여 보호되고 있습니다. Azure는 Service Bus 네임 스페이스에 대 한 액세스 권한을 부여 하는 다음과 같은 기본 제공 RBAC 역할을 제공 합니다.

- [데이터 소유자 Azure Service Bus](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Service Bus 네임 스페이스와 해당 엔터티 (큐, 항목, 구독 및 필터)에 대 한 데이터 액세스를 사용 하도록 설정 합니다.
- [데이터 발신자 Azure Service Bus](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): 이 역할을 사용 하 여 Service Bus 네임 스페이스 및 해당 엔터티에 대 한 액세스 권한을 제공 합니다.
- [Azure Service Bus 데이터 수신기](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): 이 역할을 사용 하 여 Service Bus 네임 스페이스 및 해당 엔터티에 대 한 액세스 권한을 부여할 수 있습니다. 

## <a name="resource-scope"></a>리소스 범위 
보안 주체에 RBAC 역할을 할당 하기 전에 보안 주체에 게 부여 해야 하는 액세스 범위를 결정 합니다. 모범 사례는 항상 가장 좁은 범위를 부여 하는 것이 가장 좋습니다.

다음 목록에서는 가장 좁은 범위에서 시작 하 여 Service Bus 리소스에 대 한 액세스 범위를 지정할 수 있는 수준을 설명 합니다.

- **큐**, **토픽**또는 **구독**: 역할 할당은 특정 Service Bus 엔터티에 적용 됩니다. 현재 Azure Portal는 구독 수준에서 RBAC 역할을 Service Bus 하는 사용자/그룹/관리 id 할당을 지원 하지 않습니다. 
- **Service Bus 네임 스페이스**: 역할 할당은 네임 스페이스 아래 Service Bus의 전체 토폴로지 및 이와 연결 된 소비자 그룹으로 확장 됩니다.
- **리소스 그룹**: 역할 할당은 리소스 그룹 아래의 모든 Service Bus 리소스에 적용 됩니다.
- **구독**: 역할 할당은 구독의 모든 리소스 그룹에 있는 모든 Service Bus 리소스에 적용 됩니다.

> [!NOTE]
> RBAC 역할 할당을 전파 하는 데 최대 5 분이 걸릴 수 있다는 점에 유의 하세요. 

기본 제공 역할을 정의 하는 방법에 대 한 자세한 내용은 [역할 정의 이해](../role-based-access-control/role-definitions.md#management-and-data-operations)를 참조 하세요. 사용자 지정 RBAC 역할을 만드는 방법에 대 한 자세한 내용은 [Azure 역할 기반 Access Control에 대 한 사용자 지정 역할 만들기](../role-based-access-control/custom-roles.md)를 참조 하세요.


## <a name="assign-rbac-roles-using-the-azure-portal"></a>Azure Portal를 사용 하 여 RBAC 역할 할당  
RBAC 및 Azure Portal를 사용 하 여 Azure 리소스에 대 한 액세스를 관리 하는 방법에 대 한 자세한 내용은 [이 문서](..//role-based-access-control/role-assignments-portal.md)를 참조 하세요. 

역할 할당에 적절 한 범위를 결정 한 후 Azure Portal에서 해당 리소스로 이동 합니다. 리소스에 대 한 액세스 제어 (IAM) 설정을 표시 하 고 다음 지침에 따라 역할 할당을 관리 합니다.

> [!NOTE]
> 아래에 설명 된 단계는 Service Bus 네임 스페이스에 역할을 할당 합니다. 동일한 단계에 따라 다른 지원 되는 범위 (리소스 그룹, 구독 등)에 역할을 할당할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에서 Service Bus 네임 스페이스로 이동 합니다. 왼쪽 메뉴에서 **Access Control (IAM)** 을 선택 하 여 네임 스페이스에 대 한 액세스 제어 설정을 표시 합니다. Service Bus 네임 스페이스를 만들어야 하는 경우이 문서의 지침을 따르세요. [Service Bus 메시징 네임 스페이스를 만듭니다](service-bus-create-namespace-portal.md).

    ![왼쪽 메뉴에서 Access Control를 선택 합니다.](./media/authenticate-application/select-access-control-menu.png)
1. **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다. 도구 모음에서 **추가** 단추를 선택한 다음 **역할 할당 추가**를 선택 합니다. 

    ![도구 모음에 단추 추가](./media/authenticate-application/role-assignments-add-button.png)
1. **역할 할당 추가** 페이지에서 다음 단계를 수행 합니다.
    1. 할당 하려는 **Service Bus 역할** 을 선택 합니다. 
    1. 역할을 할당 하려는 **보안 주체** (사용자, 그룹, 서비스 사용자)를 검색 합니다.
    1. **저장** 을 선택 하 여 역할 할당을 저장 합니다. 

        ![사용자에 게 역할 할당](./media/authenticate-application/assign-role-to-user.png)
    4. 역할을 할당받은 ID가 해당 역할에 따라 나열되어 표시됩니다. 예를 들어 다음 이미지는 Azure 사용자가 Azure Service Bus 데이터 소유자 역할에 있음을 보여 줍니다. 
        
        ![목록의 사용자](./media/authenticate-application/user-in-list.png)

비슷한 단계에 따라 리소스 그룹 또는 구독에 범위가 지정 된 역할을 할당할 수 있습니다. 역할 및 해당 범위를 정의한 후 [에는 GitHub에서 샘플](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)을 사용 하 여이 동작을 테스트할 수 있습니다.


## <a name="authenticate-from-an-application"></a>애플리케이션에서 인증
Service Bus에서 Azure AD를 사용 하는 경우의 주요 이점은 자격 증명을 코드에 더 이상 저장할 필요가 없다는 것입니다. 대신 Microsoft id 플랫폼에서 OAuth 2.0 액세스 토큰을 요청할 수 있습니다. Azure AD는 응용 프로그램을 실행 하는 보안 주체 (사용자, 그룹 또는 서비스 사용자)를 인증 합니다. 인증에 성공 하면 Azure AD는 응용 프로그램에 액세스 토큰을 반환 하 고 응용 프로그램은 액세스 토큰을 사용 하 여 Azure Service Bus에 대 한 요청에 권한을 부여할 수 있습니다.

다음 섹션에서는 Microsoft id platform 2.0 인증을 위해 네이티브 응용 프로그램 또는 웹 응용 프로그램을 구성 하는 방법을 보여 줍니다. Microsoft id 플랫폼 2.0에 대 한 자세한 내용은 v2.0 [(microsoft identity platform) 개요](../active-directory/develop/v2-overview.md)를 참조 하세요.

OAuth 2.0 코드 권한 부여 흐름의 개요는 [OAuth 2.0 코드 권한 부여 흐름을 사용하여 Azure Active Directory 웹 애플리케이션에 대한 액세스 권한 부여](../active-directory/develop/v2-oauth2-auth-code-flow.md)를 참조하세요.

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Azure AD 테넌트에 애플리케이션 등록
Azure AD를 사용 하 여 Service Bus 엔터티에 권한을 부여 하는 첫 번째 단계는 클라이언트 응용 프로그램을 [Azure Portal](https://portal.azure.com/)의 Azure ad 테넌트에 등록 하는 것입니다. 클라이언트 응용 프로그램을 등록할 때 응용 프로그램에 대 한 정보를 AD에 제공 합니다. 그런 다음 azure AD는 응용 프로그램을 Azure AD 런타임에 연결 하는 데 사용할 수 있는 클라이언트 ID (응용 프로그램 ID 라고도 함)를 제공 합니다. 클라이언트 ID에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 사용자 개체](../active-directory/develop/app-objects-and-service-principals.md)를 참조하세요. 

다음 이미지는 웹 응용 프로그램을 등록 하는 단계를 보여 줍니다.

![애플리케이션 등록](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> 응용 프로그램을 네이티브 응용 프로그램으로 등록 하는 경우 리디렉션 URI에 대 한 유효한 URI를 지정할 수 있습니다. 네이티브 응용 프로그램의 경우이 값은 실제 URL 일 필요가 없습니다. 웹 응용 프로그램의 경우 리디렉션 URI는 토큰을 제공 하는 URL을 지정 하므로 유효한 URI 여야 합니다.

응용 프로그램을 등록 한 후에는 **설정**아래에 **응용 프로그램 (클라이언트) ID가** 표시 됩니다.

![등록 된 응용 프로그램의 응용 프로그램 ID](./media/authenticate-application/application-id.png)

Azure AD에서 애플리케이션을 등록하는 방법에 대한 자세한 정보는 [Azure Active Directory와 애플리케이션 통합](../active-directory/develop/quickstart-v2-register-an-app.md)을 참조하세요.

> [!IMPORTANT]
> **TenantId** 와 **ApplicationId**를 적어 둡니다. 응용 프로그램을 실행 하려면 이러한 값이 필요 합니다.

### <a name="create-a-client-secret"></a>클라이언트 비밀 만들기   
응용 프로그램은 토큰을 요청할 때 해당 id를 증명 하기 위해 클라이언트 암호가 필요 합니다. 클라이언트 암호를 추가 하려면 다음 단계를 수행 합니다.

1. 페이지에 아직 없는 경우 Azure Portal에서 앱 등록으로 이동 합니다.
1. 왼쪽 메뉴에서 **인증서 & 암호** 를 선택 합니다.
1. **클라이언트 암호**에서 새 **클라이언트 암호** 를 선택 하 여 새 암호를 만듭니다.

    ![새 클라이언트 암호-단추](./media/authenticate-application/new-client-secret-button.png)
1. 비밀에 대 한 설명을 입력 하 고 원하는 만료 간격을 선택한 다음 **추가**를 선택 합니다.

    ![클라이언트 암호 추가 페이지](./media/authenticate-application/add-client-secret-page.png)
1. 새 암호의 값을 안전한 위치에 즉시 복사 합니다. 채우기 값은 한 번만 표시 됩니다.

    ![클라이언트 암호](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>Service Bus API에 대 한 사용 권한
응용 프로그램이 콘솔 응용 프로그램 인 경우 네이티브 응용 프로그램을 등록 하 고 **필요한 권한** 집합에 **ServiceBus** 에 대 한 API 권한을 추가 해야 합니다. 또한 네이티브 응용 프로그램에는 식별자 역할을 하는 Azure AD의 **리디렉션 URI** 가 필요 합니다. URI는 네트워크 대상이 될 필요가 없습니다. 이 예제의 경우 샘플 코드가 이미 해당 URI를 사용하므로 `https://servicebus.microsoft.com`을 사용합니다.

### <a name="client-libraries-for-token-acquisition"></a>토큰 획득을 위한 클라이언트 라이브러리  
응용 프로그램을 등록 하 고 Azure Service Bus에서 데이터를 보내고 받을 수 있는 권한을 부여 하면 응용 프로그램에 보안 주체를 인증 하 고 OAuth 2.0 토큰을 획득 하는 코드를 추가할 수 있습니다. 토큰을 인증 하 고 얻으려면 [Microsoft id 플랫폼 인증 라이브러리나](../active-directory/develop/reference-v2-libraries.md) 1.0 openid connect을 지 원하는 다른 오픈 소스 라이브러리 중 하나를 사용할 수 있습니다. 그러면 응용 프로그램은 액세스 토큰을 사용 하 여 Azure Service Bus에 대 한 요청에 권한을 부여할 수 있습니다.

토큰 획득을 지 원하는 시나리오 목록은 .NET GitHub 리포지토리의 [MSAL (Microsoft 인증 라이브러리)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) 의 [시나리오](https://aka.ms/msal-net-scenarios) 섹션을 참조 하세요.

## <a name="sample-on-github"></a>GitHub의 샘플
GitHub의 다음 샘플을 참조 하세요. [Service Bus에 대 한 역할 기반 액세스 제어](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)입니다. 

**대화형 사용자 로그인** 옵션이 아닌 **클라이언트 암호 로그인** 옵션을 사용 합니다. 클라이언트 암호 옵션을 사용 하면 팝업 창이 표시 되지 않습니다. 응용 프로그램은 인증을 위해 테넌트 ID 및 앱 ID를 활용 합니다. 

### <a name="run-the-sample"></a>샘플 실행

샘플을 실행 하기 전에 **app.config** 파일을 편집 하 고 시나리오에 따라 다음 값을 설정 합니다.

- `tenantId`: **TenantId** 값으로 설정합니다.
- `clientId`: **ApplicationId** 값으로 설정합니다.
- `clientSecret`: 클라이언트 암호를 사용하여 로그인하려는 경우 Azure AD에서 만듭니다. 또한 네이티브 앱 대신 웹앱 또는 API를 사용합니다. 또한 앱을 이전에 만든 네임스페이스의 **액세스 제어(IAM)** 에 추가합니다.
- `serviceBusNamespaceFQDN`: 새로 만든 Service Bus 네임스페이스의 전체 DNS 이름으로 설정합니다(예: `example.servicebus.windows.net`).
- `queueName`: 만든 큐의 이름으로 설정합니다.
- 이전 단계에서 앱에 지정된 리디렉션 URI입니다.

콘솔 응용 프로그램을 실행 하면 시나리오를 선택 하 라는 메시지가 표시 됩니다. 해당 숫자를 입력 하 고 ENTER 키를 눌러 **대화형 사용자 로그인** 을 선택 합니다. 애플리케이션에서는 로그인 창이 표시되고, Service Bus에 액세스할지 묻는 메시지가 표시된 다음, 서비스를 사용하여 로그인 ID를 통해 보내기/받기 시나리오를 실행합니다.


## <a name="next-steps"></a>다음 단계
- RBAC에 대해 자세히 알아보려면 [rbac (역할 기반 액세스 제어)](../role-based-access-control/overview.md)란?을 참조 하세요.
- Azure PowerShell, Azure CLI 또는 REST API를 사용하여 RBAC 역할 할당을 할당하고 관리하는 방법을 알아보려면 다음과 같은 아티클을 참조하세요.
    - [Azure PowerShell을 사용하여 RBAC(역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-powershell.md)  
    - [Azure CLI를 사용하여 RBAC(역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-cli.md)
    - [REST API를 사용하여 RBAC(역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-rest.md)
    - [Azure Resource Manager 템플릿으로 RBAC (역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-template.md)

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

- [RBAC 샘플 Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
- [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
- [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)
