---
title: Azure Service Bus 미리 보기를 통해 Azure 리소스에 관리 ID 사용 | Microsoft Docs
description: Azure Service Bus를 통해 Azure 리소스에 관리 ID 사용
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/01/2018
ms.author: aschhab
ms.openlocfilehash: 5edeebd9698384785082e5a441c24e136ed22481
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61317078"
---
# <a name="managed-identities-for-azure-resources-with-service-bus"></a>Service Bus를 통해 Azure 리소스에 관리 ID 사용 

[Azure 리소스용 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)는 애플리케이션 코드가 실행되는 배포와 관련된 보안 ID를 만들 수 있도록 하는 Azure 간 기능입니다. 그런 다음 애플리케이션에 필요한 특정 Azure 리소스에 액세스하기 위한 사용자 지정 권한을 부여하는 액세스 제어 역할에 해당 ID를 연결할 수 있습니다.

관리 ID를 사용하는 경우 Azure 플랫폼에서 이 런타임 ID를 관리합니다. ID 자체 또는 액세스해야 하는 리소스에 대한 액세스 키를 애플리케이션 코드나 구성에 저장하고 보호할 필요가 없습니다. Azure App Service 애플리케이션 내에서 실행되거나 Azure 리소스용 관리 ID를 지원하도록 설정된 가상 머신에서 실행되는 Service Bus 클라이언트 앱은 SAS 규칙과 키 또는 기타 액세스 토큰을 처리할 필요가 없습니다. 클라이언트 앱은 Service Bus 메시지 네임스페이스의 엔드포인트 주소만 있으면 됩니다. 앱이 연결되면 Service Bus는 이 문서 뒷부분의 예제에 나와 있는 작업에서 관리 ID의 컨텍스트를 클라이언트에 바인딩합니다. 관리 ID와 연결된 Service Bus 클라이언트는 권한이 부여된 모든 작업을 수행할 수 있습니다. 관리 ID를 Service Bus 역할에 연결하는 방식을 통해 권한이 부여됩니다. 

## <a name="service-bus-roles-and-permissions"></a>Service Bus 역할 및 사용 권한

관리 ID는 Service Bus 네임스페이스의 "소유자" 또는 "참가자" 역할에만 추가할 수 있습니다. 이러한 역할에 ID를 추가하면 네임스페이스의 모든 엔터티에 대한 모든 제어 권한이 ID에 부여됩니다. 그러나 네임스페이스 토폴로지를 변경하는 관리 작업은 처음에는 Azure Resource Manager를 통해서만 지원되고 네이티브 Service Bus REST 관리 인터페이스를 통해서는 지원되지 않습니다. 그러므로 관리 ID 내에서는 .NET Framework 클라이언트 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 개체를 사용할 수 없습니다.

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Service Bus를 통해 Azure 리소스에 관리 ID 사용

다음 섹션에서는 관리 ID에서 실행되는 애플리케이션 예제를 만들고 배포하는 데 필요한 단계, 해당 ID에 Service Bus 메시징 네임스페이스에 대한 액세스 권한을 부여하는 방법, 애플리케이션이 해당 ID를 사용하여 Service Bus와 상호 작용하는 방식을 설명합니다.

