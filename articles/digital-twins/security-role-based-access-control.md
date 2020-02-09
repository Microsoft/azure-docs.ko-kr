---
title: 역할 기반 액세스 제어 이해-Azure Digital Twins | Microsoft Docs
description: Azure Digital Twins의 역할 기반 액세스 제어 및 관리 권한에 대해 알아봅니다.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: feda4b3a7f21b581fb4f08aec013f87c0fabb7e5
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044949"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Azure Digital Twins의 역할 기반 액세스 제어

Azure Digital Twins를 사용 하면 공간 그래프의 특정 데이터, 리소스 및 작업에 대한 정확한 액세스를 제어할 수 있습니다. 이를 위해 [역할 기반 액세스 제어](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) 라고 하는 세분화 된 역할 및 권한 관리를 통해 수행 됩니다. RBAC는 _역할_ 및 _역할 할당_으로 구성됩니다. 역할은 권한 수준을 식별합니다. 역할 할당은 역할을 사용자나 디바이스와 연결합니다.

RBAC를 사용하여 권한을 다음에 부여할 수 있습니다.

- 사용자입니다.
- 디바이스
- 서비스 주체
- 사용자 정의 함수
- 도메인에 속한 모든 사용자
- 테넌트

액세스 수준을 세부적으로 조정할 수도 있습니다.

RBAC는 권한이 공간 그래프에서 상속된다는 점에서 고유합니다.

## <a name="what-can-i-do-with-rbac"></a>RBAC로 무엇을 할 수 있나요?

개발자는 RBAC를 다음 용도로 사용할 수 있습니다.

- 사용자에게 전체 건물에 대해서나 특정 방이나 층에 대해서만 디바이스를 관리하는 기능을 부여합니다.
- 관리자에게 전체 그래프의 모든 공간 그래프 노드에 대한 전역 액세스 권한이나 그래프의 특정 섹션에 대한 권한을 부여합니다.
- 지원 전문가에게 액세스 키를 제외하고 그래프에 대한 읽기 액세스 권한을 부여합니다.
- 도메인의 모든 멤버에게 모든 그래프 개체에 대한 읽기 액세스 권한을 부여합니다.

## <a name="rbac-best-practices"></a>RBAC 모범 사례

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>역할

### <a name="role-definitions"></a>역할 정의

역할 정의는 사용 권한 및 역할을 구성하는 기타 특성의 컬렉션입니다. 역할 정의에는 해당 역할을 지닌 모든 개체가 수행할 수 있는 *만들기*, *읽기*, *업데이트* 및 *삭제*를 포함한 허용되는 작업이 있습니다. 또한 사용 권한이 적용 되는 개체 유형을 지정 합니다.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> 이전 역할에 대한 전체 정의를 검색하려면 시스템/역할 API를 쿼리합니다.
> 자세한 내용은 [역할 할당 만들기 및 관리](./security-create-manage-role-assignments.md#retrieve-all-roles)를 참조하세요.

### <a name="object-identifier-types"></a>개체 식별자 유형

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> [역할 할당 만들기 및 관리](./security-create-manage-role-assignments.md#grant-permissions-to-your-service-principal)를 참조하여 서비스 주체에 권한을 부여하는 방법을 알아봅니다.

다음 참조 설명서 문서는 다음을 설명합니다.

- [사용자에 대한 개체 ID를 쿼리하는](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0) 방법.
- [서비스 주체에 대한 개체 ID를 가져오는](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal) 방법.
- [Azure AD 테넌트에 대한 개체 ID를 검색하는](../active-directory/develop/quickstart-create-new-tenant.md) 방법.

## <a name="role-assignments"></a>역할 할당

Azure Digital Twins 역할 할당은 개체(사용자, Azure AD 테넌트 등)를 역할 및 공간에 연결합니다. 권한은 해당 공간에 속하는 모든 개체에 부여됩니다. 공간은 그 아래의 전체 공간 그래프를 포함합니다.

예를 들어, 사용자에게 건물을 나타내는 공간 그래프의 루트 노드에 대한 `DeviceInstaller` 역할로 역할 할당을 부여한 경우 그러면 이 사용자는 해당 노드와, 건물 내 모든 다른 하위 공간에 대해 디바이스를 읽고 업데이트할 수 있습니다.

받는 사람에게 권한을 부여하려면 역할 할당을 만듭니다. 사용 권한을 취소하려면 역할 할당을 제거합니다.

>[!IMPORTANT]
> 역할 할당에 대한 자세한 내용은 [역할 할당 만들기 및 관리](./security-create-manage-role-assignments.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure Digital Twins 역할 할당 만들기 및 관리에 대한 자세한 내용은 [역할 할당 만들기 및 관리](./security-create-manage-role-assignments.md)를 참조하세요.

- [Azure 용 RBAC](https://docs.microsoft.com/azure/role-based-access-control/)에 대해 자세히 알아보세요.
