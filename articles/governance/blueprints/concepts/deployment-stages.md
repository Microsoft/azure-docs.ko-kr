---
title: 청사진 배포 단계
description: Azure Blueprint는 서비스 배포 중 거치는 단계에 알아봅니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d7000813b51fb9c9aae9a21cbded3ae0028e83f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684695"
---
# <a name="stages-of-a-blueprint-deployment"></a>청사진 배포 단계

청사진을 배포 때 일련의 동작을 청사진에 정의 된 리소스를 배포 하려면 Azure 청사진 서비스에서 가져옵니다. 이 문서에서는 각 단계 수반 됩니다 하는 방법에 대 한 세부 정보를 제공 합니다.

구독에 blueprint를 할당 하 여 청사진 배포가 트리거됩니다 또는 [기존 할당을 업데이트](../how-to/update-existing-assignments.md)합니다. 청사진을 배포 하는 동안 다음과 같은 개략적인 단계를 사용 합니다.

> [!div class="checklist"]
> - 청사진은 소유자 권한 부여
> - Blueprint 할당 개체가 만들어집니다.
> - 선택 사항-청사진 만듭니다 **시스템 할당** 관리 id
> - 청사진 아티팩트를 배포 하는 관리 되는 id
> - Blueprint 서비스 및 **시스템 할당** 관리 id 권한이 취소 됩니다.

## <a name="blueprints-granted-owner-rights"></a>청사진은 소유자 권한 부여

Azure 청사진 서비스 주체에는 할당 된 구독 또는 구독 소유자 권한이 부여 됩니다. 부여 된 역할을 만들고 나중에 해지 청사진을 허용 합니다 [관리 시스템 할당 id](../../../active-directory/managed-identities-azure-resources/overview.md)합니다.

할당 포털을 통해 이루어지는 경우 권한은 자동으로 부여 됩니다. 그러나 할당 가능 해야 하는 권한 부여 REST API를 통해 수행 되는 경우 수행 하는 별도 API를 사용 하 여 호출 합니다. Azure Blueprint AppId는 `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`, 하지만 서비스 주체의 테 넌 트에 따라 다릅니다. 사용 하 여 [Azure Active Directory Graph API](../../../active-directory/develop/active-directory-graph-api.md) REST 끝점 [servicePrincipals](/graph/api/resources/serviceprincipal) 서비스 주체를 가져오려고 합니다. Azure 청사진을 부여 합니다는 _소유자_ 을 통해 역할을 [포털](../../../role-based-access-control/role-assignments-portal.md)를 [Azure CLI](../../../role-based-access-control/role-assignments-cli.md)를 [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), [REST API](../../../role-based-access-control/role-assignments-rest.md), 또는 [Resource Manager 템플릿을](../../../role-based-access-control/role-assignments-template.md)합니다.

청사진 서비스 리소스를 직접 배포 하지 않습니다.

## <a name="the-blueprint-assignment-object-is-created"></a>Blueprint 할당 개체가 만들어집니다.

사용자, 그룹 또는 서비스 주체 구독에 blueprint를 할당합니다. Blueprint가 할당 하는 구독 수준에서 할당 개체가 있습니다. 배포에서 만든 리소스를 배포 엔터티의 컨텍스트에서 수행 되지 않습니다.

형식의 blueprint 할당을 만드는 동안 [관리 되는 id](../../../active-directory/managed-identities-azure-resources/overview.md) 을 선택 합니다. 기본값은는 **시스템 할당** 관리 id입니다. A **사용자 할당** 관리 id를 선택할 수 있습니다. 사용 하는 경우는 **사용자 할당** 관리 id를 정의 및 blueprint 할당을 만들기 전에 권한을 부여 해야 합니다.

## <a name="optional---blueprints-creates-system-assigned-managed-identity"></a>선택 사항-청사진 관리 되는 id 시스템 할당을 만듭니다.

때 [관리 시스템 할당 id](../../../active-directory/managed-identities-azure-resources/overview.md) 선택 할당 중 청사진 id가 생성 및 관리 되는 id를 부여 합니다 [소유자](../../../role-based-access-control/built-in-roles.md#owner) 역할입니다. 경우는 [기존 할당 업그레이드](../how-to/update-existing-assignments.md), 청사진 이전에 만든된 관리 되는 id를 사용 합니다.

Blueprint 할당에 관련 된 관리 되는 id 청사진에 정의 된 리소스를 배포 하거나 다시 배포 됩니다. 이 디자인 부주의 하 게 서로 방해 하는 할당을 피할 수 있습니다.
이 디자인도 지원 합니다 [리소스 잠금](./resource-locking.md) blueprint에서 배포 된 각 리소스의 보안을 제어 하 여 기능 합니다.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>청사진 아티팩트를 배포 하는 관리 되는 id

관리 되는 id 다음에 정의 된 blueprint 내의 아티팩트의 Resource Manager 배포를 트리거합니다 [시퀀싱 순서](./sequencing-order.md)합니다. 올바른 순서로 배포 된 아티팩트가 다른 아티팩트에 종속 되도록 순서를 조정할 수 있습니다.

배포에서 액세스 오류가 종종 관리 서비스 id에 부여 된 액세스 수준 결과입니다. 청사진 서비스 보안 수명 주기를 관리 합니다 **시스템 할당** 관리 id입니다. 그러나 사용자가 권한 및의 수명 주기 관리를 담당를 **사용자 할당** 관리 id입니다.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Blueprint 서비스 및 관리 되는 id 시스템 할당 권한을 철회 되 면

청사진의 권한을 해지 배포가 완료 되 면 합니다 **시스템 할당** 구독에서 관리 되는 id입니다. 그런 다음 청사진 서비스 구독에서 해당 권한을 취소합니다. 청사진 구독에서 영구 소유자가 되는 것 방지 하는 권한을 제거 합니다.

## <a name="next-steps"></a>다음 단계

- [정적 및 동적 매개 변수](parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결