---
title: 청사진 운영자를 위한 환경 설정
description: Azure 기본 제공 역할 청사진에 사용 하도록 Azure 환경을 구성 하는 방법을 알아봅니다.
ms.date: 08/27/2020
ms.topic: how-to
ms.openlocfilehash: ef911553b4985a6497fb397a1ae405387c6539fb
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051460"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>청사진 운영자를 위한 환경 구성

청사진 정의 및 청사진 할당의 관리를 다른 팀에 할당할 수 있습니다. 운영 팀은 중앙에서 제어 되는 중앙 집중식 청사진 정의의 할당을 관리 하는 동안 청사진 정의의 수명 주기 관리를 담당 하는 것이 일반적입니다.

**청사진 연산자** 기본 제공 RBAC (역할 기반 액세스 제어)는 이러한 유형의 시나리오에서 사용 하도록 특별히 설계 되었습니다. 역할을 사용 하면 작업 유형 팀에서 조직 청사진 정의의 할당을 관리할 수 있지만 수정할 수는 없습니다. 이렇게 하려면 Azure 환경에서 몇 가지 구성이 필요 합니다 .이 문서에서는 필요한 단계를 설명 합니다.

## <a name="grant-permission-to-the-blueprint-operator"></a>청사진 연산자에 사용 권한 부여

첫 번째 단계는 청사진을 할당할 계정 또는 보안 그룹 (권장)에 **청사진 운영자** 역할을 부여 하는 것입니다. 이 작업은 운영 팀에서 청사진 할당 액세스 권한을 보유 해야 하는 관리 그룹 및 구독을 모두 포함 하는 관리 그룹 계층 구조의 최상위 수준에서 수행 해야 합니다. 이러한 사용 권한을 부여할 때 최소 권한의 원칙을 따르는 것이 좋습니다.

1. 바람직하지 [보안 그룹 만들기 및 멤버 추가](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. 계정 또는 보안 그룹에 **청사진 연산자** 의 [역할 할당을 추가](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) 합니다.

## <a name="user-assign-managed-identity"></a>사용자 할당 관리 id

청사진 정의는 시스템 할당 또는 사용자 할당 관리 id 중 하나를 사용할 수 있습니다. 그러나 **청사진 연산자** 역할을 사용 하는 경우에는 사용자 할당 관리 id를 사용 하도록 청사진 정의를 구성 해야 합니다. 또한 **청사진 운영자** 역할을 부여 하는 계정 또는 보안 그룹에는 사용자 할당 관리 id에 대해 **관리 id 운영자** 역할을 부여 해야 합니다. 이 권한이 없으면 사용 권한이 부족 하 여 청사진 할당이 실패 합니다.

1. 할당 된 청사진에서 사용할 [사용자 할당 관리 Id 만들기](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)

1. **관리 Id 운영자** 의 [역할 할당을](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) 계정 또는 보안 그룹에 추가 합니다. 역할 할당의 범위를 새 사용자 할당 관리 id로 지정 합니다.

1. **청사진 연산자**는 새로운 사용자 할당 관리 id를 사용 하는 [청사진을 할당](../create-blueprint-portal.md#assign-a-blueprint) 합니다.

## <a name="next-steps"></a>다음 단계

- [청사진 수명 주기](../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결