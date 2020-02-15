---
title: Azure Event Hubs 리소스에 액세스 하는 응용 프로그램 인증
description: 이 문서에서는 Azure Event Hubs 리소스에 액세스 하 Azure Active Directory 응용 프로그램 인증에 대 한 정보를 제공 합니다.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 4256cebe44b732b190ef1666d0438d17e058b820
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169284"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>Azure Active Directory를 사용 하 여 응용 프로그램을 인증 Event Hubs 리소스에 액세스
Microsoft Azure는 Azure AD(Active Directory)를 기반으로 하는 리소스 및 애플리케이션에 대해 통합된 액세스 제어 관리 기능을 제공합니다. Azure Event Hubs에서 Azure AD를 사용 하는 경우의 주요 이점은 더 이상 코드에 자격 증명을 저장할 필요가 없다는 것입니다. 대신 Microsoft Id 플랫폼에서 OAuth 2.0 액세스 토큰을 요청할 수 있습니다. 토큰을 요청 하는 리소스 이름이 `https://eventhubs.azure.net/`되었습니다. Azure AD는 응용 프로그램을 실행 하는 보안 주체 (사용자, 그룹 또는 서비스 사용자)를 인증 합니다. 인증에 성공 하면 Azure AD는 응용 프로그램에 액세스 토큰을 반환 하 고 응용 프로그램은 액세스 토큰을 사용 하 여 Azure Event Hubs 리소스에 대 한 요청에 권한을 부여할 수 있습니다.

Azure AD 보안 주체에 역할을 할당 하는 경우 Azure는 해당 보안 주체에 대 한 해당 리소스에 대 한 액세스 권한을 부여 합니다. 액세스의 범위는 구독, 리소스 그룹, Event Hubs 네임 스페이스 또는 그 아래에 있는 리소스의 수준으로 지정할 수 있습니다. Azure AD 보안은 사용자, 그룹, 응용 프로그램 서비스 주체 또는 [azure 리소스에 대 한 관리 되는 id](../active-directory/managed-identities-azure-resources/overview.md)에 역할을 할당할 수 있습니다. 

> [!NOTE]
> 역할 정의는 사용 권한 컬렉션입니다. RBAC (역할 기반 액세스 제어)는 역할 할당을 통해 이러한 사용 권한을 적용 하는 방법을 제어 합니다. 역할 할당은 보안 주체, 역할 정의, 범위의 세 가지 요소로 구성됩니다. 자세한 내용은 [다른 역할 이해](../role-based-access-control/overview.md)를 참조 하세요.

## <a name="built-in-roles-for-azure-event-hubs"></a>Azure Event Hubs에 대 한 기본 제공 역할
Azure는 Azure AD 및 OAuth를 사용 하 여 Event Hubs 데이터에 대 한 액세스 권한을 부여 하는 다음과 같은 기본 제공 RBAC 역할을 제공 합니다.

- [Azure Event Hubs 데이터 소유자](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner):이 역할을 사용 하 여 Event Hubs 리소스에 대 한 완전 한 액세스를 제공 합니다.
- [Azure Event Hubs 데이터 발신자](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender):이 역할을 사용 하 여 Event Hubs 리소스에 대 한 송신 액세스를 제공 합니다.
- [Azure Event Hubs 데이터 수신기](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver):이 역할을 사용 하 여 Event Hubs 리소스에 대 한 액세스를 받을 수 있습니다.   

