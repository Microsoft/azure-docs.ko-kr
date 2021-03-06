---
title: 'Azure Front 도어: Front 도어 규칙 집합 구성'
description: 이 문서에서는 규칙 집합을 구성 하는 방법에 대 한 지침을 제공 합니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 6863c492059ccee152ecf3d03a09e61793576bcb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715601"
---
# <a name="configure-a-rule-set-with-azure-front-door-standardpremium-preview"></a>Azure 전면 도어 표준/프리미엄 (미리 보기)을 사용 하 여 규칙 집합 구성

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

이 문서에서는 Azure Portal에서 규칙 집합 및 규칙의 첫 번째 집합을 만드는 방법을 보여 줍니다. 그런 다음 규칙 집합 페이지 또는 끝점 관리자에서 규칙 집합을 경로에 연결 하는 방법을 알아봅니다.

> [!IMPORTANT]
> Azure 전면 도어 표준/프리미엄 (미리 보기)은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* 규칙 집합을 구성 하려면 먼저 Azure 전면 도어 표준/프리미엄을 만들어야 합니다. 자세한 내용은 [빠른 시작: Azure Front 도어 표준/프리미엄 프로필 만들기](create-front-door-portal.md)를 참조 하세요.

## <a name="configure-rule-set-in-azure-portal"></a>Azure Portal에서 규칙 집합 구성

1. 프런트 도어 프로필 내에서 **설정** 아래에 있는 **규칙 집합** 을 선택 합니다. **추가** 를 선택 하 고 규칙 집합 이름을 지정 합니다.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set-1.png" alt-text="규칙 집합 방문 페이지의 스크린샷":::
    
1. **규칙 추가** 를 선택 하 여 첫 번째 규칙을 만듭니다. 규칙 이름을 지정 합니다. 그런 다음 **조건 추가** 또는 **작업 추가** 를 선택 하 여 규칙을 정의 합니다. 한 규칙에 대해 최대 10 개의 조건과 5 개의 작업을 추가할 수 있습니다. 이 예에서는 서버 변수를 사용 하 여 URL에 *contoso* 를 포함 하는 요청에 대 한 응답 헤더를 추가 합니다.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-create-rule-set.png" alt-text="규칙 집합 구성 페이지의 스크린샷":::
    
    > [!NOTE]
    > * 규칙에서 조건 또는 동작을 삭제 하려면 특정 조건 또는 동작의 오른쪽에 있는 휴지통을 사용 합니다.
    > * 들어오는 모든 트래픽에 적용되는 규칙을 만들려면 조건을 지정하지 마세요.
    > * 특정 규칙이 충족 될 경우 남은 규칙 평가를 중지 하려면 **남은 규칙 평가 중지** 를 선택 합니다. 이 옵션을 선택 하면 일치 하는 조건이 충족 되는지에 관계 없이 규칙 집합의 나머지 모든 규칙이 실행 되지 않습니다.  

1. 규칙 집합 내에서 규칙의 우선 순위는 화살표 단추를 사용 하 여 규칙을 더 높거나 낮은 우선 순위로 이동 하 여 확인할 수 있습니다. 목록이 오름차순 이므로 가장 중요 한 규칙이 먼저 나열 됩니다.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-rule-set-change-orders.png" alt-text="규칙 집합 우선 순위의 스크린샷" lightbox="../media/how-to-configure-rule-set/front-door-rule-set-change-orders-expanded.png":::

1. 하나 이상의 규칙을 만든 후에는 **저장** 을 선택 하 여 규칙 집합 만들기를 완료 합니다.

