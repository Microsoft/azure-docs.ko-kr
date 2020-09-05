---
title: Azure Digital Twins 솔루션에 대한 보안
titleSuffix: Azure Digital Twins
description: Azure Digital Twins를 사용 하 여 보안 모범 사례를 이해 합니다.
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: d528ff41d8b7098e313e070dfe734960cdc89eaa
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88827531"
---
# <a name="secure-azure-digital-twins"></a>Azure Digital Twins 보안

보안을 위해 Azure Digital Twins를 사용 하면 배포의 특정 데이터, 리소스 및 작업에 대 한 정확한 액세스를 제어할 수 있습니다. 이를 위해 **RBAC (역할 기반 액세스 제어)** 라고 하는 세분화 된 역할 및 권한 관리 전략을 통해이를 수행 합니다. [여기](../role-based-access-control/overview.md)에서 Azure에 대 한 RBAC의 일반적인 원칙에 대해 알아볼 수 있습니다.

또한 Azure Digital Twins는 미사용 데이터의 암호화를 지원 합니다.

## <a name="granting-permissions-with-rbac"></a>RBAC를 사용 하 여 권한 부여

RBAC는 azure AD ( [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) )와의 통합을 통해 Azure Digital twins에 제공 됩니다.

RBAC를 사용 하 여 사용자, 그룹 또는 응용 프로그램 서비스 사용자 일 수 있는 *보안 주체*에 권한을 부여할 수 있습니다. 보안 주체는 Azure AD에서 인증 되며 반환에서 OAuth 2.0 토큰을 받습니다. 이 토큰은 Azure Digital Twins 인스턴스에 대 한 액세스 요청에 권한을 부여 하는 데 사용할 수 있습니다.

### <a name="authentication-and-authorization"></a>인증 및 권한 부여

Azure AD를 사용 하 여 액세스는 2 단계 프로세스로 진행 됩니다. 보안 주체 (사용자, 그룹 또는 응용 프로그램)가 Azure Digital Twins에 액세스를 시도 하는 경우 요청을 *인증* 하 고 *권한을 부여*해야 합니다. 

1. 먼저, 보안 주체의 id가 *인증*되 고 OAuth 2.0 토큰이 반환 됩니다.
2. 그런 다음 토큰은 지정 된 리소스에 대 한 액세스 *권한을 부여* 하기 위해 Azure Digital twins 서비스에 대 한 요청의 일부로 전달 됩니다.

인증 단계에는 런타임에 OAuth 2.0 액세스 토큰을 포함 하는 응용 프로그램 요청이 필요 합니다. 응용 프로그램이 [Azure Functions](../azure-functions/functions-overview.md) 앱과 같은 Azure 엔터티 내에서 실행 되는 경우 **관리 id** 를 사용 하 여 리소스에 액세스할 수 있습니다. 관리 되는 id에 대 한 자세한 내용은 다음 섹션을 참조 하세요.

권한 부여 단계를 수행 하려면 Azure 역할을 보안 주체에 할당 해야 합니다. 보안 주체에 할당 된 역할에 따라 보안 주체에 부여 되는 사용 권한이 결정 됩니다. Azure Digital Twins는 azure Digital Twins 리소스에 대 한 권한 집합을 포함 하는 Azure 역할을 제공 합니다. 이러한 역할에 대해서는이 문서의 뒷부분에서 설명 합니다.

Azure에서 지원 되는 역할 및 역할 할당에 대 한 자세한 내용은 Azure RBAC 설명서의 [*여러 역할 이해*](../role-based-access-control/rbac-and-directory-admin-roles.md) 를 참조 하세요.

#### <a name="authentication-with-managed-identities"></a>관리 ID를 사용하여 인증

[Azure 리소스에 대 한 관리](../active-directory/managed-identities-azure-resources/overview.md) 되는 id는 응용 프로그램 코드가 실행 되는 배포와 관련 된 보안 id를 만들 수 있도록 하는 azure 간 기능입니다. 그런 다음 해당 id를 액세스 제어 역할과 연결 하 여 응용 프로그램에 필요한 특정 Azure 리소스에 액세스 하기 위한 사용자 지정 권한을 부여할 수 있습니다.

관리 ID를 사용하는 경우 Azure 플랫폼에서 이 런타임 ID를 관리합니다. ID 자체 또는 액세스해야 하는 리소스에 대한 액세스 키를 애플리케이션 코드나 구성에 저장하고 보호할 필요가 없습니다. Azure App Service 응용 프로그램 내에서 실행 되는 Azure Digital Twins 클라이언트 앱은 SAS 규칙 및 키 또는 다른 액세스 토큰을 처리할 필요가 없습니다. 클라이언트 앱은 Azure Digital Twins 네임 스페이스의 끝점 주소만 필요 합니다. 앱이 연결 되 면 Azure Digital Twins는 관리 되는 엔터티의 컨텍스트를 클라이언트에 바인딩합니다. 관리 되는 id와 연결 되 면 Azure Digital Twins 클라이언트는 모든 권한 있는 작업을 수행할 수 있습니다. 그런 다음 관리 되는 엔터티를 Azure Digital Twins Azure 역할에 연결 하 여 권한 부여를 부여 합니다 (아래 설명 참조).

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>권한 부여: Azure Digital Twins의 Azure 역할

