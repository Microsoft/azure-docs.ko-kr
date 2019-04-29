---
title: Azure Service Bus RBAC(역할 기반 액세스 제어) 미리 보기 | Microsoft Docs
description: Azure Service Bus 역할 기반 액세스 제어
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
ms.date: 09/19/2018
ms.author: aschhab
ms.openlocfilehash: 7ef152b130e77e833e19c51ff97d0cea577216c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472253"
---
# <a name="active-directory-role-based-access-control-preview"></a>Active Directory 역할 기반 액세스 제어(미리 보기)

Microsoft Azure는 Azure AD(Active Directory)를 기반으로 하는 리소스 및 애플리케이션에 대해 통합된 액세스 제어 관리 기능을 제공합니다. Azure AD를 통해 Azure 기반 애플리케이션에 특화된 사용자 계정 및 애플리케이션을 관리하거나, Azure 리소스 및 Azure 호스트 애플리케이션을 포함하는 회사 차원의 Single-Sign-On을 위해 기존 Active Directory 인프라를 Azure AD와 페더레이션할 수 있습니다. 그런 다음 Azure 리소스에 대한 액세스를 부여하기 위해 전역 및 서비스 특정 역할에 Azure AD 사용자 및 애플리케이션 ID를 할당할 수 있습니다.

Azure Service Bus의 경우 Azure Portal 및 Azure 리소스 관리 API를 통한 네임스페이스 및 관련된 모든 리소스의 관리는 이미 RBAC(*역할 기반 액세스 제어*) 모델을 사용하여 보호되고 있습니다. 런타임 작업에 대한 RBAC는 이제 공개 미리 보기에서 사용할 수 있습니다.

Azure AD RBAC를 사용하는 애플리케이션은 SAS 규칙 및 키 또는 특정 Service Bus에 대한 다른 액세스 토큰을 처리할 필요가 없습니다. 클라이언트 앱은 Azure AD와 상호 작용하여 인증 컨텍스트를 설정하고, Service Bus에 대한 액세스 토큰을 획득합니다. 대화형 로그인이 필요한 도메인 사용자 계정을 사용하면 애플리케이션은 어떠한 자격 증명도 직접 처리하지 않습니다.

## <a name="service-bus-roles-and-permissions"></a>Service Bus 역할 및 사용 권한

초기 공개 미리 보기의 경우 Azure AD 계정 및 서비스 주체를 Service Bus 메시지 네임스페이스의 “소유자” 또는 “기여자” 역할로만 추가할 수 있습니다. 이 작업은 네임스페이스의 모든 엔터티에 대한 모든 제어 권한을 ID에 부여합니다. 네임스페이스 토폴로지를 변경하는 관리 작업은 처음에는 Azure 리소스 관리를 통해서만 지원되고 네이티브 Service Bus REST 관리 인터페이스를 통해서는 지원되지 않습니다. 이 지원은 또한 Azure AD 계정으로 .NET Framework 클라이언트 [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) 개체를 사용할 수 없음을 의미합니다.

## <a name="use-service-bus-with-an-azure-ad-domain-user-account"></a>Azure AD 도메인 사용자 계정으로 Service Bus 사용

다음 섹션에서는 대화형 Azure AD 사용자 로그인을 요구하는 애플리케이션 예제를 만들고 배포하는 데 필요한 단계, Service Bus에 해당 사용자 계정에 대한 액세스 권한을 부여하는 방법, 해당 ID를 사용하여 Event Hubs에 액세스하는 방법을 설명합니다.

이 소개에서는 [GitHub에 있는 코드](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)인 간단한 콘솔 애플리케이션을 설명합니다.

### <a name="create-an-active-directory-user-account"></a>Active Directory 사용자 계정 만들기

이 첫 번째 단계는 선택 사항입니다. 모든 Azure 구독은 자동으로 Azure Active Directory 테넌트와 쌍을 이루며, Azure 구독에 액세스할 수 있는 경우 사용자 계정은 이미 등록되어 있습니다. 즉, 사용자 계정을 바로 사용할 수 있습니다.

