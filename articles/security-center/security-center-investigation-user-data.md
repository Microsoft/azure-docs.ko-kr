---
title: Azure Security Center 조사에서 사용자 데이터 관리 | Microsoft Docs
description: " Azure Security Center 조사 기능에서 사용자 데이터를 관리하는 방법을 알아봅니다. "
services: operations-management-suite
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: operations-management-suite
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: rkarlin
ms.openlocfilehash: bec9be627937913c268d65bb6cdadcf68c7a3fb4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60909216"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Azure Security Center 조사에서 사용자 데이터 관리
이 아티클에서는 Azure Security Center 조사 기능에서 사용자 데이터를 관리하는 방법에 대한 정보를 제공합니다. 조사 데이터에 저장 됩니다 [Azure Monitor 로그](../log-analytics/log-analytics-overview.md) Security Center에서 노출 합니다. 사용자 데이터 관리에는 데이터를 삭제하거나, 내보내는 기능이 포함됩니다.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>개인 데이터 검색 및 식별
Azure Portal에서 Security Center의 [조사 기능](../security-center/security-center-investigation.md)을 사용하여 개인 데이터를 검색할 수 있습니다. 조사 기능은 **보안 경고** 아래에서 사용할 수 있습니다.

조사 기능은 **엔터티** 탭 아래에서 모든 엔터티, 사용자 정보 및 데이터를 표시합니다.

## <a name="securing-and-controlling-access-to-personal-information"></a>개인 정보에 대한 액세스 보안 및 제어
읽기 권한자, 소유자, 기여자 또는 계정 관리자 역할이 할당된 Security Center 사용자는 도구 내에서 고객 데이터에 액세스할 수 있습니다.

읽기 권한자, 소유자 및 기여자 역할에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어의 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조하세요. 계정 관리자 역할에 대한 자세한 내용은 [Azure 구독 관리자](../billing/billing-add-change-azure-subscription-administrator.md)를 참조하세요.

## <a name="deleting-personal-data"></a>개인 데이터 삭제
소유자, 기여자 또는 계정 관리자 역할이 할당된 Security Center 사용자는 조사 정보를 삭제할 수 있습니다.

조사를 삭제하기 위해 Azure Resource Manager REST API에 `DELETE` 요청을 제출할 수 있습니다.

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

`GET` 요청을 사용하는 모든 인시던트를 나열하여 `incidentName` 입력을 찾을 수 있습니다.

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>개인 데이터 내보내기
소유자, 기여자 또는 계정 관리자 역할이 할당된 Security Center 사용자는 조사 정보를 내보낼 수 있습니다. 조사 정보를 내보내려면 **엔터티** 탭으로 이동하여 관련 정보를 복사하고 붙여넣습니다.

## <a name="next-steps"></a>다음 단계
사용자 데이터를 관리하는 방법에 대한 자세한 내용은 [Azure Security Center에서 사용자 데이터 관리](security-center-privacy.md)를 참조하세요.
Azure Monitor 로그에서 개인 데이터를 삭제 하는 방법에 대 한 자세한 내용은 참조 하세요 [내보내고 개인 데이터를 삭제 하는 방법을](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data)합니다.
