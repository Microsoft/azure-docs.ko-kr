---
title: Azure Active Directory를 사용하여 액세스 권한 부여
description: 이 문서에서는 Azure Active Directory를 사용 하 여 Event Hubs 리소스에 대 한 액세스 권한을 부여 하는 방법을 설명 합니다.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 021d00b9fb02f2f5ea2560038741efec11b8cbc0
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169198"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Azure Active Directory를 사용 하 여 Event Hubs 리소스에 대 한 액세스 권한 부여
Azure Event Hubs는 Azure Active Directory (Azure AD)를 사용 하 여 Event Hubs 리소스에 대 한 요청에 권한을 부여 합니다. Azure AD를 사용 하면 RBAC (역할 기반 액세스 제어)를 사용 하 여 사용자 또는 응용 프로그램 서비스 사용자 일 수 있는 보안 주체에 권한을 부여할 수 있습니다. 역할 및 역할 할당에 대해 자세히 알아보려면 [다른 역할 이해](../role-based-access-control/overview.md)를 참조 하세요.

## <a name="overview"></a>개요
보안 주체 (사용자 또는 응용 프로그램)가 Event Hubs 리소스에 액세스 하려고 하는 경우 요청에 권한이 부여 되어야 합니다. Azure AD를 사용 하 여 리소스에 대 한 액세스는 2 단계 프로세스입니다. 

 1. 먼저, 보안 주체의 id가 인증 되 고 OAuth 2.0 토큰이 반환 됩니다. 토큰을 요청 하는 리소스 이름이 `https://eventhubs.azure.net/`되었습니다.
 1. 그런 다음 토큰은 지정 된 리소스에 대 한 액세스 권한을 부여 하기 위해 Event Hubs 서비스에 대 한 요청의 일부로 전달 됩니다.

인증 단계를 수행 하려면 응용 프로그램 요청이 런타임에 OAuth 2.0 액세스 토큰을 포함 해야 합니다. 응용 프로그램이 azure VM, 가상 머신 확장 집합 또는 azure 함수 앱과 같은 Azure 엔터티 내에서 실행 되는 경우 관리 id를 사용 하 여 리소스에 액세스할 수 있습니다. 관리 id에서 Event Hubs 서비스에 대 한 요청을 인증 하는 방법을 알아보려면 azure [리소스에 대 한 Azure Active Directory 및 관리 id를 사용 하 여 azure Event Hubs 리소스에 대 한 액세스 인증](authenticate-managed-identity.md)을 참조 하세요. 

