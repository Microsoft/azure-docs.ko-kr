---
title: 포함 파일
description: 포함 파일
services: azure-resource-manager
author: rockboyfor
manager: digimobile
ms.service: azure-resource-manager
ms.topic: include
origin.date: 02/16/2018
ms.date: 04/30/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: f77a5d482c3f8632a3d86bd8e027fbb4418168c3
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122909"
---
조직의 사용자에게 이러한 리소스에 대한 적절한 수준의 액세스 권한이 있는지 확인하려고 합니다. 사용자에게 무제한 액세스 권한은 부여하지 않으면서 동시에 사용자가 작업을 수행할 수 있는지 확인해야 합니다. RBAC(역할 기반 액세스 제어)를 통해 범위에서 특정 작업을 완료할 수 있는 권한이 있는 사용자를 관리할 수 있습니다. 역할은 허용되는 작업의 집합을 정의합니다. 역할을 범위에 할당하고, 범위에 대해 해당 역할에 속하는 사용자를 지정합니다.

액세스 제어 전략을 계획할 때 작업을 완료하기 위해 필요한 최소한의 권한만을 사용자에게 부여합니다. 다음 그림에서는 RBAC를 할당하기 위한 제안된 패턴을 보여 줍니다.

![범위](./media/resource-manager-governance-rbac/role-examples.png)

모든 리소스 유형에 적용되는 3가지 역할(소유자, 참여자 및 읽기 권한자)이 있습니다. 소유자 역할에 할당된 모든 계정은 엄격하게 제어해야 하며 거의 사용되지 않아야 합니다. 솔루션의 상태를 관찰해야 하는 사용자에게는 읽기 역할을 부여해야 합니다.

대부분 사용자는 구독 또는 리소스 그룹 수준의 [리소스 관련 역할](../articles/role-based-access-control/built-in-roles.md) 또는 [사용자 정의 역할](../articles/role-based-access-control/custom-roles.md)이 부여됩니다. 이러한 역할은 허용된 작업을 엄격하게 정의합니다. 이러한 역할에 사용자를 할당하면 너무 많은 제어를 허용하지 않고도 사용자에 필요한 액세스 권한을 부여합니다. 하나의 계정을 둘 이상의 역할에 할당할 수 있으며, 해당 사용자는 역할의 결합된 사용 권한을 가지게 됩니다. 리소스 수준의 액세스 권한 부여는 종종 사용자에 대해 너무 제한적이지만 특정 작업을 위해 고안된 자동화된 프로세스에 작동할 수 있습니다.

### <a name="who-can-assign-roles"></a>역할을 할당할 수 있는 사람

역할 할당을 만들고 제거하려면 사용자에게 `Microsoft.Authorization/roleAssignments/*` 액세스가 있어야 합니다. 이 액세스는 소유자 또는 사용자 액세스 관리자 역할을 통해 부여됩니다.
<!--ms.date: 04/30/2018-->