> [!IMPORTANT]
> Preview 릴리스는 소유자 또는 참가자 역할에 Event Hubs 데이터 액세스 권한을 추가 하는 것을 지원 합니다. 그러나 소유자 및 참여자 역할에 대 한 데이터 액세스 권한은 더 이상 허용 되지 않습니다. 소유자 또는 참가자 역할을 사용 하는 경우 Azure Event Hubs 데이터 소유자 역할을 사용 하도록 전환 합니다.

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Azure Portal를 사용 하 여 RBAC 역할 할당  
RBAC 및 Azure Portal를 사용 하 여 Azure 리소스에 대 한 액세스를 관리 하는 방법에 대 한 자세한 내용은 [이 문서](..//role-based-access-control/role-assignments-portal.md)를 참조 하세요. 

역할 할당에 적절 한 범위를 결정 한 후 Azure Portal에서 해당 리소스로 이동 합니다. 리소스에 대 한 액세스 제어 (IAM) 설정을 표시 하 고 다음 지침에 따라 역할 할당을 관리 합니다.

> [!NOTE]
> 아래에 설명 된 단계는 Event Hubs 네임 스페이스에서 이벤트 허브에 역할을 할당 하지만 동일한 단계를 수행 하 여 범위를 Event Hubs 리소스에 할당할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에서 Event Hubs 네임 스페이스로 이동 합니다.
2. **개요** 페이지에서 역할을 할당할 이벤트 허브를 선택 합니다.

    ![이벤트 허브를 선택 합니다.](./media/authenticate-application/select-event-hub.png)
1. **Access Control (IAM)** 을 선택 하 여 이벤트 허브에 대 한 액세스 제어 설정을 표시 합니다. 
1. **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다. 도구 모음에서 **추가** 단추를 선택한 다음 **역할 할당 추가**를 선택 합니다. 

    ![도구 모음에 단추 추가](./media/authenticate-application/role-assignments-add-button.png)
1. **역할 할당 추가** 페이지에서 다음 단계를 수행 합니다.
    1. 할당 하려는 **Event Hubs 역할** 을 선택 합니다. 
    1. 역할을 할당 하려는 **보안 주체** (사용자, 그룹, 서비스 사용자)를 검색 합니다.
    1. **저장** 을 선택 하 여 역할 할당을 저장 합니다. 

        ![사용자에 게 역할 할당](./media/authenticate-application/assign-role-to-user.png)
    4. 역할을 할당받은 ID가 해당 역할에 따라 나열되어 표시됩니다. 예를 들어 다음 이미지는 Azure 사용자가 Azure Event Hubs 데이터 소유자 역할에 있음을 보여 줍니다. 
        
        ![목록의 사용자](./media/authenticate-application/user-in-list.png)

비슷한 단계에 따라 네임 스페이스, 리소스 그룹 또는 구독 Event Hubs 범위에 역할을 할당할 수 있습니다. 역할 및 해당 범위를 정의한 후 [에는이 GitHub 위치에서](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)샘플을 사용 하 여이 동작을 테스트할 수 있습니다.


## <a name="authenticate-from-an-application"></a>애플리케이션에서 인증
Event Hubs에서 Azure AD를 사용 하는 경우의 주요 이점은 자격 증명을 코드에 더 이상 저장할 필요가 없다는 것입니다. 대신 Microsoft id 플랫폼에서 OAuth 2.0 액세스 토큰을 요청할 수 있습니다. Azure AD는 응용 프로그램을 실행 하는 보안 주체 (사용자, 그룹 또는 서비스 사용자)를 인증 합니다. 인증에 성공 하면 Azure AD는 응용 프로그램에 액세스 토큰을 반환 하 고 응용 프로그램은 액세스 토큰을 사용 하 여 Azure Event Hubs에 대 한 요청에 권한을 부여할 수 있습니다.

다음 섹션에서는 Microsoft id platform 2.0 인증을 위해 네이티브 응용 프로그램 또는 웹 응용 프로그램을 구성 하는 방법을 보여 줍니다. Microsoft id 플랫폼 2.0에 대 한 자세한 내용은 v2.0 [(microsoft identity platform) 개요](../active-directory/develop/v2-overview.md)를 참조 하세요.

OAuth 2.0 코드 권한 부여 흐름의 개요는 [OAuth 2.0 코드 권한 부여 흐름을 사용하여 Azure Active Directory 웹 애플리케이션에 대한 액세스 권한 부여](../active-directory/develop/v2-oauth2-auth-code-flow.md)를 참조하세요.

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Azure AD 테넌트에 애플리케이션 등록
Azure AD를 사용 하 여 Event Hubs 리소스에 권한을 부여 하는 첫 번째 단계는 [Azure Portal](https://portal.azure.com/)에서 azure ad 테 넌 트에 클라이언트 응용 프로그램을 등록 하는 것입니다. 클라이언트 응용 프로그램을 등록할 때 응용 프로그램에 대 한 정보를 AD에 제공 합니다. 그런 다음 azure AD는 응용 프로그램을 Azure AD 런타임에 연결 하는 데 사용할 수 있는 클라이언트 ID (응용 프로그램 ID 라고도 함)를 제공 합니다. 클라이언트 ID에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 및 서비스 사용자 개체](../active-directory/develop/app-objects-and-service-principals.md)를 참조하세요. 

다음 이미지는 웹 응용 프로그램을 등록 하는 단계를 보여 줍니다.

![애플리케이션 등록](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> 응용 프로그램을 네이티브 응용 프로그램으로 등록 하는 경우 리디렉션 URI에 대 한 유효한 URI를 지정할 수 있습니다. 네이티브 응용 프로그램의 경우이 값은 실제 URL 일 필요가 없습니다. 웹 응용 프로그램의 경우 리디렉션 URI는 토큰을 제공 하는 URL을 지정 하므로 유효한 URI 여야 합니다.

응용 프로그램을 등록 한 후에는 **설정**아래에 **응용 프로그램 (클라이언트) ID가** 표시 됩니다.

![등록 된 응용 프로그램의 응용 프로그램 ID](./media/authenticate-application/application-id.png)

Azure AD에서 애플리케이션을 등록하는 방법에 대한 자세한 정보는 [Azure Active Directory와 애플리케이션 통합](../active-directory/develop/quickstart-v2-register-an-app.md)을 참조하세요.


### <a name="create-a-client-secret"></a>클라이언트 비밀 만들기   
응용 프로그램은 토큰을 요청할 때 해당 id를 증명 하기 위해 클라이언트 암호가 필요 합니다. 클라이언트 암호를 추가 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 앱 등록으로 이동 합니다.
1. **인증서 & 암호** 설정을 선택 합니다.
1. **클라이언트 암호**에서 새 **클라이언트 암호** 를 선택 하 여 새 암호를 만듭니다.
1. 비밀에 대 한 설명을 제공 하 고 원하는 만료 간격을 선택 합니다.
1. 새 암호의 값을 안전한 위치에 즉시 복사 합니다. 채우기 값은 한 번만 표시 됩니다.

    ![클라이언트 암호](./media/authenticate-application/client-secret.png)


### <a name="client-libraries-for-token-acquisition"></a>토큰 획득을 위한 클라이언트 라이브러리  
응용 프로그램을 등록 하 고 Azure Event Hubs에서 데이터를 보내고 받을 수 있는 권한을 부여 하면 응용 프로그램에 보안 주체를 인증 하 고 OAuth 2.0 토큰을 획득 하는 코드를 추가할 수 있습니다. 토큰을 인증 하 고 얻으려면 [Microsoft id 플랫폼 인증 라이브러리나](../active-directory/develop/reference-v2-libraries.md) 1.0 openid connect을 지 원하는 다른 오픈 소스 라이브러리 중 하나를 사용할 수 있습니다. 그러면 응용 프로그램은 액세스 토큰을 사용 하 여 Azure Event Hubs에 대 한 요청에 권한을 부여할 수 있습니다.

토큰 획득을 지 원하는 시나리오 목록은 .NET GitHub 리포지토리의 [MSAL (Microsoft 인증 라이브러리)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) 의 [시나리오](https://aka.ms/msal-net-scenarios) 섹션을 참조 하세요.

## <a name="samples"></a>샘플
- [EventHubs 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    이러한 샘플은 이전 **EventHubs** 라이브러리를 사용 하지만, 최신 **EventHubs** 라이브러리를 사용 하 여 쉽게 업데이트할 수 있습니다. 이전 라이브러리를 사용 하 여 샘플을 새 라이브러리로 이동 하려면 [EventHubs에서 EventHubs로 마이그레이션 가이드](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md)를 참조 하세요.
- [EventHubs 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    이 샘플은 최신 **EventHubs** 라이브러리를 사용 하도록 업데이트 되었습니다.

## <a name="next-steps"></a>다음 단계
- RBAC에 대해 자세히 알아보려면 [rbac (역할 기반 액세스 제어)](../role-based-access-control/overview.md)란?을 참조 하세요.
- Azure PowerShell, Azure CLI 또는 REST API를 사용하여 RBAC 역할 할당을 할당하고 관리하는 방법을 알아보려면 다음과 같은 아티클을 참조하세요.
    - [Azure PowerShell을 사용하여 RBAC(역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-powershell.md)  
    - [Azure CLI를 사용하여 RBAC(역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-cli.md)
    - [REST API를 사용하여 RBAC(역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-rest.md)
    - [Azure Resource Manager 템플릿으로 RBAC (역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-template.md)

다음 관련 문서를 참조 하세요.
- [Event Hubs 리소스에 액세스 하기 위해 Azure Active Directory를 사용 하 여 관리 id 인증](authenticate-managed-identity.md)
- [공유 액세스 서명을 사용 하 여 Azure Event Hubs에 대 한 요청 인증](authenticate-shared-access-signature.md)
- [Azure Active Directory를 사용 하 여 Event Hubs 리소스에 대 한 액세스 권한 부여](authorize-access-azure-active-directory.md)
- [공유 액세스 서명을 사용 하 여 Event Hubs 리소스에 대 한 액세스 권한 부여](authorize-access-shared-access-signature.md)

