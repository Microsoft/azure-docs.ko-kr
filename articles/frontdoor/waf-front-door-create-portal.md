---
title: Azure portal을 사용 하 여 웹 응용 프로그램 방화벽 정책에 대 한 Azure 프런트 도어 만들기
titlesuffix: Azure web application firewall
description: Azure portal을 사용 하 여 웹 응용 프로그램 방화벽 (WAF) 정책을 만드는 방법에 알아봅니다.
services: frontdoor
documentationcenter: na
author: KumudD
manager: twooley
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/8/2019
ms.author: kumud;tyao
ms.openlocfilehash: 26db3a67c3efbd0ba2a5c58facd0c07175f7ed12
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61460252"
---
# <a name="create-a-waf-policy-for-azure-front-door-by-using-the-azure-portal"></a>Azure portal을 사용 하 여 WAF 정책에 대 한 Azure 프런트 도어 만들기

이 문서에서는 Azure의 기본 웹 응용 프로그램 방화벽 (WAF) 정책을 만들고 Azure 프런트 도어에서 프런트 엔드 호스트에 적용 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>필수 조건

[빠른 시작: Front Door 프로필 만들기](quickstart-create-front-door.md)에 설명된 지침에 따라 Front Door 프로필을 만듭니다. 

## <a name="create-a-waf-policy"></a>WAF 정책 만들기

먼저 기본 WAF 정책 만들기와 관리 되는 기본 규칙 설정 (DRS) 포털을 사용 하 여 

1. 맨 위 왼쪽 화면에서 선택 **리소스 만들기**>에 대 한 검색 **WAF**> 선택 **웹 응용 프로그램 방화벽 (미리 보기)** > 선택  **만들**합니다.
2. 에 **기본 사항** 탭의 **WAF 정책 만들기** 페이지, 입력 또는 다음 정보를 선택, 나머지 설정에 대 한 기본값을 적용 한 다음 선택 **검토 + 만들기**:

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | 구독            |첫 번째 관문 구독 이름을 선택 합니다.|
    | 리소스 그룹          |첫 번째 관문 리소스 그룹 이름을 선택 합니다.|
    | 정책 이름             |WAF 정책의 고유한 이름을 입력 합니다.|

   ![WAF 정책 만들기](./media/waf-front-door-create-portal/basic.png)

3. 에 **연결** 탭의 **WAF 정책 만들기** 페이지에서 **추가 프런트 엔드 호스트**, 다음 설정을 입력 하 고 선택한 **추가**:

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | 첫 번째 관문              | 첫 번째 관문 프로필 이름을 선택 합니다.|
    | 프런트 엔드 호스트           | 첫 번째 관문 호스트와의 이름을 선택한 다음 선택 **추가**합니다.|
    
    > [!NOTE]
    > 프런트 엔드 호스트에 WAF 정책에 연결 된 경우 회색으로 표시 됩니다. 먼저 연결 된 정책에서 프런트 엔드 호스트를 제거 하 고 새 WAF 정책에 프런트 엔드 호스트에 다시 연결 해야 합니다.
1. 선택 **검토 + 만들기**을 선택한 후 **만들기**합니다.

## <a name="configure-waf-rules-optional"></a>(선택 사항) WAF 규칙 구성

### <a name="change-mode"></a>모드 변경

WAF 정책을 만들 때 기본 WAF에서 정책이입니다 **검색** 모드입니다. **검색** WAF 로그는 WAF 규칙을 일치 하는 요청은 기록 하는 대신, WAF 모드는 모든 요청을 차단 하지 않습니다.
작업에서 WAF를 보려는 모드 설정을 변경할 수 있습니다 **감지** 하 **방지**합니다. **방지** 일치 규칙의 기본 규칙 설정 (DRS)를 정의 된 차단 된 후 WAF 로그에서 기록 모드를 요청 합니다.

 ![WAF 변경 정책 모드](./media/waf-front-door-create-portal/policy.png)

### <a name="default-rule-set-drs"></a>기본 규칙 집합 (DRS)

Azure 관리 되는 기본 규칙 집합은 기본적으로 사용 됩니다. 규칙 그룹 내의 개별 규칙을 사용 하지 않으려면 선택 되는 규칙 그룹 내에서 규칙을 확장 합니다 **확인란** 선택한 규칙 번호 앞에 **사용 하지 않도록 설정** 위의 탭 합니다. 규칙 내에서 개별 규칙 설정 규칙 번호 앞에 있는 확인란을 선택 하 고 선택한 작업 형식을 변경 하는 **동작을 변경** 위의 탭 합니다.

 ![WAF 규칙 집합 변경](./media/waf-front-door-create-portal/managed.png)

## <a name="next-steps"></a>다음 단계

- 에 대 한 자세한 [Azure 웹 응용 프로그램 방화벽](waf-overview.md)합니다.
- 에 대해 자세히 알아보세요 [Azure 프런트 도어](front-door-overview.md)합니다.




