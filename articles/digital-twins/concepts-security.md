---
title: Azure Digital Twins 솔루션에 대한 보안
titleSuffix: Azure Digital Twins
description: Azure Digital Twins를 사용 하 여 보안 모범 사례를 이해 합니다.
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 81a44d4d0025c841cf56e19d6afee5e95bd44a55
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730510"
---
# <a name="secure-azure-digital-twins"></a>Azure Digital Twins 보안

보안을 위해 Azure Digital Twins를 사용 하면 배포의 특정 데이터, 리소스 및 작업에 대 한 정확한 액세스를 제어할 수 있습니다. Azure **RBAC (역할 기반 액세스 제어)** 라고 하는 세분화 된 역할 및 권한 관리 전략을 통해이를 수행 합니다. Azure RBAC의 일반적인 원칙에 대 한 자세한 내용은 [여기](../role-based-access-control/overview.md)를 참조 하세요.

또한 Azure Digital Twins는 미사용 데이터의 암호화를 지원 합니다.

## <a name="roles-and-permissions-with-azure-rbac"></a>Azure RBAC를 사용 하 여 역할 및 사용 권한

Azure RBAC는 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (azure AD)와의 통합을 통해 Azure Digital twins에 제공 됩니다.

Azure RBAC를 사용 하 여 사용자, 그룹 또는 응용 프로그램 서비스 사용자 일 수 있는 *보안 주체* 에 권한을 부여할 수 있습니다. 보안 주체는 Azure AD에서 인증 되며 반환에서 OAuth 2.0 토큰을 받습니다. 이 토큰은 Azure Digital Twins 인스턴스에 대 한 액세스 요청에 권한을 부여 하는 데 사용할 수 있습니다.

### <a name="authentication-and-authorization"></a>인증 및 권한 부여

Azure AD를 사용 하 여 액세스는 2 단계 프로세스로 진행 됩니다. 보안 주체 (사용자, 그룹 또는 응용 프로그램)가 Azure Digital Twins에 액세스를 시도 하는 경우 요청을 *인증* 하 고 *권한을 부여* 해야 합니다. 

1. 먼저, 보안 주체의 id가 *인증* 되 고 OAuth 2.0 토큰이 반환 됩니다.
2. 그런 다음 토큰은 지정 된 리소스에 대 한 액세스 *권한을 부여* 하기 위해 Azure Digital twins 서비스에 대 한 요청의 일부로 전달 됩니다.

인증 단계에는 런타임에 OAuth 2.0 액세스 토큰을 포함 하는 응용 프로그램 요청이 필요 합니다. 응용 프로그램이 [Azure Functions](../azure-functions/functions-overview.md) 앱과 같은 Azure 엔터티 내에서 실행 되는 경우 **관리 id** 를 사용 하 여 리소스에 액세스할 수 있습니다. 관리 되는 id에 대 한 자세한 내용은 다음 섹션을 참조 하세요.

권한 부여 단계를 수행 하려면 Azure 역할을 보안 주체에 할당 해야 합니다. 보안 주체에 할당 된 역할에 따라 보안 주체에 부여 되는 사용 권한이 결정 됩니다. Azure Digital Twins는 azure Digital Twins 리소스에 대 한 권한 집합을 포함 하는 Azure 역할을 제공 합니다. 이러한 역할에 대해서는이 문서의 뒷부분에서 설명 합니다.

Azure에서 지원 되는 역할 및 역할 할당에 대 한 자세한 내용은 Azure RBAC 설명서의 [*여러 역할 이해*](../role-based-access-control/rbac-and-directory-admin-roles.md) 를 참조 하세요.

#### <a name="authentication-with-managed-identities"></a>관리 ID를 사용하여 인증

[Azure 리소스에 대 한 관리](../active-directory/managed-identities-azure-resources/overview.md) 되는 id는 응용 프로그램 코드가 실행 되는 배포와 관련 된 보안 id를 만들 수 있도록 하는 azure 간 기능입니다. 그런 다음 해당 id를 액세스 제어 역할과 연결 하 여 응용 프로그램에 필요한 특정 Azure 리소스에 액세스 하기 위한 사용자 지정 권한을 부여할 수 있습니다.

