---
title: RBAC를 사용하여 Azure Portal 대시보드 공유 | Microsoft Docs
description: 이 문서에서는 역할 기반 Access Control을 사용하여 Azure Portal에서 대시보드를 공유하는 방법을 설명합니다.
services: azure-portal
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/01/2016
ms.author: tomfitz
ms.openlocfilehash: fbbc8a4f636a95d18baa0dc5de541279ce36789b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551997"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>역할 기반 Access Control을 사용하여 Azure 대시보드 공유
대시보드를 구성한 후에는 이를 게시하고 조직 내의 다른 사용자와 공유할 수 있습니다. 다른 사람이 Azure [역할 기반 Access Control](../role-based-access-control/role-assignments-portal.md)을 사용하여 대시보드를 볼 수 있도록 허용합니다. 역할에 사용자 또는 사용자 그룹을 할당하고 해당 역할이 사용자가 게시된 대시보드를 보거나 수정할 수 있는지 정의합니다. 

모든 게시된 대시보드는 Azure 리소스로 구현됩니다. 따라서 구독 내에서 관리 가능한 항목으로 존재하며 리소스 그룹에 포함됩니다.  액세스 제어 관점에서 대시보드는 가상 머신 또는 저장소 계정과 같은 다른 리소스와 차이가 없습니다.

> [!TIP]
> 대시보드의 개별 타일은 표시하는 리소스에 따라 고유한 액세스 제어 요구 사항을 적용합니다.  그러므로 개별 타일에 있는 데이터를 보호하면서 광범위하게 공유할 수 있는 대시보드를 디자인할 수 있습니다.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>대시보드에 대한 액세스 제어 이해
RBAC(역할 기반 Access Control)를 사용하여 세 개의 다른 수준 범위로 역할에 사용자를 할당할 수 있습니다.

* subscription
* 리소스 그룹
* resource

할당한 사용 권한은 구독에서 리소스까지 상속됩니다. 게시된 대시보드는 리소스입니다. 따라서 게시된 대시보드에 대해서도 작동하는 구독의 역할에 할당된 사용자가 이미 있을 수도 있습니다. 

다음은 예제입니다.  Azure 구독을 보유하고 구독의 **소유자**, **참여자** 또는 **읽기 권한자** 역할에 할당된 팀의 여러 멤버가 있다고 가정해 보겠습니다. 소유자 또는 참여자인 사용자는 구독 내에서 대시보드를 나열, 보기, 만들기, 수정 또는 삭제할 수 있습니다.  읽기 권한자인 사용자는 대시보드를 나열 및 볼 수 있지만 수정 또는 삭제할 수 없습니다.  읽기 권한자 액세스를 보유한 사용자는 게시된 대시보드에 대한 로컬 편집을 만들 수 있지만(예: 문제 해결 시) 해당 변경 내용을 서버로 다시 게시할 수 없습니다.  대시보드의 개인 복사본을 직접 만들 수 있는 옵션을 갖게 됩니다.

그러나 여러 대시보드를 포함하는 리소스 그룹 또는 개별 대시보드에 대한 사용 권한을 할당할 수도 있습니다. 예를 들어, 사용자 그룹이 구독에 걸친 제한된 사용 권한을 갖지만 특정 대시보드에 더 큰 액세스 권한을 가져야 한다고 결정할 수 있습니다. 해당 대시보드에 대한 역할에 해당 사용자를 할당합니다. 

## <a name="publish-dashboard"></a>대시보드 게시
구독에 사용자 그룹과 공유할 대시보드의 구성을 완료한 경우를 가정하겠습니다. 다음 단계에서는 저장소 관리자라는 사용자 지정된 그룹을 설명하지만 원하는 모든 그룹에 이름을 지정할 수 있습니다. Active Directory 그룹을 만들고 해당 그룹에 사용자를 추가하는 방법에 대한 정보는 [Azure Active Directory에서 그룹 관리](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요.

1. 대시보드에서 **공유**를 선택합니다.
   
     ![공유 선택](./media/azure-portal-dashboard-share-access/select-share.png)
2. 액세스를 할당하기 전에 대시보드를 게시해야 합니다. 기본적으로 대시보드는 **대시보드**라는 리소스 그룹에 게시됩니다. **게시**를 선택합니다.
   
     ![게시](./media/azure-portal-dashboard-share-access/publish.png)

대시보드가 이제 게시되었습니다. 구독에서 상속된 사용 권한이 적절한 경우 작업을 더 수행할 필요가 없습니다. 조직에서 다른 사용자가 해당 구독 수준 역할에 따라 대시보드에 액세스하고 대시보드를 수정할 수 있습니다. 그러나 이 자습서에서는 해당 대시보드에 대한 역할에 사용자 그룹을 할당하겠습니다.

## <a name="assign-access-to-a-dashboard"></a>대시보드에 액세스 할당
1. 대시보드를 게시한 후에 **사용자 관리**를 선택합니다.
   
     ![사용자 관리](./media/azure-portal-dashboard-share-access/manage-users.png)
2. 이 대시보드에 대한 역할을 이미 할당한 기존 사용자의 목록이 표시됩니다. 기존 사용자 목록은 아래 이미지와 다를 수 있습니다. 대부분의 경우 할당은 구독에서 상속됩니다. 새 사용자 또는 그룹을 추가하려면 **추가**를 선택합니다.
   
     ![사용자 추가](./media/azure-portal-dashboard-share-access/existing-users.png)
3. 부여하려는 사용 권한을 나타내는 역할을 선택합니다. 이 예에서는 **참가자**를 선택합니다.
   
     ![역할 선택](./media/azure-portal-dashboard-share-access/select-role.png)
4. 역할에 할당하려는 사용자 또는 그룹을 선택합니다. 목록에 원하는 사용자 또는 그룹이 표시되지 않으면 검색 상자를 사용합니다. 사용 가능한 그룹 목록은 Active Directory에서 만든 그룹에 따라 달라 집니다.
   
     ![사용자 선택](./media/azure-portal-dashboard-share-access/select-user.png) 
5. 사용자 또는 그룹을 추가했으면 **확인**을 선택합니다. 
6. 새 할당은 사용자 목록에 추가됩니다. 해당 **액세스**는 **상속됨**이 아닌 **할당**으로 나열됩니다.
   
     ![할당된 역할](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>다음 단계
* 역할의 목록을 참조 하세요. [RBAC: 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조하세요.
* 리소스 관리에 관한 자세한 내용은 [포털을 통한 Azure 리소스 관리](resource-group-portal.md)를 참조하세요.