이 소개에서는 [Azure App Service](https://azure.microsoft.com/services/app-service/)에 호스트되는 웹 애플리케이션에 대해 설명합니다. VM 호스트 애플리케이션에 필요한 단계는 비슷합니다.

### <a name="create-an-app-service-web-application"></a>App Service 웹 애플리케이션 만들기

첫 번째 단계는 App Service ASP.NET 애플리케이션을 만드는 것입니다. Azure에서 이 작업을 수행하는 방법을 잘 모를 경우 [이 방법 가이드](../app-service/app-service-web-get-started-dotnet-framework.md)를 따르세요. 단, 자습서에 나와 있는 MVC 애플리케이션 대신, Web Forms 애플리케이션을 만듭니다.

### <a name="set-up-the-managed-identity"></a>관리 ID 설정

애플리케이션을 만든 후 Azure Portal에서 새로 만든 웹앱으로 이동한 후(방법에 제공된 설명 참조) **관리 서비스 ID** 페이지로 이동하여 해당 기능을 사용하도록 설정합니다. 

![](./media/service-bus-managed-service-identity/msi1.png)

기능을 사용하도록 설정하면 새 서비스 ID가 Azure Active Directory에서 생성되고 App Service 호스트로 구성됩니다.

### <a name="create-a-new-service-bus-messaging-namespace"></a>새 Service Bus 메시지 네임스페이스 만들기

그런 다음, RBAC에 대한 미리 보기 지원이 있는 Azure 지역 중 한 곳에 [Service Bus 메시지 네임스페이스를 만듭니다](service-bus-create-namespace-portal.md). **미국 동부**, **미국 동부 2** 또는 **유럽 서부**. 

포털의 네임스페이스 **액세스 제어(IAM)** 페이지로 이동한 후 **역할 할당 추가**를 클릭하여 관리형 ID를 **소유자** 역할에 추가합니다. 이렇게 하려면 **권한 추가** 패널의 **선택** 필드에서 웹 애플리케이션의 이름을 검색하고 해당 항목을 클릭합니다. 그런 다음 **Save**를 클릭합니다.

이제 웹 애플리케이션의 관리 ID가 Service Bus 네임스페이스 및 이전에 만든 큐에 액세스할 수 있습니다. 

### <a name="run-the-app"></a>앱 실행

이제 만든 ASP.NET 애플리케이션의 기본 페이지를 수정합니다. [이 GitHub 리포지토리](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet)의 웹 애플리케이션 코드를 사용할 수 있습니다.  

Default.aspx 페이지가 방문 페이지입니다. 코드는 Default.aspx.cs 파일에서 찾을 수 있습니다. 그 결과 몇 가지 입력 필드와 메시지를 보내거나 받기 위해 Service Bus에 연결되는 **전송** 및 **수신** 단추가 있는 최소 웹 애플리케이션이 생성됩니다.

[MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) 개체가 어떻게 초기화되는지에 유의합니다. 이 코드는 SAS(공유 액세스 토큰) 토큰 공급자를 사용하는 대신 `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.ServiceBusAudience)` 호출을 사용하여 관리 ID용 토큰 공급자를 만듭니다. 이와 같이 유지하고 사용하는 데 특별한 비결은 없습니다. Service Bus 및 권한 부여 핸드셰이크에 대한 관리 ID 컨텍스트의 흐름은 토큰 공급자가 자동으로 처리합니다. 이 모델은 SAS를 사용하는 방식보다 단순합니다.

이와 같이 변경한 후에는 애플리케이션을 게시하고 실행합니다. Visual Studio에서 게시 프로필을 다운로드한 다음 가져오면 올바른 게시 데이터를 쉽게 가져올 수 있습니다.

![](./media/service-bus-managed-service-identity/msi3.png)
 
메시지를 보내거나 받으려면 만든 엔터티의 이름과 네임스페이스의 이름을 입력합니다. 그런 후에 **보내기**나 **받기**를 클릭합니다.


> [!NOTE]
> - 관리 ID는 Azure 환경, App 서비스, Azure VM 및 확장 집합 내에서만 작동합니다. .NET 애플리케이션의 경우 Service Bus NuGet 패키지에서 사용되는 Microsoft.Azure.Services.AppAuthentication 라이브러리는 이 프로토콜에 대한 추상화를 제공하고 로컬 개발 환경을 지원합니다. 또한 이 라이브러리를 통해 개발 머신에서 Visual Studio, Azure CLI 2.0 또는 Active Directory 통합 인증의 사용자 계정을 사용하여 코드를 로컬로 테스트할 수 있습니다. 이 라이브러리를 통한 로컬 개발 옵션에 대한 자세한 내용은 [.NET을 사용하여 Azure Key Vault에 대한 서비스 간 인증](../key-vault/service-to-service-authentication.md)을 참조하세요.  
> 
> - 현재 관리 ID는 App Service 배포 슬롯에서 작동하지 않습니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)