관리 ID를 사용하는 경우 Azure 플랫폼에서 이 런타임 ID를 관리합니다. ID 자체 또는 액세스해야 하는 리소스에 대한 액세스 키를 애플리케이션 코드나 구성에 저장하고 보호할 필요가 없습니다. Azure App Service 응용 프로그램 내에서 실행 되는 Azure Digital Twins 클라이언트 앱은 SAS 규칙 및 키 또는 다른 액세스 토큰을 처리할 필요가 없습니다. 클라이언트 앱은 Azure Digital Twins 네임 스페이스의 끝점 주소만 필요 합니다. 앱이 연결 되 면 Azure Digital Twins는 관리 되는 엔터티의 컨텍스트를 클라이언트에 바인딩합니다. 관리 되는 id와 연결 되 면 Azure Digital Twins 클라이언트는 모든 권한 있는 작업을 수행할 수 있습니다. 그런 다음 관리 되는 엔터티를 Azure Digital Twins Azure 역할에 연결 하 여 권한 부여를 부여 합니다 (아래 설명 참조).

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>권한 부여: Azure Digital Twins의 Azure 역할

Azure는 Azure Digital Twins [데이터 평면 api](how-to-use-apis-sdks.md#overview-data-plane-apis)에 대 한 액세스 권한을 부여 하기 위한 **두 개의 azure 기본 제공 역할** 을 제공 합니다. 이름 또는 ID를 기준으로 역할을 참조할 수 있습니다.

| 기본 제공 역할 | Description | ID | 
| --- | --- | --- |
| Azure Digital Twins 데이터 소유자 | Azure Digital Twins 리소스에 대 한 모든 권한을 제공 합니다. | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
| Azure Digital Twins 데이터 판독기 | Azure Digital Twins 리소스에 대 한 읽기 전용 액세스를 제공 합니다. | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |

다음 두 가지 방법으로 역할을 할당할 수 있습니다.
* Azure Portal에서 Azure Digital Twins의 액세스 제어 (IAM) 창을 통해 ( [*Azure Portal를 사용 하 여 azure 역할 할당*](../role-based-access-control/role-assignments-portal.md)참조)
* CLI 명령을 통해 역할 추가 또는 제거

이 작업을 수행 하는 방법에 대 한 자세한 단계는 Azure Digital Twins [*자습서: 종단 간 솔루션 연결*](tutorial-end-to-end.md)에서 사용해 보세요.

기본 제공 역할을 정의 하는 방법에 대 한 자세한 내용은 Azure RBAC 설명서의 [*역할 정의 이해*](../role-based-access-control/role-definitions.md) 를 참조 하세요. Azure 사용자 지정 역할을 만드는 방법에 대 한 자세한 내용은 [*azure 사용자 지정 역할*](../role-based-access-control/custom-roles.md)을 참조 하세요.

##### <a name="automating-roles"></a>역할 자동화

자동화 된 시나리오에서 역할을 참조 하는 경우 이름 대신 **id** 를 참조 하는 것이 좋습니다. 이름은 릴리스 사이에서 변경 될 수 있지만 Id는 더 안정적이 지 않으므로 자동화에서 참조할 수 없습니다.

> [!TIP]
> (참조)와 같은 cmdlet을 사용 하 여 역할을 assiging 하는 경우 대신 `New-AzRoleAssignment` 매개 변수를 사용 하 여[](/powershell/module/az.resources/new-azroleassignment) `-RoleDefinitionId` `-RoleDefinitionName` 역할에 대 한 이름 대신 ID를 전달할 수 있습니다.

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

## <a name="managed-identity-for-accessing-other-resources-preview"></a>다른 리소스에 액세스 하기 위한 관리 id (미리 보기)

Azure Digital Twins 인스턴스에 대 한 [Azure Active Directory (AZURE ad)](../active-directory/fundamentals/active-directory-whatis.md) **관리 id** 를 설정 하면 인스턴스가 [Azure Key Vault](../key-vault/general/overview.md)같은 다른 Azure AD 보호 리소스에 쉽게 액세스할 수 있습니다. Id는 Azure 플랫폼에서 관리 하며, 암호를 프로 비전 하거나 회전할 필요가 없습니다. Azure AD에서 관리 되는 id에 대 한 자세한 내용은  [*azure 리소스에 대 한 관리 되는 id*](../active-directory/managed-identities-azure-resources/overview.md)를 참조 하세요. 

Azure는 시스템 할당 및 사용자 할당 이라는 두 가지 관리 id 유형을 지원 합니다. 현재 Azure Digital Twins는 **시스템 할당 id** 만 지원 합니다. 

Azure 디지털 인스턴스에 대해 시스템 할당 관리 id를 사용 하 여 [사용자 정의 끝점](concepts-route-events.md#create-an-endpoint)을 인증할 수 있습니다. Azure Digital Twins는 [이벤트 허브](../event-hubs/event-hubs-about.md) 및 [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)   대상의 끝점과 [](../storage/blobs/storage-blobs-introduction.md)   [배달 못 한 편지 이벤트](concepts-route-events.md#dead-letter-events)의 Azure Storage 컨테이너 끝점에 대 한 시스템 할당 id 기반 인증을 지원 합니다. [Event Grid](../event-grid/overview.md)   현재 끝점은 관리 id에 대해 지원 되지 않습니다.

Azure Digital Twins에 대해 시스템 관리 id를 사용 하도록 설정 하 고이를 사용 하 여 이벤트를 라우팅하는 방법에 대 한 지침은 [*방법: 라우팅 이벤트에 대해 관리 Id 사용 (미리 보기)*](./how-to-enable-managed-identities-portal.md)을 참조 하세요.

## <a name="private-network-access-with-azure-private-link-preview"></a>Azure 개인 링크를 사용한 개인 네트워크 액세스 (미리 보기)

Azure [개인 링크](../private-link/private-link-overview.md) 는 azure [Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md)의 개인 끝점을 통해 azure 리소스 (예: [azure Event Hubs](../event-hubs/event-hubs-about.md), [Azure Storage](../storage/common/storage-introduction.md)및 [Azure Cosmos DB](../cosmos-db/introduction.md))와 azure에서 호스트 되는 고객 및 파트너 서비스에 액세스할 수 있도록 하는 서비스입니다. 

마찬가지로, Azure 디지털 쌍 인스턴스에 대해 개인 끝점을 사용 하 여 가상 네트워크에 있는 클라이언트가 개인 링크를 통해 인스턴스를 안전 하 게 액세스할 수 있도록 할 수 있습니다. 

개인 끝점은 Azure VNet 주소 공간의 IP 주소를 사용 합니다. 개인 네트워크의 클라이언트와 Azure Digital Twins 인스턴스 간의 네트워크 트래픽은 VNet을 통과 하 고 Microsoft 백본 네트워크의 개인 링크를 통해 이동 하 여 공용 인터넷에 대 한 노출을 제거 합니다. 다음은이 시스템의 시각적 표현입니다.

:::image type="content" source="media/concepts-security/private-link.png" alt-text="CityOfTwins 라는 Azure Digital Twins 인스턴스에 개인 링크를 통해 연결 하 여 인터넷/공용 클라우드 액세스가 없는 보호 된 VNET 인 PowerGrid 회사의 네트워크를 보여 주는 다이어그램입니다.":::

Azure digital twins 인스턴스에 대 한 개인 끝점을 구성 하면 azure 디지털 twins 인스턴스를 보호 하 고, 공용 노출을 제거 하 고 VNet에서 데이터 반출을 방지할 수 있습니다.

Azure Digital Twins에 대해 개인 링크를 설정 하는 방법에 대 한 지침은 [*방법: 개인 링크를 사용 하 여 개인 액세스 사용 (미리 보기)*](./how-to-enable-private-link-portal.md)을 참조 하세요.

### <a name="design-considerations"></a>설계 고려 사항 

Azure Digital Twins의 개인 링크를 사용 하 여 작업할 때 고려해 야 할 몇 가지 요소는 다음과 같습니다.
* **가격** 책정에 대 한 자세한 내용은  [Azure 개인 링크 가격 책정](https://azure.microsoft.com/pricing/details/private-link)을 참조 하세요. 
* **지역 가용성**: Azure Digital twins의 경우이 기능은 azure 디지털 쌍을 사용할 수 있는 모든 azure 지역에서 사용할 수 있습니다. 
* **Azure Digital Twins 인스턴스당 최대 개인 끝점 수**: 10

개인 링크의 제한에 대 한 자세한 내용은 [Azure 개인 링크 설명서: 제한 사항](../private-link/private-link-service-overview.md#limitations)을 참조 하세요.

## <a name="service-tags"></a>서비스 태그

**서비스 태그** 는 지정 된 Azure 서비스에서 IP 주소 접두사 그룹을 나타냅니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트하여 네트워크 보안 규칙을 자주 업데이트할 때 발생하는 복잡성을 최소화합니다. 서비스 태그에 대 한 자세한 내용은  [*Virtual network 태그*](../virtual-network/service-tags-overview.md)를 참조 하세요. 

 [](../virtual-network/network-security-groups-overview.md#security-rules)   보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용 하 여 네트워크 보안 그룹 또는 [Azure 방화벽](../firewall/service-tags.md)에서 네트워크 액세스 제어를 정의 하는 데 서비스 태그를 사용할 수 있습니다. 규칙의 적절 한 *원본* 또는 대상 필드에서 서비스 태그 이름 (이 경우 **AzureDigitalTwins**)을 지정 하 여    **   해당 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다. 

**AzureDigitalTwins** service 태그의 세부 정보는 다음과 같습니다.

| 태그 | 목적 | 인바운드 또는 아웃바운드를 사용할 수 있나요? | 지역 범위를 지원할 수 있나요? | Azure Firewall에서 사용할 수 있나요? |
| --- | --- | --- | --- | --- |
| AzureDigitalTwins | Azure Digital Twins<br>참고:이 태그 또는이 태그에 포함 된 IP 주소는 [이벤트 경로](concepts-route-events.md)에 대해 구성 된 끝점에 대 한 액세스를 제한 하는 데 사용할 수 있습니다. | 인바운드 | 예 | 예 |

### <a name="using-service-tags-for-accessing-event-route-endpoints"></a>서비스 태그를 사용 하 여 이벤트 경로 끝점 액세스 

Azure Digital Twins에서 서비스 태그를 사용 하 여 [이벤트 경로](concepts-route-events.md) 끝점에 액세스 하는 단계는 다음과 같습니다.

1. 먼저 Azure ip 범위 및 서비스 태그를 표시 하는이 JSON 파일 참조를 다운로드 합니다 ( [*AZURE Ip 범위 및 서비스 태그*](https://www.microsoft.com/download/details.aspx?id=56519)). 

2. JSON 파일에서 "AzureDigitalTwins" IP 범위를 찾습니다.  

3. 해당 리소스에 대 한 IP 필터를 설정 하는 방법을 보려면 끝점에 연결 된 외부 리소스의 설명서 (예: [Event Grid](../event-grid/overview.md), [이벤트 허브](../event-hubs/event-hubs-about.md), [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)또는 [배달 못한 편지 이벤트](concepts-route-events.md#dead-letter-events) [Azure Storage](../storage/blobs/storage-blobs-overview.md) )를 참조 하세요.

4. *2 단계의* ip 범위를 사용 하 여 외부 리소스에 대 한 ip 필터를 설정 합니다.  

5. 필요에 따라 주기적으로 IP 범위를 업데이트 합니다. 범위는 시간이 지남에 따라 변경 될 수 있으므로 정기적으로 확인 하 고 필요할 때 새로 고치는 것이 좋습니다. 이러한 업데이트의 빈도는 달라질 수 있지만 매주 한 번 확인 하는 것이 좋습니다.

## <a name="encryption-of-data-at-rest"></a>저장소 데이터 암호화

Azure Digital Twins는 데이터 센터에서 기록 되는 미사용 데이터 및 전송 중인 데이터의 암호화를 제공 하 고 사용자가 액세스할 때 암호를 해독 합니다. 이 암호화는 Microsoft에서 관리 하는 암호화 키를 사용 하 여 수행 됩니다.

## <a name="cross-origin-resource-sharing-cors"></a>CORS(크로스-원본 자원 공유)

Azure Digital Twins는 현재 **CORS (크로스-원본 자원 공유)** 를 지원 하지 않습니다. 따라서 브라우저 앱, [API Management (APIM)](../api-management/api-management-key-concepts.md) 인터페이스 또는 [Power Apps](/powerapps/powerapps-overview) 커넥터에서 REST API를 호출 하는 경우 정책 오류가 표시 될 수 있습니다.

이 오류를 해결 하려면 다음 작업 중 하나를 수행 하면 됩니다.
* 메시지에서 CORS 헤더를 제거 합니다 `Access-Control-Allow-Origin` . 이 헤더는 응답을 공유할 수 있는지 여부를 나타냅니다. 
* 또는 CORS 프록시를 만들고 Azure Digital Twins에서 요청을 REST API 요청 합니다. 

## <a name="next-steps"></a>다음 단계

* [*방법: 인스턴스 및 인증 설정에서 수행 하는*](how-to-set-up-instance-portal.md)이러한 개념을 참조 하세요.

* [*방법: 앱 인증 코드 작성*](how-to-authenticate-client.md)의 클라이언트 응용 프로그램 코드에서 이러한 개념과 상호 작용 하는 방법을 참조 하세요.

* [AZURE RBAC](../role-based-access-control/overview.md)에 대해 자세히 알아보세요.