그런데도 이 시나리오에 대한 특정 계정을 만들고자 하는 경우 [다음 단계를 수행](../automation/automation-create-aduser-account.md)합니다. 대규모 엔터프라이즈 시나리오에 해당하지 않을 수도 있는 Azure Active Directory 테넌트에 계정을 만들 수 있는 권한이 있어야 합니다.

### <a name="create-a-service-bus-namespace"></a>Service Bus 네임스페이스 만들기

그런 다음, RBAC에 대한 미리 보기 지원이 있는 Azure 지역 중 하나에 [Service Bus 메시지 네임스페이스를 만듭니다](service-bus-create-namespace-portal.md). **미국 동부**, **미국 동부 2** 또는 **유럽 서부**.

네임스페이스를 만들고 나면, 포털의 **액세스 제어(IAM)** 페이지로 이동한 후 **역할 할당 추가**를 클릭하여 Azure AD 사용자 계정을 소유자 역할에 추가합니다. 사용자 고유의 사용자 계정을 사용하고 네임스페이스를 만든 경우 이미 소유자 역할에 있는 것입니다. 다른 계정을 역할에 추가하려면 **권한 추가** 패널의 **선택** 필드에서 웹 애플리케이션의 이름을 검색한 다음, 해당 항목을 클릭합니다. 그런 다음 **Save**를 클릭합니다.

이제 사용자 계정은 Service Bus 네임스페이스 및 이전에 만든 큐에 액세스할 수 있습니다.

### <a name="register-the-application"></a>애플리케이션 등록

애플리케이션 예제를 실행하려면 먼저 Azure AD에 등록하고 애플리케이션이 대신 Service Bus에 액세스할 수 있도록 허가하는 동의 확인 프롬프트를 승인해야 합니다.

애플리케이션 예제는 콘솔 애플리케이션이므로 네이티브 애플리케이션을 등록하고 **Microsoft.ServiceBus**에 대한 API 사용 권한을 “필요한 권한” 집합에 추가해야 합니다. 네이티브 애플리케이션은 ID 역할을 하는 Azure AD의 **리디렉션 URI**가 필요합니다. URI가 네트워크 대상일 필요는 없습니다. 이 예제의 경우 샘플 코드가 이미 해당 URI를 사용하므로 `https://servicebus.microsoft.com`을 사용합니다.

자세한 등록 단계는 [이 자습서](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)에 설명되어 있습니다. **네이티브** 앱을 등록하는 단계를 수행한 다음, **Microsoft.ServiceBus** API를 필요한 권한에 추가하는 업데이트 지시를 수행합니다. 단계를 수행하면서 **TenantId** 및 **ApplicationId**를 기록해 두어야 합니다. 이러한 값은 애플리케이션을 실행하는 데 필요합니다.

### <a name="run-the-app"></a>앱 실행

샘플을 실행하려면 먼저 시나리오에 따라 App.config 파일을 편집하고 다음 값을 설정합니다.

- `tenantId`: **TenantId** 값으로 설정합니다.
- `clientId`: **ApplicationId** 값으로 설정합니다.
- `clientSecret`: 클라이언트 암호를 사용하여 로그온하려는 경우 Azure AD에서 만듭니다. 또한 네이티브 앱 대신 웹앱 또는 API를 사용합니다. 또한 앱을 이전에 만든 네임스페이스의 **액세스 제어(IAM)** 에 추가합니다.
- `serviceBusNamespaceFQDN`: 새로 만든 Service Bus 네임스페이스의 전체 DNS 이름으로 설정합니다(예: `example.servicebus.windows.net`).
- `queueName`: 만든 큐의 이름으로 설정합니다.
- 이전 단계에서 앱에 지정된 리디렉션 URI입니다.

콘솔 애플리케이션을 실행하면 시나리오를 선택하라는 메시지가 표시됩니다. 번호를 입력하고 ENTER 키를 눌러 **대화형 사용자 로그인**을 클릭합니다. 애플리케이션에서는 로그인 창이 표시되고, Service Bus에 액세스할지 묻는 메시지가 표시된 다음, 서비스를 사용하여 로그인 ID를 통해 보내기/받기 시나리오를 실행합니다.

## <a name="next-steps"></a>다음 단계

Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Service Bus 큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
* [Service Bus 큐 시작](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](service-bus-dotnet-how-to-use-topics-subscriptions.md)
