---
title: Azure 데이터 탐색기에서 데이터베이스 또는 테이블을 만들거나 삭제하지 못함
description: 이 문서에서는 Azure 데이터 탐색기에서 데이터베이스와 테이블의 만들기 또는 삭제와 관련된 문제를 해결하는 단계를 설명합니다.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 0d221138914230d5455dc0addbe08cdaaed36a0b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60826257"
---
# <a name="troubleshoot-failure-to-create-or-delete-a-database-or-table-in-azure-data-explorer"></a>문제 해결: Azure 데이터 탐색기에서 데이터베이스 또는 테이블을 만들거나 삭제하지 못함

Azure 데이터 탐색기에서는 정기적으로 데이터베이스와 테이블을 사용합니다. 이 문서에서는 발생할 수 있는 문제를 해결하는 단계를 제공합니다.

## <a name="creating-a-database"></a>데이터베이스 만들기

1. 적절한 권한이 있는지 확인합니다. 데이터베이스를 만들려면 Azure 구독에 대한 *기여자* 또는 *소유자* 역할의 멤버여야 합니다. 필요한 경우 사용자는 구독 관리자와 협력하여 해당 역할에 추가될 수 있습니다.

1. 데이터베이스 이름에 대한 이름 유효성 검사 오류가 없는지 확인합니다. 이름은 최대 260자의 영숫자여야 합니다.

1. 데이터베이스 보존 및 캐싱 값이 허용되는 범위 내에 있는지 확인합니다. 보존 기간은 1-36,500일(100년)이어야 합니다. 캐싱은 1과 보존에 설정된 최댓값 사이여야 합니다.

## <a name="deleting-or-renaming-a-database"></a>데이터베이스 삭제 또는 이름 바꾸기

적절한 권한이 있는지 확인합니다. 데이터베이스를 삭제하거나 이름을 바꾸려면 Azure 구독에 대한 *기여자* 또는 *소유자* 역할의 멤버여야 합니다. 필요한 경우 사용자는 구독 관리자와 협력하여 해당 역할에 추가될 수 있습니다.

## <a name="creating-a-table"></a>테이블 만들기

1. 적절한 권한이 있는지 확인합니다. 테이블을 만들려면 데이터베이스에 대한 *데이터베이스 관리자* 또는 *데이터베이스 사용자* 역할의 멤버이거나 Azure 구독에 대한 *기여자* 또는 *소유자* 역할의 멤버여야 합니다. 필요한 경우 사용자는 구독 관리자 또는 클러스터 관리자와 협력하여 해당 역할에 추가될 수 있습니다.

    권한에 대한 자세한 내용은 [데이터베이스 권한 관리](manage-database-permissions.md)를 참조하세요.

1. 동일한 이름의 테이블이 아직 없는지 확인합니다. 존재 하는 경우 다음을 수행할 수 있습니다. 다른 이름을;를 사용 하 여 테이블 만들기 기존 테이블의 이름을 바꾸고 (필요 *테이블 관리* 역할); 기존 테이블을 삭제 하거나 (필요 *데이터베이스 관리자* 역할). 다음 명령을 사용합니다.

    ```Kusto
    .drop table <TableName>

   .rename table <OldTableName> to <NewTableName>
    ```

## <a name="deleting-or-renaming-a-table"></a>테이블 삭제 또는 이름 바꾸기

적절한 권한이 있는지 확인합니다. 테이블을 삭제하거나 이름을 바꾸려면 데이터베이스에 대한 *데이터베이스 관리자* 또는 *테이블 관리자* 역할의 멤버여야 합니다. 필요한 경우 사용자는 구독 관리자 또는 클러스터 관리자와 협력하여 해당 역할에 추가될 수 있습니다.

권한에 대한 자세한 내용은 [데이터베이스 권한 관리](manage-database-permissions.md)를 참조하세요.

## <a name="general-guidance"></a>일반 지침

1. [Azure 서비스 상태 대시보드](https://azure.microsoft.com/status/)를 확인하세요. 데이터베이스 또는 테이블을 사용하려는 지역의 Azure 데이터 탐색기 상태를 확인합니다.

    **양호**(녹색 확인 표시) 상태가 아닌 경우 해당 상태가 개선된 후에 다시 시도하세요.

1. 문제를 해결하는 데 여전히 지원이 필요한 경우 [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)에서 지원 요청을 여세요.

## <a name="next-steps"></a>다음 단계

[클러스터 상태 확인](check-cluster-health.md)