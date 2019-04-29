---
title: 'Azure Portal: SQL Database 동적 데이터 마스킹 | Microsoft Docs'
description: Azure Portal에서 SQL 데이터베이스 동적 데이터 마스킹을 시작하는 방법
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 03/04/2018
ms.openlocfilehash: 3d5ab203268ced1951d2ba9c852ece5bd5467c68
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61077853"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Azure Portal에서 SQL Database 동적 데이터 마스킹 시작

이 문서에서는 Azure Portal을 사용하여 [동적 데이터 마스킹](sql-database-dynamic-data-masking-get-started.md)을 구현하는 방법을 보여 줍니다. [Azure SQL Database cmdlet](https://docs.microsoft.com/powershell/module/az.sql/) 또는 [REST API](https://docs.microsoft.com/rest/api/sql/)를 사용하여 동적 데이터 마스킹을 구현할 수도 있습니다.

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Azure Portal을 사용하여 데이터베이스에 대한 동적 데이터 마스킹 설정

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal을 시작합니다.
2. 마스킹하려는 중요한 데이터가 포함된 데이터베이스의 설정 페이지로 이동합니다.
3. **동적 데이터 마스킹** 구성 페이지를 시작하는 **동적 데이터 마스킹** 타일을 클릭합니다.

   * 아래쪽의 **작업** 섹션으로 스크롤한 다음 **동적 데이터 마스킹**을 클릭해도 됩니다.

     ![탐색 창](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)

4. **동적 데이터 마스킹** 구성 페이지에서 권장 엔진이 마스킹에 대한 플래그를 지정한 일부 데이터베이스 열이 표시될 수 있습니다. 권장 사항을 적용하려면 하나 이상의 열에 대해 **마스크 추가**를 클릭합니다. 그러면 해당 열의 기본 형식에 기반한 마스크가 만들어집니다. 마스킹 규칙을 클릭하고 마스킹 필드 형식을 원하는 다른 형식으로 편집하면 마스킹 기능을 변경할 수 있습니다. 반드시 **저장**을 클릭하여 설정을 저장합니다.

    ![탐색 창](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)

5. 데이터베이스의 모든 열에 마스크를 추가하려면 **동적 데이터 마스킹** 구성 페이지의 위쪽에서 **마스크 추가**를 클릭하여 **마스킹 규칙 추가** 구성 페이지를 엽니다.

    ![탐색 창](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)

6. **스키마**, **테이블** 및 **열**을 선택하여 마스킹하도록 지정되는 필드를 정의합니다.
7. 중요한 데이터 마스킹 범주 목록에서 **마스킹 필드 형식** 을 선택합니다.

    ![탐색 창](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)

8. 데이터 마스킹 규칙 페이지에서 **저장**을 클릭하여 동적 데이터 마스킹 정책의 마스킹 규칙 집합을 업데이트합니다.
9. 마스킹에서 제외되며 마스크되지 않은 중요 데이터에 액세스할 수 있는 SQL 사용자 또는 AAD ID를 입력합니다. 세미콜론으로 구분된 사용자 목록이어야 합니다. 관리자 권한이 있는 사용자는 언제든지 마스킹되지 않은 원래의 데이터에 액세스할 수 있습니다.

    ![탐색 창](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    > [!TIP]
    > 애플리케이션 계층에서 권한이 있는 애플리케이션 사용자에 대해 중요한 데이터를 표시할 수 있도록 지정하려면 애플리케이션이 데이터베이스 쿼리에 사용할 SQL 사용자나 AAD ID를 추가합니다. 중요한 데이터의 노출을 최소화하려면 권한이 있는 사용자 수를 최소한으로 이 목록에 포함하는 것이 좋습니다.

10. 데이터 마스킹 구성 페이지에서 **저장**을 클릭하여 새 마스킹 정책 또는 업데이트된 마스킹 정책을 저장합니다.

## <a name="next-steps"></a>다음 단계

* 동적 데이터 마스킹의 개요는 [동적 데이터 마스킹](sql-database-dynamic-data-masking-get-started.md)을 참조하세요.
* [Azure SQL Database cmdlet](https://docs.microsoft.com/powershell/module/az.sql/) 또는 [REST API](https://docs.microsoft.com/rest/api/sql/)를 사용하여 동적 데이터 마스킹을 구현할 수도 있습니다.