Azure는 Azure Digital Twins 리소스에 대 한 액세스 권한을 부여 하는 다음과 같은 Azure 기본 제공 역할을 제공 합니다.
* *Azure 디지털 쌍 소유자 (미리 보기)* –이 역할을 사용 하 여 Azure Digital twins 리소스에 대 한 모든 권한을 부여 합니다.
* *Azure Digital Twins 판독기 (미리 보기)* –이 역할을 사용 하 여 Azure Digital twins 리소스에 대 한 읽기 전용 액세스 권한을 부여 합니다.

> [!TIP]
> *Azure Digital Twins 판독기 (미리 보기)* 역할은 이제 검색 관계도 지원 합니다.

기본 제공 역할을 정의 하는 방법에 대 한 자세한 내용은 Azure RBAC 설명서의 [*역할 정의 이해*](../role-based-access-control/role-definitions.md) 를 참조 하세요. Azure 사용자 지정 역할을 만드는 방법에 대 한 자세한 내용은 [*azure 사용자 지정 역할*](../role-based-access-control/custom-roles.md)을 참조 하세요.

다음 두 가지 방법으로 역할을 할당할 수 있습니다.
* Azure Portal에서 Azure Digital Twins의 액세스 제어 (IAM) 창을 통해 ( [*AZURE RBAC 및 Azure Portal를 사용 하 여 역할 할당 추가 또는 제거*](../role-based-access-control/role-assignments-portal.md)참조)
* CLI 명령을 통해 역할 추가 또는 제거

이 작업을 수행 하는 방법에 대 한 자세한 단계는 Azure Digital Twins [*자습서: 종단 간 솔루션 연결*](tutorial-end-to-end.md)에서 사용해 보세요.

### <a name="permission-scopes"></a>사용 권한 범위

Azure 역할을 보안 주체에 할당하기 전에 보안 주체에게 부여해야 하는 액세스 범위를 결정합니다. 모범 사례에 따라 가능한 가장 좁은 범위만 부여 하는 것이 좋습니다.

다음 목록에서는 Azure Digital Twins 리소스에 대 한 액세스 범위를 지정할 수 있는 수준을 설명 합니다.
* 모델:이 리소스에 대 한 작업은 Azure Digital Twins에 업로드 된 [모델](concepts-models.md) 에 대 한 제어를 지시 합니다.
* Query digital 쌍 graph:이 리소스에 대 한 작업은 Azure digital 쌍 그래프 내에서 디지털 쌍에 대해 [쿼리 작업](concepts-query-language.md) 을 실행 하는 기능을 결정 합니다.
* 디지털 쌍:이 리소스에 대 한 작업은 쌍 그래프의 [디지털](concepts-twins-graph.md) 쌍에 대 한 CRUD 작업 제어를 제공 합니다.
* 디지털 쌍 관계:이 리소스에 대 한 작업은 쌍 그래프에서 디지털 쌍 간의 [관계](concepts-twins-graph.md) 에 대 한 CRUD 작업 제어를 정의 합니다.
* 이벤트 경로:이 리소스에 대 한 작업은 Azure Digital Twins에서 [이벤트 허브](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md)또는 [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)같은 끝점 서비스로 [이벤트를 라우팅하](concepts-route-events.md) 는 권한을 결정 합니다.

### <a name="troubleshooting-permissions"></a>권한 문제 해결

사용자가 해당 역할에서 허용 하지 않는 작업을 수행 하려고 하면 서비스 요청 읽기에서 오류가 발생할 수 있습니다 `403 (Forbidden)` . 자세한 내용 및 문제 해결 단계는 [*문제 해결: Azure 디지털 Twins 요청 실패 상태: 403 (사용할 수 없음)*](troubleshoot-error-403.md)을 참조 하세요.

## <a name="encryption-of-data-at-rest"></a>미사용 데이터 암호화

Azure Digital Twins는 데이터 센터에서 기록 되는 미사용 데이터 및 전송 중인 데이터의 암호화를 제공 하 고 사용자가 액세스할 때 암호를 해독 합니다. 이 암호화는 Microsoft 관리 암호화 키를 사용 하 여 수행 됩니다.

## <a name="next-steps"></a>다음 단계

* [*방법: 인스턴스 및 인증 설정에서 수행 하는*](how-to-set-up-instance-scripted.md)이러한 개념을 참조 하세요.

* [*방법: 앱 인증 코드 작성*](how-to-authenticate-client.md)의 클라이언트 응용 프로그램 코드에서 이러한 개념과 상호 작용 하는 방법을 참조 하세요.

* [Azure 용 RBAC](../role-based-access-control/overview.md)에 대해 자세히 알아보세요.
