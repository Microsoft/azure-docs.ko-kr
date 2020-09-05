---
title: Azure Security Center의 보안 권장 사항
description: 이 문서에서는 Azure Security Center의 권장 사항이 Azure 리소스를 보호하고 보안 정책을 준수하는 데 어떤 도움이 되는지 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2019
ms.author: memildin
ms.openlocfilehash: 6363100c844d071a3bb47521cec6ff7e988f6af8
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263230"
---
# <a name="security-recommendations-in-azure-security-center"></a>Azure Security Center의 보안 권장 사항 
이 항목에서는 Azure 리소스를 보호 하는 데 도움이 되는 Azure Security Center의 권장 사항을 확인 하 고 이해 하는 방법을 설명 합니다.

> [!NOTE]
> 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다.  이 문서는 단계별 가이드가 아닙니다.
>

## <a name="what-are-security-recommendations"></a>보안 권장 사항이란?

권장 사항은 리소스를 보호 하기 위해 수행할 작업입니다.

Security Center는 Azure 리소스의 보안 상태를 주기적으로 분석 하 여 잠재적인 보안 취약성을 식별 합니다. 그런 다음 이러한 취약성을 해결 하는 방법에 대 한 권장 사항을 제공 합니다.

각 권장 사항은 다음을 제공 합니다.

- 문제에 대 한 간단한 설명입니다.
- 권장 사항을 구현 하기 위해 수행할 수정 단계입니다.
- 영향을 받는 리소스입니다.

## <a name="monitor-recommendations"></a>권장 사항 모니터링 <a name="monitor-recommendations"></a>

Security Center는 리소스의 보안 상태를 분석 하 여 잠재적인 취약성을 식별 합니다. **개요** 의 **권장 사항** 타일에는 Security Center에서 식별 된 권장 사항의 총 수가 표시 됩니다.

![Security center 개요](./media/security-center-recommendations/asc-overview.png)

1. **개요**에서 **권장 사항 타일** 을 선택 합니다. **권장 사항** 목록이 열립니다.

1. 권장 사항은 보안 제어로 그룹화 됩니다.

      ![보안 제어 별로 그룹화 된 권장 사항](./media/security-center-recommendations/view-recommendations.png)

1. 컨트롤을 확장 하 고 특정 권장 사항을 선택 하 여 권장 사항 페이지를 표시 합니다.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="권장 사항 세부 정보 페이지입니다." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    페이지에는 다음이 포함 됩니다.

    - **심각도 표시기**
    - **새로 고침 간격**  (관련 된 경우) 
    - **설명** -문제에 대 한 간단한 설명입니다.
    - **수정 단계** -영향을 받는 리소스에 대 한 보안 문제를 해결 하는 데 필요한 수동 단계에 대 한 설명입니다. ' 빠른 수정 '을 사용 하는 권장 사항에 대해서는 리소스에 제안 된 픽스를 적용 하기 전에 **업데이트 관리 논리 보기** 를 선택할 수 있습니다. 
    - **영향을 받는 리소스** -리소스는 탭으로 그룹화 됩니다.
        - **정상 리소스** – 영향을 받지 않거나 이미 문제를 해결 한 관련 리소스입니다.
        - **비정상 리소스** – 식별 된 문제의 영향을 받는 리소스입니다.
        - **해당 하지 않는 리소스** – 권장 구성이 명확한 답변을 제공할 수 없는 리소스입니다. 해당 없음 탭에는 각 리소스의 이유가 포함 됩니다. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="이유가 있는 해당 리소스가 아닙니다.":::


 
## <a name="next-steps"></a>다음 단계

이 문서에서는 보안 센터의 보안 권장 사항을 소개했습니다. 권장 사항을 수정 하는 방법을 알아보려면 다음을 수행 하십시오.

* [권장 사항](security-center-remediate-recommendations.md) 수정-Azure 구독 및 리소스 그룹에 대 한 보안 정책을 구성 하는 방법을 알아봅니다.
