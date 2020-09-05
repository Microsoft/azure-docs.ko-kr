---
title: Privileged Identity Management (PIM)에서 권한 있는 액세스 그룹 할당에 대 한 감사 보고서 보기-Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM)에서 권한 있는 액세스 그룹 할당에 대 한 활동 및 감사 기록을 봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94dd5c2579eb7ce96dea70c6354df8ec84125bd9
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141672"
---
# <a name="audit-activity-history-for-privileged-access-group-assignments-preview-in-privileged-identity-management"></a>Privileged Identity Management의 권한 있는 액세스 그룹 할당 (미리 보기)에 대 한 작업 기록 감사

PIM (Privileged Identity Management)을 사용 하 여 Azure Active Directory (Azure AD) 조직 내의 Azure 권한 있는 액세스 그룹 구성원 및 소유자에 대 한 활동, 활성화 및 감사 기록을 볼 수 있습니다.

> [!NOTE]
> 조직에서 [Azure 위임 된 리소스 관리](../../lighthouse/concepts/azure-delegated-resource-management.md)를 사용 하는 서비스 공급자에 대해 아웃소싱 된 관리 기능을 사용 하는 경우 해당 서비스 공급자가 승인한 역할 할당은 여기에 표시 되지 않습니다.

권한 있는 액세스 그룹의 감사 기록을 보려면 다음 단계를 따르세요.

## <a name="view-resource-audit-history"></a>리소스 감사 기록 보기

**리소스 감사** 는 권한 있는 액세스 그룹과 연결 된 모든 활동에 대 한 보기를 제공 합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **권한 있는 액세스 그룹 (미리 보기)** 을 선택 합니다.

1. 감사 기록을 보려는 권한 있는 액세스 그룹을 선택 합니다.

1. **활동**에서 **리소스 감사**를 선택 합니다.

1. 미리 정의된 날짜 또는 사용자 지정 범위를 사용하여 기록을 필터링합니다.

    ![필터를 사용 하는 리소스 감사 목록](media/groups-audit/groups-resource-audit.png)

## <a name="view-my-audit"></a>내 감사 보기

**내 감사** 를 사용 하 여 권한 있는 액세스 그룹에 대 한 개인 역할 활동을 볼 수 있습니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **권한 있는 액세스 그룹 (미리 보기)** 을 선택 합니다.

1. 감사 기록을 보려는 권한 있는 액세스 그룹을 선택 합니다.

1. **활동**에서 **내 감사**를 선택 합니다.

1. 미리 정의된 날짜 또는 사용자 지정 범위를 사용하여 기록을 필터링합니다.

    ![현재 사용자에 대 한 감사 목록](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 그룹 멤버 자격 및 소유권을 할당 합니다.](groups-assign-member-owner.md)
- [PIM의 권한 있는 액세스 그룹에 대 한 요청 승인 또는 거부](groups-approval-workflow.md)
- [PIM에서 Azure AD 역할에 대 한 감사 기록 보기](groups-audit.md)
