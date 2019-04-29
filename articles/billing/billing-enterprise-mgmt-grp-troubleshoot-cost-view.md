---
title: Azure 기업 비용 보기 문제 해결 | Microsoft Docs
description: Azure Portal 내에서 조직의 비용 보기에 발생할 수 있는 문제를 해결하는 방법에 대해 알아봅니다.
author: rthorn17
manager: adpick
editor: ''
ms.assetid: ''
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2017
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: d35996b16d615a198b9a6039386f6b295172f388
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615745"
---
# <a name="troubleshoot-enterprise-cost-views"></a>기업 비용 보기 문제 해결

기업 등록계약 내에 등록된 사용자에게 비용이 표시되지 않도록 하는 여러 설정이 있습니다.  이러한 설정은 등록 관리자가 관리합니다. 또는 Microsoft를 통해 직접 등록을 구입하지 않은 경우 파트너가 설정을 관리합니다.  이 문서를 통해 이러한 설정이 무엇이고 등록에 미치는 영향을 이해할 수 있습니다. 이러한 설정은 Azure RBAC(역할 기반 액세스 제어) 역할과는 별개입니다.

## <a name="enabling-access-to-costs"></a>비용에 대한 액세스 사용

비용 정보를 검색할 때 권한 없음 또는 *"등록에서 비용 보기를 사용할 수 없습니다."* 라는 메시지가 표시되나요?
![구독에 대한 현재 비용 필드에 “권한 없음”이 표시된 스크린샷](media/billing-enterprise-mgmt-groups/unauthorized.png)

다음 이유 중 하나 때문일 수 있습니다.

1. 기업 파트너를 통해 Azure를 구입했으며 파트너가 아직 가격 책정을 릴리스하지 않았습니다. [Enterprise Portal](https://ea.azure.com) 내의 가격 책정 설정을 업데이트하도록 파트너에게 요청하세요.
2. EA Direct 고객인 경우 다음과 같은 몇 가지 가능성이 있습니다.
    * 사용자가 계정 소유자이며 등록 관리자가 **AO 요금 보기** 설정을 사용하지 않도록 설정했습니다.  
    * 사용자가 부서 관리자이며 등록 관리자가 **DA 요금 보기** 설정을 사용하지 않도록 설정했습니다.
    * 등록 관리자에게 액세스 권한을 문의하세요. 등록 관리자는 [Enterprise Portal](https://ea.azure.com/manage/enrollment)에서 설정을 업데이트할 수 있습니다.

      ![요금 보기에 대한 Enterprise Portal 설정을 보여 주는 스크린샷](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)

## <a name="asset-is-unavailable"></a>자산을 사용할 수 없음

구독 또는 관리 그룹에 액세스하려고 할 때 “이 자산을 사용할 수 없습니다.”라는 오류 메시지가 표시되는 경우에는 이 항목을 볼 수 있는 올바른 역할이 없는 것입니다.  

![“자산을 사용할 수 없음” 메시지를 보여 주는 스크린샷](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Azure 구독 또는 관리 그룹 관리자에게 액세스 권한을 요청하세요. 자세한 내용은 [RBAC 및 Azure Portal을 사용하여 액세스 관리](../role-based-access-control/role-assignments-portal.md)를 참조하세요.