1. 이제 규칙 집합을 경로에 연결 하 여 적용할 수 있습니다. 규칙 집합 페이지를 통해 설정 된 규칙을 연결 하거나, 끝점 관리자로 이동 하 여 연결을 만들 수 있습니다.
 
    **규칙 집합 페이지**: 
    
    1. 연결할 규칙 집합을 선택 합니다.
    
    1. 연결 되지 않은 링크를 *선택 합니다.*
     

    1. 그런 다음 **경로 연결** 페이지에서 규칙 집합과 연결할 끝점과 경로를 선택 합니다. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set.png" alt-text="경로 만들기 페이지의 스크린샷":::    
        
    1. 선택한 경로 아래에 규칙 집합이 여러 개 있는 경우 규칙 집합 순서를 변경 하려면 *다음* 을 선택 합니다. 규칙 집합이 위에서 아래로 실행 됩니다. 규칙 집합을 선택 하 여 순서를 변경 하 고 위나 아래로 이동할 수 있습니다. 그런 다음 *연결* 을 선택 합니다.
    
        > [!Note]
        > 하나의 규칙 집합만이 페이지의 단일 경로에 연결할 수 있습니다. 여러 경로를 사용 하 여 규칙 집합을 연결 하려면 끝점 관리자를 사용 하세요.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-2.png" alt-text="규칙 집합 주문의 스크린샷":::
    
    1. 이제 규칙 집합은 경로에 연결 됩니다. 응답 헤더를 살펴보면 지역 국가가 추가 된 것을 볼 수 있습니다.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-3.png" alt-text="경로와 연결 된 규칙의 스크린샷":::

   **끝점 관리자**: 
    
    1. 끝점 관리자로 이동 하 여 규칙 집합과 연결할 끝점을 선택 합니다.
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1.png" alt-text="끝점 관리자에서 끝점을 선택 하는 스크린샷" lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-1-expanded.png":::

    1. *끝점 편집* 을 선택 합니다.  
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2.png" alt-text="끝점 관리자에서 끝점 편집을 선택 하는 스크린샷" lightbox="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-2-expanded.png":::

    1. 경로를 선택 합니다. 
    
         :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-3.png" alt-text="경로를 선택 하는 스크린샷":::
    
    1. *경로 업데이트* 페이지의 *규칙* 에서 드롭다운에서 경로에 연결할 규칙 집합을 선택 합니다. 그런 다음 규칙 집합을 위아래로 이동 하 여 순서를 변경할 수 있습니다. 
    
        :::image type="content" source="../media/how-to-configure-rule-set/front-door-associate-rule-set-endpoint-manager-4.png" alt-text="경로 업데이트 페이지의 스크린샷":::
    
    1. 그런 다음 *업데이트* 또는 *추가* 를 선택 하 여 연결을 완료 합니다.

## <a name="delete-a-rule-set-from-your-azure-front-door-profile"></a>Azure Front 도어 프로필에서 규칙 집합을 삭제 합니다.

이전 단계에서 규칙 집합을 구성 하 고 경로에 연결 했습니다. 앞 도어와 연결 된 규칙 집합이 더 이상 필요 하지 않은 경우 다음 단계를 완료 하 여 규칙 집합을 제거할 수 있습니다.

1. **설정** 아래의 **규칙 집합 페이지로** 이동 하 여 연결 된 모든 경로에서 규칙 집합의 연결을 해제 합니다.

1. 경로를 확장 하 고 세 개의 점을 선택 합니다. 그런 다음 *경로 편집* 을 선택 합니다.

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-1.png" alt-text="규칙 집합에서 확장 된 경로 스크린샷":::

1. 경로 페이지에서 규칙 섹션으로 이동 하 고 규칙 집합을 선택한 다음 *삭제* 단추를 선택 합니다. 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2.png" alt-text="규칙 집합을 삭제 하기 위한 경로 업데이트 페이지의 스크린샷" lightbox="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-2-expanded.png":::

1. *업데이트* 를 선택 하면 규칙 집합이 경로에서 분리 됩니다.

1. 2-5 단계를 반복 하 여이 규칙 집합에 연결 된 다른 경로를 분리 *합니다. 경로* 상태에 연결 되지 않음이 표시 됩니다.

1. 연결 되지 않은 규칙 집합의 경우 오른쪽에 있는 세 개의 점을 클릭 하 고 *삭제* *를 선택* 하 여 규칙 집합을 삭제할 수 있습니다. 

   :::image type="content" source="../media/how-to-configure-rule-set/front-door-disassociate-rule-set-3.png" alt-text="규칙 집합을 삭제 하는 방법의 스크린샷":::

1. 이제 규칙 집합이 삭제 됩니다.

## <a name="next-steps"></a>다음 단계

[규칙 집합을 사용 하 여 보안 헤더](how-to-add-security-headers.md)를 추가 하는 방법을 알아봅니다.