권한 부여 단계를 수행 하려면 하나 이상의 RBAC 역할을 보안 주체에 할당 해야 합니다. Azure Event Hubs는 Event Hubs 리소스에 대 한 권한 집합을 포함 하는 RBAC 역할을 제공 합니다. 보안 주체에 할당 된 역할에 따라 보안 주체에 부여 되는 사용 권한이 결정 됩니다. RBAC 역할에 대 한 자세한 내용은 [Azure Event Hubs에 대 한 기본 제공 rbac 역할](#built-in-rbac-roles-for-azure-event-hubs)을 참조 하세요. 

Event Hubs에 대 한 요청을 하는 네이티브 응용 프로그램 및 웹 응용 프로그램은 Azure AD를 사용 하 여 권한을 부여할 수도 있습니다. 액세스 토큰을 요청 하 고이를 사용 하 여 Event Hubs 리소스에 대 한 요청을 인증 하는 방법을 알아보려면 [응용 프로그램에서 AZURE AD를 사용 하 여 azure Event Hubs에 대 한 액세스 인증](authenticate-application.md)을 참조 하세요. 

## <a name="assign-rbac-roles-for-access-rights"></a>액세스 권한에 대 한 RBAC 역할 할당
Azure AD(Azure Active Directory)에서는 [RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 통해 보호된 리소스에 액세스 권한을 부여합니다. Azure Event Hubs는 이벤트 허브 데이터에 액세스 하는 데 사용 되는 일반 권한 집합을 포함 하는 기본 제공 RBAC 역할 집합을 정의 하 고 데이터에 액세스 하기 위한 사용자 지정 역할을 정의할 수도 있습니다.

RBAC 역할이 Azure AD 보안 주체에 할당 되 면 Azure는 해당 보안 주체에 대 한 해당 리소스에 대 한 액세스 권한을 부여 합니다. 액세스의 범위는 구독, 리소스 그룹, Event Hubs 네임 스페이스 또는 그 아래에 있는 리소스의 수준으로 지정할 수 있습니다. Azure AD 보안 주체는 사용자 또는 응용 프로그램 서비스 사용자 이거나 [azure 리소스에 대 한 관리 id](../active-directory/managed-identities-azure-resources/overview.md)일 수 있습니다.

## <a name="built-in-rbac-roles-for-azure-event-hubs"></a>Azure Event Hubs에 대 한 기본 제공 RBAC 역할
Azure는 Azure AD 및 OAuth를 사용 하 여 Event Hubs 데이터에 대 한 액세스 권한을 부여 하는 다음과 같은 기본 제공 RBAC 역할을 제공 합니다.

- [Azure Event Hubs 데이터 소유자](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner):이 역할을 사용 하 여 Event Hubs 리소스에 대 한 완전 한 액세스를 제공 합니다.
- [Azure Event Hubs 데이터 발신자](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver):이 역할을 사용 하 여 Event Hubs 리소스에 대 한 송신 액세스를 제공 합니다.
- [Azure Event Hubs 데이터 수신기](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender):이 역할을 사용 하 여 Event Hubs 리소스에 대 한 사용/수신 액세스를 제공 합니다.

## <a name="resource-scope"></a>리소스 범위 
보안 주체에 RBAC 역할을 할당 하기 전에 보안 주체에 게 부여 해야 하는 액세스 범위를 결정 합니다. 모범 사례는 항상 가장 좁은 범위를 부여 하는 것이 가장 좋습니다.

다음 목록에서는 가장 좁은 범위에서 시작 하 여 Event Hubs 리소스에 대 한 액세스 범위를 지정할 수 있는 수준을 설명 합니다.

- **소비자 그룹**:이 범위에서 역할 할당은이 엔터티에만 적용 됩니다. 현재 Azure Portal는이 수준에서 보안 주체에 RBAC 역할을 할당 하는 것을 지원 하지 않습니다. 
- **이벤트 허브**: 역할 할당은 이벤트 허브 엔터티와 그 아래에 있는 소비자 그룹에 적용 됩니다.
- **네임 스페이스**: 역할 할당은 네임 스페이스에 있는 Event Hubs의 전체 토폴로지 및 이와 연결 된 소비자 그룹에 적용 됩니다.
- **리소스 그룹**: 역할 할당은 리소스 그룹 아래의 모든 Event Hubs 리소스에 적용 됩니다.
- **구독**: 역할 할당은 구독의 모든 리소스 그룹에 있는 모든 Event Hubs 리소스에 적용 됩니다.

> [!NOTE]
> RBAC 역할 할당을 전파 하는 데 최대 5 분이 걸릴 수 있다는 점에 유의 하세요. 

기본 제공 역할을 정의 하는 방법에 대 한 자세한 내용은 [역할 정의 이해](../role-based-access-control/role-definitions.md#management-and-data-operations)를 참조 하세요. 사용자 지정 RBAC 역할을 만드는 방법에 대 한 자세한 내용은 [Azure 역할 기반 Access Control에 대 한 사용자 지정 역할 만들기](../role-based-access-control/custom-roles.md)를 참조 하세요.

## <a name="samples"></a>샘플
- [EventHubs 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    이러한 샘플은 이전 **EventHubs** 라이브러리를 사용 하지만, 최신 **EventHubs** 라이브러리를 사용 하 여 쉽게 업데이트할 수 있습니다. 이전 라이브러리를 사용 하 여 샘플을 새 라이브러리로 이동 하려면 [EventHubs에서 EventHubs로 마이그레이션 가이드](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md)를 참조 하세요.
- [EventHubs 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    이 샘플은 최신 **EventHubs** 라이브러리를 사용 하도록 업데이트 되었습니다.

## <a name="next-steps"></a>다음 단계
- 보안 주체에 대 한 기본 제공 RBAC 역할을 할당 하는 방법에 대해 알아봅니다. [Azure Active Directory를 사용 하 여 Event Hubs 리소스에 대 한 액세스 인증](authenticate-application.md)을 참조 하세요.
- [RBAC를 사용 하 여 사용자 지정 역할을 만드는 방법](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole)에 대해 알아봅니다.
- [EH와 Azure Active Directory를 사용 하는 방법을](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK) 알아봅니다.

다음 관련 문서를 참조 하세요.

- [Azure Active Directory를 사용 하 여 응용 프로그램에서 Azure Event Hubs에 대 한 요청 인증](authenticate-application.md)
- [Event Hubs 리소스에 액세스 하기 위해 Azure Active Directory를 사용 하 여 관리 id 인증](authenticate-managed-identity.md)
- [공유 액세스 서명을 사용 하 여 Azure Event Hubs에 대 한 요청 인증](authenticate-shared-access-signature.md)
- [공유 액세스 서명을 사용 하 여 Event Hubs 리소스에 대 한 액세스 권한 부여](authorize-access-shared-access-signature.md)