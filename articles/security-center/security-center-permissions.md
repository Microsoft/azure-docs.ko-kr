---
title: Azure Security Center의 권한 | Microsoft Docs
description: 이 문서는 Azure Security Center가 역할 기반 액세스 제어를 사용하여 사용자에게 권한을 할당하는 방법과 각 역할에 대해 허용된 작업을 식별하는 방법에 대해 설명합니다.
services: security-center
cloud: na
documentationcenter: na
author: rkarlin
manager: barbkess
ms.assetid: ''
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: aa6c154e14fbf5291e2493f4f27b9d4501ba18f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60905622"
---
# <a name="permissions-in-azure-security-center"></a>Azure Security Center의 권한

Azure Security Center는 Azure에서 사용자, 그룹 및 서비스에 [기본 제공 역할](../role-based-access-control/built-in-roles.md)을 제공하는 [RBAC(역할 기반 Access Control)](../role-based-access-control/role-assignments-portal.md)를 사용합니다.

Security Center는 리소스 구성을 평가하여 보안 문제 및 취약성을 식별합니다. Security Center에서는 리소스가 속한 구독이나 리소스 그룹에 대한 소유자, 참가자 또는 독자 역할을 할당 받을 때 리소스와 관련된 항목만 볼 수 있습니다.

이러한 역할 외에도 두 개의 특정한 Security Center 역할이 있습니다.

* **보안 읽기 권한자**: 이 역할에 속하는 사용자는 Security Center에 대한 보기 권한이 있습니다. 사용자가 권장 사항, 경고, 보안 정책 및 보안 상태를 볼 수 있지만 변경할 수는 없습니다.
* **보안 관리자**: 이 역할에 속하는 사용자는 보안 읽기 권한자와 동일한 권한이 있으며 보안 정책을 업데이트하고 경고 및 권장 사항을 해제할 수도 있습니다.

> [!NOTE]
> 보안 역할(보안 읽기 권한자 및 보안 관리자)은 Security Center에만 액세스할 수 있습니다. 보안 역할은 Storage, 웹 및 모바일, 사물 인터넷 등 Azure의 다른 서비스 영역에 액세스할 수 없습니다.
>
>

## <a name="roles-and-allowed-actions"></a>역할 및 허용되는 작업

다음 표는 Security Center의 역할 및 허용되는 작업을 보여 줍니다. X는 작업이 해당 역할에 대해 허용된다는 것을 나타냅니다.

| 역할 | 보안 정책 편집 | 리소스에 보안 권장 사항 적용 | 경고 및 권장 사항 해제 | 경고 및 권장 사항 보기 |
|:--- |:---:|:---:|:---:|:---:|
| 구독 소유자 | X | X | X | X |
| 구독 참가자 | -- | X | X | X |
| 리소스 그룹 소유자 | -- | X | -- | X |
| 리소스 그룹 참가자 | -- | X | -- | X |
| 판독기 | -- | -- | -- | X |
| 보안 관리자 | X | -- | X | X |
| 보안 판독기 | -- | -- | -- | X |

> [!NOTE]
> 사용자가 자신의 작업을 완료하는 데 필요한 최소한의 역할을 할당하는 것이 좋습니다. 예를 들어, 리소스의 보안 상태에 대한 정보를 보기만 하고 권장 사항 적용이나 정책 편집 등의 조치는 취하지 않는 사용자에게 독자 역할을 할당합니다.
>
>

## <a name="next-steps"></a>다음 단계
이 문서는 Security Center에서 RBAC를 사용하여 사용자에게 사용 권한을 할당하고 각 역할에 대해 허용된 작업을 식별하는 방법에 대해 설명합니다. 이제 구독의 보안 상태를 모니터링하고, 보안 정책을 편집하고, 권장 사항을 적용하는 데 필요한 역할 할당에 익숙해졌으므로 다음을 수행하는 방법을 알아봅니다.

- [Security Center에서 보안 정책 설정](tutorial-security-policy.md)
- [Security Center의 보안 권장 사항 관리](security-center-recommendations.md)
- [Azure 리소스의 보안 상태 모니터링](security-center-monitoring.md)
- [Security Center에서 보안 경고 관리 및 응답](security-center-managing-and-responding-alerts.md)
- [파트너 보안 솔루션 모니터링](security-center-partner-solutions.md)
