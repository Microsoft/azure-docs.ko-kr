---
title: 청사진 배포 단계
description: 청사진 할당을 만드는 동안 Azure 청사진 서비스가 거치는 보안 및 아티팩트 관련 단계를 알아봅니다.
ms.date: 08/27/2020
ms.topic: conceptual
ms.openlocfilehash: 73d5d67cf77d4ccc48517c895630b8299081e2e0
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89049777"
---
# <a name="stages-of-a-blueprint-deployment"></a>청사진 배포 단계

청사진을 배포할 때 Azure 청사진 서비스에서 청사진에 정의 된 리소스를 배포 하는 일련의 작업을 수행 합니다. 이 문서에서는 각 단계에 대 한 세부 정보를 제공 합니다.

청사진 배포는 구독에 청사진을 할당 하거나 [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방식으로 트리거됩니다. 배포 하는 동안 Azure 청사진은 다음과 같은 개략적인 단계를 수행 합니다.

> [!div class="checklist"]
> - Azure 청사진에 소유자 권한 부여
> - 청사진 할당 개체가 만들어집니다.
> - 선택 사항-Azure 청사진은 **시스템 할당** 관리 id를 만듭니다.
> - 관리 id가 청사진 아티팩트를 배포 합니다.
> - Azure 청사진 서비스 및 **시스템 할당** 관리 id 권한은 해지 됩니다.

## <a name="azure-blueprints-granted-owner-rights"></a>Azure 청사진에 소유자 권한 부여

[시스템 할당 관리 id](../../../active-directory/managed-identities-azure-resources/overview.md) 관리 id를 사용 하는 경우 Azure 청사진 서비스 주체에 게 할당 된 구독 또는 구독에 대 한 소유자 권한이 부여 됩니다. 부여 된 역할을 통해 Azure 청사진은 **시스템 할당** 관리 id를 만들고 나중에 해지할 수 있습니다. **사용자 할당** 관리 id를 사용 하는 경우 Azure 청사진 서비스 주체는 구독에 대 한 소유자 권한이 부여 되지 않으며 필요 하지 않습니다.

포털을 통해 할당을 수행 하는 경우에는 권한이 자동으로 부여 됩니다. 그러나 REST API를 통해 할당을 수행 하는 경우 별도의 API 호출을 사용 하 여 권한을 부여 해야 합니다. Azure 청사진 AppId는 `f71766dc-90d9-4b7d-bd9d-4499c4331c3f` 이지만 서비스 사용자는 테 넌 트에 따라 다릅니다. [Azure Active Directory Graph API](../../../active-directory/develop/active-directory-graph-api.md) 및 REST 끝점 [serviceprincipals](/graph/api/resources/serviceprincipal) 을 사용 하 여 서비스 주체를 가져옵니다. 그런 다음 [포털](../../../role-based-access-control/role-assignments-portal.md), [Azure CLI](../../../role-based-access-control/role-assignments-cli.md), [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), [REST API](../../../role-based-access-control/role-assignments-rest.md)또는 [Azure Resource Manager 템플릿을](../../../role-based-access-control/role-assignments-template.md)통해 Azure 청사진에 _소유자_ 역할을 부여 합니다.

Azure 청사진 서비스는 리소스를 직접 배포 하지 않습니다.

## <a name="the-blueprint-assignment-object-is-created"></a>청사진 할당 개체가 만들어집니다.

사용자, 그룹 또는 서비스 사용자가 청사진을 구독에 할당 합니다. 할당 개체는 청사진이 할당 된 구독 수준에 있습니다. 배포를 통해 생성 된 리소스는 배포 하는 엔터티의 컨텍스트에서 수행 되지 않습니다.

청사진 할당을 만드는 동안 [관리 id](../../../active-directory/managed-identities-azure-resources/overview.md) 유형이 선택 됩니다. 기본값은 **시스템 할당** 관리 id입니다. **사용자 할당** 관리 id를 선택할 수 있습니다. **사용자 할당** 관리 id를 사용 하는 경우 청사진 할당을 만들기 전에 해당 id를 정의 하 고 권한을 부여 해야 합니다. [소유자](../../../role-based-access-control/built-in-roles.md#owner) 및 [청사진 연산자](../../../role-based-access-control/built-in-roles.md#blueprint-operator) 기본 제공 역할에는 `blueprintAssignment/write` **사용자 할당** 관리 id를 사용 하는 할당을 만드는 데 필요한 권한이 있습니다.

## <a name="optional---azure-blueprints-creates-system-assigned-managed-identity"></a>선택 사항-Azure 청사진은 시스템 할당 관리 id를 만듭니다.

할당 중에 [시스템 할당 관리 id](../../../active-directory/managed-identities-azure-resources/overview.md) 를 선택 하면 Azure 청사진에서 id를 만들고 관리 id에 [소유자](../../../role-based-access-control/built-in-roles.md#owner) 역할을 부여 합니다. [기존 할당이 업그레이드 되](../how-to/update-existing-assignments.md)면 Azure 청사진은 이전에 만든 관리 id를 사용 합니다.

청사진 할당과 관련 된 관리 id는 청사진에 정의 된 리소스를 배포 하거나 다시 배포 하는 데 사용 됩니다. 이 디자인은 실수로 서로 방해 하는 할당을 방지 합니다.
또한이 디자인은 청사진에서 배포 된 각 리소스의 보안을 제어 하 여 [리소스 잠금](./resource-locking.md) 기능을 지원 합니다.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>관리 id가 청사진 아티팩트를 배포 합니다.

그런 다음 관리 되는 id는 청사진 내에서 정의 된 [시퀀싱 순서로](./sequencing-order.md)아티팩트의 리소스 관리자 배포를 트리거합니다. 다른 아티팩트에 종속 된 아티팩트를 올바른 순서로 배포 하도록 순서를 조정할 수 있습니다.

배포에의 한 액세스 오류는 관리 되는 id에 부여 된 액세스 수준으로 인해 발생 하는 경우가 많습니다. Azure 청사진 서비스는 **시스템 할당** 관리 id의 보안 수명 주기를 관리 합니다. 그러나 사용자는 **사용자 할당** 관리 id의 권한 및 수명 주기를 관리 해야 합니다.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>청사진 서비스 및 시스템 할당 관리 id 권한이 해지 됩니다.

배포가 완료 되 면 Azure 청사진은 구독에서 **시스템 할당** 관리 id의 권한을 해지 합니다. 그런 다음 Azure 청사진 서비스는 구독에서 해당 권한을 해지 합니다. 권한 제거를 통해 Azure 청사진은 구독의 영구 소유자가 되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [정적 및 동적 매개 변수](./parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](./sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](./resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결
