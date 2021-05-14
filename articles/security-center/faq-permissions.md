---
title: Azure Security Center FAQ - 사용 권한에 대한 질문
description: 이 FAQ에서는 위협을 예방, 탐지, 대응하는 데 도움이 되는 제품인 Azure Security Center의 사용 권한에 대한 질문에 답합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 88d6207bf44d1ec04fde4f74ac9295412ea88fbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87824616"
---
# <a name="permissions"></a>권한

## <a name="how-do-permissions-work-in-azure-security-center"></a>Azure Security Center의 사용 권한 적용 방식

Azure Security Center는 Azure에서 사용자, 그룹 및 서비스에 할당할 수 있는 [기본 제공 역할](../role-based-access-control/built-in-roles.md)을 제공하는 [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/role-assignments-portal.md)를 사용합니다.

Security Center는 리소스 구성을 평가하여 보안 문제 및 취약성을 식별합니다. Security Center에서는 리소스가 속한 구독이나 리소스 그룹에 대한 소유자, 참가자 또는 독자 역할을 할당 받을 때 리소스와 관련된 항목만 볼 수 있습니다.

Security Center의 역할 및 허용된 작업에 대한 자세한 내용은 [Azure Security Center의 권한](security-center-permissions.md)을 참조하세요.



## <a name="who-can-modify-a-security-policy"></a>보안 정책을 누가 수정할 수 있나요?

보안 정책을 수정하려면 해당 구독의 보안 관리자이거나 소유자 또는 기여자여야 합니다.

보안 정책을 구성하는 방법을 자세히 알아보려면 [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md)을 참조하세요.